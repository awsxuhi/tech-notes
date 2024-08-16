

Select g4dn.4xlarge which has 16 cpu.

```shell
sudo apt update
sudo apt install wget git python3 python3-venv
dpkg -l python3-venv // check if the package was installed.
sudo apt install libgl1-mesa-glx
bash <(curl -s https://raw.githubusercontent.com/AUTOMATIC1111/stable-diffusion-webui/master/webui.sh) --skip-torch-cuda-test

/* install net-tools so that we cause use netstat -tlup */
sudo apt install net-tools

/* install pip and xformers */
sudo apt install python3-pip
pip install xformers
export PATH="$HOME/.local/bin:$PATH" // put it into ~/.bashrc
// I found we don't need to install xformers like this. All we need to do is to add --xformers in the ARGs https://github.com/AUTOMATIC1111/stable-diffusion-webui/wiki/Xformers


/* install gpu driver */
lspci | grep -i nvidia // to check the card if it's T4

wget https://us.download.nvidia.com/tesla/460.106.00/nvidia-driver-local-repo-ubuntu2004-460.106.00_1.0-1_amd64.deb
sudo dpkg -i nvidia-driver-local-repo-ubuntu2004-460.106.00_1.0-1_amd64.deb
sudo apt-key add /var/nvidia-driver-local-repo-ubuntu2004-460.106.00/7fa2af80.pub
sudo apt update
sudo apt install nvidia-driver-460
sudo reboot

/* running the webui, and listening on port 7680 */
./webui.sh --skip-torch-cuda-test --precision full --no-half --xformers
```

同样的参数，当硬件gpu不一样的时候，出来的图也是有一些差别的，比如颜色等。当挑战长款的像数也会产生完全不一样的图片，