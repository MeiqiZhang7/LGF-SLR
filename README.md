# LGF-SLR:Hand Local-Global Fusion Network for Skeleton-Based Sign Language Recognition
## Abstract
For those who are deaf, sign language recognition (SLR) technology can offer a more practical and effective means of communication. A skeleton-based sign language recognition system has gained popularity and been used in real-world scenarios because of its benefits in generalization, computing efficiency, anti-interference, and privacy protection. Unfortunately, this method's low recognition rate is frequently caused by inadequate hand-skeletal information. In order to recognize sign language, the multi-stream fusion technique is also frequently employed; however, this approach frequently uses human weight assignment, which has drawbacks including subjectivity and inefficiency. This research offers a Hand Local-Global Fusion Network for Skeleton-Based Sign Language Recognition (LGF-SLR), based on the aforementioned difficulties. To the greatest extent possible, the skeleton information is preserved while it is extracted and inputted into the network's global feature streams (upper body) and local feature streams (left and right hands) independently. Simultaneously, the multi-stream fusion component introduces Bayesian optimization, which automatically gives weights to different prediction knots and lessens the impact of manually assigned weights on the trial outcomes. Lastly, the AUTSL sign language recognition dataset, the WLASL American Sign Language dataset, and the CSL Chinese sign language dataset are used to assess the LGF-SLR framework. The results show that the accuracy of the framework is higher than the state-of-the-art, with accuracies of 95.93%, 52.74%, and x%, respectively.
## Table of Contents
* Data Preparation
* Requirements
* Usage
## Data Preparation
* Download [AUTSL](https://chalearnlap.cvc.uab.es/dataset/40/description/), [CSL/SLR500](https://link.zhihu.com/?target=http%3A//home.ustc.edu.cn/~pjh/openresources/cslr-dataset-2015/index.html), and [WLASL2000](https://dxli94.github.io/WLASL/) dataset following their instructions.
* Generate whole-body skeleton keypoints and save as npy
  * Use pretrained model of whole-body pose estimation to extract 133 landmarks from rgb videos and save as npy files.
  * Go to wholepose folder, change input_path and output_npy variables as the path of input videos and output npy files.
  * Download pretrained whole-body pose model: [Google Drive](https://drive.google.com/file/d/1f_c3uKTDQ4DR3CrwMSI8qdsTKJvKVt7p/view?usp=sharing)
  * Copy generated npy files to corresponding data folders.
* Run the following code to prepare the data for bone, joint motion and bone motion.<br>
```
    cd data_gen  
    python sign_gendata.py  
    python gen_bone_data.py <br>
    python gen_motion.py
```
* Use save_npy.py to extract the left-hand and right-hand npy files. Note that the data path should be modified.
* Preprocessed skeleton data for the global feature streams of the AUTSL, SLR500, and WLASL2000 datasets are provided [here](https://drive.google.com/drive/folders/1VUQsh_nf70slT4YsC-UzTCAZ3jB_uFKX?usp=sharing). Please be sure to follow their rules and protocols when using preprocessed data.
## Requirements
  The code is written using Anaconda Python >= 3.6 and Pytorch 1.7 with OpenCV.
## Usage
  To train, and test our models, please change the config path to corresponding config files.  
  Basic usage:
  ```
python main.py --config /path/to/config/file
```
  We take the WLASL dataset as an example to demonstrate its usage. 
  ### Train:
  #### 全局特征流（上半身）
  ```
python main.py --config config/sign/WLASL/train/all/train_joint.yaml  
python main.py --config config/sign/WLASL/train/all/train_bone.yaml
python main.py --config config/sign/WLASL/train/all/train_joint_motion.yaml
python main.py --config config/sign/WLASL/train/all/train_bone_motion.yaml
```
  ####  局部特征流（右手）
  ```
python main.py --config config/sign/WLASL/train/right/train_joint_right.yaml  
python main.py --config config/sign/WLASL/train/right/train_bone_right.yaml
python main.py --config config/sign/WLASL/train/right/train_joint_motion_right.yaml
python main.py --config config/sign/WLASL/train/right/train_bone_motion_right.yaml
  ```
 左手的方法与右手相同，只需将文件夹和文件名由right改为left即可。
  ### Test:
   测试的方法与训练相同，只需将文件夹和文件名由train改为test即可。  
  ### Multi-stream ensemble:
  对于局部特征和全局特征轨道，所有模态的测试结果都需要集成在一起才能生成最终结果。首先先将单流的四种运动形式的预测结果进行融合，得到单流的预测结果，然后再将三个单流的结果进行融合得到最终结果。以全局特征流（all）为例，局部特征流（左手和右手）操作流程同理。
   * 将workdir中的结果.pkl 文件从所有流（关节、骨、关节运动和骨运动）复制到 . /ensemble/gcn /test-best并正确重命名它们。
   * 修改calculated weight.py中的数据路径和标签路径，然后运行python calculated weight.py，得到四种运动结果的权重， 复制到ensemble.py中。
   * 修改ensemble.py文件中的数据路径和标签路径，运行python ensemble.py得到全局特征流的结果，同时在gcn_ensembled文件夹中得到全局特征流的结果pkl文件。
   * 修改2.py中的数据路径和标签路径，运行python 2.py生成全局特征流和局部特征流的权重，复制到ensemble_all.py中。
   * 修改ensemble_all.py文件中的数据路径和标签路径，运行python ensemble_all.py得到最终的预测结果。
   
