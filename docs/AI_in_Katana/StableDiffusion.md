# Stable Diffusion on an HPC Environment

This document explains how to run Stable Diffusion on a High-Performance Computing (HPC) system.
It covers installation, running inference using a job scheduler, and fine-tuning a Stable Diffusion model.
All commands are shown as plain text to make copying easier.

<figure>
    <img src="../../assets/SD_logo.png" alt="Stable Diffusion" width="800">
</figure>

--------------------------------------------------

1. Installation

Create the Working Directory

Create a directory on scratch storage (or another suitable high-capacity filesystem on your HPC system).
This directory will contain the Stable Diffusion code, virtual environment, and model files.

```bash
export base_dir="/srv/scratch/$USER/<your_directory>"
mkdir -p $base_dir
cd $base_dir
```


Load the Python Module

Load an appropriate Python module available on your HPC system.

```bash
module load python
```


Create and Activate a Virtual Environment

Create a Python virtual environment to isolate Stable Diffusion dependencies.

```bash
python -m venv venv
source venv/bin/activate
```


Install Required Dependencies

Install Stable Diffusion dependencies such as PyTorch, Diffusers, and related libraries.

```bash
pip install torch diffusers transformers accelerate pillow tqdm pyyaml
```

--------------------------------------------------

2. Inference Using Job Scheduling

On HPC systems, inference should be run through the job scheduler to request GPU and memory resources.
For PBS arguments on Katana, see:
../using_katana/running_jobs.md


Create a PBS Script

Example PBS script for Stable Diffusion inference:

```bash
#!/bin/bash
#PBS -l select=1:ncpus=4:ngpus=1:mem=16GB:gpu_model=V100
#PBS -l walltime=2:00:00
#PBS -j oe
#PBS -N sd-inference
#PBS -M <your_email>@unsw.edu.au
#PBS -m abe

module load python
source $base_dir/venv/bin/activate

export HF_HOME="/srv/scratch/$USER/huggingface_cache"

PROMPT="A serene landscape with mountains and a lake"
MODEL_ID="CompVis/stable-diffusion-v1-4"
LORA_CHECKPOINT="/srv/scratch/$USER/finetune/sd-flower-model/<your_checkpoint>"

python inference.py --prompt "$PROMPT" --model_id "$MODEL_ID" --lora_checkpoint "$LORA_CHECKPOINT"
```


Important Note on Hugging Face Cache

By default, Hugging Face stores models in your home directory, which often has limited space.
You should redirect this cache to scratch storage.

```bash
export HF_HOME="/srv/scratch/<your_user>/huggingface_cache"
```

Scratch storage is not backed up. Move important files to your home directory when finished.


Inference Script (inference.py)

```bash
import argparse
import torch
from diffusers import StableDiffusionPipeline
from PIL import Image

def inference(prompt, model_id, lora_checkpoint=None):
    pipeline = StableDiffusionPipeline.from_pretrained(
        model_id, torch_dtype=torch.float16
    )
    device = "cuda" if torch.cuda.is_available() else "cpu"
    pipeline.to(device)

    if lora_checkpoint:
        pipeline.unet.load_attn_procs(lora_checkpoint)

    generator = torch.Generator(device).manual_seed(42)
    images = pipeline(
        prompt,
        num_inference_steps=30,
        generator=generator,
        num_images_per_prompt=4,
        height=512,
        width=512,
        guidance_scale=7.5
    ).images

    grid = Image.new("RGB", (1024, 1024))
    for i, img in enumerate(images):
        grid.paste(img, ((i % 2) * 512, (i // 2) * 512))

    grid.save(f"image_grid_{prompt.replace(' ', '_')}.png")

if __name__ == "__main__":
    parser = argparse.ArgumentParser()
    parser.add_argument("--prompt", required=True)
    parser.add_argument("--model_id", required=True)
    parser.add_argument("--lora_checkpoint")
    args = parser.parse_args()
    inference(args.prompt, args.model_id, args.lora_checkpoint)
```


Submit the Job

```bash
qsub <pbs_script_name>.pbs
```

--------------------------------------------------

3. Finetuning

Fine-tuning adapts Stable Diffusion to your own dataset.

Prepare Dataset

See Hugging Face dataset documentation:
https://huggingface.co/docs/datasets/image_dataset#imagefolder


Transfer Dataset to Katana

You may use rsync, WinSCP, or Globus depending on your operating system.


Clone Diffusers Repository

```bash
git clone https://github.com/huggingface/diffusers
```


PBS Script for Finetuning

```bash
#!/bin/bash
#PBS -l select=1:ncpus=4:ngpus=1:mem=16GB:gpu_model=V100
#PBS -l walltime=2:00:00
#PBS -j oe
#PBS -N sd-finetune
#PBS -M <your_email>@unsw.edu.au
#PBS -m abe

module load python

export base_dir="/srv/scratch/$USER/finetune"
export model_name="CompVis/stable-diffusion-v1-4"
export dataset_name="pranked03/flowers-blip-captions"
export HF_HOME="/srv/scratch/$USER/huggingface_cache"

source $base_dir/venv_deforum/bin/activate

accelerate launch $base_dir/diffusers/examples/text_to_image/train_text_to_image_lora.py   --pretrained_model_name_or_path=$model_name   --dataset_name=$dataset_name   --resolution=128   --train_batch_size=32   --mixed_precision=fp16   --max_train_steps=4000   --learning_rate=1e-05   --output_dir=$base_dir/sd-flower-model
```


Submit the Job

```bash
qsub <pbs_script_name>.pbs
```


Important Reminder

Scratch storage is not backed up. Copy trained models to your home directory.

```bash
cp -r /srv/scratch/$USER/finetune/sd-flower-model /home/$USER/
```


Video Tutorial on Deforum

Deforum project:
https://github.com/deforum-art/deforum-stable-diffusion

<iframe src="https://unsw.sharepoint.com/sites/ResearchTechnologyTraining/_layouts/15/embed.aspx?UniqueId=9d044957-ad7e-4e06-a352-29493aba1900&embed=%7B%22ust%22%3Atrue%2C%22hv%22%3A%22CopyEmbedCode%22%7D&referrer=StreamWebApp&referrerScenario=EmbedDialog.Create"
width="640" height="360" frameborder="0" scrolling="no" allowfullscreen
title="Running and fine-tuning GenAI Stable Diffusion on Katana HPC cluster.mp4"></iframe>
