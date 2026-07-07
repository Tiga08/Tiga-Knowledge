# Kaggle Notebooks YOLO GPU 训练教程

> 适用对象：需要使用 Kaggle 免费 GPU 训练 YOLO 模型的用户。  
> Notebook 入口：<https://www.kaggle.com/account/login>  
> 示例环境：Kaggle Notebooks + Ultralytics + NVIDIA GPU。  
> 更新时间：2026-06-17。

---

## 1. 环境准备

### 1.1 Kaggle Notebooks 常用目录

| 路径 | 作用 | 是否可写 |
|---|---|---|
| `/kaggle/input/` | 挂载 Dataset 数据 | 只读 |
| `/kaggle/working/` | 工作目录、训练输出目录 | 可写 |
| `/kaggle/temp/` | 临时目录 | 可写，不建议保存重要结果 |

**核心原则：数据放 `/kaggle/input/`，训练输出放 `/kaggle/working/`。**

### 1.2 免费 GPU / TPU 额度

额度按"周"统计，以下为常见配额（可能随账号和平台策略浮动，以页面显示的 quota 为准）：

| 资源类型 | 约每周额度 | 折算每月约 |
|---|---:|---:|
| GPU | 30 小时 | 120 小时 |
| TPU | 20 小时 | 80 小时 |
| CPU | 免费，有会话时长限制 | — |

参考：[Kaggle Notebooks 文档](https://www.kaggle.com/docs/notebooks) · [GPU 使用指南](https://www.kaggle.com/docs/efficient-gpu-usage) · [TPU 文档](https://www.kaggle.com/docs/tpu)

### 1.3 创建 Notebook 并开启 GPU

1. 登录 Kaggle → `Create` → `New Notebook`；
2. 在右侧 `Session options` 中设置 `Accelerator: GPU`；
3. 确认网络：`Internet: On`（否则无法安装包）。

验证 GPU 是否可用：

```bash
!nvidia-smi
```

### 1.4 执行命令行与安装依赖

Kaggle Code cell 支持 shell 命令：

```bash
# 单行命令
!pwd

# 多行命令
%%bash
pwd
ls -lh /kaggle/input
```

安装或升级 Ultralytics：

```bash
!pip install -U ultralytics
```

---

## 2. 数据准备

### 2.1 上传数据集

**方式一：直接上传**（适合小数据集）

在 Notebook 右侧 `Input` → `Upload`，上传压缩包。文件挂载到 `/kaggle/input/<your-dataset>/`。

**方式二：创建 Kaggle Dataset**（推荐，适合大数据集）

1. Kaggle 首页 `Create` → `New Dataset` → 上传数据；
2. 回到 Notebook → 右侧 `Add Input` → 搜索并添加你的 Dataset。

### 2.2 YOLO 数据集目录结构

```text
<your-dataset>/
├── train/
│   ├── images/
│   │   ├── 000001.jpg
│   │   └── ...
│   └── labels/
│       ├── 000001.txt
│       └── ...
├── val/
│   ├── images/
│   └── labels/
└── data.yaml
```

标注格式：

| 任务类型 | label 格式 |
|---|---|
| 普通目标检测 | `class_id x_center y_center width height` |
| OBB 旋转目标检测 | `class_id x1 y1 x2 y2 x3 y3 x4 y4` |

坐标均为归一化坐标，范围 `[0, 1]`。

### 2.3 配置 data.yaml

`data.yaml` 告诉 YOLO 训练集/验证集路径和类别信息：

```yaml
path: /kaggle/input/<your-dataset>
train: train/images
val: val/images

names:
  0: object
```

如果 `data.yaml` 在数据集中，复制到可写目录后使用：

```bash
!cp /kaggle/input/<your-dataset>/data.yaml /kaggle/working/data.yaml
```

---

## 3. 模型训练

### 3.1 通用训练命令

```bash
!yolo <task> train \
  model=<pretrained-model> \
  data=/kaggle/working/data.yaml \
  imgsz=<size> \
  epochs=<n> \
  batch=<bs> \
  device=0 \
  project=/kaggle/working/runs \
  name=<exp-name>
```

参数说明：

| 参数 | 含义 | 示例值 |
|---|---|---|
| `<task>` | 任务类型 | `detect` / `obb` |
| `model` | 预训练模型 | `yolo11m.pt` / `yolo11m-obb.pt` |
| `data` | 数据配置文件 | `/kaggle/working/data.yaml` |
| `imgsz` | 输入图片尺寸 | `640` / `1024` |
| `epochs` | 训练轮数 | `50` / `100` |
| `batch` | batch size | `4` / `8` / `16` |
| `device` | GPU 编号 | `0`（单卡）或 `0,1`（双卡） |
| `project` | 输出主目录 | `/kaggle/working/runs` |
| `name` | 实验名称 | `obb_exp` / `detect_exp` |

### 3.2 推荐起步配置

| 场景 | task | model | imgsz | epochs | batch |
|---|---|---|---:|---:|---:|
| 快速验证 | `obb` | `yolo11n-obb.pt` | 640 | 20 | 8 |
| 正式训练 | `obb` | `yolo11m-obb.pt` | 1024 | 100 | 8 |
| 显存不足 | `obb` | `yolo11s-obb.pt` | 768 | 100 | 4 |
| 普通检测 | `detect` | `yolo11m.pt` | 640 | 100 | 16 |

如果预训练模型在数据集中，使用完整路径：`model=/kaggle/input/<your-dataset>/yolo11m-obb.pt`。

---

## 4. 训练结果

### 4.1 输出文件

训练完成后，输出位于 `project/name` 目录（具体以训练日志中的 `save_dir` 为准）：

| 文件 | 作用 |
|---|---|
| `weights/best.pt` | 验证集最优模型权重 |
| `weights/last.pt` | 最后一轮权重 |
| `results.csv` | 每 epoch 指标记录 |
| `results.png` | 训练曲线图 |
| `confusion_matrix.png` | 混淆矩阵 |
| `args.yaml` | 训练参数 |
| `val_batch*_pred.jpg` | 验证集预测可视化 |

### 4.2 验证与预测可视化

```bash
# 验证
!yolo <task> val \
  model=/kaggle/working/runs/<exp-name>/weights/best.pt \
  data=/kaggle/working/data.yaml \
  imgsz=1024 batch=8 device=0 \
  project=/kaggle/working/runs name=val_exp \
  save_json=True

# 预测可视化
!yolo <task> predict \
  model=/kaggle/working/runs/<exp-name>/weights/best.pt \
  source=/kaggle/input/<your-dataset>/val/images \
  imgsz=1024 conf=0.25 device=0 \
  project=/kaggle/working/runs name=predict_exp \
  save=True
```

### 4.3 下载结果

**方式一**：直接在右侧 `Output → /kaggle/working` 中逐个下载。

**方式二**（推荐）：打包后下载：

```bash
!cd /kaggle/working && zip -r exp_outputs.zip runs
!ls -lh /kaggle/working/*.zip
```

然后在 `Output` 中下载压缩包。

> 建议训练完成后立即下载或 `Save Version`，Kaggle session 重启后 `/kaggle/working` 中的文件可能丢失。

---

## 5. 完整 Cell 模板

以下模板可直接复制到 Kaggle Notebook 使用，将 `<your-dataset>` 替换为你的数据集名称。

### Cell 1：检查环境

```bash
!nvidia-smi
!pip list | grep ultralytics
```

### Cell 2：安装依赖

```bash
!pip install -U ultralytics
```

### Cell 3：检查数据

```bash
!ls -lh /kaggle/input
!find /kaggle/input/<your-dataset> -maxdepth 3 -type d | head -50
```

### Cell 4：准备 data.yaml

```python
from pathlib import Path

yaml_text = """
train: /kaggle/input/<your-dataset>/train/images
val: /kaggle/input/<your-dataset>/val/images

names:
  0: object
"""

Path('/kaggle/working/data.yaml').write_text(yaml_text.strip() + '\n', encoding='utf-8')
print(Path('/kaggle/working/data.yaml').read_text(encoding='utf-8'))
```

### Cell 5：训练

```bash
!yolo obb train \
  model=yolo11m-obb.pt \
  data=/kaggle/working/data.yaml \
  imgsz=1024 epochs=50 batch=8 \
  device=0 \
  project=/kaggle/working/runs name=obb_exp
```

### Cell 6：验证

```bash
!yolo obb val \
  model=/kaggle/working/runs/obb_exp/weights/best.pt \
  data=/kaggle/working/data.yaml \
  imgsz=1024 batch=8 device=0 \
  project=/kaggle/working/runs name=obb_val \
  save_json=True
```

### Cell 7：预测可视化

```bash
!yolo obb predict \
  model=/kaggle/working/runs/obb_exp/weights/best.pt \
  source=/kaggle/input/<your-dataset>/val/images \
  imgsz=1024 conf=0.25 device=0 \
  project=/kaggle/working/runs name=obb_predict \
  save=True
```

### Cell 8：打包下载

```bash
!cd /kaggle/working && zip -r obb_exp_outputs.zip runs data.yaml
!ls -lh /kaggle/working/*.zip
```

---

## 6. 常见问题

### No such file or directory

Kaggle Dataset 路径与 slug 相关，不一定等于本地文件夹名。用 `!ls /kaggle/input` 和 `!find` 确认实际路径。

### /kaggle/input 无法写入

正常现象，`/kaggle/input` 是只读的。需要修改文件时先复制到 `/kaggle/working`。

### CUDA out of memory

按优先级调整：减小 `batch` → 减小 `imgsz` → 换小模型（`n` / `s` 系列）→ 确认 `device` 未指定不存在的 GPU。

### 训练中断后恢复

```bash
!yolo obb train resume=True model=/kaggle/working/runs/<exp-name>/weights/last.pt
```

Kaggle session 重启后 `/kaggle/working` 可能被清空，重要结果应及时下载或 `Save Version`。

### 训练很慢

使用更小模型、减小 `imgsz`、优先使用 Kaggle Dataset 而非运行时上传碎文件。`cache=True` 在大数据集上会占用大量内存，谨慎使用。
