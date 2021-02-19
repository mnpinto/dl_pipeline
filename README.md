# dl_pipeline
> A general deep learning pipeline (in construction) for kaggle competitions and other projects.


## Install

Setup with pip:

`pip install dl_pipeline`

Clone and editable setup:
```bash
git clone https://github.com/mnpinto/dl_pipeline
cd dl_pipeline
pip install -e .
```

## Rainforest Connection Species Audio Detection
* Final ranking: 29th place (top 3%)
* Final score: 0.940
* Best single model (5-fold): 0.931
* Train time for 5-folds of best single model (gtx 1080, i7-7700): ~150 min
* Writeup: https://www.kaggle.com/c/rfcx-species-audio-detection/discussion/220306

* Update 1: With [this](https://www.kaggle.com/c/rfcx-species-audio-detection/discussion/220389) post processing the final score improves to 0.950 and R

```bash
#!/bin/bash
arch='densenet121'
model_name='model_0'
sample_rate=32000
n_mels=128
hop_length=640

for fold in 0 1 2 3 4
do
    echo "Training $model for fold $fold"
    kaggle_rainforest2021 --fold $fold --model_name $model_name \
        --model_arch $arch --sample_rate $sample_rate --n_mels $n_mels \
        --hop_length $hop_length --bs 32 --head_ps 0.8 \
        --tile_width 1024 --mixup true >> log.train
done

for tw in 64 128 256
do
    echo "Generate predictions for $model with tile_width of $tw"
    kaggle_rainforest2021 --run_test true --model_name $model_name \
        --model_arch $arch --sample_rate $sample_rate --n_mels $n_mels \
        --hop_length $hop_length --tile_width $tw \
        --save_preds true >> log.predict
done
```
