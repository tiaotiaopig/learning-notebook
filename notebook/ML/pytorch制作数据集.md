# Pytorch 制作数据集

## 自定义dataset 类

实现必要的内置方法:

- `__init__`方法里面进行读取数据文件
- `__getitem__`方法进行支持下标访问
- `__len__`方法返回自定义数据集的大小，方便后期遍历

我们只需给出自己的dataset和idxs(数据的索引列表)

```python
from torch.utils.data import DataLoader, Dataset
class DatasetSplit(Dataset):
    """An abstract Dataset class wrapped around Pytorch Dataset class.
    """
    def __init__(self, dataset, idxs):
        self.dataset = dataset
        self.idxs = [int(i) for i in idxs]
 
    def __len__(self):
        return len(self.idxs)
 
    def __getitem__(self, item):
        image, label = self.dataset[self.idxs[item]]
        return torch.as_tensor(image), torch.as_tensor(label)
    
train_loader = DataLoader(DatasetSplit(train_dataset, client_idxs),batch_size=args.local_bs, shuffle=True)
```

上面的train_dataset是你的数据集，client_idx是你的数据的索引列表，比如[1,2,345,33,54...........]，数字代表数据在dataset中的位置。这样制作后的数据集就是client_idx索引的数据集。

---

## 直接使用torch.utils.data.TensorDataset()封装数据集

```python
#划分数据集
import torch
import numpy as np
import torch.utils.data as Data
from sklearn.model_selection import train_test_split
x_train, x_test, y_train,y_test = train_test_split(feature, labels, test_size=0.25)
#制作pytorch识别的数据集
train_dataset = Data.TensorDataset(torch.from_numpy(x_train).float(), torch.from_numpy(y_train))
test_dataset = Data.TensorDataset(torch.from_numpy(x_test).float(), torch.from_numpy(y_test))
#制作可迭代的数据集
train_iter = Data.DataLoader(dataset = train_dataset,batch_size = batch_size,
                             shuffle = True, num_workers = 2)
test_iter = Data.DataLoader(dataset = test_dataset, batch_size= batch_size,
                           shuffle = True, num_workers = 2) 
```

