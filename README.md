# DATA620004-PJ3: NeRF & 3D Gaussian Splatting 物体重建与新视角合成

本项目是张力老师神经渲染课程的期末作业。我们以《魔兽世界》中的牛头人手办为重建对象，使用如下三种方法完成了物体重建与新视角视频生成：

- 原始 **NeRF**
- 加速版 **TensoRF**
- 高效的 **3D Gaussian Splatting**

---

## 📌 算法简介

### 🌀 NeRF（Neural Radiance Fields）

NeRF 使用多视角图像和相机参数，通过神经网络学习体积密度与颜色分布，可实现高质量的新视角合成。但由于其依赖全空间 MLP，训练时间较长。

### ⚡ TensoRF

TensoRF 将三维场建模为张量乘积形式，通过低秩张量分解显著降低存储与计算复杂度，从而实现 NeRF 的高效训练与渲染。

### ✨ 3D Gaussian Splatting

该方法将场景建模为多个可学习参数的三维高斯分布，以光栅化的方式快速渲染，兼具质量与速度，能够实现实时新视角合成。

---

## 📷 数据采集与可视化

我们使用手机对牛头人手办进行了多角度拍摄，并使用 COLMAP 重建了相机位姿。以下为示例渲染视图：

<div align="center">
  <img src="https://github.com/user-attachments/assets/eb794283-158c-40ba-929c-36fef3b0dfa8" width="400"/>
  <img src="https://github.com/user-attachments/assets/acfae0e0-6db4-421d-bb53-d8897c22049f" width="400"/>
</div>

---

## 🛠️ 项目环境与运行流程

### 1️⃣ COLMAP 安装与相机参数提取

```bash
# 安装 COLMAP
sudo add-apt-repository ppa:colmap/colmap
sudo apt-get update
sudo apt-get install colmap

# 使用 xvfb 执行特征提取
xvfb-run colmap feature_extractor \
  --database_path ./data/train/database.db \
  --image_path ./data/train_images/ \
  --ImageReader.single_camera 1 \
  --SiftExtraction.use_gpu 0

# 匹配图像特征
colmap exhaustive_matcher --database_path ./data/train/database.db --SiftMatching.use_gpu 0

# 运行重建
colmap mapper \
  --database_path ./data/train/database.db \
  --image_path ./data/train_images/ \
  --export_path ./data/train/sparse

# 模型格式转换为 NeRF 所需 JSON
mkdir -p ./data/train/sparse/0_txt
colmap model_converter \
  --input_path ./data/train/sparse/0 \
  --output_path ./data/train/sparse/0_txt \
  --output_type TXT

python colmap2nerf.py --text ./data/train/sparse/0_txt --images ./data/train_images --out ./data/train/transforms_train.json
```

### 2️⃣ 各方法训练入口
#### ▶️ 原版 NeRF
```bash
cd ./nerf_pytorch
python run_nerf.py --config configs/own_data.txt
```

#### ▶️ TensoRF（加速 NeRF）
```bash
cd ./TensoRF
python train.py --config configs/your_own_data.txt
```
#### ▶️ 3D Gaussian Splatting
```bash
cd ./gaussian-splatting
# 按照官方说明运行 convert.py + train.py + render.py
```

### 🎬 实验结果可视化
我们将每种方法生成的视频裁剪统一尺寸，并转换为 GIF 与 MP4：

<div align="center"> <img src="https://github.com/user-attachments/assets/eb794283-158c-40ba-929c-36fef3b0dfa8" width="320"/> <img src="https://github.com/user-attachments/assets/acfae0e0-6db4-421d-bb53-d8897c22049f" width="320"/> </div>

#### 📹 视频展示：
[点击查看 MP4 渲染效果](https://github.com/user-attachments/assets/b462196e-b681-4bf4-ab7b-f2964044bb3e)

> 🎥 使用命令行将 mp4 转换为 gif：
```bash
ffmpeg -i input.mp4 -vf "setpts=2.0*PTS,fps=10,scale=640:-1:flags=lanczos" output.gif
```
---
### 🔗 模型与视频下载链接
Google Drive 提供全部训练好的模型权重与渲染结果视频：

👉 **[点击下载模型与视频文件](https://drive.google.com/drive/folders/12_LvpeNoJgO7-nbG5TcMjZ_zc8q7Ik5m?usp=sharing)**

---

如需复现，请参考各文件夹中的训练脚本及配置文件。如遇问题欢迎提 Issue 交流！


