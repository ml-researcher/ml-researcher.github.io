# Stable Diffusion

我觉得根本没时间研究的那么细，只能是用到什么研究什么。比如暂时，就先研究怎么inference。

第一步，先把inference跑起来！

## 代码研读

stable-diffusion关键就是三个模块：model、data、callback。

### model

model会涉及到两部分：diffusion model和unet model。diffusion model是用于处理扩散逻辑的，unet model是用来预测每一步扩散的。

#### unet model

必要参数：

```python
image_size,
in_channels,
model_channels,
out_channels,
num_res_blocks,
attention_resolutions,
```

attention_resolutions代表需要加attention的downsample fractor。

## MNIST示例

梳理一下原始pytorch的实现，对比一下stable diffusion和diffusers的实现。

https://github.com/1049451037/stable-diffusion

## Inference

model是LatentDiffusion

model.model是DiffusionWrapper，用的是c_crossattn，暂时还没理解为什么DiffusionWrapper是一个LightningModule

model.model.diffusion_model是UNet，也是我需要去加速的核心位置。

* 输入有三个参数：x，t和context
* x: (bs*2, 4, 64, 64) float32
* t: (6,) int64 tensor([981, 981, 981, 981, 981, 981], device='cuda:0')
* context: (bs*2, 77, 768) float32

