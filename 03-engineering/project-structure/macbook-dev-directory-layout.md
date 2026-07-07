# MacBook Pro 开发目录结构规范

> 适用角色：计算机视觉算法工程师
> 适用设备：MacBook Pro (macOS)

---

## 设计原则

1. **职责分离** — 个人项目、公司项目、数据集、个人文件各占独立顶层目录，避免混杂
2. **命名一致** — 目录统一使用 PascalCase（顶层）或 kebab-case（项目内部），避免特殊字符
3. **层级克制** — 顶层目录不超过 4 个，每个目录内部按用途/领域分一层子目录即可
4. **归档机制** — 每个区域设 `_archive/` 目录，收纳不再活跃但不想删除的内容

---

## 一、主目录 `~/Projects/` — 个人与开源项目

```
~/Projects/
├── Tiga/                       # 个人系统（知识库、配置、技能等）
│   ├── Clockwork/
│   ├── Configs/
│   ├── Knowledge/
│   ├── Life/
│   ├── Money/
│   ├── OS/
│   ├── Resume/
│   ├── Skills/
│   ├── Tmp-Tasks/
│   └── Writer/
│
├── CV-Tools/                   # 计算机视觉工具与框架
│   ├── ultralytics/            #   YOLOv8/YOLO11 训练与推理
│   ├── sam2/                   #   Segment Anything 2
│   └── X-AnyLabeling/         #   标注工具
│
├── AG-Tools/                   # AI Agent 工具与技能集合
│   ├── claude-code/
│   ├── anthropics-skills/
│   ├── superpowers/
│   └── .../
│
├── Deploy-Tools/               # 模型部署与服务化工具
│   └── label-studio-ml-backend/
│
├── Tools/                      # 通用开发工具
│   ├── docling/                #   文档解析
│   ├── markitdown/             #   Markdown 转换
│   └── pandoc/                 #   格式转换
│
└── _archive/                   # 归档的旧项目
```

---

## 二、公司项目 `~/Company/` — 工作相关代码

```
~/Company/
└── Easyair/                    # 当前公司
    ├── easyair-mta/            #   MTA 项目
    └── guangzhou-terminal/     #   广州航站楼项目
```

### 使用说明

- 按公司名建立一级子目录，公司下按项目名组织
- 公司代码不与个人项目混放，降低误提交风险
- 离职后将整个公司目录移入 `_archive/` 或删除

---

## 三、数据集 `~/Datasets/` — 训练与评估数据

```
~/Datasets/
├── detection/                  # 目标检测数据集
│   ├── coco/
│   ├── voc/
│   └── custom-xxx/
│
├── segmentation/               # 图像分割数据集
│
├── classification/             # 图像分类数据集
│
├── ocr/                        # OCR 相关数据集
│
├── multimodal/                 # 多模态数据集
│
└── _archive/                   # 不再使用的旧数据集
```

### 使用说明

- 按任务类型（而非项目名）组织，同一数据集可被多个项目引用
- 大文件不要提交到 Git，使用符号链接或在项目中配置数据路径指向此目录
- 临时数据集用完及时清理或归档，避免磁盘占用失控

---

## 四、桌面 `~/Desktop/` — 个人文件

```
~/Desktop/
├── Family/                     # 家庭文件
│   ├── Grit/
│   ├── Tiga/
│   └── YouMe/
│
├── Books/                      # 电子书
│   ├── 交互设计/
│   └── 计算机系统/
│
├── Papers/                     # 论文 PDF
│   ├── CV-图像分类/
│   ├── CV-目标检测/
│   ├── CV-视觉Transformer/
│   ├── CV-对比学习/
│   ├── 生成模型-GAN与扩散模型/
│   ├── 多模态学习/
│   ├── NLP-语言模型/
│   ├── OCR/
│   └── 优化与泛化理论/
│
└── _Archive/                   # 桌面归档
    └── Roomtour/
```

### 需要清理的问题

| 现状 | 问题 | 建议操作 |
|------|------|----------|
| `Tiga&Grit/` | 目录名含 `&` 特殊字符，命令行操作需转义 | 重命名为 `Family/` |
| `_Archive/zhongwang-datasets/` | 数据集混放在桌面归档中 | 迁移到 `~/Datasets/_archive/` |

---

## 五、全局总览

```
~/
├── Projects/                   # 个人与开源项目
├── Company/                    # 公司项目
├── Datasets/                   # 数据集
└── Desktop/                    # 个人文件（家庭、书籍、论文）
```

---

## 六、迁移清单

按优先级排序，可逐步执行：

```bash
# 1. 新建顶层目录
mkdir -p ~/Company ~/Datasets/{detection,segmentation,classification,ocr,multimodal,_archive}

# 2. 迁移公司项目
mv ~/Projects/Easyair ~/Company/Easyair

# 3. 合并重复的 CV-Tools
# 检查 cv_tools/ultralytics 与 CV-Tools/ultralytics 是否相同
diff -rq ~/Projects/cv_tools/ultralytics ~/Projects/CV-Tools/ultralytics
# 确认无差异后删除
rm -rf ~/Projects/cv_tools

# 4. 重命名桌面家庭目录
mv ~/Desktop/Tiga\&Grit ~/Desktop/Family

# 5. 迁移桌面归档中的数据集
mv ~/Desktop/_Archive/zhongwang-datasets ~/Datasets/_archive/zhongwang-datasets
```
