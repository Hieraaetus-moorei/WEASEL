# 黃鼠狼號  
_反坦克自走炸彈車_  

### 目錄
* [簡介](https://codeberg.org/Codeglacier/WEASEL/src/branch/main/Read_me.md#%E9%80%99%E6%98%AF%E4%BB%80%E9%BA%BC-%E8%83%BD%E5%90%83%E5%97%8E)
* [硬體要求](https://codeberg.org/Codeglacier/WEASEL/src/branch/main/Read_me.md#%E6%89%80%E9%9C%80%E7%A1%AC%E9%AB%94)
* [運作原理](https://codeberg.org/Codeglacier/WEASEL/src/branch/main/Read_me.md#%E9%81%8B%E4%BD%9C%E5%8E%9F%E7%90%86)
* [示範影片](https://codeberg.org/Codeglacier/WEASEL/src/branch/main/Read_me.md#%E7%A4%BA%E7%AF%84%E5%BD%B1%E7%89%87)
* [環境建置](https://codeberg.org/Codeglacier/WEASEL/src/branch/main/Read_me.md#%E7%92%B0%E5%A2%83%E5%BB%BA%E7%BD%AE)
* [Donkey Car 應用程式建立](https://codeberg.org/Codeglacier/WEASEL/src/branch/main/Read_me.md#%E5%BB%BA%E7%BD%AE-donkey-car-%E6%87%89%E7%94%A8%E7%A8%8B%E5%BC%8F)
* [啟用坦克偵測、避障與特定路線行駛功能](https://codeberg.org/Codeglacier/WEASEL/src/branch/main/Read_me.md#%E5%95%9F%E7%94%A8%E5%9D%A6%E5%85%8B%E5%81%B5%E6%B8%AC-%E9%81%BF%E9%9A%9C%E8%88%87%E7%89%B9%E5%AE%9A%E8%B7%AF%E7%B7%9A%E8%A1%8C%E9%A7%9B%E5%8A%9F%E8%83%BD)
* [路徑搬移](https://codeberg.org/Codeglacier/WEASEL/src/branch/main/Read_me.md#%E7%84%B6%E5%BE%8C%E5%B0%87-models-%E8%B3%87%E6%96%99%E5%A4%BE%E5%92%8C-manage_model-py-%E8%85%B3%E6%9C%AC%E7%A7%BB%E8%87%B3-mycar-%E8%B3%87%E6%96%99%E5%A4%BE%E4%B8%AD-%E6%88%96%E4%BD%A0%E5%8F%96%E7%9A%84%E5%85%B6%E4%BB%96%E5%90%8D%E5%AD%97)
* [任務執行](https://codeberg.org/Codeglacier/WEASEL/src/branch/main/Read_me.md#%E4%BB%BB%E5%8B%99%E5%9F%B7%E8%A1%8C)
* [附錄](https://codeberg.org/Codeglacier/WEASEL/src/branch/main/Read_me.md#%E9%99%84%E9%8C%84)

### 這是什麼？能吃嗎？
#### 黃鼠狼號 - 微型無人地面載具，能自動駕駛、避障、偵測並摧毀坦克
該自走車能沿特定路徑行駛並避開障礙物
若有類似坦克的目標入鏡，車輛會靠近目標，並在距離夠近時引爆炸彈

![動畫展示 WEASEL 功能：自動避障、偵測並摧毀坦克](https://codeberg.org/Codeglacier/WEASEL/raw/commit/a44c56c46b26c2e11aae8ea221885ff5fa3ff6ee/weasel%20gif.gif)

### 所需硬體：  
* Donkey Car  
_組裝配件可參閱[官網](https://docs.donkeycar.com/guide/build_hardware/)，官網亦有[通路資訊](https://docs.donkeycar.com/)_
* 行動電源  
* 樹莓派  
  * 系統：64 位元
  * 核心版本：6.6
  * Debian 版本：12（bookworm）  
* 樹莓派相機模組
* 網路攝影機
* 超音波感測器（例如：HC-SR04）
* 麵包板、電線與 LED（模擬爆炸裝置）  
_還需另一台裝置如筆電或手機，以遠端執行樹莓派上的程式_  

#### 運作原理 
執行程式後，WEASEL 會使用 **CNN 模型** 自走，並透過客製化訓練的 **YOLO 模型** 偵測路上是否有坦克
一旦發現坦克，WEASEL 會靠近目標，當距離足夠近時 (距離可由用戶定義），啟動 LED（即爆炸裝置） 

#### 示範影片  
[![YouTube](http://i.ytimg.com/vi/zE20AeRn17I/hqdefault.jpg)](https://www.youtube.com/watch?v=zE20AeRn17I)  

### 環境建置  
#### 如果你已建好 Donkey Car 環境，則可跳過本段
* 確保你的樹莓派已安裝 Debian 12（bookworm）映像檔，或使用 **最新版本**  
可下載樹莓派官方工具並選擇最新映像檔 [點此前往](https://www.raspberrypi.com/software/)  
* 組裝 Donkey Car，將 LED、超音波感測器、樹莓派相機模組等安裝到車輛上
可參考 [Donkey Car 官網](https://docs.donkeycar.com/)，但其教學不甚詳細
* 依 [Donkey Car 官方手冊](https://docs.donkeycar.com/guide/robot_sbc/setup_raspberry_pi/) 指引在樹莓派上建置 Donkey Car 專案  
相關指令如下：  
```bash  
sudo apt-get update --allow-releaseinfo-change  
sudo apt-get upgrade  
sudo raspi-config  
```  
接著選擇 `Interfacing Options` -> `I2C` 並啟用  
`Advanced Options` -> `Expand Filesystem` -> `Finish`  
`sudo reboot`  
重啟後執行以下指令：  
```bash  
# 安裝相關函式庫  
sudo apt install libatlas-base-dev libhdf5-dev python3-h5py  
# 安裝 Tensorflow  
pip3 install tensorflow --break-system-packages  
# 創建虛擬環境  
python3 -m venv donkeyEnv --system-site-packages  
# 設定自啟動虛擬環境
echo 'source ~/donkeyEnv/bin/activate' >> ~/.bashrc  
# 啟用虛擬環境
source ~/.bashrc  

# 安裝處理網路封包的 libcap
sudo apt install libcap-dev  
# 安裝 gcc 編譯器與開發工具
sudo apt-get install gcc python3-dev  
# 安裝 psutil 函式庫
pip install psutil  

# 安裝相機套件及雲端連接工具  
sudo apt install -y python3-libcamera  
sudo apt install -y python3-kms++  

# 最後，安裝 git 並下載 Donkey Car 官方程式貯藏庫
sudo apt install git  
git clone https://github.com/autorope/donkeycar.git  
# 切換至特定版本（'main' 版本可能有問題）  
cd donkeycar/  
git checkout 5.1.0  

# 安裝開發模式
pip install -e .[pi]  

# 若遇版本衝突，可用以下指令強制解決  
deactivate  
pip3 install tensorflow==2.12 --break-system-packages --upgrade  
cd ..  
source ~/donkeyEnv/bin/activate  
cd donkeycar/  
pip install -e .[pi]  
```  

### 建置 Donkey Car 應用程式 
切換到從 GitHub 抓下來的 Donkey Car 目錄，並確保虛擬環境已啟用  
然後根據 [官方指引](https://docs.donkeycar.com/guide/create_application/) 建置 Donkey Car 專案  
* 命令如下：
```bash  
donkey createcar --path ~/mycar  
```  

## 啟用坦克偵測、避障與特定路線行駛功能  
```bash  
# 將 WEASEL 主程式抓到樹莓派（放在 'mycar' 資料夾外面）  
git clone https://codeberg.org/Codeglacier/WEASEL.git  

mv manage_model.py tank_detection models ..

# 安裝坦克偵測功能的依賴項 
cd tank_detection  
pip install -r requirements.txt  

# 安裝 onnx 套件（此步驟可能會失敗，因此需特例處理）  
pip uninstall onnxruntime  
pip install onnxruntime  
# 或指定版本
# onnxruntime==1.20.1  
```  
### 然後將 `models` 資料夾和 `manage_model.py` 腳本移至 `mycar` 資料夾中（或你取的其他名字）  
* 提醒：  
    * 坦克偵測模型位於 `tank_detection/yolo_models` 目錄下，執行 `manage_model.py` 後將自動啟用  
    * 自駕模型位於 `models` 資料夾，可選擇是否使用 

準備就緒，開車囉！

### 任務執行
* 第一步：當然是開啟 Donkey Car 的電源 
* 接著，`ssh` 登入樹莓派並依任務需求執行以下命令：
    * 僅自動駕駛：  
    ```bash  
    python manage.py drive --model ~/mycar/models/mypilot.h5  
    # 不會執行坦克偵測、啟動 LED  
    ```  
    * 自動駕駛與坦克相關任務：  
    ```bash  
    python manage_model.py drive --model ~/mycar/models/mypilot.h5  
    # manage_model.py 包含 YOLO 模型部署程式碼  
    ```  
執行後，前往樹莓派發出的網站，並將 `(M)ode` 或 `Mode & Pilot` 從 `(U)ser` 切換到 `Full (A)uto`  

全部完成，祝好運！

以下是新增內容的繁體中文翻譯，保持了 Markdown 格式：  

---

### 附錄  
* 資料夾結構：  
    * `crawler_and_model_training`:  
        * `crawler.ipynb`：使用 Python Selenium 爬取坦克圖片的腳本  
        * `model_training.ipynb`：訓練自定義 YOLO 模型的腳本  

* 使用說明：  
    * 圖片爬取：按照 `crawler.ipynb` 中的說明，即可用 Python Selenium 爬蟲技術從網路上收集坦克圖片  
    * 模型訓練：依 `model_training.ipynb` 中的指示，便可用爬到的資料或自訂資料集訓練 YOLO 模型  