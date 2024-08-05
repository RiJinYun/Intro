---
license: other
license_name: cogvideox
license_link: https://huggingface.co/THUDM/CogVideoX-2b/blob/main/LICENSE
language:
  - en
tags:
  - cogvideox
  - video-generation
  - thudm
  - text-to-video
inference: false
---

# CogVideoX-2B

<p style="text-align: center;">
  <div align="center">
  <img src=https://github.com/THUDM/CogVideo/raw/main/resources/logo.svg width="50%"/>
  </div>
  <p align="center">
  <a href="https://huggingface.co/THUDM/CogVideoX-2b/blob/main/README_zh.md">📄 中文阅读</a> | 
  <a href="https://github.com/THUDM/CogVideo">🌐 Github</a> | 
  <a href="#">📜 arxiv (coming soon) </a>
</p>

## Demo Show

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

## Model Introduction

CogVideoX is an open-source video generation model that shares the same origins as [清影](https://chatglm.cn/video).
The table below provides a list of the video generation models we currently offer, along with their basic information.

| Model Name                                 | CogVideoX-2B (Current Repos)                     | 
|--------------------------------------------|--------------------------------------------------|
| Supported Prompt Language                  | English                                          | 
| GPU Memory Required for Inference          | 36GB (will be optimized before the PR is merged) | 
| GPU Memory Required for Fine-tuning (bs=1) | 46.2GB                                           |
| Prompt Length                              | 226 Tokens                                       |
| Video Length                               | 6 seconds                                        | 
| Frames Per Second                          | 8 frames                                         | 
| Resolution                                 | 720 * 480                                        |
| Positional Embeddings                      | Sinusoidal                                       |
| Quantized Inference                        | Not Supported                                    |          
| Multi-card Inference                       | Not Supported                                    |                             

## Quick Start 🤗

This model supports deployment using the huggingface diffusers library. You can deploy it by following these steps.

**We recommend that you visit our [GitHub](https://github.com/THUDM/CogVideo) and check out the relevant prompt
optimizations and conversions to get a better experience.**

1. Install the required dependencies

```shell
pip install --upgrade opencv-python transformers 
pip install git+https://github.com/huggingface/diffusers.git@878f609aa5ce4a78fea0f048726889debde1d7e8#egg=diffusers # Still in PR
```

2. Run the code

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

**Using a single A100 GPU, generating a video with the above configuration takes approximately 90 seconds**

If the generated model appears “all green” and not viewable in the default MAC player, it is a normal phenomenon (due to
OpenCV saving video issues). Simply use a different player to view the video.

## Explore the Model

Welcome to our [github](https://github.com/THUDM/CogVideo), where you will find:

1. More detailed technical details and code explanation.
2. Optimization and conversion of prompt words.
3. Reasoning and fine-tuning of SAT version models, and even pre-release.
4. Project update log dynamics, more interactive opportunities.
5. CogVideoX toolchain to help you better use the model.

## Model License

This model is released under the [CogVideoX LICENSE](LICENSE).

## Citation

The technical report is still being written, stay tuned.