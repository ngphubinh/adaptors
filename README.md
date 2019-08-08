## Introduction

Adaptor proteins are carrier proteins that play a crucial role in signal transduction. They commonly consist of several modular domains, each having its own binding activity and operating by forming complexes with other intracellular-signaling molecules. Many studies determined that the adaptor proteins had been implicated in a variety of human diseases. Therefore, creating a precise model to predict the function of adaptor proteins is one of the vital tasks in bioinformatics and computational biology. Few computational biology studies have been conducted to predict the protein functions, and in most of those studies, position specific scoring matrix (PSSM) profiles had been used as the features to be fed into the neural networks. However, the neural networks could not reach the optimum result because the sequential information in PSSMs has been lost. This study proposes an innovative approach by incorporating recurrent neural networks (RNN) and PSSM profiles to resolve this problem.

## Dataset

We collected all the proteins from UniProt with GO molecular function annotations related to adaptor proteins. An important selection criteria is that we had to select the reviewed sequences, which means they had been published in scientific papers. Thus, the full query for collecting data was:

$\texttt{``keyword:``adaptor'' OR goa:(``adaptor'')) AND reviewed:yes''}$

After this step, we received 4,049 adaptor proteins in all species. 

We solved the proposed problem as a binary classification problem, thus we collected a set of general proteins as negative samples. Actually, our classifier aimed to classify between adaptor proteins and non-adaptor proteins. So we needed a real set of adaptors and non-adaptors to train model. However, in practice, if we collect all non-adaptor proteins as negative data, the number of negative dataset will reach hundred thousands of data. This will result in serious data imbalance and affect the model's performance. Therefore, in most of the related problems in bioinformatics, scientists can only select a subset of negative data and treat them as general proteins. In this study, we chose membrane protein, which is a general protein including a big enough number of sequences and functions. Briefly, we extracted all of the membrane proteins in UniProt and excluded the adaptor proteins. Similar to the previous step, only reviewed proteins were retained.

Subsequently, BLAST was applied to all the collected data to remove redundant sequences with sequence identity level of more than 30%. This was an important step to prevent over-fitting in training model. The remaining sequences were regarded as valid for the benchmark dataset and were naturally divided into 1,224 adaptor proteins and 11,078 non-adaptor proteins. For fair comparison, we held up one-fifths of both the adaptor proteins and the non-adaptor proteins as the test set to evaluate model performance. The rest of the valid sequences were used as a cross-validation set for model training. The figure illustrates the flowchart of the study and shows the statistics of the benchmark dataset.

<img src="flowchart.svg?sanitize=true" width="800">

## RNN Model

An overview of the proposed RNN model is shown in the figure below. The RNN model takes PSSM profiles as inputs and extracts their features by several 1-D convolution layers and 1-D average pooling layers. The extracted features are then fed forward to gated recurrent units (GRUs), where the spatial context within the entire PSSM profile is explored and utilized for final prediction. The input sequence has a length of $N$. After going through two layers of 1-D CNN and 1-D Max-Pool, the length became $N/9$. Subsequently, this $N/9$ vector was fed into GRU, taking the output of GRU (256 features) to the input of the last vector for which the characteristic of the sequence was formed. Finally, our model took this output through a Fully Connected (FC) layer (512 nodes), and passed to a Sigmoid layer to produce a prediction probability value.

<img src="architecture.svg?sanitize=true" width="800">

## Experimental Results

The comparison between our proposed method and other methods is shown in the table below. Note that we used grid search cross-validation to find the optimal parameters of all algorithms. This ensures that our comparison is fair and reliable among these methods.

| Method                                                                                              | Cross Validation |                    |                 |             |             | Independent Test |                    |                 |             |             |
| :-------------------------------------------------------------------------------------------------: | :--------------: | :----------------: | :-------------: | :---------: | :---------: | :--------------: | :----------------: | :-------------: | :---------: | :---------: |
|                                                                                                     | Sensitivity      | Specificity        | Accuracy        | AUC         | MCC         | Sensitivity      | Specificity        | Accuracy        | AUC         | MCC         |
| k-NN                                                                                                | 0\.635           | 0\.750             | 0\.738          | 0\.770      | 0\.254      | 0\.671           | 0\.751             | 0\.743          | 0\.791      | 0\.280      |
| RF                                                                                                  | 0\.185           | **0\.968**         | **0\.890**      | 0\.837      | 0\.214      | 0\.290           | 0\.923             | 0\.860          | 0\.838      | 0\.216      |
| SVM                                                                                                 | 0\.397           | 0\.934             | 0\.881          | 0\.818      | 0\.332      | 0\.426           | **0\.932**         | **0\.881**      | 0\.806      | 0\.353      |
| CNN                                                                                                 | 0\.532           | 0\.875             | 0\.841          | 0\.774      | 0\.328      | 0\.548           | 0\.873             | 0\.841          | 0\.783      | 0\.339      |
| RNN                                                                                                 | **0\.812**       | 0\.751             | 0\.757          | **0\.853**  | **0\.373**  | **0\.856**       | 0\.798             | 0\.804          | **0\.893**  | **0\.446**  |


## Conclusions

Compared to other state-of-the-art methods which had been applied successfully in other problems, our method achieves enhancement in all of the common measurement metrics. The area under the receiver operating characteristic curve (AUC) metric in prediction of adaptor proteins in the cross-validation and independent datasets are 0.893 and 0.853, respectively.

This study opens a research path that can promote the use of RNN and PSSM profiles in bioinformatics and computational biology. Our approach is reproducible by scientists that aim to improve the performance results of different protein function prediction problems. Our source code and datasets can be downloaded from the links below.

## Download

- Data and code for training models: [download (223MB)](TrainDataPSSM.zip)
- Data and code for testing models: [download (34MB)](TestDataPSSM.zip)

## Contact

[Go to contact information](http://homepages.ecs.vuw.ac.nz/~nguyenb5/contact.html)
