## Introduction
1. hifi-gan 오픈 소스와 한국어 데이터셋(KSS)을 사용해 빠르게 학습하고, 사전 학습된 tacotron2와 end to end inference를 수행합니다. 
2. KSS 데이터셋은 기본적으로 44kHz인 점을 감안하여 22kHz로 resampling할 수 있도록 meldataset.load_wav()를 수정했습니다.
4. 기존의 hifi-gan stft를 사용하면 변환되는 mel-spectrogram의 범위가 약 2 ~ -11 사이로 변환되어 학습하는데 많은 시간이 소요되기 때문에 torch version stft는 사용하지 않습니다.
5. mel-spectrogram 생성 과정이 변경되었기 때문에 mel-spectrogram reconstructure l1 loss를 사용하지 않고 stft loss를 대신하여 사용합니다.
6. mel-spectrogram 생성 과정이 변경되었기 때문에 tacotron2에서 사용한 audio_processing.py 스크립트의 추가 및 meldataset.py, train.py가 수정되었습니다.
7. mel-spectrogram 생성 과정이 변경되었기 때문에 생성과정에 필요한 일부 parameter가 추가되거나 일부 중복된 parameter가 있을 수 있습니다.
8. command line에서 입력하는 path 일부를 주석처리 하고 config_v1.json 파일에 추가되었습니다.
9. conda 환경으로 진행해도 무방하지만 본 레포지토리에서는 docker 환경만 제공합니다. 기본적으로 ubuntu에 docker, nvidia-docker가 설치되었다고 가정합니다.
10. GPU, CUDA 종류에 따라 Dockerfile 상단 torch image 수정이 필요할 수도 있습니다.
11. 별도의 pre-processing 과정은 필요하지 않습니다.


## Dataset
1. download dataset - https://www.kaggle.com/datasets/bryanpark/korean-single-speaker-speech-dataset
2. `mkdir /path/to/the/hifi-gan/data`
3. `mkdir /path/to/the/hifi-gan/data/dataset`
4. `mv /path/to/the/kss.zip /path/to/the/hifi-gan/data/dataset`
5. `cd /path/to/the/hifi-gan/data/dataset`
6. `unzip kss.zip -d kss`

## Docker build
1. `cd /path/to/the/hifi-gan`
2. `docker build --tag hifigan:latest .`

## Training
1. `nvidia-docker run -it -n 'hifigan' -v /path/to/the/hifi-gan:/home/work/tacotron2 --ipc=host --privileged hifigan:latest`
2. `cd /home/work/hifi-gan`
3. `python train.py --config=config_v1.json`
4. `python train.py --config=config_v1.json --checkpoint_path=/path/to/the/hifi-gan/cp_hifigan`
5. arguments
  * --config : config path
  * --checkpoint_path : checkpoint path(output directory)
5. (OPTIONAL) `tensorboard --logdir=outdir/logdir`
