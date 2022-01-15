---
title: receptive field
date: 2022-01-15 17:07:57
categories:
- cv
tags:
- cv
cover:
---

### Receptive Field

为了方便计算receptive field的大小，我自己总结了规律，有这样一个递推式：


$$
\left \{ \begin{aligned}
R_i &= d_i k_i S_{i-1} + (R_{i-1} - S_{i-1}) \\
S_i &= S_{i-1}s_i
\end{aligned} \right .
$$

其中：$k_i$ 为卷积核的大小， $s_i$ 为 卷积的步长stride， 由此可知$S_i$ 为feature map的步长， $R_i$ 为receptive field大小 

对于`eca-ResNet` 感受野计算如下：

```python
if __name__ == "__main__":
    model = eca_resnet18_small()
    S = [1]
    R = [1]
    for name, v in model.named_modules():
        if name == "" or "downsample" in name : continue
        k,s= 1,1
        if isinstance(v, nn.Conv2d):
        #if v is nn.Conv2d:
            k = v.kernel_size[0]*v.dilation[0]
            s = v.stride[0]
        elif isinstance(v, nn.AvgPool2d) or isinstance(v, nn.MaxPool2d):
            k = v.kernel_size
            s = v.stride
        else : pass
        R.append(S[-1] * k + R[-1] - S[-1])
        S.append(S[-1] * s)
        print(name, f" receptive filed size: {R[-1]}")
```

需要注意啊，python中判断是否是一个类的实例，需要使用`isinstance` 而不是 `is` 
