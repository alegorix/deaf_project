# 📌 Utiliser Kinect avec libfreenect et OpenCV sur Raspberry Pi

## 🎯 Objectif
Capturer les données de profondeur du Kinect (Xbox 360) avec **libfreenect** et les afficher avec **OpenCV** pour traiter les images de manière simple.

---

## 🛠️ Matériel requis
- **Kinect v1 (Xbox 360)**
- **Raspberry Pi 4 ou supérieur**
- **Carte microSD avec Raspberry Pi OS installé**
- **Câble USB 2.0 ou 3.0**

---

## 🔧 Prérequis logiciels
1. **Raspberry Pi OS installé et mis à jour** :
   Ouvre un terminal et mets à jour ton Raspberry Pi :
   ```bash
   sudo apt update && sudo apt upgrade -y
   	```
   	
   	
2. **Installation des dépendances nécessaires : Installe les dépendances qui sont nécessaires pour compiler et exécuter libfreenect et utiliser OpenCV :

```bash
sudo apt install cmake libusb-1.0-0-dev libturbojpeg0-dev libglfw3-dev libopencv-dev
	```
	
```bash	
	git clone https://github.com/OpenKinect/libfreenect.git
cd libfreenect
	```
```bash
mkdir build && cd build
cmake ..
make
sudo make install
sudo ldconfig
	```
```bash
lsusb
	```
