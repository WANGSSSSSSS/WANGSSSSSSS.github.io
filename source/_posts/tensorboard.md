---
title: tensorboard
date: 2021-12-07 10:12:08
categories:
- cv
tags:
- cv
- environment
cover:
---

### TensorBoard 文件

用tensorboard写好tfevent了，但是有些时候需要自己手动用plt画图

```python
from tensorboard.backend.event_processing import event_accumulator
#from tensorboard.backend.application import
grain = event_accumulator.EventAccumulator(")
#grain.scalars.size = 1000000
grain.Reload()


normal = event_accumulator.EventAccumulator("")
#normal.scalars.size = 1000000
normal.Reload()


grain_loss = grain.scalars.Items("loss")
normal_loss = normal.scalars.Items("loss")
print(len(normal_loss))


import matplotlib.pyplot as plt
grain_loss = [ g.value for g in grain_loss]
normal_loss = [ n.value for n in normal_loss]

plt.figure()
plt.xlabel("step")
plt.ylabel("loss")
plt.plot(normal_loss[1000:16000:20], label="without multi grain")
plt.plot(grain_loss[1000:16000:20], label="with multi grain")
plt.legend(loc="upper right")
plt.show()
```

tensorboard数据点显示不完全 [[StackOverflow](https://stackoverflow.com/questions/43702546/tensorboard-doesnt-show-all-data-points)]：

```bash
ensorboard --logdir=./logs --bind_all --samples_per_plugin scalars=9999999
```

自己画图读的数据点不完全，修改一下：

```python
# located tensorboard.backend.event_processing / event_accumulator.py
DEFAULT_SIZE_GUIDANCE = {
    COMPRESSED_HISTOGRAMS: 500,
    IMAGES: 4,
    AUDIO: 4,
    SCALARS: 10000,
    HISTOGRAMS: 1,
    TENSORS: 10,
}
```

#### 重要教训

看清tensorboard的坐标轴，有时候坐标轴会变化。。。要不然汇报工作的时候很尴尬

