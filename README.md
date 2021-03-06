# CPM-Generate

为了促进中文自然语言处理研究的发展，本项目提供了 **CPM-LM** (2.6B) 模型的文本生成代码，可用于文本生成的本地测试，并以此为基础进一步研究零次学习/少次学习等场景。[[项目首页](https://cpm.baai.ac.cn)] [[模型下载](https://cpm.baai.ac.cn/download.html)] 

## 安装

首先安装pytorch等基础依赖，再安装[APEX](https://github.com/NVIDIA/apex#quick-start)以支持fp16：
```
pip install -r requirements.txt
git clone https://github.com/NVIDIA/apex
cd apex
pip install -v --no-cache-dir --global-option="--cpp_ext" --global-option="--cuda_ext" ./
```

注：感谢hqhuan同学提供了基于TensorFlow的[使用代码](https://github.com/qhduan/CPM-LM-TF2)，用作Pytorch之外的备选。

## 模型

模型下载后文件夹的目录结构需设置如下：
```
.
├── 80000
│   ├── mp_rank_00_model_states.pt
│   └── mp_rank_01_model_states.pt
└── latest_checkpointed_iteration.txt
```
为保证下载文件的正确性，文件的checksum如下：
```
SHA1
7a8461098dae374a836f55bf2002bf21cba05964  80000/mp_rank_00_model_states.pt
6ab97bd06979c1707080b33cfddff4f08c9af675  80000/mp_rank_01_model_states.pt
MD5
ffb39d8e477aedf9f8714cc467f34eae  80000/mp_rank_00_model_states.pt
8af02c1aa85e76a430d45f3ad073edc5  80000/mp_rank_01_model_states.pt
```

## 使用

提供了命令行交互式生成：
```
bash scripts/generate_text.sh /path/to/CPM
```
如不使用交互式输入，可增加第二个参数，告知输入文本的位置
```
bash scripts/generate_text.sh /path/to/CPM example.txt
```
运行该脚本需要两块GPU，每张卡的GPU内存占用约为7GB。该项目主要基于 [Megatron-LM](https://github.com/NVIDIA/Megatron-LM) 进行修改。模型的主体架构与GPT-2一致。

## Tokenization

Tokenization实现主要在`data_util/tokenization_gpt2.py`，先对于文本进行分词，再使用 SentencePiece 得到 BPE 的结果。由于 SentencePiece 不能有效编码空格和换行符，在 BPE 之前，我们将文本中的空格和换行符替换为`\u2582`和`\u2583`。生成文本的时候也会对应的把生成的`\u2582`和`\u2583`替换回空格和换行符。

对应[问题](https://kexue.fm/archives/7912)已解决。

## TODO

- 实验环境的docker镜像
- 提供各个任务具体的使用模板
- 公开技术报告
- 开源实验中使用的小规模模型参数
- Fine-tune代码

## 引用

```
@article{cpm-v1,
  title={CPM: A Large-scale Generative Chinese Pre-trained Language Model},
  author={Zhang, Zhengyan and Han, Xu, and Zhou, Hao, and Ke, Pei, and Gu, Yuxian and Ye, Deming and Qin, Yujia and Su, Yusheng and Ji, Haozhe and Guan, Jian and Qi, Fanchao and Wang, Xiaozhi and Zheng, Yanan and Cao, Jiannan and Zeng, Guoyang and Cao, Huanqi and Chen, Shengqi and Li, Daixuan and Sun, Zhenbo and Liu, Zhiyuan and Huang, Minlie and Han, Wentao and Tang, Jie and Li, Juanzi and Sun, Maosong},
  year={2020}
}
```
