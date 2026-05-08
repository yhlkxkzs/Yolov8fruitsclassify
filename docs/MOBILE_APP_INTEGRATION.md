# 移动端与 GitHub YOLOv8 检测仓库对接说明

本文档供 **手机 App 开发** 与 **本仓库（含 GitHub Actions）** 对齐使用：完成「用户授权 → 上传图片到 `incoming/` → 触发自动检测」的集成。

---

## 1. 仓库与约定

| 项 | 值 |
|----|-----|
| 仓库 | `yhlkxkzs/Yolov8fruitsclassify` |
| 默认分支 | `main` |
| 图片上传目录 | `incoming/`（可子目录） |
| 权重文件 | `models/yolov8_fruit_detect_best.pt` |
| 推理 Workflow | `Fruit detection (YOLOv8n)` |
| 工作流文件 | `.github/workflows/infer_fruit.yml` |

**触发条件**：向 **`incoming/**`** 路径产生 **push** 后，会启动上述 Workflow。

---

## 2. 架构角色

- **App**：完成 GitHub OAuth、将图片通过 **GitHub Contents API** 写入 `incoming/`。  
- **GitHub**：托管仓库、执行 Actions（安装 `ultralytics`、运行 `scripts/infer_fruit_detect.py`）。  
- **结果**：**Actions → Artifacts** 下载 `predictions`（JSON）；**Summary** 中可能展示同一份 JSON。

检测 JSON 中每张图含 **`detections`** 列表，每项含 `class_id`、`class`、`confidence`、`xyxy`（像素坐标）。

---

## 2.1 自建服务器接收图片（Actions + `curl` POST）

与 MobileNet 分类仓库相同：在推理前可将 **`incoming/`** 中相关图片 **POST** 到你的 **HTTPS** 接口。在仓库 **Settings → Secrets** 配置：

| Secret | 说明 |
|--------|------|
| `FRUIT_SERVER_UPLOAD_URL` | 不配则跳过；需 **公网 HTTPS** |
| `FRUIT_SERVER_UPLOAD_TOKEN` | 可选 Bearer |

字段：`file`、`path`、`commit`、`repo`。详见分类仓库文档中的同一小节（行为一致）。

---

## 3. 类别说明

6 类名称与训练一致，见仓库根目录 `models/classes.json`（草莓成熟度相关标签：`Green`、`Turning`、`Red` 等）。
