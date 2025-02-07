# WEASEL
_Wide Enemy Assaulting Suicidal Explosive Lorry_

### Category
* [What this project do?](https://codeberg.org/Codeglacier/WEASEL#what-this-project-do)
* [Required hardware](https://codeberg.org/Codeglacier/WEASEL#required-hardware)
* [Demo](https://codeberg.org/Codeglacier/WEASEL#demo)
* [Set up the environment](https://codeberg.org/Codeglacier/WEASEL#how-to-set-up-the-environment)
* [Building up the application](https://codeberg.org/Codeglacier/WEASEL#building-up-the-donkey-car-application)
* [WEASEL ability rendering](https://codeberg.org/Codeglacier/WEASEL#enable-your-car-to-detect-tanks-avoid-obstacle-and-go-along-the-certain-route)
* [Directory management](https://codeberg.org/Codeglacier/WEASEL#then-move-the-models-folder-along-with-the-manage_model-py-script-into-your-mycar-folder-or-whatever-name-you-gave-it)
* [Drive the car](https://codeberg.org/Codeglacier/WEASEL#start-the-mission)
* [Appendix](https://codeberg.org/Codeglacier/WEASEL/src/branch/main#appendix)

### What does this project do?
#### WEASEL - a tiny unmanned ground vehicle (UGV) with autopilot, obstacle avoidance, tank detection and destruction abilities
The robotcar can drive itself along a certain path and dodge obstacles  
If a tank-like object comes into its vision, it will approach the target and then ignite the loaded bomb to destroy it once they are close enough.

![animated weasel's mission: detecting a tank and assaulting it after avoiding an obstacle en route to its target](https://codeberg.org/Codeglacier/WEASEL/raw/commit/a44c56c46b26c2e11aae8ea221885ff5fa3ff6ee/weasel%20gif.gif)

### Required hardware:
* Donkey Car  
_refer to [official website](https://docs.donkeycar.com/guide/build_hardware/) for details, or you can [buy one](https://docs.donkeycar.com/) directly_
* Power bank
* Raspberry pi (We used 4G RAM Model B)
    * System: 64-bit
    * Kernel version: 6.6
    * Debian version: 12 (bookworm)
* Pi Camera
* Webcam
* Ultrasonic sensor (e.g. HC-SR04)
* Breadboard, wires and LED (Proxy for explosive)
_Another device, such as a laptop or mobile phone, is also required to execute the programme on the Raspberry Pi_

#### Mechanism
After the programme is executed, WEASEL will drive itself with a CNN model and detect any tank en route using a fine-tuned **YOLO model**.
Once tanks found, WEASEL will move towards it and turn on the LED (i.e. the explosive) when they are close enough (The distance can be defined by user).

### Demo
[![YouTube](http://i.ytimg.com/vi/zE20AeRn17I/hqdefault.jpg)](https://www.youtube.com/watch?v=zE20AeRn17I)

### How to set up the environment
#### _You can skip this section if you've already built up your donkey car environment_
* Ensure your Raspberry Pi has Debian 12 (bookworm) image, or the **latest one**
You can download the official Raspberry Pi Imager and toggle the latest image [here](https://www.raspberrypi.com/software/)
* Build up your Donkey Car, equipping the LED, ultrasonic sensor, Pi camera, etc. onto it in a way you see fit
You can refer to [Donkey Car official website](https://docs.donkeycar.com/), but their tutorial is not quite clear
* Follow the steps in [Donkey Car official manual](https://docs.donkeycar.com/guide/robot_sbc/setup_raspberry_pi/) to set up the Donkey Car project on your Raspberry Pi
The snippets are also shown below
```bash
sudo apt-get update --allow-releaseinfo-change
sudo apt-get upgrade
sudo raspi-config
```
Then choose `Interfacing Options` -> `I2C` and enable it
`Advanced Options` -> `Expand Filesystem` -> `Finish`
`sudo reboot`
After reboot:
```bash
# install related library
sudo apt install libatlas-base-dev libhdf5-dev python3-h5py
# install Tensorflow
pip3 install tensorflow --break-system-packages
# create a virtual environment
python3 -m venv donkeyEnv --system-site-packages
# create a script to auto-activate the virtual environment
echo 'source ~/donkeyEnv/bin/activate' >> ~/.bashrc
# activate the virtual environment
source ~/.bashrc

# install libcap for link-layer packets processing
sudo apt install libcap-dev
# gcc: turn C++ code into machine code
sudo apt-get install gcc python3-dev
# psutil: library for system information retrieval
pip install psutil

# Then, the camera library, cloud connection
sudo apt install -y python3-libcamera
sudo apt install -y python3-kms++

# finally install git, and pull down the official Donkey Car repository
sudo apt install git
git clone https://github.com/autorope/donkeycar.git
# switching into the certain version (the 'main' one seems problematic)
cd donkeycar/
git checkout 5.1.0

# install the developer mode
pip install -e .[pi]

# if you face a version conflict, you can use these commands to brute force it
deactivate 
pip3 install tensorflow==2.12 --break-system-packages --upgrade
cd ..
source ~/donkeyEnv/bin/activate
cd donkeycar/
pip install -e .[pi]
```

### building up the Donkey Car application
change directory to the Donkey Car folder cloned from github, and make sure the virtual environment has been activated
then, build up your Donkey Car project following [official guidance](https://docs.donkeycar.com/guide/create_application/)
* the commands $\approx$ official ones provided below:
```bash
donkey createcar --path ~/mycar
```

## Enable your car to detect tanks, avoid obstacles, and follow a certain route
```bash
# clone WEASEL's home to your Raspberry Pi outside the 'mycar' folder
git clone https://codeberg.org/Codeglacier/WEASEL.git

mv manage_model.py tank_detection models ..

# install the dependencies for tank detection functions
cd tank_detection
pip install -r requirements.txt

# install the onnx package (Its installation somehow will fail, so treat it differently despite installed in the last step)
pip uninstall onnxruntime
pip install onnxruntime
# you may want to specify its version
# onnxruntime==1.20.1
```
### then, move the `models` folder along with the `manage_model.py` script into your `mycar` folder (or whatever name you gave it)
* Reminder:
    * The tank detection model is under `tank_detection/yolo_models` directory, and it will work automatically once `manage_model.py` executed
    * The autopilot model is inside `models` folder. You can choose whether to use it

All things are set up, driving now!

### start the mission
* first step: turn on the power of your Donkey Car, of course
* next, `ssh` into your Raspberry Pi and give it the following command based on your purpose:
    * autopilot only:
    ```bash
    python manage.py drive --model ~/mycar/models/mypilot.h5
    # Thus, your car won't perform tank detection nor ignite the LED
    ```
    * autopilot & tank mission:
    ```bash
    python manage_model.py drive --model ~/mycar/models/mypilot.h5
    # manage_model.py includes YOLO model deploying code
    ```
After execution, go to the website hosted by your Raspberry Pi and toggle the `(M)ode` or `Mode & Pilot` from `(U)ser` to `Full (A)uto`

Everything is done, good luck! Hooyah!

---

好的，我來幫您檢查這段 README 的最後幾行，並提供一些建議：

原版：
Appendix

The directory crawler_and_model_training contains the scripts for tank-image crawling (crawler.ipynb), and customised YOLO model training (model_training.ipynb)

Follow the guilds in the ipynb files to get the online images with python selenium crawler, or train an YOLO model with your own dataset.

---

### Appendix
* Directory Structure:
    * `crawler_and_model_training`:
        * `crawler.ipynb`: Contains scripts for crawling tank images using Python Selenium
        * `model_training.ipynb`: Contains scripts for training a custom YOLO model

* Instructions:
    * Image Crawling: Follow the instructions in `crawler.ipynb` to gather tank images from online sources using Python Selenium crawler technique
    * Model Training: Follow the instructions in `model_training.ipynb` to train a YOLO model using your crawled dataset or a custom dataset



