# Yolov8fruitsclassify

GitHub 侧用于存放**水果检测**推理权重（**YOLOv8n**，Ultralytics），供 Actions 或本地脚本拉取使用。

**移动端（App）对接说明**（OAuth、Contents API、与 Actions 配合）：见 **[docs/MOBILE_APP_INTEGRATION.md](docs/MOBILE_APP_INTEGRATION.md)**。  
若需 **Actions 将 `incoming/` 新图 POST 到自建 HTTPS 服务**，在仓库 **Settings → Secrets** 中配置 `FRUIT_SERVER_UPLOAD_URL`（及可选 `FRUIT_SERVER_UPLOAD_TOKEN`），详见该文档 **§2.1**。

## 当前权重说明

| 文件 | 说明 |
|------|------|
| `models/yolov8_fruit_detect_best.pt` | 检测 checkpoint（**YOLOv8n**） |

- **任务**：`detect`（边界框 + 类别）  
- **类别数**：6  
- **类别顺序**（与 checkpoint 内 `names` 一致）：见 `models/classes.json`  
- **来源**：流水线 log 对应 run  
  `fruit_detection_formal100_20260430_164036`  
  本地路径  
  `.../YOLO/versions/v8/runs/train/fruit_detection_formal100_20260430_164036/weights/best.pt`  
  复制并重命名为 `models/yolov8_fruit_detect_best.pt`。

## 加载示例（Ultralytics）

```python
from ultralytics import YOLO

model = YOLO("models/yolov8_fruit_detect_best.pt")
results = model.predict("incoming/example.jpg", imgsz=640, conf=0.25)
```

## 克隆与推送

```bash
git clone git@github.com:yhlkxkzs/Yolov8fruitsclassify.git
# 或: git clone https://github.com/yhlkxkzs/Yolov8fruitsclassify.git
```

维护者推送更新：

```bash
git remote add origin git@github.com:yhlkxkzs/Yolov8fruitsclassify.git
git push -u origin main
```

## 用 GitHub Actions 检测图片

1. 将图片放到 **`incoming/`**（支持子目录；常见图片后缀）。  
2. **提交并推送** 到 `main`（或包含 `incoming/` 变更的分支）。  
3. 打开 **Actions** → **Fruit detection (YOLOv8n)** → 查看 **Summary** 或下载 Artifacts 中的 **`predictions`**（`predictions.json`）。

也可 **Run workflow** 手动触发（会扫描 `incoming/` 下全部图片）。

### 本地推理

```bash
pip install ultralytics
python scripts/infer_fruit_detect.py incoming/your_photo.jpg
# 或扫描整个 incoming/：
python scripts/infer_fruit_detect.py
```

默认输出：`output/predictions.json`（已加入 `.gitignore`）。
