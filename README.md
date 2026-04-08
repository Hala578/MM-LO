# MM-LO Release

## Overview
This repository is a lightweight public release of our mmWave-guided ILVR + ADM inference pipeline. It only includes the files required for inference reproduction: the released checkpoint, a small set of demo reference images, the inference code, and step-by-step instructions.

This release does **not** include any training data.

## What Is Included / Not Included
Included:
- Inference code: `scripts/ilvr_sample.py`, `scripts/resizer.py`, `scripts/guided_diffusion/`
- Main released checkpoint: `checkpoints/ema_0.9999_028000.pt`
- Six demo push reference categories:
  - `ref_imgs/1/pushtarget40`
  - `ref_imgs/1/pushtarget50`
  - `ref_imgs/1/pushtarget60`
  - `ref_imgs/1/pushtarget70`
  - `ref_imgs/1/pushtarget80`
  - `ref_imgs/1/pushtarget90`
- Documentation and dependency list: `README.md`, `requirements.txt`, `LICENSE`

Not included:
- Training data
- Optimizer states
- Training outputs and intermediate experiment files
- Unrelated MATLAB / WIFI / RFID / BVP assets
- Additional local checkpoints that were not selected for this release

Notes:
- `scripts/data_save1/ema_0.9999_009000.pt` in the original working repository is a newer local experimental checkpoint, but it is **not** the default public release model.
- `scripts/data_save1/ema_0.9999_022500.pt` was excluded because it is abnormal / corrupted.

## Directory Structure
```text
MM-LO/
  README.md
  LICENSE
  requirements.txt
  checkpoints/
    ema_0.9999_028000.pt
  ref_imgs/
    1/
      pushtarget40/
      pushtarget50/
      pushtarget60/
      pushtarget70/
      pushtarget80/
      pushtarget90/
  scripts/
    ilvr_sample.py
    resizer.py
    __init__.py
    guided_diffusion/
```

## Environment Setup
We recommend Python 3.8 or later.

1. Enter the release folder:
```bash
cd MM-LO
```

2. Install dependencies:
```bash
pip install -r requirements.txt
```

3. If you need a CUDA-enabled PyTorch build, install the matching PyTorch version first, then run the command above.

Notes:
- This release supports direct single-process inference by default.
- You do **not** need MPI or `mpiexec`.
- CPU inference is possible, but it will be much slower than GPU inference.

## Quick Start
Run the following command from the `MM-LO/` root directory. The example below uses `pushtarget90` and saves results to `outputs/push_demo/`.

```bash
python scripts/ilvr_sample.py --model_path checkpoints/ema_0.9999_028000.pt --base_samples ref_imgs/1/pushtarget90 --save_dir outputs/push_demo --attention_resolutions 16 --class_cond False --diffusion_steps 500 --dropout 0.0 --image_size 128 --learn_sigma True --noise_schedule linear --num_channels 128 --num_head_channels 64 --num_res_blocks 1 --resblock_updown True --use_fp16 False --use_scale_shift_norm True --timestep_respacing 100 --down_N 2 --range_t 5 --batch_size 1 --num_samples 1
```

If you also want to save intermediate diffusion steps, append:

```bash
--save_intermediate
```

If you want to start from a custom initial image, append:

```bash
--start_image path/to/your_start_image.png
```

## Parameter Summary
Required arguments:
- `--model_path`: path to the released checkpoint
- `--base_samples`: path to the reference image directory
- `--save_dir`: output directory

Default example model settings:
- `--image_size 128`
- `--num_channels 128`
- `--num_head_channels 64`
- `--num_res_blocks 1`
- `--attention_resolutions 16`
- `--resblock_updown True`
- `--learn_sigma True`
- `--noise_schedule linear`
- `--use_scale_shift_norm True`

Default example sampling settings:
- `--diffusion_steps 500`
- `--timestep_respacing 100`
- `--down_N 2`
- `--range_t 5`
- `--batch_size 1`
- `--num_samples 1`

Optional arguments:
- `--start_image`: custom initial image
- `--save_intermediate`: save diffusion step images to `<save_dir>/steps/`

Important:
- `--down_N` must be a power of 2.
- The released demo reference directories each contain one image, so `--batch_size 1` is recommended.

## Switching Between Push Categories
The release package includes these six reference directories:
- `ref_imgs/1/pushtarget40`
- `ref_imgs/1/pushtarget50`
- `ref_imgs/1/pushtarget60`
- `ref_imgs/1/pushtarget70`
- `ref_imgs/1/pushtarget80`
- `ref_imgs/1/pushtarget90`

To switch categories, only replace the `--base_samples` path.

Example for `pushtarget40`:

```bash
python scripts/ilvr_sample.py --model_path checkpoints/ema_0.9999_028000.pt --base_samples ref_imgs/1/pushtarget40 --save_dir outputs/push40_demo --attention_resolutions 16 --class_cond False --diffusion_steps 500 --dropout 0.0 --image_size 128 --learn_sigma True --noise_schedule linear --num_channels 128 --num_head_channels 64 --num_res_blocks 1 --resblock_updown True --use_fp16 False --use_scale_shift_norm True --timestep_respacing 100 --down_N 2 --range_t 5 --batch_size 1 --num_samples 1
```

## Output Files
Default output behavior:
- Final generated images are saved under `--save_dir`
- File names follow the format `sample_0000.png`, `sample_0001.png`, and so on

If `--save_intermediate` is enabled:
- Intermediate diffusion step images are saved under `<save_dir>/steps/`

## FAQ
**Why are the training data not released?**

This public package is intended for inference reproduction only, so it includes released weights, demo reference images, and inference code, but not the training data.

**Why is `ema_0.9999_009000.pt` not the default release model?**

It is a newer local experimental checkpoint in the original working repository, but it was not selected as the default public release model.

**Why was `ema_0.9999_022500.pt` excluded?**

It was identified as an abnormal / corrupted checkpoint during release cleanup.

**Do I need MPI or multi-GPU setup?**

No. This release supports direct single-process execution with `python scripts/ilvr_sample.py ...`.

**What if inference is slow?**

Please make sure that you installed a GPU-enabled PyTorch build and that CUDA is available. CPU inference will be much slower.

## License
This release is distributed under the MIT License. See `LICENSE` for details.
