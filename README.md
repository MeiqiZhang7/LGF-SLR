# LGF-SLR:Hand Local-Global Fusion Network for Skeleton-Based Sign Language Recognition
## Abstract
For those who are deaf, sign language recognition (SLR) technology can offer a more practical and effective means of communication. A skeleton-based sign language recognition system has gained popularity and been used in real-world scenarios because of its benefits in generalization, computing efficiency, anti-interference, and privacy protection. Unfortunately, this method's low recognition rate is frequently caused by inadequate hand-skeletal information. In order to recognize sign language, the multi-stream fusion technique is also frequently employed; however, this approach frequently uses human weight assignment, which has drawbacks including subjectivity and inefficiency. This research offers a Hand Local-Global Fusion Network for Skeleton-Based Sign Language Recognition (LGF-SLR), based on the aforementioned difficulties. To the greatest extent possible, the skeleton information is preserved while it is extracted and inputted into the network's global feature streams (upper body) and local feature streams (left and right hands) independently. Simultaneously, the multi-stream fusion component introduces Bayesian optimization, which automatically gives weights to different prediction knots and lessens the impact of manually assigned weights on the trial outcomes. Lastly, the AUTSL sign language recognition dataset, the WLASL American Sign Language dataset, and the CSL Chinese sign language dataset are used to assess the LGF-SLR framework. The results show that the accuracy of the framework is higher than the state-of-the-art, with accuracies of 95.93%, 52.74%, and x%, respectively.
## Table of Contents
* [Data Preparation](https://github.com/MeiqiZhang7/LGF-SLR#data-preparation)
    
* [Requirements](https://github.com/MeiqiZhang7/LGF-SLR#requirements)  
    
* [Usage](https://github.com/MeiqiZhang7/LGF-SLR/blob/main/README.md#usage)
  * [Train](https://github.com/MeiqiZhang7/LGF-SLR/edit/main/README.md#train)
  * [Test](https://github.com/MeiqiZhang7/LGF-SLR/edit/main/README.md#test)
  * [Multi-stream](https://github.com/MeiqiZhang7/LGF-SLR/edit/main/README.md#multi-stream-ensemble) 
## Data Preparation
* Download [AUTSL](https://chalearnlap.cvc.uab.es/dataset/40/description/), [CSL/SLR500](https://link.zhihu.com/?target=http%3A//home.ustc.edu.cn/~pjh/openresources/cslr-dataset-2015/index.html), and [WLASL2000](https://dxli94.github.io/WLASL/) dataset following their instructions.
    
* Generate whole-body skeleton keypoints and save as npy  
    
  * Use pretrained model of whole-body pose estimation to extract 133 landmarks from rgb videos and save as npy files.
  * Go to `wholepose` folder, change input_path and output_npy variables as the path of input videos and output npy files.
  * Download pretrained whole-body pose model: [Google Drive](https://drive.google.com/file/d/1f_c3uKTDQ4DR3CrwMSI8qdsTKJvKVt7p/view?usp=sharing)
  * Copy generated npy files to corresponding data folders.
      
* Run the following code to prepare the data for bone, joint motion and bone motion.<br>
```
    cd data_gen
  
    python sign_gendata.py
  
    python gen_bone_data.py
  
    python gen_motion.py
```
* Use `save_npy.py` to extract the left-hand and right-hand npy files. Note that the data path should be modified.
    
* Preprocessed skeleton data for the global feature streams of the AUTSL, SLR500, and WLASL2000 datasets are provided [here](https://drive.google.com/drive/folders/1VUQsh_nf70slT4YsC-UzTCAZ3jB_uFKX?usp=sharing). Please be sure to follow their rules and protocols when using preprocessed data.
## Requirements
  The code is written using Anaconda Python >= 3.6 and Pytorch 1.7 with OpenCV.
## Usage
  To train, and test our models, please change the config path to corresponding config files.  
    
  Basic usage:
  ```
python main.py --config /path/to/config/file
```
  ## Train:
  We take the WLASL dataset as an example to demonstrate its usage. 
  #### Global feature flow(Upper body)
  ```
python main.py --config config/sign/WLASL/train/all/train_joint.yaml
  
python main.py --config config/sign/WLASL/train/all/train_bone.yaml
  
python main.py --config config/sign/WLASL/train/all/train_joint_motion.yaml
  
python main.py --config config/sign/WLASL/train/all/train_bone_motion.yaml
```
  ####  Local feature flow(Right hand)
  ```
python main.py --config config/sign/WLASL/train/right/train_joint_right.yaml
  
python main.py --config config/sign/WLASL/train/right/train_bone_right.yaml
  
python main.py --config config/sign/WLASL/train/right/train_joint_motion_right.yaml
  
python main.py --config config/sign/WLASL/train/right/train_bone_motion_right.yaml
  ```
The method for the left hand is the same as for the right hand; just change the folder and file name from "right" to "left".
  ## Test:
#### Global feature flow(Upper body)
  ```
python main.py --config config/sign/WLASL/test/all/test_joint.yaml
  
python main.py --config config/sign/WLASL/test/all/test_bone.yaml
  
python main.py --config config/sign/WLASL/test/all/test_joint_motion.yaml
  
python main.py --config config/sign/WLASL/test/all/test_bone_motion.yaml
```
  ####  Local feature flow(Right hand)
  ```
python main.py --config config/sign/WLASL/test/right/test_joint_right.yaml
  
python main.py --config config/sign/WLASL/test/right/test_bone_right.yaml
  
python main.py --config config/sign/WLASL/test/right/test_joint_motion_right.yaml
  
python main.py --config config/sign/WLASL/test/right/test_bone_motion_right.yaml
  ```
The method for the left hand is the same as for the right hand; just change the folder and file name from "right" to "left". 
  ## Multi-stream ensemble:
   For both local feature and global feature tracks, the testing results of all modalities need to be integrated together to generate the final result. First, the predicted results of the four motion modes of a single track are fused to obtain the prediction result of the single track, and then the results of the three single tracks are fused to get the final result. Taking the global feature track (all) as an example, the operation process for local feature tracks (left hand and right hand) is the same.
   * Copy the results.pkl file from the `work_dir` to`./ensemble/gcn/test-best` for all streams (joints, bones, joint motion, and bone motion), and rename them correctly.
      
   * Modify the data path and label path in `calculated_weight.py`, then run `python calculated_weight.py` to obtain the weights for the four types of motion results, and copy them to `ensemble.py`.
       
   * Modify the data path and label path in the `ensemble.py` file, then run `python ensemble.py` to obtain the results for the global feature track. Additionally, you will get the result.pkl file for the global feature track in the `gcn_ensembled` folder.
       
   * Modify the data path and label path in `calculated_weight2.py`, run `python calculated_weight2.py` to generate the weights for global feature track and local feature track, then copy them to `ensemble_all.py`.
       
   * Modify the data path and label path in `ensemble_all.py`, then run `python ensemble_all.py` to obtain the final prediction results.
  
## Reference
[1] [Skeleton Aware Multi-modal Sign Language Recognition](https://openaccess.thecvf.com/content/CVPR2021W/ChaLearn/papers/Jiang_Skeleton_Aware_Multi-Modal_Sign_Language_Recognition_CVPRW_2021_paper.pdf)   
  
 Code : [https://github.com/jackyjsy/CVPR21Chal-SLR](https://github.com/jackyjsy/CVPR21Chal-SLR)  
  
[2] [Sign Language Recognition via Skeleton-Aware Multi-Model Ensemble](https://arxiv.org/pdf/2110.06161.pdf)  
  
 Code : [https://github.com/jackyjsy/CVPR21Chal-SLR](https://github.com/jackyjsy/SAM-SLR-v2)
