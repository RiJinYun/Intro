# CogVideoX-2B

<p style="text-align: center;">
  <div align="center">
  <img src=https://github.com/THUDM/CogVideo/raw/main/resources/logo.svg width="50%"/>
  </div>
  <p align="center">
  <a href="https://huggingface.co/THUDM/CogVideoX-2b/blob/main/README.md">📄 Read in English</a> | 
  <a href="https://github.com/THUDM/CogVideo">🌐 Github</a> | 
  <a href="#">📜 arxiv (即将发布) </a>
</p>

## 作品案例

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Video Gallery with Captions</title>
    <style>
        .video-container {
            display: flex;
            flex-wrap: wrap;
            justify-content: space-around;
        }
        .video-item {
            width: 45%;
            margin-bottom: 20px;
            transition: transform 0.3s;
        }
        .video-item:hover {
            transform: scale(1.1);
        }
        .caption {
            text-align: center;
            margin-top: 10px;
            font-size: 11px;
        }
    </style>
</head>
<body>
    <div class="video-container">
        <div class="video-item">
            <video width="100%" controls>
                <source src="https://github.com/THUDM/CogVideo/raw/main/resources/videos/1.mp4" type="video/mp4">
            </video>
            <div class="caption">A detailed wooden toy ship with intricately carved masts and sails is seen gliding smoothly over a plush, blue carpet that mimics the waves of the sea. The ship's hull is painted a rich brown, with tiny windows. The carpet, soft and textured, provides a perfect backdrop, resembling an oceanic expanse. Surrounding the ship are various other toys and children's items, hinting at a playful environment. The scene captures the innocence and imagination of childhood, with the toy ship's journey symbolizing endless adventures in a whimsical, indoor setting.</div>
        </div>
        <div class="video-item">
            <video width="100%" controls>
                <source src="https://github.com/THUDM/CogVideo/raw/main/resources/videos/2.mp4" type="video/mp4">
            </video>
            <div class="caption">The camera follows behind a white vintage SUV with a black roof rack as it speeds up a steep dirt road surrounded by pine trees on a steep mountain slope, dust kicks up from it’s tires, the sunlight shines on the SUV as it speeds along the dirt road, casting a warm glow over the scene. The dirt road curves gently into the distance, with no other cars or vehicles in sight. The trees on either side of the road are redwoods, with patches of greenery scattered throughout. The car is seen from the rear following the curve with ease, making it seem as if it is on a rugged drive through the rugged terrain. The dirt road itself is surrounded by steep hills and mountains, with a clear blue sky above with wispy clouds.</div>
        </div>
        <div class="video-item">
            <video width="100%" controls>
                <source src="https://github.com/THUDM/CogVideo/raw/main/resources/videos/3.mp4" type="video/mp4">
            </video>
            <div class="caption">A street artist, clad in a worn-out denim jacket and a colorful bandana, stands before a vast concrete wall in the heart, holding a can of spray paint, spray-painting a colorful bird on a mottled wall.</div>
        </div>
        <div class="video-item">
            <video width="100%" controls>
                <source src="https://github.com/THUDM/CogVideo/raw/main/resources/videos/4.mp4" type="video/mp4">
            </video>
            <div class="caption"> In the haunting backdrop of a war-torn city, where ruins and crumbled walls tell a story of devastation, a poignant close-up frames a young girl. Her face is smudged with ash, a silent testament to the chaos around her. Her eyes glistening with a mix of sorrow and resilience, capturing the raw emotion of a world that has lost its innocence to the ravages of conflict.</div>
        </div>
    </div>
</body>
</html>

## 模型介绍

CogVideoX是 [清影](https://chatglm.cn/video) 同源的开源版本视频生成模型。下表战展示目前我们提供的视频生成模型列表，以及相关基础信息。

| Model Name    | CogVideoX-2B (当前仓库) | 
|---------------|---------------------|
| 提示词语言         | English             | 
| 推理显存消耗        | 21.6GB              | 
| 微调显存消耗 (bs=1) | 46.2GB              |
| 提示词长度上限       | 226 Tokens          |
| 视频生成长度        | 6 seconds           | 
| 视频生成帧率 (每秒)   | 8 frames            | 
| 视频生成分辨率       | 720 * 480           |
| 位置编码          | Sinusoidal          |
| 量化            | 不支持                 |          
| 多卡推理          | 不支持                 |                             

## 快速上手 🤗

本模型已经支持使用 huggingface 的 diffusers 库进行部署，你可以按照以下步骤进行部署。

**我们推荐您进入我们的 [github](https://github.com/THUDM/CogVideo) 并查看相关的提示词优化和转换，以获得更好的体验。**

1. 安装对应的依赖

```shell
pip install --upgrade opencv-python transformers 
pip install git+https://github.com/huggingface/diffusers.git@32da2e7673cfe0475a47c41b859f5fbd8bf17a40#egg=diffusers # Still in PR
```

2. 运行代码

```python
import torch
from diffusers import CogVideoXPipeline
from diffusers.utils import export_to_video

prompt = "A panda, dressed in a small, red jacket and a tiny hat, sits on a wooden stool in a serene bamboo forest. The panda's fluffy paws strum a miniature acoustic guitar, producing soft, melodic tunes. Nearby, a few other pandas gather, watching curiously and some clapping in rhythm. Sunlight filters through the tall bamboo, casting a gentle glow on the scene. The panda's face is expressive, showing concentration and joy as it plays. The background includes a small, flowing stream and vibrant green foliage, enhancing the peaceful and magical atmosphere of this unique musical performance."

pipe = CogVideoXPipeline.from_pretrained(
    "THUDM/CogVideoX-2b",
    torch_dtype=torch.float16
).to("cuda")

prompt_embeds, _ = pipe.encode_prompt(
    prompt=prompt,
    do_classifier_free_guidance=True,
    num_videos_per_prompt=1,
    max_sequence_length=226,
    device="cuda",
    dtype=torch.float16,
)

video = pipe(
    num_inference_steps=50,
    guidance_scale=6,
    prompt_embeds=prompt_embeds,
).frames[0]

export_to_video(video, "output.mp4", fps=8)
```

如果您生成的模型在 MAC 默认播放器上表现为 "全绿" 无法正常观看，属于正常现象 (OpenCV保存视频问题)，仅需更换一个播放器观看。

## 深入研究

欢迎进入我们的 [github](https://github.com/THUDM/CogVideo)，你将获得：

1. 更加详细的技术细节介绍和代码解释。
2. 提示词的优化和转换。
3. SAT版本模型进行推理和微调，甚至预发布。
4. 项目更新日志动态，更多互动机会。
5. CogVideoX 工具链，帮助您更好的使用模型。

## 模型协议

该模型根据 [CogVideoX LICENSE](LICENSE) 许可证发布。

## 引用

技术报告仍在撰写中，敬请期待
