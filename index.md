# MCalib: Multi-camera Calibration Dataset
This dataset contains records of outside-in perspective from 7 synchronized global-shutter RGB cameras observing a moving calibration objects (i.e., active dual-marker wand, a big ChArUco board, a passive single-marker wand) within the target capture volume. 
Each type of record contains 33 rounds of repeating records to allow consistency benchmarking.  
For non-overfitting accuracy benchmark, an independent record of a 3D marker trajectory from a marker-based motion capture system is also provided. 

## Calibration output generation guideline 
You have an option to calibrate the system from the original videos and images, or from pre-extracted 2D marker centers.
In any way you choose, you are allowed to used only information from one record at a time together with optional chessboard info for a fair comparison to the past and the future studies.
Note that the benchmark record must not be used as an input to any of your calibration method.

### If you choose to calibrate from original videos and images,
Download the compressed file related to the method that you are developing from [here](https://entuedu-my.sharepoint.com/:f:/g/personal/prayook_staff_main_ntu_edu_sg/EtBd_TI-VV9Nq2Lr8qHJApwBS6Gm_l3PJy9djfQKDwvbEg?e=4hjdva) (~220 GB if you want to download everything). The password is provided in the article. These compressed files can be extracted by 7Zip.
- chessboard.7z contains 30 png images of chessboard at different pose from each camera.
- activeWandRecord.7z contains 33 different records of moving active dual-marker wand. Each record contain 7 videos in mjpeg format.
- aniposeRecord.7z contains 33 different records of a moving ChArUco board. Each record contain 7 videos in avi format.
- mocapAssistedRecord.7z contains 33 different records of a moving passive single-marker wand in parallel with a marker-based motion capture system. Each record contain 7 videos in mjpeg format recorded at 50 fps and a tsv file to represent the 3D trajectory of the passive marker recorded at 200 Hz from Qualisys.
- benchmarkRecord.7z contains 1 record of a moving passive single-marker wand in parallel with a marker-based motion capture system. This record contains 7 videos in mjpeg format recorded at 50 fps and a tsv file to represent the 3D trajectory of the passive marker recorded at 200 Hz from Qualisys. You should not use this data as an input to your calibration method as it is use exclusively for independent benchmarking.

### If you choose to calibrate from pre-extracted input,
Download all the pre-extracted data from [here](https://drive.google.com/file/d/14S-sDUGC9x6hr8-vcOYlJcvWw5kFrmKP/view?usp=sharing) (~17 MB). The password is provided in the article. This compressed file can be extracted by 7Zip. 
- chessboard folder contain 7 pickled file. One file is for one camera. Each pickled file contain the following dictionary.
```
{
    "boardCornerXYZ": A numpy array with shape (70, 3). Each row is a 3D position of chessboard corner in its object reference frame.,
    "boardCornerUV": A list of 30 numpy arrays with shape (70, 1, 2). Each array contains the 2D positions of all the 70 chessboard corner detected on an image.    
    "boardRvec": A tuple of 30 numpy arrays with shape (3,1) that store rotation vector of the chessboard.
    "boardTvec": A tuple of 30 numpy arrays with shape (3,1) that store translation vector of the chessboard.
}
```
- intrinsicInit folder contains intrinsic parameter initialization. Each picked file contain dictionary of intrinsic parameters from one camera calculated form the data in chessboard folder.
- activeWandRecord folder contains 33 subfolders, each for one record. Each record folder contains 7 correctedDualUV{camId}.pkl. Each file is a pickled list of this format.
```
[
    [array([u,v]),array([u,v])],    #2D coordinate of the green and the red marker respectively of the first frame.
    [None        ,array([u,v])],    #None represent discarded data as it might not be seen or considered as noise.
    [None        ,None        ],
    ...
    [array([u,v]),array([u,v])],    #2D coordinate of the green and the red marker respectively of the last frame.
]
```
- aniposeRecord folder does not contain anything as nothing is pre-extracted from the video data in Anipose calibration workflow.
- mocapAssistedRecord folder contains 33 subfolders, each for one record of a moving passive single-marker wand in parallel with a marker-based motion capture system. Each record contain 7 pickled file of marker center extraction at 50 fps and a tsv file to represent the 3D trajectory of the passive marker recorded at 200 Hz from Qualisys. Each centroidsUV{camId}.pkl contain a pickled list of this format.
```
[
    array([u,v]),   #2D coordinate of the passive marker of the first frame
    None,           #None represent unseen or discarded data for that frame
    array([u,v]),
    ...
    array([u,v]),   #2D coordinate of the passive marker of the last frame
]
```
- benchmarkRecord folder is similar to one record folder under mocapAssistedRecord. You should not use this data as an input to your calibration method as it is use exclusively for independent benchmarking.

## How to generate and benchmark your calibration output?
For each calibration method you have, you should run it on the corresponding 33 records and generate calibration outputs 1.pkl, 2.pkl, 3.pkl, ..., 33.pkl. Each file must pickled the following dictionary structure.

```
{
    'c29d1e0': 
    {
        'K': array([[1.60638035e+03, 0.00000000e+00, 9.27126854e+02],
                    [0.00000000e+00, 1.61125493e+03, 5.87891682e+02],
                    [0.00000000e+00, 0.00000000e+00, 1.00000000e+00]]), 
        'D': array([-4.24380210e-01,  2.72369026e-01, -8.58653737e-05, -5.08228733e-05, -1.29334163e-01]), 
        'rvec': array([0., 0., 0.]), 
        'tvec': array([0., 0., 0.])
    }, 
    '2b9dc514': 
    {
        'K': array([[1.48526860e+03, 0.00000000e+00, 9.34157160e+02],
       [0.00000000e+00, 1.49044296e+03, 5.95209743e+02],
       [0.00000000e+00, 0.00000000e+00, 1.00000000e+00]]), 
       'D': array([-4.32001621e-01,  2.60196887e-01,  2.40472266e-04,  2.99634888e-04, -1.00312102e-01]), 
       'rvec': array([ 0.05996833, -0.53365835, -0.08818506]), 
       'tvec': array([ 3.514376  , -0.07925262,  0.31371611])
    }, 
    '6d75421': 
    {
        'K': array([[1.75638841e+03, 0.00000000e+00, 9.33622382e+02],
                    [0.00000000e+00, 1.76151526e+03, 5.89651358e+02],
                    [0.00000000e+00, 0.00000000e+00, 1.00000000e+00]]), 
        'D': array([-4.17236563e-01,  2.68250504e-01,  2.40043999e-04,  1.56659702e-04, -1.39634444e-01]), 
        'rvec': array([-0.00562291, -1.10117956, -0.13112948]), 
        'tvec': array([ 6.03608957, -0.49507924,  4.0306344 ])
    }, 
    '44c4b2e': 
    {
        'K': array([[1.47485970e+03, 0.00000000e+00, 9.22660479e+02],
                    [0.00000000e+00, 1.47872876e+03, 5.80566457e+02],
                    [0.00000000e+00, 0.00000000e+00, 1.00000000e+00]]), 
        'D': array([-4.28415292e-01,  2.46441260e-01,  1.11880910e-04, -4.33731842e-06, -8.53963474e-02]), 
        'rvec': array([-0.043201  , -2.97004285, -0.53009452]), 
        'tvec': array([-0.45762487, -2.03048474,  9.25204108])
    }, 
    '216f21c1': 
    {
        'K': array([[1.53389605e+03, 0.00000000e+00, 9.24469152e+02],
                    [0.00000000e+00, 1.53698817e+03, 5.90180703e+02],
                    [0.00000000e+00, 0.00000000e+00, 1.00000000e+00]]), 
        'D': array([-0.42687273,  0.26699676,  0.00065826,  0.00092075, -0.11832041]), 
        'rvec': array([0.01595467, 2.70595223, 0.4026878 ]), 
        'tvec': array([-2.91759615, -1.30138793,  8.26407356])    
    }, 
    '3e0f8f0': 
    {
        'K': array([[1.85406662e+03, 0.00000000e+00, 9.55801748e+02],
                    [0.00000000e+00, 1.85900662e+03, 5.76207960e+02],
                    [0.00000000e+00, 0.00000000e+00, 1.00000000e+00]]), 
        'D': array([-0.40643604,  0.257588  , -0.00077748,  0.00223889, -0.13589637]), 
        'rvec': array([0.02136482, 2.06457293, 0.26503882]), 
        'tvec': array([-3.99163087, -0.77208312,  6.89545639])
    }, 
    '969eac0': 
    {
        'K': array([[1.73321569e+03, 0.00000000e+00, 9.14708222e+02],
                    [0.00000000e+00, 1.73620507e+03, 5.99544643e+02],
                    [0.00000000e+00, 0.00000000e+00, 1.00000000e+00]]), 
        'D': array([-4.13679495e-01,  2.67889777e-01, -7.43252841e-04,  1.98952567e-04, -1.40394043e-01]), 
        'rvec': array([0.02400399, 1.05927945, 0.16115194]), 
        'tvec': array([-3.6184519 , -0.47712094,  3.25794084])
    }
}
```
Given that 
* 'K' is the camera intrinsic matrix, a numpy array with shape (3,3)
* 'D' is a vector of distortion coefficients, a numpy array with shape (5,). \[k1, k2, p1, p2, k3\]
* 'rvec' is a vector of rotation, a numpy array with shape (3,)
* 'tvec' is a vector of translation, a numpy array with shape (3,)

To benchmark the accuracy of your calibration, 
1. Clone calibBenchmark repository.
```
git clone git@github.com:koonyook/calibBenchmark.git
```
2. Install all the requirements.
Install Anaconda, open Anaconda prompt, and create a new python environment with commmand.
```
conda create -n py37 python=3.7
```
Then, activate the environment.
```
conda activate py37
```
Install openCV and numpy.
```
pip install opencv-python numpy
```
3. Replace all the pkl files in calibResults/ to the pkl files you generated.
4. Run the benchmarking script. It will print the mean and standard deviation of error against the benchmark record.
```
python main.py
``` 

# Cite Us
If you gain something from our dataset, please cite our publication.
```
Bibtex will be here soon
```

# Contact Us
- Prayook Jatesiktat. prayook001@e.ntu.edu.sg

I am from Nanyang Technological University.
