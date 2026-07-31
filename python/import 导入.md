### import导入相关

**目录切换运行**

```
.user 目录
├── a_module
│   ├── __init__.py
│   └── a.py
└── b_module
    ├── __init__.py
    ├── b.py
    └── c_module
        ├── __init__.py
        └── c.py
```



```
tdd@TddM1-Pro user % python3 -m b_module.b      //-m  按包查找，而不是目录；所以user/目录下要加__init__.py，告诉python解释器这是一个包，不是目录
Hello from A with value: 10
Hello from C with value: 30



tdd@TddM1-Pro b_module % PYTHONPATH=.. python3 -m b_module.b   //切换到b_module,必须要加PYTHONPATH，找到项目根目录 user/ 加入 sys.path  
Hello from A with value: 10
Hello from C with value: 30
```



| 要素               | 作用                               |
| ------------------ | ---------------------------------- |
| `PYTHONPATH=..`    | 项目根目录 `user/` 加入 `sys.path` |
| `-m b_module.b`    | 以包形式运行，相对导入正常工作     |
| `from a_module.a`  | 绝对导入，通过 `PYTHONPATH` 找到   |
| `from .c_module.c` | 相对导入，父包 `b_module` 已知 ✅   |













**__all__作用**

```
myapp/
├── __init__.py
├── db/
│   ├── __init__.py
│   ├── connection.py
│   └── models.py
└── api/
    ├── __init__.py
    └── routes.py
```



示例demo

```python
from .connection import get_conn
from .models import User, Order

__all__ = ["get_conn", "User", "Order"]
```

__all__ 的作用其实很单一，但很关键：它定义了"当别人用 from 包 import * 时，到底导入哪些名字"。



**先看不加 `__all__` 会发生什么 **

```
# mypackage/__init__.py
from .connection import get_conn
from .models import User, Order
from .utils import helper_func
import os
import json

# 没有 __all__
```

```
# 别人用的时候
from mypackage import *
```

```
from mypackage import *
# 导入了一坨：get_conn, User, Order, helper_func, os, json 😱
```

用户根本不想要 `os`、`json`、`helper_func`，但它们全被卷进来了。污染命名空间。



**加了 `__all__` 之后 **

```
from .connection import get_conn
from .models import User, Order
from .utils import helper_func
import os
import json

__all__ = ["get_conn", "User", "Order"]
```

```
from mypackage import *
# 只导入：get_conn, User, Order
```

  __all__ 就是白名单。 名单上有的才被 * 导入，没列的统统忽略。



## 但它只管一件事：`from xxx import *`

### ✅ `__all__` 管的事

```
from mypackage import *        # ✅ 受 __all__ 约束
```

❌ `__all__` 不管的事

```
import mypackage              # ❌ 不受影响，mypackage 照样完整导入
mypackage.helper_func         # 能用

from mypackage import helper_func  # ❌ 不受影响，直接指定名字照样能导入
```

__all__ 不阻止你显式导入任何东西，它只拦截通配符 *。





**测试**

```
>>> from mypackage import *
>>> dir()
['User', 'Order', 'get_conn']  # 干净的公共接口
```

