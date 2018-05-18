# Classification-of-Pitch-Imagery-from-EEG
This project is based on classification of audio electroencephalogram (EEG) data for 3 pitches: C4, D4, E4. Classification was done using Extreme Learning Machine Model in Matlab. 
The data available was preprocessed according to the following description:

1. Original EEG raw data, 32 channels, .edf format, 5 subjects, 3 pitches.
2. Pre-processed data ran on EEGLab, extension from Matlab on the following process, saved in .set & .txt formats:
    - Retain of channels for auditory imagery: F7, F8, AF3, AF4
    - Retain of channels for auditory perception: T7, T8, TP7, TP8
    - Passband filter to remove noise: 0.1-100Hz
    - Notch Filter: 60Hz
    - Epoch Extraction at time -0.25 to 1.5 seconds
    - Baseline Removal [-252 0]
    - Artifact Removal using ICA
    - Alpha band IIR filter (8 - 15Hz)
    - Automatic Epoch Rejection of Voltage over 100uV

## Approach for Data preprocessing and extraction:

.edf files for each pitch, c4,d4,e4 was respectively put into seperate folders and all converted to .mat files using the code file attached ‘readedf.m’. Usage: 

```
[header, data] = readedf('filename.edf');
```

These mat files were then loaded into matlab and data for ⅘ subjects were concatenated and labelled for training dataset. 
Labels are such as C4 => 0, D4 => 1, E4 =>2. The data for 8/32 channels was then extracted for channels of auditory imagery: F7, F8, AF3, AF4 and Auditory perception: T7, T8, TP7, TP8.
```
d = 'F:\upwork\EEG\10thMAY\testsets\';

ff =  what(d);
con=[];
for j =1:length(ff.mat)
    fname = strcat(d, ff.mat{j});
    M=load(fname);
    temp=M.data;
    %for concatenating individual files
    con=[con, temp]; 
    %for concatenating sets
    %con=vertcat(con, temp); 
end
con = con'; %comment it out for concatenating sets

```
 These files were all combined together to make the final training dataset attached as ‘Training_dataset.mat’. Rest of the ⅕ subject’s data was concatenated, labelled and extracted on 8 channels successively and put into ‘Test_dataset.mat’. 

```
%labelling
data = load(filepath);
label = 2;
data = (data.data)';
[r,c]= size(data);
z = label.* ones(r,1);
data=horzcat(z,data);

```
Each dataset has the structure of labels in column 1 and data points of 8 channels in next 8 columns. Data of each subject is concatenated vertically. 

## Training and testing of model:

ELM implementation of Mathworks was utilized and modified for our problem. The code files are available in the EA_SRC.zip along with the datasets. The code file ‘Demo_Classification.m’ takes our training and test datasets to perform classification on the ELM and Regularized ELM.
```
load('Training_dataset.mat'); %loading Training_dataset
con = con'; %transpose of dataset into column vectors
trainlabel=con(1,:); %extract labels from first column
traindata=con(2:9,:); %extract data values from rest
```
```
load('Test_dataset.mat'); %same as above steps for test dataset
con = con';
testlabel=con(1,:);
testdata=con(2:9,:);
```

**ELM and RELM was performed using an Neural network trained on our labels.**
```
method            = {'ELM','RELM'};

nn.hiddensize     = 200; %number of hidden neurons
nn.activefunction = 's'; %sigmoid
nn.inputsize      = size(traindata,1);
nn.method         = method{1};  %pick ELM or RELM
nn.type           = 'classification';
```

## Improvements:
The results can be improved by:
    1. Increasing the number of hidden layers on the neural networks. Currently it uses 200 layers.
    2. Using a larger training dataset. This dataset was of only 4 subjects (approx.  #50000)  reduced to 8 channels and 5th subject was used as test data. The results can be much better with a larger dataset. 
    3. Using a deep convolutional network for multi label classification. 

