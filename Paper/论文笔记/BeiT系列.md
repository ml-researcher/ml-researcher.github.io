## BeiT v1

ViT输入图像，输出patch，用VQVAE的token做MLM。

## BeiT v2

把VQVAE换成了ViT+VQKD。

预训练除了MLM还加上了[CLS]。

## BeiT v3

vision expert和language expert的Multiway Transformer。

