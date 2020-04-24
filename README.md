# Common-Sense-QA
This repository contains code for training models on 3 different question answering tasks alongwith code for generating commonsense inferences to use for such tasks.
## Introduction
The goal of this prject was to assess the extent to which commonsense reasoning plays a role in the quality of answers generated by Question Answering (QA) models.
## Directory Structure
```
.
+- Common-Sense-QA
   +- comet
   |  +- load_data.py
   |  +- parameters_names.json
   |  +- run_sqa_cs.py
   |  +- run_squad_cs.py
   |  +- sqa_loader_cs.py
   +- cove
   +- decaNLP
   +- plots
   +- socialiqa
   |  +- run_sqa.py
   |  +- sqa_loader.py
   +- requirements.txt
   +- custom_bert.py
   +- log_sqa_comet
   +- mlt_batch_scheduler.py
   +- multi_task_batch_scheduler.py
   +- run_glue.py
   +- run_squad.py
   +- run_squad_sst.py
   +- torchsampler.py
```
## Installation
```
pip install -r requirements.txt
```

## Dataset Downloads
1. SQuAD data can be downloaded from the links given below and should be saved in $SQUAD_DIR directory:    
[train-v1.1.json](https://rajpurkar.github.io/SQuAD-explorer/dataset/train-v1.1.json)  
[dev-v1.1.json](https://rajpurkar.github.io/SQuAD-explorer/dataset/dev-v1.1.json)
2.  From the link given below, download the 'The Stanford Sentiment Treebank' dataset and save in $SST_DIR directory:    
[SST-2](https://gluebenchmark.com/tasks)
3.  From the link given below, download the 'MultiNLI Matched' dataset and save in $MNLI_DIR directory:    
[MNLI](https://gluebenchmark.com/tasks)
4. SocialIQA data can be downloaded from the link given below:    
[SocialIQA](https://storage.googleapis.com/ai2-mosaic/public/socialiqa/socialiqa-train-dev.zip)
5. ATOMIC data can be downloaded from the link given below:    
[ATOMIC](https://homes.cs.washington.edu/~msap/atomic/data/atomic_data.tgz)
## Instructions to run code
1. To train the Question Answering model using the SQuAD dataset, run the following command from the main directory:
```
python run_squad.py \
  --model_type bert \
  --model_name_or_path bert-base-uncased \
  --do_train \
  --do_eval \
  --train_file $SQUAD_DIR/train-v1.1.json \
  --predict_file $SQUAD_DIR/dev-v1.1.json \
  --per_gpu_train_batch_size 12 \
  --learning_rate 3e-5 \
  --num_train_epochs 2.0 \
  --max_seq_length 384 \
  --doc_stride 128 \
  --output_dir /tmp/debug_squad/
  ```
 This will save model checkpoints at path specified by --output_dir.
 
 2. To train the Sentiment Analysis model using the SST-2 dataset, run the following command from the main directory:
 ```
 python run_glue.py \
  --model_type bert \
  --model_name_or_path bert-base-cased \
  --task_name SST-2 \
  --do_train \
  --do_eval \
  --data_dir $SST_DIR/SST-2 \
  --max_seq_length 128 \
  --per_gpu_train_batch_size 32 \
  --learning_rate 2e-5 \
  --num_train_epochs 3.0 \
  --output_dir /tmp/SST-2/
  ```
  3. To train the Natural Language Inference model using the MNLI dataset, run the following command from the main directory:
```
python run_glue.py \
  --model_type bert \
  --model_name_or_path bert-base-cased \
  --task_name MNLI \
  --do_train \
  --do_eval \
  --data_dir $MNLI_DIR/MNLI \
  --max_seq_length 128 \
  --per_gpu_train_batch_size 32 \
  --learning_rate 2e-5 \
  --num_train_epochs 3.0 \
  --output_dir /tmp/MNLI/
  ```
  4. To train the multitask model on SQuAD and SST-2, run the following command from the main directory:
```
python run_squad.py \
  --model_type bert \
  --model_name_or_path bert-base-uncased \
  --do_train \
  --do_eval \
  --train_file $SQUAD_DIR/train-v1.1.json \
  --predict_file $SQUAD_DIR/dev-v1.1.json \
  --per_gpu_train_batch_size 12 \
  --learning_rate 3e-5 \
  --num_train_epochs 2.0 \
  --max_seq_length 384 \
  --doc_stride 128 \
  --output_dir /tmp/debug_squad/
  ```
  5. To train the vanilla BERT model using SocialIQA dataset, run the following command from the main directory:
```
python run_sqa.py \
  --model_type bert \
  --model_name_or_path bert-base-uncased \
  --num_train_epochs 100 \
  --do_train \
  --do_eval \
  --evaluate_during_training \
  --overwrite_cache \
  --overwrite_output_dir \
  --output_dir output_sqa_run_100epochs/ 
  ```
  6. To train the QA model using SQuAD and inferences from commonsense, run the following command from the comet directory:
```
python run_squad_cs.py \
  --model_type bert \
  --model_name_or_path bert-base-uncased \
  --do_train \
  --do_eval \
  --train_file $SQUAD_DIR/train-v1.1.json \
  --predict_file $SQUAD_DIR/dev-v1.1.json \
  --per_gpu_train_batch_size 12 \
  --learning_rate 3e-5 \
  --num_train_epochs 2.0 \
  --max_seq_length 384 \
  --doc_stride 128 \
  --output_dir /tmp/debug_squad/
  ```
  7. To train the model using SocialIQA and inferences from commonsense, run the following command from the comet directory:
  ```
  python run_sqa_cs.py \
  --model_type bert \
  --model_name_or_path bert-base-uncased \
  --num_train_epochs 100 \
  --do_train \
  --do_eval \
  --evaluate_during_training \
  --overwrite_cache \
  --overwrite_output_dir \
  --output_dir output_sqa_run_100epochs/
  ```
  8. For running the SQuAD to SocialIQA transfer learning experiment, first train the model on SQuAD (as given in step 1 above) and copy a checkpoint (we have used checkpoint-14000) from ```/tmp/debug_squad``` to the main directory.
  Run the following command from the main directory:
  ```
python run_sqa.py \
  --model_type bert \
  --model_name_or_path checkpoint-14000 \
  --num_train_epochs 100 \
  --do_train \
  --do_eval \
  --evaluate_during_training \
  --overwrite_cache \
  --overwrite_output_dir \
  --output_dir output_sqa_run_100epochs/ 
  ```
## Contact Us
Due to space constraints, we have not hosted the data and models/model checkpoints in this repository. If interested in running our code, feel free to get in touch with us by email and do share questions, comments and suggestions!
- asawaree.bhide@gmail.com 
- rohit.gajawada@gmail.com
- sahithdn@gmail.com
