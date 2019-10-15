# 7th place solution to The 3rd YouTube-8M Video Understanding Challenge

(WIP) This is the final states of the codebase at the end of the competition. Code cleanup and documentation are under way.

## System Environment

A conda [environment.yml](enviroment.yml) generated from running `conda env export` command is provided.

Special care: you need to install PyTorchHelperBot manually via `pip install PyTorchHelperBot/.`. (A copy of [PyTorch Helper Bot](https://github.com/ceshine/pytorch_helper_bot/) is included in this repo via Git subtree.)

Highlights:

- conda
  - python=3.7.4=h265db76_1
  - pytorch=1.3.0.dev20190909=py3.7_cuda10.0.130_cudnn7.6.2_0
- pip
  - tensorflow==2.0.0rc0
  - joblib==0.13.2
  - pandas==0.25.1
  - python-telegram-bot==12.0.0

TODO: provide a Dockerfile.

## Folder Structure

- data
  - segment — Put the data from [YouTube-8M segment-rated frame-level features dataset](https://research.google.com/youtube8m/download.html) here.
    - train
    - test
  - video — Put the data from [YouTube-8M frame-level features dataset](https://research.google.com/youtube8m/download.html) here.
    - train
      - Please check [train_file_list.txt](data/video/train/train_file_list.txt) for the list of shards used.
    - valid
      - Please check [valid_file_list.txt](data/video/train/valid_file_list.txt) for the list of shards used.
  - cache — generated files will be stored in this folder.
    - predictions — dumped `numpy.memmap` files during inference will be stored here.
    - video — pre-trained video-level models will be stored here.
    - segment — both context-aware and context-agnostic models will be stored here.
    - inference — when making competition submissions, logs will be stored here.
- yt8m — this is where the main code, include code that performs model pre-training, finetuning, and inference, resides.
- PyTorchHelperBot — a copy of [PyTorchHelperBot](https://github.com/ceshine/pytorch_helper_bot/). A simple high-level PyTorch wrapper I wrote for my personal use.

## Hardwares

I used both local comoputer and instances from Google Cloud Compute to train models and make inference.

My local computer:

- 1 Intel i7-7700K CPU
- 1 NVIDIA GTX 1070 GPU
- 16 GB RAM
- Linux Mint 19.2

Google Cloud Compute instance:

- 8 vCPU
- 20 GB RAM
- 1 NVIDIA Tesla T4 or 1 NVIDIA Tesla P100
- Debian 9

In addition to the disk space for the datasets, 100 GB extra space is needed for the `cache` folder.

Local computer can only be used to train context-agnostic models, make inference, and create submission files. Video-level model pre-training and context-aware models requries at least T4 to run.

## Reproduction Instructions

1. pretraining: `bash scripts/pretraning.bash`
2. finetuning context-agnostic models: `bash scripts/context-agnostic.bash`
3. finetuning context-aware models: `bash scripts/context-aware.bash`
4. prepare the metadata for the test set: `python -m yt8m.prepare_test_meta`
5. creating and dumping predictions to disk: `python -m yt8m.inference_memmap`
6. create submission file: `python -m yt8m.create_submission_from_memmaps`

The submission file will be create as `sub.csv` at the project root folder.

(if you have trained segment classifiers at hand, put them into `data/cache/segment` folder, and run step 4 to 6.)

## Troubleshooting

- **RuntimeError: received 0 items of ancdata**: [Increasing ulimit and file descriptors limit on Linux](https://glassonionblog.wordpress.com/2013/01/27/increase-ulimit-and-file-descriptors-limit/).
