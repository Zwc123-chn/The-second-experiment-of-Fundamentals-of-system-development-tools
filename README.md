# The-second-experiment-of-Fundamentals-of-system-development-tools
# 系统开发工具基础｜第 2 周 实验检查题

> 
> 时间：2026 年 8 月 31 日 (周一，第 5‑8 节)
> 授课内容：命令行环境；开发环境与工具；Debugging and Profiling
> 说明：共 4 道题目，每题建议用时 12‑15 分钟

## 第 1 题 控制一个可清理的后台任务

**主题**：命令行环境：进程、信号与任务控制｜建议用时：12‑15 分钟

将下方脚本保存为 `q05/worker.sh`，再完成任务控制。

`worker.sh` 给定内容：

```
#!/usr/bin/env bash
trap 'echo CLEAN_EXIT >> cleanup.log; exit 0' TERM INT
n=0
while true; do
echo "$n"
n=$((n+1))
sleep 1
done
```

**题目要求**

1. 赋予脚本执行权限，在前台启动，并把 stdout、stderr 分别写入 `stdout.log`、`stderr.log`。
2. 使用 `Ctrl‑Z` 挂起任务，再用 `bg` 让它在后台继续；使用 `jobs` 确认状态。
3. 使用 `jobs -p` 或等价方式取得 PID，不得手工抄写 PID；发送 `SIGTERM` 并等待任务结束。
4. 确认 `cleanup.log` 中出现 `CLEAN_EXIT`，且任务已经退出；禁止使用 `kill -9`。

---

## 第 2 题 语义重构与本地开发反馈

**主题**：开发环境与工具｜建议用时：12‑15 分钟

在 `q06` 中按照给定代码创建三个 Python 文件，并使用编辑器语言服务器完成一次语义重构。

`math_utils.py`

```
def total_price(price: float, count: int) -> float:
    return price * count
```

`app.py`

```
from math_utils import total_price
print(total_price(12.5, 4))
```

`test_math_utils.py`

```
from math_utils import total_price

def test_total_price():
    assert total_price(12.5, 4) == 50.0
```

**题目要求**
在已配置好 Python、pytest 和 ruff 的课程环境中打开 `q06`，并启用 Python 语言服务器。

1. 分别演示跳转到定义和查找引用。
2. 使用 “重命名符号” 把 `total_price` 改为 `calculate_total`，保证定义和所有代码引用同步改变。
3. 在 `app.py` 中临时加入未使用的 `import os`，用 ruff 发现并删除；最后运行 ruff 和 pytest 并保证通过。

---

## 第 3 题 用调试器定位归并排序缺陷

**主题**：Debugging｜建议用时：12‑15 分钟

将下列存在缺陷的归并排序代码保存为 `q07/merge_sort.py`，再使用调试器定位并修复。

`merge_sort.py` 给定内容：

```
def merge(left, right):
    result = []
    i = j = 0
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i]); i += 1
        else:
            return result + left[i:] + right[j:]
            result.append(right[i]); j += 1 # 此处存在缺陷

def merge_sort(a):
    if len(a) <= 1:
        return a
    m = len(a) // 2
    return merge(merge_sort(a[:m]), merge_sort(a[m:]))

print(merge_sort([3, 1, 4, 1, 5, 9, 2, 6]))
```

**题目要求**

1. 先运行给定输入，确认结果错误或出现异常。
2. 使用 pdb、IDE debugger 或等价调试器，在 `merge` 中设置断点并观察 `i`、`j`、`left[i]`、`right[j]`。
3. 完成最小修复，不得用 `sorted` 替换归并排序。
4. 使用 pytest 增加两个测试：给定输入和含重复元素的列表；保证测试通过。

---

## 第 4 题 先测量，再优化慢速词频程序

**主题**：Profiling｜建议用时：12‑15 分钟

在 `q08` 中创建可重复生成的词表和故意低效的去重程序，再使用性能分析工具优化。

`generate_words.py`

```
import random
random.seed(20260831)
vocab = [f"w{i}" for i in range(1000)]
with open("words.txt", "w", encoding="utf-8") as f:
    f.write(" ".join(random.choice(vocab) for _ in range(30000)))
```

`wordfreq.py`

```
words = open("words.txt", encoding="utf-8").read().split()
unique = []
for word in words:
    if word not in unique:
        unique.append(word)
print("count=", len(unique))
```

**题目要求**

1. 生成 `words.txt`，使用 `time` 运行原程序 2 次并记录耗时。
2. 使用 `cProfile -s cumulative` 运行一次，找出主要耗时位置。
3. 在不改变最终 `count` 的前提下优化程序；不得写死 1000。
4. 使用相同输入再运行 2 次，计算优化前后中位数和加速比。
