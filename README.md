# SogCLR [![pdf](https://img.shields.io/badge/Arxiv-pdf-orange.svg?style=flat)](https://arxiv.org/pdf/2202.12387.pdf)

This is the official implementation of the paper "**Provable Stochastic Optimization for Global Contrastive Learning: Small Batch Does Not Harm Performance**".


Requirements
---
```
Python3.7
tensorflow==2.7.0
tensorflow-datasets
```

Datasets
---
**ImageNet100** is a subset of ImageNet1000. 
**ImageNet1000** 


#### Usage
Please copy the provided `imagenet.py` to your local directory under `tensorflow_datasets`:
```bash
cp imagenet.py  /usr/local/lib/python3.7/dist-packages/tensorflow_datasets/image_classification/imagenet.py 
```
Please specify the `num_classes` and `data_dir`:
- ImageNet100: `--num_classes=100 --data_dir=gs://<path-to-tensorflow-dataset>`
- ImageNet1000: `--num_classes=1000 --data_dir=gs://<path-to-tensorflow-dataset>`


Pretraining
---
To pretrain the `ResNet50` on `ImageNet1000` with TPUs using **SogCLR**, run the following command:
```bash
python run.py --train_mode=pretrain \
  --train_batch_size=512 --train_epochs=100 --temperature=0.1 \
  --learning_rate=0.075 --learning_rate_scaling=sqrt --weight_decay=1e-6 \
  --dataset=imagenet2012 --image_size=224 --eval_split=validation --num_classes=1000 \
  --num_proj_layers=2 \
  --BI_mode=True --gamma=0.9  \
  --data_dir=gs://<path-to-tensorflow-dataset> \
  --model_dir=gs://<path-to-store-checkpoints>\
  --use_tpu=True
```
For baseline, set `BI_mode=False` to use SimCLR. To use GPU, you could set `use_tpu=False`.

Linear Evaluation
---
By default, `lineareval_while_pretraining=True` will train the linear classifier with a `stop_gradient` operator during pretraining, which is simlar to linear evaluation after trainng [[Ref]](https://github.com/google-research/simclr/issues/151). 

```
!python run.py --mode=train_then_eval --train_mode=finetune \
  --fine_tune_after_block=4 --zero_init_logits_layer=True \
  --num_proj_layers=0 --ft_proj_selector=-1 \
  --global_bn=False --optimizer=momentum --learning_rate=0.1 \
  --learning_rate_scaling=linear --weight_decay=0 \
  --train_epochs=90 --train_batch_size=4096 --warmup_epochs=0 \
  --dataset=imagenet2012 --image_size=224 --eval_split=validation \
  --data_dir=gs://<path-to-tensorflow-dataset> \
  --model_dir=gs://<path-to-store-checkpoints> \
  --checkpoint=gs://<path-to-store-checkpoint>/ckpt-xxxx' \
  --use_tpu=True \
```


Citation
---------
If you find this repo helpful, please cite the following paper:

```
@article{yuan2022sogclr,
  title={Provable Stochastic Optimization for Global Contrastive Learning: Small Batch Does Not Harm Performance},
  author={Zhuoning Yuan, Yuexin Wu, Zihao Qiu, Xianzhi Du, Lijun Zhang, Denny Zhou, Tianbao Yang},
  journal={arXiv preprint arXiv:2202.12387},
  year={2022}
}
```

Reference
---
- https://github.com/google-research/simclr/tree/master/tf2
- https://github.com/wuyuebupt/LargeScaleIncrementalLearning/tree/master/dataImageNet100