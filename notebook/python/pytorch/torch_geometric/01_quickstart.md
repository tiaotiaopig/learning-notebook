# torch_geometric 快速入门

## 数据结构

A graph is used to model pairwise relations (edges) between objects (nodes). A single graph in PyTorch Geometric is described by an instance of [`torch_geometric.data.Data`](https://pytorch-geometric.readthedocs.io/en/1.7.2/modules/data.html#torch_geometric.data.Data), which holds the following attributes by default:

- `data.x`: Node feature matrix with shape `[num_nodes, num_node_features]`
- `data.edge_index`: Graph connectivity in COO format with shape `[2, num_edges]` and type `torch.long`，有向边存储，两行多列，真的反人类
- `data.edge_attr`: Edge feature matrix with shape `[num_edges, num_edge_features]`
- `data.y`: Target to train against (may have arbitrary shape), *e.g.*, node-level targets of shape `[num_nodes, *]` or graph-level targets of shape `[1, *]`，就是label
- `data.pos`: Node position matrix with shape `[num_nodes, num_dimensions]`

None of these attributes is required. In fact, the [`Data`](https://pytorch-geometric.readthedocs.io/en/1.7.2/modules/data.html#torch_geometric.data.Data) object is not even restricted to these attributes. We can, *e.g.*, extend it by `data.face` to save the connectivity of triangles from a 3D mesh in a tensor with shape `[3, num_faces]` and type `torch.long`.不局限于上述属性

### 代码示例

We show a simple example of an unweighted and undirected graph with three nodes and four edges. Each node contains exactly one feature:

```python
import torch
from torch_geometric.data import Data

edge_index = torch.tensor([[0, 1, 1, 2],
                           [1, 0, 2, 1]], dtype=torch.long)
x = torch.tensor([[-1], [0], [1]], dtype=torch.float)

data = Data(x=x, edge_index=edge_index)
>>> Data(edge_index=[2, 4], x=[3, 1])
```

<img src="https://i.loli.net/2021/10/31/5Nsrj8gRLxdUCPX.png" alt="image-20211031172903373" style="zoom:67%;" />

Note that `edge_index`, *i.e.* the tensor defining the source and target nodes of all edges, is **not** a list of index tuples. If you want to write your indices this way, you should transpose and call `contiguous` on it before passing them to the data constructor:

```python
import torch
from torch_geometric.data import Data

edge_index = torch.tensor([[0, 1],
                           [1, 0],
                           [1, 2],
                           [2, 1]], dtype=torch.long)
x = torch.tensor([[-1], [0], [1]], dtype=torch.float)

data = Data(x=x, edge_index=edge_index.t().contiguous())
>>> Data(edge_index=[2, 4], x=[3, 1])
```

可以看出`Data`这个类用来表示图，一些常用属性

```python
print(data.keys)
>>> ['x', 'edge_index']

print(data['x'])
>>> tensor([[-1.0],
            [0.0],
            [1.0]])

for key, item in data:
    print("{} found in data".format(key))
>>> x found in data
>>> edge_index found in data

'edge_attr' in data
>>> False

data.num_nodes
>>> 3

data.num_edges
>>> 4

data.num_node_features
>>> 1

data.contains_isolated_nodes()
>>> False

data.contains_self_loops()
>>> False

data.is_directed()
>>> False

# Transfer data object to GPU.
device = torch.device('cuda')
data = data.to(device)
```

## 基准数据集

