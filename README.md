# DeepVGL
Visual Global Localization Based on  Deep Neural Netwoks for Self-Driving Cars

See DeepVGL video: https://drive.google.com/file/d/1_82n_fol89TteE_VZp-4YPR0AFLKq9Lh/view?usp=sharing

Volta-da-UFES dataset download link: https://drive.google.com/drive/folders/1tqRKGO3DtW1yreoxYeD9Ssc3Ip8fxaXC?usp=sharing



A Deep neural network approach for Visual Global Localization, running in real time on low-cost GPUs.

## LCAD previous works:

- **SABGL:** https://github.com/LCAD-UFES/SABGL
- **WNN-CNN-GL:** https://github.com/LCAD-UFES/WNN-CNN-GL

# Operating MODE

Enable DeepVGL in Carmen.

Remember to copy/generate all necessary files to the module's config folder.

required files:
* table of poses / images.
* network weights.
* network configuration file.



# Training MODE

## Install Carmen first and enable the localize_neural module to perform the following steps.
(needs to have python2.7 installed on system)

## Get started
If you want to train with our logs (Volta-da-UFES), follow to STEP 2.

## STEP 1

Define the logs that will be used to train the Darknet(make sure the bumblebee and velodyne folders are present).

They are usually in "/dados".

```bash

ls /dados
log_volta_da_ufes-20160825.txt
log_volta_da_ufes-20160825.txt_bumblebee
log_volta_da_ufes-20160825.txt_velodyne
log_volta_da_ufes-20191003.txt
log_volta_da_ufes-20191003.txt_bumblebee
log_volta_da_ufes-20191003.txt_velodyne

```

(Use 2 or more logs to correct execution of the scripts)

## STEP 1.1

Generate the logs.txt file with:

1 - the absolute path to the logs.

2 - the absolute path to the images target directory.

3 - selected camera.

4 - crop height (to eliminate IARA's car-hood).

5 - log format (1 or 2).

The first will be used to generate base poses, the other to live poses.

ex.:

```bash

/dados/log_volta_da_ufes-20191003.txt   /dados/ufes/20191003    3   380     1
/dados/log_volta_da_ufes-20160825.txt   /dados/ufes/20160825    3   380     1

```

## STEP 1.2
Generate the pose files associated with the images, here called camerapos_files. They are a preview of datasets, but without any treatment.

For that, I run a playback of each log using the process-ground-truth-generator.ini, with the localize_neural_dataset module turned on.

Edit the process-ground-truth-generator.ini to adjust these lines to your case :

```bash

playback    gt_log          1   0   ./playback /dados/log_voltadaufes-20160825.txt
exporter    gt_generator    1   0   ./localize_neural_dataset -camera_id 3 -output_dir /dados/ufes/20160825 -output_txt /dados/ufes/camerapos-20160825.txt 
 
```
## STEP 1.3
and run:

```bash

cd $CARMEN_HOME/bin
./central &
./proccontrol process-ground-truth-generator.ini

```

### Repeat steps 1.2 and 1.3 to each log selected for training.

## STEP 1.4

Generate images:

execute the following command to generate the images from each log selected.

```bash

./scripts/generate_images.sh

```

## If you are going to use only your logs, go to step 3.

## STEP 2

## STEP 2.1 Download all image folders and respective camera pose files

- UFES's dataset download link: https://drive.google.com/drive/u/1/folders/1tqRKGO3DtW1yreoxYeD9Ssc3Ip8fxaXC

- Save them at /dados/ufes

## STEP 2.2

Generate the logs.txt file with:

1 - the absolute path to the logs.

2 - the absolute path to the images target directory.

3 - selected camera.

4 - crop height (to eliminate IARA's car-hood).

5 - log format (1 or 2).

The first will be used to generate base poses, the other to live poses.

ex.:

```bash

/dados/log_volta_da_ufes-20191003.txt   /dados/ufes/20191003    3   380     1
/dados/log_volta_da_ufes-20160825.txt   /dados/ufes/20160825    3   380     1

```

## STEP 3

Configure the following parameters on \'script/config.txt\':

```bash

- image_path="/dados/ufes/" # images target directory from previous steps
- output_path="/dados/ufes_gt/" # outpu directory 
- base_offset=5 # spacing between base poses
- live_offset=1 # spacing between live poses

```

Generate the dataset itself:

```bash

./scripts/dataset.sh

```

## After finishing running the dataset.sh script, start training the darknet.

