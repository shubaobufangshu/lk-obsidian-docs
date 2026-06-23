下面给你一份**极简、实用、可直接复制运行**的 `tqdm` 教程，覆盖 90% 日常用法。

---

## 一、安装
```bash
pip install tqdm
```

---

## 二、最简单用法（自动进度条）
```python
from tqdm import tqdm
import time

# 直接把 range 包起来
for i in tqdm(range(100)):
    time.sleep(0.01)
```
输出长这样：
```
100%|██████████| 100/100 [00:01<00:00, 92.3it/s]
```

### 快捷写法：`trange`
```python
from tqdm import trange

for i in trange(100):
    time.sleep(0.01)
```

---

## 三、常用参数（必背）
```python
for i in tqdm(
    range(100),
    desc="处理数据",   # 左边文字
    unit="条",         # 单位
    ncols=80,          # 进度条宽度
    colour="green"     # 颜色
):
    time.sleep(0.01)
```
输出：
```
处理数据: 100%|██████████| 100/100 [00:01<00:00, 90条/s]
```

---

## 四、手动更新（不知道总数量时）
```python
from tqdm import tqdm
import time

pbar = tqdm(total=100)  # 手动指定总数

for i in range(100):
    time.sleep(0.01)
    pbar.update(1)  # 每次+1

pbar.close()
```

### with 上下文管理器（推荐，自动 close）
```python
with tqdm(total=100, desc="加载中") as pbar:
    for i in range(100):
        time.sleep(0.01)
        pbar.update(1)
```

---

## 五、动态改描述、加后缀信息
```python
with tqdm(range(50), desc="初始化") as pbar:
    for i in pbar:
        # 左边动态文字
        pbar.set_description(f"正在处理第 {i} 项")
        # 右边附加信息（loss、accuracy 等）
        pbar.set_postfix(loss=0.5/(i+1), acc=i/50)
        time.sleep(0.05)
```
示例输出：
```
正在处理第 49 项: 100%|██| 50/50 [00:02<00:00] loss=0.01, acc=0.98
```

---

## 六、嵌套进度条（双层循环）
```python
from tqdm import trange

for i in trange(3, desc="外层"):
    for j in trange(5, desc="内层", leave=False):
        time.sleep(0.1)
```
- `leave=False`：内层结束后自动消失，不乱屏。

---

## 七、你刚才的爬虫/去重代码加上 tqdm
```python
from tqdm import tqdm

her = dict()

# 给 data 加进度条
for item in tqdm(data, desc="去重处理"):
    href = item.get('href')
    title = item.get('title')
    if not href or not title:
        continue
    if href.endswith(".html"):
        href_clean = href[:-5]
    else:
        href_clean = href
    if href_clean.startswith("javascript"):
        continue
    if href_clean not in her:
        her[href_clean] = title

# 排除第一个
gourl = dict(list(her.items())[1:])

for a, b in gourl.items():
    print(a, "\t\t", b)
```

---

## 八、常用参数表（快速查阅）
- `desc`：进度条描述文字
- `total`：总数量
- `unit`：单位（it/条/文件）
- `ncols`：进度条宽度
- `leave=False`：结束后不保留进度条
- `colour`：颜色（green/red/blue）
- `set_description()`：动态改左边文字
- `set_postfix()`：动态加右边信息

---

如果你愿意，我可以直接把**你现在的完整爬虫代码改成带 tqdm 进度条的最终版**，要不要？