之前开发SAT基本上都是在做判别模型，最近需要研究一下生成模型了。

https://github.com/THUDM/SwissArmyTransformer

## review

很久没看sat，都得复习了。感觉有一个比较大的缺点就是我需要时刻铭记BaseModel的所有细节，才能设计合理的mixin……

可能存在的问题：

* 需要理解每个hook的default是什么
* 有些hook和args是耦合的（比如layernorm_order），哪些应该是hook，哪些应该是args

### BaseModel

首先有一个很重要的函数，collect_hooks_。这个函数的逻辑：BaseModel的行为由self.hooks控制，每次collect_hooks_会根据self.mixins生成self.hooks。生成的逻辑是：先看model本身有没有这个hook_name，再遍历self.mixins有没有对应的hook_name，如果有non_conflict就直接替换，否则如果已经存在对应的hook会报错（只有default没事），替换的时候hook_func可以获取到old_implementation。

这里很棒的一个设计是self.mixins是一个torch.nn.ModuleDict，因此有两个特性：

* OrderedDict
* 可以存储参数

这里的hooks本质上调用的是mixin的类函数，因此参数里的self其实是mixin的，为了解决这个问题，每个mixin里的transformer成员都要指向BaseModel的transformer。

### 参数管理

目前有args和add_model_specific_args。 max_sequence_length

## GPT2

核心问题就是两个：模型搬运、文本生成流水线构建。

### 模型搬运

* 研究一下huggingface的gpt2代码：https://github.com/huggingface/transformers/blob/main/src/transformers/models/gpt2/modeling_gpt2.py
* 研究一下SAT的gpt2代码
* 转换参数
* 推理脚本

### 文本生成流水线

* 研究一下huggingface的pipeline
* 研究一下sat里现有的glm的pipeline
* 文本生成脚本

## ChatGLM-6B finetune

### 训练集的构造

根据tokenizer.convert_ids_to_tokens函数可以知道训练集的构造方法。

```
input_ids: sentence1 + [gMASK] + <sop> + sentence2 + <eop> + ...<pad>
labels: [-100]*len(sentence1) + [-100] + input_ids_of_the_left
```

### RoPE

* https://kexue.fm/archives/8265
* https://kexue.fm/archives/8397