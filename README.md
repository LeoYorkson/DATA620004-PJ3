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

![image](https://github.com/user-attachments/assets/7a1c4133-028a-4a3f-b0e5-a25f4886fe41)

