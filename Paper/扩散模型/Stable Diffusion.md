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
