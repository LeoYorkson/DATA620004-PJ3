# DATA620004-PJ3
张力的课的期末PJ

## colmap
```
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

```
