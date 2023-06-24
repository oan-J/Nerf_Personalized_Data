# DS-Nerf
COLMAP is built in MacOS, while DS-Nerf is in Linux with CUDA support.

## Quick Start

### Dependencies

Install requirements:
```
pip install -r requirements.txt
```


### Prepare your own data

The personalized data is already in 'data/earphone'(partly), you can use pictures in this directory or create your own data following these steps.

The whole dataset is here: [data](https://drive.google.com/drive/folders/1OrtjEtpFqFLwFICv6_yJVholQ0pWaXmC)


#### Take pictures
- Take pictures of one subject, make sure to turn them into .png file.
- Put the pictures into Dir 'data/your_object'
    
#### Generate the poses and sparse point cloud

##### Use COLMAP app (recommended)
[reference: how to generate poses and sparse point cloud using COLMAP](https://blog.csdn.net/qq_40514113/article/details/131228304)

After following the steps above, do:

- Put the image data you generated in 'data', the structure should look like this:

    ```
    .
    ├── earphone
    │   ├── earphone.db
    │   ├── images
    │   │   ├── IMG_6697.png
    │   │   ├── IMG_6698.png
    │   │   ├── IMG_6699.png
    │   │   ├── IMG_6700.png
    │   │   ├── ...
    │   ├── poses_bounds.npy
    │   └── sparse
    │       └── 0
    │           ├── cameras.bin
    │           ├── images.bin
    │           ├── points3D.bin
    │           └── project.ini
    ```

##### Use COLMAP code
- You probably need to change the file path of colmap in 'llff/poses/colmap_wrapper.py'
  or example:
  > [feat_output = subprocess.check_output(['/usr/bin/colmap'] + feature_extractor_args, universal_newlines=True)]
- If you don't have graphical interface environment, do this:
  - download xvfb
  ``` apt install xvfb```
  - use xvfb to get sparse data
    
  ```xvfb-run -s "-screen 0 1024x768x24" /usr/bin/colmap feature_extractor --database_path data/earphone_2v/database.db --image_path data/earphone_2v/images --ImageReader.single_camera 1 --SiftExtraction.use_gpu 0```

  ```xvfb-run -s "-screen 0 1024x768x24" /usr/bin/colmap exhaustive_matcher --database_path data/earphone_2v/database.db```
  
  ```xvfb-run -s "-screen 0 1024x768x24" /usr/bin/colmap mapper --database_path data/earphone_2v/database.db --image_path data/earphone_2v/images --export_path data/earphone_2v/sparse```
  - If you meet this problem :
    > QStandardPaths: XDG_RUNTIME_DIR not set, defaulting to '/tmp/runtime-aadithyasb'
    
    do this:
    
    ```export XDG_RUNTIME_DIR=/NeRF/DSNeRF-main```
    
    ```export RUNLEVEL=3```
    
    ```cd ~```
    
    ```source .bashrc```

- If you do have graphical interface environment, do this:
  
  ```python3 imgs2poses.py data/earphone_2v/```
  
  
#### Testing
```python run_nerf.py --config configs/earphone.txt --render_only --dataset_type llff```

### Train
 ```python run_nerf.py --config configs/earphone.txt --dataset_type llff```

### Results
https://drive.google.com/drive/folders/1OrtjEtpFqFLwFICv6_yJVholQ0pWaXmC

The rendered movie is here: [movie](https://drive.google.com/file/d/1CY0blVsoPt5J9Q9DBUxrrQMu6-l4y2xL/view?usp=share_link)

---

## Acknowledgments

[colmap](https://github.com/colmap/colmap)

[DS-Nerf](https://github.com/dunbar12138/DSNeRF)
