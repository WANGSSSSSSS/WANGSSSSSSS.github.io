---
title: orbslam-tracking线程
date: 2021-04-01 15:25:40
categories:
- cv
- slam
tags:
- cv
- slam
cover: image-20210401174702609.png
---

# Tracking 线程

Tracking 线程整体流程包括关键帧的插入，当前帧的位置确定。位置确定的过程根据跟踪成功与否，可以分为，匀速运动模型估计，以及失败情况下的重定位。

![系统框图](./orbslam1/image-20210401174702609.png)

## 初始化

####  第一次初始化

根据当前帧的关键点个数进行判断，如果关键点的个数大于100个，则将当前帧作为初始化帧，同时状态转化为初始化阶段

```c++
void Tracking::FirstInitialization()
{
    //We ensure a minimum ORB features to continue, otherwise discard frame
    if(mCurrentFrame.mvKeys.size()>100)
    {
        mInitialFrame = Frame(mCurrentFrame);
        mLastFrame = Frame(mCurrentFrame);
        mvbPrevMatched.resize(mCurrentFrame.mvKeysUn.size());
        for(size_t i=0; i<mCurrentFrame.mvKeysUn.size(); i++)
            mvbPrevMatched[i]=mCurrentFrame.mvKeysUn[i].pt;

        if(mpInitializer)
            delete mpInitializer;

        mpInitializer =  new Initializer(mCurrentFrame,1.0,200);


        mState = INITIALIZING;
    }
}
```

#### 特征点检查

检查当前帧特征点的数量，如果特征点太少，删除初始化帧，重启

```c++
void Tracking::Initialize()
{
    if(mCurrentFrame.mvKeys.size()<=100)
    {
        fill(mvIniMatches.begin(),mvIniMatches.end(),-1);
        mState = NOT_INITIALIZED;
        return;
    }    
    ...
}
```

进行特征点之间的匹配（当前帧和初始化帧），如果匹配结果太差，重新初始化，重启

```c++
void Tracking::Initialize()
{
    ...
    // Find correspondences
    ORBmatcher matcher(0.9,true);
    int nmatches = matcher.SearchForInitialization(
        			mInitialFrame,             
       				mCurrentFrame,
        			mvbPrevMatched,
        			mvIniMatches,
        			100);

    // Check if there are enough correspondences
    if(nmatches<100)
    {
        mState = NOT_INITIALIZED;
        return;
    }  
    ...
}
```

#### 计算位姿与地图点

利用pnp算法计算两个图像之间的相对位姿，之后三角化出地图点

```c++
void Tracking::Initialize()
{
    ...
    cv::Mat Rcw; // Current Camera Rotation
    cv::Mat tcw; // Current Camera Translation
    vector<bool> vbTriangulated; 
    // Triangulated Correspondences (mvIniMatches)
    if(mpInitializer->Initialize(
        mCurrentFrame, 
        mvIniMatches, 
        Rcw, tcw, 
        mvIniP3D, 
        vbTriangulated))
    {
        for(size_t i=0, iend=mvIniMatches.size(); i<iend;i++)
        {
            // 如果没有匹配点或者没有被三角化出来
            if(mvIniMatches[i]>=0 && !vbTriangulated[i])
            {
                mvIniMatches[i]=-1;
                nmatches--;
            }           
        }
		// 初始化地图
        CreateInitialMap(Rcw,tcw);
    }
    ...
}
```

#### 初始化地图

```c++
void Tracking::CreateInitialMap(cv::Mat &Rcw, cv::Mat &tcw)
{
    // Set Frame Poses
    mInitialFrame.mTcw = cv::Mat::eye(4,4,CV_32F);
    mCurrentFrame.mTcw = cv::Mat::eye(4,4,CV_32F);
    Rcw.copyTo(mCurrentFrame.mTcw.rowRange(0,3).colRange(0,3));
    tcw.copyTo(mCurrentFrame.mTcw.rowRange(0,3).col(3));

    // Create KeyFrames
    KeyFrame* pKFini = new KeyFrame(mInitialFrame,mpMap,mpKeyFrameDB);
    KeyFrame* pKFcur = new KeyFrame(mCurrentFrame,mpMap,mpKeyFrameDB);

    pKFini->ComputeBoW();
    pKFcur->ComputeBoW();

    // Insert KFs in the map
    mpMap->AddKeyFrame(pKFini);
    mpMap->AddKeyFrame(pKFcur);

    // Create MapPoints and asscoiate to keyframes
    // 建立地图点观测与关键帧之间的联系
    for(size_t i=0; i<mvIniMatches.size();i++)
    {
        if(mvIniMatches[i]<0)
            continue;

        //Create MapPoint.
        cv::Mat worldPos(mvIniP3D[i]);

        MapPoint* pMP = new MapPoint(worldPos,pKFcur,mpMap);

        pKFini->AddMapPoint(pMP,i);
        pKFcur->AddMapPoint(pMP,mvIniMatches[i]);

        pMP->AddObservation(pKFini,i);
        pMP->AddObservation(pKFcur,mvIniMatches[i]);

        pMP->ComputeDistinctiveDescriptors();
        pMP->UpdateNormalAndDepth();

        //Fill Current Frame structure
        mCurrentFrame.mvpMapPoints[mvIniMatches[i]] = pMP;

        //Add to Map
        mpMap->AddMapPoint(pMP);

    }

    // Update Connections
    pKFini->UpdateConnections();
    pKFcur->UpdateConnections();

    // Bundle Adjustment
    ROS_INFO("New Map created with %d points",mpMap->MapPointsInMap());
	// 全局优化位姿以及地图点
    Optimizer::GlobalBundleAdjustemnt(mpMap,20);

    // Set median depth to 1
    // 单目视觉slam缺少尺度信息，这个步骤是归一化地图尺度
    float medianDepth = pKFini->ComputeSceneMedianDepth(2);
    float invMedianDepth = 1.0f/medianDepth;

    if(medianDepth<0 || pKFcur->TrackedMapPoints()<100)
    {
        ROS_INFO("Wrong initialization, reseting...");
        Reset();
        return;
    }

    // Scale initial baseline
    cv::Mat Tc2w = pKFcur->GetPose();
    Tc2w.col(3).rowRange(0,3) = Tc2w.col(3).rowRange(0,3)*invMedianDepth;
    pKFcur->SetPose(Tc2w);

    // Scale points
    // 单目视觉slam缺少尺度信息，这个步骤是归一化地图尺度
    vector<MapPoint*> vpAllMapPoints = pKFini->GetMapPointMatches();
    for(size_t iMP=0; iMP<vpAllMapPoints.size(); iMP++)
    {
        if(vpAllMapPoints[iMP])
        {
            MapPoint* pMP = vpAllMapPoints[iMP];
            pMP->SetWorldPos(pMP->GetWorldPos()*invMedianDepth);
        }
    }

    mpLocalMapper->InsertKeyFrame(pKFini);
    mpLocalMapper->InsertKeyFrame(pKFcur);

    mCurrentFrame.mTcw = pKFcur->GetPose().clone();
    mLastFrame = Frame(mCurrentFrame);
    mnLastKeyFrameId=mCurrentFrame.mnId;
    mpLastKeyFrame = pKFcur;

    mvpLocalKeyFrames.push_back(pKFcur);
    mvpLocalKeyFrames.push_back(pKFini);
    mvpLocalMapPoints=mpMap->GetAllMapPoints();
    mpReferenceKF = pKFcur;

    mpMap->SetReferenceMapPoints(mvpLocalMapPoints);

    mpMapPublisher->SetCurrentCameraPose(pKFcur->GetPose());

    mState=WORKING;
}
```

## 跟踪与位姿更新

#### 跟踪前一帧图像

```c++
bool Tracking::TrackPreviousFrame()
{
    ORBmatcher matcher(0.9,true);
    vector<MapPoint*> vpMapPointMatches;

    // Search first points at coarse scale levels to get a rough initial estimate
    int minOctave = 0;
    int maxOctave = mCurrentFrame.mvScaleFactors.size()-1;
    if(mpMap->KeyFramesInMap()>5)
        minOctave = maxOctave/2+1;

    int nmatches = matcher.WindowSearch(mLastFrame,mCurrentFrame,200,vpMapPointMatches,minOctave);

    mLastFrame.mTcw.copyTo(mCurrentFrame.mTcw);
    mCurrentFrame.mvpMapPoints=vpMapPointMatches;

    // If enough correspondeces, optimize pose and project points from previous frame to search more correspondences
    {
        // Optimize pose with correspondences
        Optimizer::PoseOptimization(&mCurrentFrame);

        for(size_t i =0; i<mCurrentFrame.mvbOutlier.size(); i++)
            if(mCurrentFrame.mvbOutlier[i])
            {
                mCurrentFrame.mvpMapPoints[i]=NULL;
                mCurrentFrame.mvbOutlier[i]=false;
                nmatches--;
            }

        // Search by projection with the estimated pose
        nmatches += matcher.SearchByProjection(mLastFrame,mCurrentFrame,15,vpMapPointMatches);
    }


    mCurrentFrame.mvpMapPoints=vpMapPointMatches;

    // Optimize pose again with all correspondences
    Optimizer::PoseOptimization(&mCurrentFrame);

    // Discard outliers
    for(size_t i =0; i<mCurrentFrame.mvbOutlier.size(); i++)
        if(mCurrentFrame.mvbOutlier[i])
        {
            mCurrentFrame.mvpMapPoints[i]=NULL;
            mCurrentFrame.mvbOutlier[i]=false;
            nmatches--;
        }

    return nmatches>=10;
}
```

#### 匀速运动模型

匀速运动模型假设，当前时刻位姿是由前一帧时刻的位姿 + 运动估计

$Pose_t = Pose_{t-1} + motion_{t-1}$

运动估计表示为上上帧与上一帧之间的位姿变化

$motion_{t} = Pose_{t} - Pose_{t-1}$

由于相机位姿由齐次坐标下的矩阵描述，不能直接加减法来定义，应当表示为：

$T_t = \left [ \begin{matrix} R_{t-2}, t_{t-2} \\ 0,1\end{matrix} \right ]\left [ \begin{matrix} R_{t-1}^T, -R_{t-1}^Tt \\ 0,1\end{matrix} \right ]\left [ \begin{matrix} R_{t-1}, t_{t-1} \\ 0,1\end{matrix} \right ]$ 

```c++
bool Tracking::TrackWithMotionModel()
{
    ORBmatcher matcher(0.9,true);
    vector<MapPoint*> vpMapPointMatches;

    // Compute current pose by motion model
    mCurrentFrame.mTcw = mVelocity*mLastFrame.mTcw;
    ...
    // 速度的计算 velocity
     cv::Mat LastRwc = mLastFrame.mTcw.rowRange(0,3).colRange(0,3).t();
     cv::Mat Lasttwc = -LastRwc*mLastFrame.mTcw.rowRange(0,3).col(3);
     cv::Mat LastTwc = cv::Mat::eye(4,4,CV_32F);
     LastRwc.copyTo(LastTwc.rowRange(0,3).colRange(0,3));
     Lasttwc.copyTo(LastTwc.rowRange(0,3).col(3));
     mVelocity = mCurrentFrame.mTcw*LastTwc;  
    ...
}
```

##### 特征点增强

利用刚才更新好的位姿，现在把上一帧的地图点投影下来，再次计算在局部范围搜索特征点

```c++
int nmatches = matcher.SearchByProjection(mCurrentFrame,mLastFrame,15);
```

##### 优化当前帧位姿

```c++
 // Optimize pose with all correspondences
    Optimizer::PoseOptimization(&mCurrentFrame);

    // Discard outliers
    for(size_t i =0; i<mCurrentFrame.mvpMapPoints.size(); i++)
    {
        if(mCurrentFrame.mvpMapPoints[i])
        {
            if(mCurrentFrame.mvbOutlier[i])
            {
                mCurrentFrame.mvpMapPoints[i]=NULL;
                mCurrentFrame.mvbOutlier[i]=false;
                nmatches--;
            }
        }
    }

    return nmatches>=10;
```

#### 重定位

##### 寻找近似关键帧

```c++
bool Tracking::Relocalisation()
{
    // Compute Bag of Words Vector
    mCurrentFrame.ComputeBoW();

    // Relocalisation is performed when tracking is lost and forced at some stages during loop closing
    // Track Lost: Query KeyFrame Database for keyframe candidates for relocalisation
    vector<KeyFrame*> vpCandidateKFs;
    if(!RelocalisationRequested())
        vpCandidateKFs= mpKeyFrameDB->DetectRelocalisationCandidates(&mCurrentFrame);
    else // Forced Relocalisation: Relocate against local window around last keyframe
    {
        boost::mutex::scoped_lock lock(mMutexForceRelocalisation);
        mbForceRelocalisation = false;
        vpCandidateKFs.reserve(10);
        vpCandidateKFs = mpLastKeyFrame->GetBestCovisibilityKeyFrames(9);
        vpCandidateKFs.push_back(mpLastKeyFrame);
    }

    if(vpCandidateKFs.empty())
        return false;
    ...
}        
```

##### 寻找地图点进行PNP

对于找到的每个相似关键帧，分别与他们的地图点族进行pnp计算当前帧的位姿

```c++
ORBmatcher matcher(0.75,true);

    vector<PnPsolver*> vpPnPsolvers;
    vpPnPsolvers.resize(nKFs);

    vector<vector<MapPoint*> > vvpMapPointMatches;
    vvpMapPointMatches.resize(nKFs);

    vector<bool> vbDiscarded;
    vbDiscarded.resize(nKFs);

    int nCandidates=0;

    for(size_t i=0; i<vpCandidateKFs.size(); i++)
    {
        KeyFrame* pKF = vpCandidateKFs[i];
        if(pKF->isBad())
            vbDiscarded[i] = true;
        else
        {
            int nmatches = matcher.SearchByBoW(pKF,mCurrentFrame,vvpMapPointMatches[i]);
            if(nmatches<15)
            {
                vbDiscarded[i] = true;
                continue;
            }
            else
            {
                PnPsolver* pSolver = new PnPsolver(mCurrentFrame,vvpMapPointMatches[i]);
                pSolver->SetRansacParameters(0.99,10,300,4,0.5,5.991);
                vpPnPsolvers[i] = pSolver;
                nCandidates++;
                
                // 进行pnp求解当前相机位姿
                ...
                PnPsolver* pSolver = vpPnPsolvers[i];
            	cv::Mat Tcw = pSolver->iterate(5,bNoMore,vbInliers,nInliers);    
                ...
            }
        } 
    }
```

#### 跟踪局部地图

```c++
// 更新局部地图的地图点与关键帧
UpdateReference();

    // Search Local MapPoints
	//当前可以看到的，在当前帧视角里的
SearchReferencePointsInFrustum();

    // Optimize Pose
mnMatchesInliers = Optimizer::PoseOptimization(&mCurrentFrame);
	// 更新局部地图点的属性
for(size_t i=0; i<mCurrentFrame.mvpMapPoints.size(); i++)
        if(mCurrentFrame.mvpMapPoints[i])
        {
            if(!mCurrentFrame.mvbOutlier[i])
                mCurrentFrame.mvpMapPoints[i]->IncreaseFound();
        }
```

