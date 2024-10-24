# DE-ID-AI-CUP-Trainer
2023 AI CUP **Privacy protection and medical data standardization competition: decoding clinical cases and letting data tell stories** dedicated code

## Description
We utilized the Qwen-14B and 7B models for the subtasks 1 and 2, respectively, based on the findings by Wang, Bi and Zhu, Ren, which indicated that Qwen models perform well in handling clinical notes. To enhance training stability for these large models, we implemented the fully sharded data parallel technique. To mitigate potential overfitting, we employ the NEFTune method, which introduces uniformly distributed noise to the model’s embedding layer. This noise helps prevent the model from memorizing specific details within the training set, allowing it to generalize more effectively to new data. Additionally, this technique reduces the model’s sensitivity to particular inputs, preventing it from developing overly complex representations too early in the training process. Another strategy we employed was randomly extracting sentences within a predefined context window and utilizing them as a basis for validating the model’s performance. The length of the context window was set to 1 for subtask 1, but 3 for subtask 2, as the normalization of temporal information often requires more contextual data. During the inference phase, we used a greedy decoding strategy to ensure the stability and reliability of the model’s output. In addition, we incorporated a rule-based post-processing method since we observed that the fine-tuned model occasionally makes erroneous predictions for some labels that should be straightforward to classify.


## Competition Ranking
* **Private Leaderboard Rank:** 2
* **Task 1 Score:** 0.9075701 
* **Task 2 Score:** 0.8124762 

### Note
Due to the DUA agreement, the dataset cannot be uploaded to public websites. Please replace the files in the following paths on your own:
```
answer/train_ansewer.txt
answer/valid_ansewer.txt
dataset/train_data
dataset/valid_data
dataset/test_data
```

Modified training and validation datasets:
```
1481, 1139, 1059, 661, 830, 943 Data errors or do not exist
1071, 111, 583 (multiple), 834 (multiple) Index errors
1906, 377, file20783 Character confusion
```

## Data Format
The format for the training and validation `answer.txt` is as follows:
```
file_name    PHI    start_idx   end_idx   target_text (separated by tabs)
                        .
                        .
file_name    PHI    start_idx   end_idx   target_text
```

The content of the `dataset` folder is structured as:
```
  |-train_data
  |    |-9.txt
  |    |-10.txt
  |       .
  |       .
  |    |-file233771.txt
  |
  |-valid_data
  |     |-24.txt
  |     |-47.txt
  |        .
  |        .
  |     |-file30810.txt
```

## Environment
* Operating System: Windows 11
* Programming Language: Python 3.8.10
* CPU Specifications: Intel(R) Core(TM) i9-10900 CPU 2.80GHz
* GPU Specifications: ASUS TURBO RTX 3090 TURBO-RTX3090-24G
* CUDA Version: 12.2

## Library Installation
* [CUDA 11.6](https://www.nvidia.com/zh-tw/geforce/technologies/cuda/) or later
* [PyTorch 1.12](https://pytorch.org/) or later
* [Flash Attention 2](https://github.com/Dao-AILab/flash-attention) (optional)

After installing the above environment, execute the following command:
```
pip install -r requirements
```

## Usage Instructions
The program is mainly divided into three parts: training code, inference code, and filter code.

1. ### Training Parameter Configuration
* Train de-identification:
```
python src/train.py ^
--prompt_path "./prompt/De-ID.txt" ^
--data_type "De-ID" ^
--windows_size 1 ^
--batch_size 4 ^
--seed 2526 ^
--epochs 10 ^
--filter_ratio 0.1 ^
--split_ratio 0.8 ^
--warmup_ratio 0.2
```
* Train time normalization:
```
python src/train.py ^
--prompt_path "./prompt/Time.txt" ^
--data_type "Time" ^
--windows_size 3 ^
--batch_size 2 ^
--seed 2526 ^
--epochs 10 ^
--filter_ratio 0.1 ^
--split_ratio 0.8 ^
--warmup_ratio 0.2
```

2. ### Inference Parameter Configuration
* Inference de-identification:
```
python src/predict.py ^
--adapter_name "Qwen-14B_9" ^
--prompt_path "./prompt/De-ID.txt" ^
--data_type "De-ID" ^
--windows_size 1
```
* Inference time normalization:
```
python src/predict.py ^
--adapter_name "Time-Qwen-7B_9" ^
--prompt_path "./prompt/Time.txt" ^
--data_type "Time" ^
--windows_size 3
```

3. ### Filter Time-Normalized Data
* Execute the following two steps (complete this step before inference de-identification):
```
cd answer
py filter.py
```

