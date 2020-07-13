# WRF_EPCNN for Metagenome-based Human Disease Feature Selection and Prediction

WRF_EPCNN is a software to select biomarkers from the combination of both known and unknown microbial organisms for metagenomic dataset as well as achieving competitive prediction performance for human diseases.

# Description
WRF_EPCNN consists of the following 2 modules:
  - WRF for feature selection to sort out the important biomarkers 
  - EPCNN for disease prediction

# Dependencies

MicroPro
https://github.com/zifanzhu/MicroPro

Mash v.2.0
https://github.com/marbl/Mash/releases

PhyLoT v2
https://phylot.biobyte.de/

Keras

Scikit-learn


# Data prapertion

The known and unknown features are obtained by using MicroPro. The taxa distributed taxonomy annotations for known features can be directly obtained by Centrifuge (In MicroPro). For unknown features, we follow the Mash distance of 0.34 to classify the microbes into the genus level (The same as MicroPro). Technically, a similar approach can be used to distribute all the MAGs into the specific levels, and the obtained taxonomy annotations can be used to generate the phylogenetic tree for the unknown features. However, bigger Mash distance also means the inaccurate distributed level without confidence. If Mash cannot distribute the taxa to any levels or the obtained Mash distance is too big to get the accurate levels, the random arrangemet for the unknown taxa sequences can also be accepted.

With the obtained taxa annotations, we use PhyLoT v2 to generate the phylogenetic trees for both known and unknown features.


After above steps, we can obtain 2 abundance tables with m*n for both known and unknown features, where m is the number of samples while n is the features with their annotions. And the control and case description is considered as the label of each sample. Also we obtain the Newick files of the phylogenetic trees for both known and unknown features.

The annotions in known features are the taxa id in NCBI database, and 'unknown_name.xlsx' includes the distributed level names of unknown features which the annotions are raw bin names. 

The input includes four files, assume the name of example set is 'Karlsson_T2D', then the files names should be  'Karlsson_T2D_known'.csv
'Karlsson_T2D_unknown'.csv and 'Karlsson_T2D_y'.csv, where the first two files are the features and the last file is labels. 'tree.txt' is the Newick file obtained by PhyLoT v2. 'unknown_name.xlsx' includes the distributed level names of unknown features which the annotions are raw bin names. 

# Running WRF

Since WRF is based on RF which only accepts the dimensional vector, therefore we just need the features and the labels here without the phylogenetic trees. We offer two features for ERF which are select the important biomarkers as well as evaluation.


- Evaluation


```sh
python3 WRF_EV.py --fn Karlsson_T2D --rs 2 --ts 0.3
```
Where 'WRF_EV' is using WRF for evaluation.  '--rs' means the repeat times using different seeds. And '--ts' means the ratio of test data. The output will be saved in 'Karlsson_T2DWRF_ev.txt'.

- Feature selection (Biomarkers)
```sh
python3 WRF_FS.py --fn Karlsson_T2D --rs 2 --tp 30
```
Where 'WRF_FS' is using WRF for feature selection.  '--rs' means the repeat times using different seeds. And '--tp' means the numbers of selected features. The output will be saved in 'Karlsson_T2DWRF_fs.txt'.

# Running EPCNN


- Phylogenetic-sorting
```sh
python3 EPCNN_PS.py --fn Karlsson_T2D
```
Where 'EPCNN_PS' is using EPCNN for phylogenetic-sorting.  '--fn' means datasets.The outputs are four files which are: postorder phylogenetic-sorting based on known features, level phylogenetic-sorting based on known features, postorder phylogenetic-sortingbased on unknown features, and level phylogenetic-sorting based on unknown features. 
- Prediction
```sh
python3 EPCNN_EV.py --fn Karlsson_T2D --et t --ts 0.3 --rs 2
```
Where 'EPCNN_EV' is using EPCNN for prediction.  '--fn' means datasets. '--et' means types of earlystopping where 't' is to train on training data and other situation is to train on separate validaiton set. '--rs' means the repeat times using different seeds. And '--ts' means the ratio of test data.

