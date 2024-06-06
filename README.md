# LGF-SLR
## Abstract
For those who are deaf, sign language recognition (SLR) technology can offer a more practical and effective means of communication. A skeleton-based sign language recognition system has gained popularity and been used in real-world scenarios because of its benefits in generalization, computing efficiency, anti-interference, and privacy protection. Unfortunately, this method's low recognition rate is frequently caused by inadequate hand-skeletal information. In order to recognize sign language, the multi-stream fusion technique is also frequently employed; however, this approach frequently uses human weight assignment, which has drawbacks including subjectivity and inefficiency. This research offers a Hand Local-Global Fusion Network for Skeleton-Based Sign Language Recognition (LGF-SLR), based on the aforementioned difficulties. To the greatest extent possible, the skeleton information is preserved while it is extracted and inputted into the network's global feature streams (upper body) and local feature streams (left and right hands) independently. Simultaneously, the multi-stream fusion component introduces Bayesian optimization, which automatically gives weights to different prediction knots and lessens the impact of manually assigned weights on the trial outcomes. Lastly, the AUTSL sign language recognition dataset, the WLASL American Sign Language dataset, and the CSL Chinese sign language dataset are used to assess the LGF-SLR framework. The results show that the accuracy of the framework is higher than the state-of-the-art, with accuracies of 95.93%, 52.74%, and x%, respectively.
## Table of Contents
* Data Preparation
* Requirements
* Pretrained Models
* Usage
## Data Preparation
* Download [AUTSL](https://chalearnlap.cvc.uab.es/dataset/40/description/), [CSL/SLR500](https://link.zhihu.com/?target=http%3A//home.ustc.edu.cn/~pjh/openresources/cslr-dataset-2015/index.html), and [WLASL2000](https://dxli94.github.io/WLASL/) dataset following their instructions.
* Generate whole-body skeleton keypoints and save as npy
  * Use pretrained model of whole-body pose estimation to extract 133 landmarks from rgb videos and save as npy files.
  * Go to wholepose folder, change input_path and output_npy variables as the path of input videos and output npy files.
  * Download pretrained whole-body pose model: [Google Drive](https://drive.google.com/file/d/1f_c3uKTDQ4DR3CrwMSI8qdsTKJvKVt7p/view?usp=sharing)
  * Copy generated npy files to corresponding data folders.
* Run the following code to prepare the data for bone, joint motion and bone motion.
     cd data_gen/
     python sign_gendata.py
     python gen_bone_data.py
     python gen_motion.py
