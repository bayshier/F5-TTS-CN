> [!NOTE]
> 🇨🇳 本仓库是 [SWivid/F5-TTS](https://github.com/SWivid/F5-TTS) 的非官方中文汉化版：仅翻译 README，代码与子目录文档（如 `src/f5_tts/infer/SHARED.md`）保持上游原样。英文原文见 [README_EN.md](README_EN.md)。

# F5-TTS：借助流匹配（Flow Matching）仿造流畅且忠实语音的"童话讲述者"

[![python](https://img.shields.io/badge/Python-3.10-brightgreen)](https://github.com/SWivid/F5-TTS)
[![arXiv](https://img.shields.io/badge/arXiv-2410.06885-b31b1b.svg?logo=arXiv)](https://arxiv.org/abs/2410.06885)
[![demo](https://img.shields.io/badge/GitHub-Demo-orange.svg)](https://swivid.github.io/F5-TTS/)
[![hfspace](https://img.shields.io/badge/🤗-HF%20Space-yellow)](https://huggingface.co/spaces/mrfakename/E2-F5-TTS)
[![msspace](https://img.shields.io/badge/🤖-MS%20Space-blue)](https://modelscope.cn/studios/AI-ModelScope/E2-F5-TTS)
[![lab](https://img.shields.io/badge/🏫-X--LANCE-grey?labelColor=lightgrey)](https://x-lance.sjtu.edu.cn/)
[![lab](https://img.shields.io/badge/🏫-SII-grey?labelColor=lightgrey)](https://www.sii.edu.cn/)
[![lab](https://img.shields.io/badge/🏫-PCL-grey?labelColor=lightgrey)](https://www.pcl.ac.cn)
<!-- <img src="https://github.com/user-attachments/assets/12d7749c-071a-427c-81bf-b87b91def670" alt="Watermark" style="width: 40px; height: auto"> -->

**F5-TTS**：采用 ConvNeXt V2 的扩散 Transformer（Diffusion Transformer），训练与推理速度更快。

**E2 TTS**：Flat-UNet Transformer，对[论文](https://arxiv.org/abs/2406.18009)最接近的复现。

**Sway Sampling**：一种推理时的流步采样策略，能显著提升性能。

### 感谢所有贡献者！

## 最新动态
- **2025/03/12**：🔥 F5-TTS v1 基础模型，训练与推理性能更佳。[部分演示](https://swivid.github.io/F5-TTS_updates)。
- **2024/10/08**：F5-TTS 与 E2 TTS 基础模型已发布于 [🤗 Hugging Face](https://huggingface.co/SWivid/F5-TTS)、[🤖 Model Scope](https://www.modelscope.cn/models/SWivid/F5-TTS_Emilia-ZH-EN)、[🟣 Wisemodel](https://wisemodel.cn/models/SJTU_X-LANCE/F5-TTS_Emilia-ZH-EN)。

## 安装

### 如有需要，可创建独立环境

```bash
# Create a conda env with python_version>=3.10  (you could also use virtualenv)
conda create -n f5-tts python=3.11
conda activate f5-tts

# Install FFmpeg if you haven't yet
conda install ffmpeg
```

### 安装与设备相匹配的 PyTorch

<details>
<summary>NVIDIA GPU</summary>

> ```bash
> # Install pytorch with your CUDA version, e.g.
> pip install torch==2.8.0+cu128 torchaudio==2.8.0+cu128 --extra-index-url https://download.pytorch.org/whl/cu128
> 
> # And also possible previous versions, e.g.
> pip install torch==2.4.0+cu124 torchaudio==2.4.0+cu124 --extra-index-url https://download.pytorch.org/whl/cu124
> # etc.
> ```

</details>

<details>
<summary>AMD GPU</summary>

> ```bash
> # Install pytorch with your ROCm version (Linux only), e.g.
> pip install torch==2.9.1+rocm7.2 torchaudio==2.9.1+rocm7.2 --extra-index-url https://download.pytorch.org/whl/rocm7.2
>
> # For older GPUs (RDNA1/2/3 only):
> # pip install torch==2.5.1+rocm6.2 torchaudio==2.5.1+rocm6.2 --extra-index-url https://download.pytorch.org/whl/rocm6.2
> ```
>
> **注意：** RDNA 3.5 与 RDNA 4 架构的 GPU（Radeon 8050S/8060S、RX 9060/9070 系列）需要
> ROCm 7.x —— 这些架构（gfx1151/gfx1201）并不包含在 ROCm 6.x 中
> （对比 [6.2 兼容性列表](https://rocm.docs.amd.com/en/docs-6.2.4/compatibility/compatibility-matrix.html) 与
> [7.2 兼容性列表](https://rocm.docs.amd.com/en/docs-7.2.3/compatibility/compatibility-matrix.html)）。
> 在这些 GPU 上使用 ROCm 6.x 会导致 `HIP error: invalid device function`（[#1236](https://github.com/SWivid/F5-TTS/issues/1236)）。

</details>

<details>
<summary>Intel GPU</summary>

> ```bash
> # Install pytorch with your XPU version, e.g.
> # Intel® Deep Learning Essentials or Intel® oneAPI Base Toolkit must be installed
> pip install torch torchaudio --index-url https://download.pytorch.org/whl/test/xpu
> 
> # Intel GPU support is also available through IPEX (Intel® Extension for PyTorch)
> # IPEX does not require the Intel® Deep Learning Essentials or Intel® oneAPI Base Toolkit
> # See: https://pytorch-extension.intel.com/installation?request=platform
> ```

</details>

<details>
<summary>Apple Silicon</summary>

> ```bash
> # Install the stable pytorch, e.g.
> pip install torch torchaudio
> ```

</details>

### 然后可以从下面任选其一：

> ### 1. 作为 pip 包安装（如果仅用于推理）
> 
> ```bash
> pip install f5-tts
> ```
> 
> ### 2. 本地可编辑安装（如果还要进行训练、微调）
> 
> ```bash
> git clone https://github.com/SWivid/F5-TTS.git
> cd F5-TTS
> # git submodule update --init --recursive  # (optional, if use bigvgan as vocoder)
> pip install -e .
> ```

### 也支持通过 Docker 使用
```bash
# Build from Dockerfile
docker build -t f5tts:v1 .

# Run from GitHub Container Registry
docker container run --rm -it --gpus=all --mount 'type=volume,source=f5-tts,target=/root/.cache/huggingface/hub/' -p 7860:7860 ghcr.io/swivid/f5-tts:main

# Quickstart if you want to just run the web interface (not CLI)
docker container run --rm -it --gpus=all --mount 'type=volume,source=f5-tts,target=/root/.cache/huggingface/hub/' -p 7860:7860 ghcr.io/swivid/f5-tts:main f5-tts_infer-gradio --host 0.0.0.0
```

### 运行时（Runtime）

基于 Triton 与 TensorRT-LLM 的部署方案。

#### 基准测试结果
在单张 L20 GPU 上解码，使用 26 组不同的 prompt_audio 与 target_text 配对，NFE 为 16。

| 模型                | 并发数          | 平均延迟     | RTF    | 模式             |
|---------------------|----------------|-------------|--------|-----------------|
| F5-TTS Base (Vocos) | 2              | 253 ms      | 0.0394 | 客户端-服务器    |
| F5-TTS Base (Vocos) | 1 (Batch_size) | -           | 0.0402 | 离线 TRT-LLM    |
| F5-TTS Base (Vocos) | 1 (Batch_size) | -           | 0.1467 | 离线 Pytorch    |

更多信息请参阅[详细说明](src/f5_tts/runtime/triton_trtllm/README.md)。


## 推理

- 若想达到理想的效果，请花点时间阅读[详细指南](src/f5_tts/infer)。
- 只要恰当地搜索所遇到问题的关键字，[issues](https://github.com/SWivid/F5-TTS/issues?q=is%3Aissue) 会非常有帮助。

### 1. Gradio 应用

当前支持的功能：

- 带分块推理（Chunk Inference）的基础 TTS
- 多风格 / 多说话人生成
- 由 Qwen2.5-3B-Instruct 驱动的语音聊天
- [支持更多语言的自定义推理](src/f5_tts/infer/SHARED.md)

```bash
# Launch a Gradio app (web interface)
f5-tts_infer-gradio

# Specify the port/host
f5-tts_infer-gradio --port 7860 --host 0.0.0.0

# Launch a share link
f5-tts_infer-gradio --share
```

<details>
<summary>NVIDIA 设备的 docker compose 文件示例</summary>

```yaml
services:
  f5-tts:
    image: ghcr.io/swivid/f5-tts:main
    ports:
      - "7860:7860"
    environment:
      GRADIO_SERVER_PORT: 7860
    entrypoint: ["f5-tts_infer-gradio", "--port", "7860", "--host", "0.0.0.0"]
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: 1
              capabilities: [gpu]

volumes:
  f5-tts:
    driver: local
```

</details>

### 2. CLI 推理

```bash
# Run with flags
# Leave --ref_text "" will have ASR model transcribe (extra GPU memory usage)
f5-tts_infer-cli --model F5TTS_v1_Base \
--ref_audio "provide_prompt_wav_path_here.wav" \
--ref_text "The content, subtitle or transcription of reference audio." \
--gen_text "Some text you want TTS model generate for you."

# Run with default setting. src/f5_tts/infer/examples/basic/basic.toml
f5-tts_infer-cli
# Or with your own .toml file
f5-tts_infer-cli -c custom.toml

# Multi voice. See src/f5_tts/infer/README.md
f5-tts_infer-cli -c src/f5_tts/infer/examples/multi/story.toml
```


## 训练

### 1. 使用 Hugging Face Accelerate

最佳实践请参阅[训练与微调指南](src/f5_tts/train)。

### 2. 使用 Gradio 应用

```bash
# Quick start with Gradio web interface
f5-tts_finetune-gradio
```

更多操作说明请阅读[训练与微调指南](src/f5_tts/train)。


## [评估](src/f5_tts/eval)


## 开发

使用 pre-commit 保证代码质量（会自动运行代码检查与格式化工具）：

```bash
pip install pre-commit
pre-commit install
```

提交 pull request 时，请在每次 commit 之前运行：

```bash
pre-commit run --all-files
```

注意：部分模型组件针对 E722 设置了 lint 豁免，以兼容张量记法。


## 致谢

- [E2-TTS](https://arxiv.org/abs/2406.18009)：出色的成果，简单而有效
- [Emilia](https://arxiv.org/abs/2407.05361)、[WenetSpeech4TTS](https://arxiv.org/abs/2406.05763)、[LibriTTS](https://arxiv.org/abs/1904.02882)、[LJSpeech](https://keithito.com/LJ-Speech-Dataset/)：宝贵的数据集
- [lucidrains](https://github.com/lucidrains)：最初的 CFM 结构，同时也感谢 [bfs18](https://github.com/bfs18) 参与讨论
- [SD3](https://arxiv.org/abs/2403.03206) 与 [Hugging Face diffusers](https://github.com/huggingface/diffusers)：DiT 与 MMDiT 代码结构
- [torchdiffeq](https://github.com/rtqichen/torchdiffeq)：用作 ODE 求解器；[Vocos](https://huggingface.co/charactr/vocos-mel-24khz) 与 [BigVGAN](https://github.com/NVIDIA/BigVGAN)：用作 vocoder（声码器）
- [FunASR](https://github.com/modelscope/FunASR)、[faster-whisper](https://github.com/SYSTRAN/faster-whisper)、[UniSpeech](https://github.com/microsoft/UniSpeech)、[SpeechMOS](https://github.com/tarepan/SpeechMOS)：评估工具
- [ctc-forced-aligner](https://github.com/MahmoudAshraf97/ctc-forced-aligner)：用于语音编辑测试
- [mrfakename](https://x.com/realmrfakename)：Hugging Face Space 演示 ~
- [f5-tts-mlx](https://github.com/lucasnewman/f5-tts-mlx/tree/main)：由 [Lucas Newman](https://github.com/lucasnewman) 基于 MLX 框架的实现
- [F5-TTS-ONNX](https://github.com/DakeQQ/F5-TTS-ONNX)：由 [DakeQQ](https://github.com/DakeQQ) 提供的 ONNX Runtime 版本
- [Yuekai Zhang](https://github.com/yuekaizhang)：Triton 与 TensorRT-LLM 支持 ~

## 引用
如果我们的工作和代码库对你有帮助，请按如下方式引用：
```
@article{chen-etal-2024-f5tts,
      title={F5-TTS: A Fairytaler that Fakes Fluent and Faithful Speech with Flow Matching}, 
      author={Yushen Chen and Zhikang Niu and Ziyang Ma and Keqi Deng and Chunhui Wang and Jian Zhao and Kai Yu and Xie Chen},
      journal={arXiv preprint arXiv:2410.06885},
      year={2024},
}
```
## 许可证

我们的代码以 MIT 许可证发布。由于训练数据 Emilia 属于真实场景（in-the-wild）数据集，预训练模型采用 CC-BY-NC 许可证授权。由此带来的不便，敬请谅解。
