# Custom DataSet
PyTorch에서 DataLoader(데이터 queue, worker 등을 제공)를 사용하기 위해서는 DataSet이라는 것을 만들어줘야함

# DataSet 정의 방법
```python
from torch.utils.data import Dataset
class CustomDataSet(Dataset):
  def __init__(self, x, transform=None): # 추가적으로 입력으로 내가 필요한 것들을 받도록 해도 됨.
    # 데이터 셋을 받아서 선처리를 해준다
    pass
    
  def __len__(self):
    # 데이터 셋의 길이를 반환
    pass
    
  def __getitem__(self, idx):
    # 데이터 셋에서 idx를 통해 데이터를 조회할 수 있도록 해준다. ex) data[1]
    pass
```

