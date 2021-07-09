### English

#### self introduction

I am wangshuai from north western polytechnic university, major in computer science and technology

### how do think the AI

ai is powerful, intelligent, expensive.

### introduce your university

nwpu is my university, nwpu is devoted to develop the aerospace technology and the national defense industry, our school is big and its location is remote, which is located in the foot of qinling mountain

### introduce your hometown

shanxi is a historical province, while xian is the captical of shanxi, xian is the ancient captical of thirteen dynasties, a lot of famous person is buried in xian, and xian is the start station of the silk road economic belt, i think xian is the center of western china.

### ICRA

i am the principal of the perception group of our firefly team for 2019 robomaster AI challenge , the duties of perception is including the target robot and its armor detection, assisting the robot to locate its position and the opponents , which we need to launch the bullet to against it. for the first task, i use SSD framework with pafpn architecture to finish, which is aimed to solve that the little object is neglected, because one pixel of the low featuremap represents 10 multipy 10 square patch, which is bigger than the smallest object, so i think it is enough to detection the little one, the obstacle is the low featuremap is weak, the fpn is a top down  feature fusion path, but i need a down top fusion ,so i introduce the pafpn to solve it, it works.

### PLA rocket force AI challenge

People's Liberation Army Rocket Force

this is a mutimodal image matching question, one is SAR image , the other is optical image,  the architecture is including two siamese feature extractor to map the different domin to the same feature space, and a matching head with moco loss to find the match point on feature map and use bicubic interpolation to find the match pixel point, the match process is using the sar featuremap as a super big convolution kernel to generate a  heatmap, then use mocoloss normalize function to convert the heatmap to score map, then interpolate the score map, the biggest value will indicate the optimal match point

why using bicubic interpolation?

for bilinear, if the start point is 0, the end point is 1, the interpolation will not bigger or smaller than this two point, if the target value is some point in the middle , the convex combination method like bilinear can not solve it, 

the moco loss?

the match is a contrastive learning process, or they are very similar. 

### What is the most interesting course for undergraduates

machine vision and artificial intelligence, which lead me to the 3 dimension computer vision, including how to get the transformation from one image to the other image, how to compute the relative pose for each image in a image set, or named muti view stereotype, and ackonwledge the industy application, like slam, simuen localization and mapping

### Why do you want to go to graduate school

because i want to know the theroy of artificial intelligence, and use this technology to change the normal world, like  study 3d computer vision to help ai agent or robot  construct the relationship of material in general environment, using the advanced perception technology, to help some engineer achieve a optimal result

### the planning of the graduate level

it is hard to answer a explict schedule
