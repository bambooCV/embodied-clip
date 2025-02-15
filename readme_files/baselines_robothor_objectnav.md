RoboTHOR ObjectNav
==============================

## Installation

We've included instructions for installing the full AllenAct library (modifiable) with conda for [our branch](https://github.com/allenai/embodied-clip/tree/allenact), although you can also use the [official AllenAct repo (v0.5.0)](https://github.com/allenai/allenact/tree/v0.5.0) or perhaps newer.

```bash
git clone -b allenact --single-branch https://github.com/bambooCV/embodied-clip.git embclip-bamboo
cd embclip-bamboo
# Full library allenact
pip install -r requirements.txt; pip install -r dev_requirements.txt

pip install -r allenact_plugins/<PLUGIN_NAME>_plugin/extra_requirements.txt
# Plugins extra requirements Install RoboTHOR and CLIP plugins

export EMBCLIP_ENV_NAME=embclip-allenact
export CONDA_BASE="$(dirname $(dirname "${CONDA_EXE}"))"
export PIP_SRC="${CONDA_BASE}/envs/${EMBCLIP_ENV_NAME}/pipsrc"
conda env create --file ./conda/environment-base.yml --name $EMBCLIP_ENV_NAME
conda activate $EMBCLIP_ENV_NAME

# Install the appropriate cudatoolkit
conda env update --file ./conda/environment-<CUDA_VERSION>.yml --name $EMBCLIP_ENV_NAME
# OR for cpu mode
conda env update --file ./conda/environment-cpu.yml --name $EMBCLIP_ENV_NAME


# Download RoboTHOR dataset
bash datasets/download_navigation_datasets.sh robothor-objectnav

# Download pretrained ImageNet and CLIP visual encoders
python -c "from torchvision import models; models.resnet50(pretrained=True)"
python -c "import clip; clip.load('RN50')"
```

Please refer to the [official AllenAct installation instructions](https://allenact.org/installation/installation-allenact) for more details.

### Headless mode

If you would like to use AllenAct on a _headless_ machine (i.e. without a monitor), you'll need to:

1. [Start an x-display](https://allenact.org/installation/installation-framework/#installation-of-ithor-ithor-plugin) (`sudo python scripts/startx.py &`).
2. Update the [`THOR_COMMIT_ID`](https://github.com/allenai/embodied-clip/blob/49347c2241c9bd481c936870aad75012af3d355d/projects/objectnav_baselines/experiments/robothor/objectnav_robothor_base.py#L16) to `91139c909576f3bf95a187c5b02c6fd455d06b48` (a headless build of AI2-THOR). Also, set `THOR_IS_HEADLESS = True` right below this line.

## Training

```bash
# ImageNet
PYTHONPATH=. python allenact/main.py -o storage/objectnav-robothor-rgb-imagenet-rn50 -b projects/objectnav_baselines/experiments/robothor objectnav_robothor_rgb_resnet50gru_ddppo

# CLIP
PYTHONPATH=. python allenact/main.py -o storage/objectnav-robothor-rgb-clip-rn50 -b projects/objectnav_baselines/experiments/robothor/clip objectnav_robothor_rgb_clipresnet50gru_ddppo
```

## Using pretrained models

```bash
# ImageNet
curl -o pretrained_model_ckpts/objectnav-robothor-imagenet-rn50.195M.pt https://prior-model-weights.s3.us-east-2.amazonaws.com/embodied-ai/navigation/exp_Objectnav-RoboTHOR-RGB-ResNet50GRU-DDPPO__stage_00__steps_000195242243.pt

# CLIP
curl -o pretrained_model_ckpts/objectnav-robothor-clip-rn50.130M.pt https://prior-model-weights.s3.us-east-2.amazonaws.com/embodied-ai/navigation/exp_Objectnav-RoboTHOR-RGB-ClipResNet50GRU-DDPPO__stage_00__steps_000130091717.pt
```

You can use these models with the `python allenact/main.py` arguments `-c pretrained_model_ckpts/objectnav-robothor-imagenet-rn50.195M.pt` or `-c pretrained_model_ckpts/objectnav-robothor-clip-rn50.130M.pt`.

## Evaluating 

Simply append the `--eval` argument to the above `python allenact/main.py` commands.
