# 本地 GPU 环境搭建（公司 / 家里通用）

目标：不依赖 Colab，两台电脑拉下代码就能用本地 GPU 跑 fastbook 笔记本。

已实测环境：

| | 公司电脑 | 家里电脑 |
|---|---|---|
| 显卡 | GTX 1650 (4GB) | RTX 4070 Ti Super (16GB) |
| 状态 | ✅ 2026-07-22 实测跑通 | 同版本组合官方支持，装完跑下面的验证即可 |

## 一、前置条件

1. **Python 3.12.x**（必须，3.13 没有对应 cu121 的 torch 包）
   - 检查：`python --version`
   - 没有就去 https://www.python.org/downloads/ 装 3.12 系列
2. **NVIDIA 驱动 ≥ 528**（正常更新过驱动都满足）
   - 检查：`nvidia-smi` 能输出显卡信息即可

## 二、安装（锁定版本，勿改成最新版）

```powershell
pip install -r requirements-local.txt
```

> ⚠️ 不要手动 `pip install torch` 裸装——那样装的是 CPU 版或最新版，
> 版本漂移正是之前反复出问题的根源。一切以 requirements-local.txt 为准。

## 三、验证 GPU 可用

```powershell
python -c "import torch, fastai; print(torch.__version__, fastai.__version__); print('CUDA:', torch.cuda.is_available(), torch.cuda.get_device_name(0))"
```

期望输出（显卡名因机器而异）：

```
2.5.1+cu121 2.8.7
CUDA: True NVIDIA GeForce RTX 4070 Ti Super
```

`CUDA: True` 就是成功；`False` 时优先检查 Python 版本是否 3.12、驱动是否过旧。

## 四、日常使用

```powershell
jupyter lab
```

打开任意章节 `.ipynb` 直接跑。注意：

- **数据集不用手动下载**：笔记本里的 `untar_data(URLs.xxx)` 第一次运行会
  自动下载到 `~/.fastai/data/`（Windows 是 `C:\Users\<你>\.fastai\data\`），
  之后全走本地缓存，不再联网。
- **4GB 显存的机器**（公司 1650）跑图像章节如果爆显存（CUDA out of memory），
  把 DataLoaders 的 `bs`（batch size）减半即可，例如 `bs=32` 改 `bs=16`；
  家里 16GB 显存无此顾虑。

## 五、修改后同步回 GitHub

本仓库是 https://github.com/inksnowhailong/fastbook-zh （fork 自 immc-lab/fastbook-zh）。

```powershell
git add -A
git commit -m "学习笔记：xxx"
git push
```

换电脑后先 `git pull` 再开始学习，两边就不会互相覆盖。
