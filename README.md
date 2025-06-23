# DATA620004-PJ3
张力的课的期末PJ

## colmap
```
#安装colmap
sudo add-apt-repository ppa:colmap/colmap
sudo apt-get update
sudo apt-get install colmap

#用xvfb进行运行提取特征
xvfb-run colmap feature_extractor \
  --database_path ./data/train/database.db \
  --image_path ./data/train_images/ \
  --ImageReader.single_camera 1 \
  --SiftExtraction.use_gpu 0

#匹配
colmap exhaustive_matcher --database_path ./data/train/database.db --SiftMatching.use_gpu 0

#映射
colmap mapper \
  --database_path ./data/train/database.db \
  --image_path ./data/train_images/ \
  --export_path ./data/train/sparse

mkdir -p ./data/train/sparse/0_txt

colmap model_converter \
  --input_path ./data/train/sparse/0 \
  --output_path ./data/train/sparse/0_txt \
  --output_type TXT

python colmap2nerf.py --text ./data/train/sparse/0_txt --images ./data/train_images --out ./data/train/transforms_train.json

CUDA_VISIBLE_DEVICES=3 python train.py --config configs/your_own_data.txt
```


```
# 原版NeRF训练
python run_nerf.py --config configs/own_data.txt
```

```
# 加速版TensoRF训练
python train.py --config configs/your_own_data.txt --ckpt log/train_88/train_88.th
```
```
# 转换为gif文件
ffmpeg -i input.mp4 -vf "setpts=2.0*PTS,fps=10,scale=640:-1:flags=lanczos" slow.gif

```
![牛头怪_with_bg](https://github.com/user-attachments/assets/eb794283-158c-40ba-929c-36fef3b0dfa8)

![牛头怪_wo_bg](https://github.com/user-attachments/assets/acfae0e0-6db4-421d-bb53-d8897c22049f)



https://github.com/user-attachments/assets/b462196e-b681-4bf4-ab7b-f2964044bb3e


