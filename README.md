# DS-Nerf
COLMAP is built in MacOS, while DS-Nerf is in Linux with CUDA support.

## Quick Start

### Dependencies

Install requirements:
```
pip install -r requirements.txt
```

You will also need [COLMAP](https://github.com/colmap/colmap) installed to compute poses if you want to run on your data.

### Data

### Pre-trained Models

You can download our pre-trained models using the following script:
```
bash download_models.sh
```

### How to Run?

#### Generate camera poses and sparse depth information using COLMAP (optional)

This step is necessary only when you want to run on your data.

First, place your scene directory somewhere. See the following directory structure for an example:
```
├── data
│   ├── fern_2v
│   ├── ├── images
│   ├── ├── ├── image001.png
│   ├── ├── ├── image002.png
```

To generate the poses and sparse point cloud:
```
python imgs2poses.py <your_scenedir>
```

Note: if you use this data format, make sure your `dataset_type` in the config file is set as `llff`.


#### Testing

Once you have the experiment directory (downloaded or trained on your own) in `./logs`, 

- to render a video:
```
python run_nerf.py --config configs/fern_dsnerf.txt --render_only
```

The video would be stored in the experiment directory.

<!-- - to only compute the evaluation metrics:
```
python run_nerf.py --config configs/fern_dsnerf.txt --eval
``` -->


#### Training

To train a DS-NeRF on the example `fern` dataset:
```
python run_nerf.py --config configs/fern_dsnerf.txt
```

It will create an experiment directory in `./logs`, and store the checkpoints and rendering examples there.

You can create your own experiment configuration to try other datasets.


### Use depth-supervised loss in your own project

We provide a tutorial on how to use depth-supervised loss in your own project [here](resources/tutorial.md).



---

## Acknowledgments

[colmap](https://github.com/colmap/colmap)
[DS-Nerf](https://github.com/dunbar12138/DSNeRF)

# temp

## Prepare your own data

### Take pictures
- Take pictures of one subject, make sure to turn them into .png file.
- Put the pictures into Dir 'data/'
  - We have already put the pictures into 'data/earphone', you can use pictures in this directory or take your own pictures and follow the directory structure for an example.
### To generate the poses and sparse point cloud

#### use COLMAP app
reference:
https://blog.csdn.net/qq_40514113/article/details/131228304

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

- Run 
```python imgs2poses.py data/earphone```
If it runs successfully, you will have the file  `poses_bounds.npy` generated.
Rename the file to 'train_images.npy'

#### use COLMAP code
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
