# Setup Stable Diffusion XL Demo (0.9) WebUI() for_vast.ai
* GPU sharing cloud service `Vast.ai` : https://cloud.vast.ai/
* Setup memo : `2023/07/08 ver` for https://github.com/FurkanGozukara/stable-diffusion-xl-demo

# Preparation
1) Setup your local PC(Windows, macOS, Linux,...)'s SSH client setting. Generate a `SSH keypair(private key & public key)` for remote access.
2) Create a new account for vast.ai `Client Account Type`.
3) Regist your SSH public key into vast.ai `Account` menu.
4) Add credit 10 USD with your credit card on `Billing` menu. I DON'T recommend `Auto charge` setting.

# GPU Instance configuration
* Base docker image : `nvidia/cuda:11.8.0-cudnn8-runtime-ubuntu22.04`
* Launch Type: `ssh`
* On-start script: `Not set`
* Disk Space To Allocate: `over ~60GB` (recommend)
* Launch mode : 
   * `Run interactive shell server, SSH`. This will allow you to connect and run commands using an SSH client.
   * `Checked` : Use direct SSH connection - faster than proxy, but limited to machines with open ports. Proxy ssh still available as backup.
* GPU Type :
   *  `Interruptible` and `On-Demand` : both OK
   *  #GPUs : `1X` (if the instance has multi GPUs, the current version of Stable Diffusion uses only 1X GPU.) 

# Setup procedures
1. Start a instance based on the above `Instance configuration`.
2. Wait until the instance loads the docker image and starts up. (5 minutes at the fastest, 20 minutes at the longest)
3. Push the `>_ CONNECT` button and copy the `Direct ssh connect` command.
* ex) 
```sh
ssh -p XXXXX root@AAA.BBB.CCC.DDD -L 8080:localhost:8080
```
4. Paste the command into your terminal/command prompt and add `-L 7860:localhost:7860` for browser access (SSH local port fowarding)
* ex)
```sh
ssh -p XXXXX root@AAA.BBB.CCC.DDD -L 7860:localhost:7860
```
5. Connect the instance via SSH with the above `4.` ssh command.
6. Install the AUTOMATIC1111 / stable-diffusion-webui (v1.1.0)

6.1 step1 (as ROOT)
```sh
apt-get install vim unzip libgl1-mesa-dev libcairo2-dev wget git -y
apt-get install python3 python3-venv python3-dev build-essential -y
adduser user1 --disabled-password --gecos ""
su user1
```

6.2 step2 (as user1 = not root user)
```sh
cd ~
git clone https://github.com/FurkanGozukara/stable-diffusion-xl-demo
cd stable-diffusion-xl-demo

python3 -m venv venv
source venv/Scripts/activate
python3 -m pip install --upgrade pip

pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118
pip install xformers==0.0.21.dev557

pip install -r requirements2.txt


```

7. change access token for `app2.py`

* apply the eary access for SDXL model of StabilityAI
   * https://huggingface.co/stabilityai/stable-diffusion-xl-base-0.9
* get the access token from `https://huggingface.co/settings/tokens`
* edit the `access_token` of app2.py

8. start webUI

```sh
PYTORCH_CUDA_ALLOC_CONF=max_split_size_mb:128
ENABLE_REFINER=false

python stable-diffusion-xl-demo\app2.py
```


9.Access the stable diffusion by your local PC's web browser
   * http://localhost:7860
   * via SSH port forward by `-L 7860:localhost:7860`



# Ref. ( &Special thanks)
* https://cloud.vast.ai
* https://github.com/FurkanGozukara/stable-diffusion-xl-demo
* https://github.com/AUTOMATIC1111/stable-diffusion-webui
* https://hub.docker.com/r/nvidia/cuda/tags
* https://pytorch.org/get-started/pytorch-2.0/
* https://github.com/CompVis/stable-diffusion
* https://note.com/mayu_hiraizumi/n/n09bd32469b20
