# deaf_project
# 📌 Connecter un Kinect à un Raspberry Pi

## 🎯 Matériel requis
- **Kinect v1 (Xbox 360) ou Kinect v2 (Xbox One)**
- **Adaptateur secteur Kinect** (obligatoire pour les modèles Xbox 360 et One)
- **Raspberry Pi 4 ou supérieur**
- **Carte microSD avec Raspberry Pi OS installé**
- **Câble USB 2.0 ou 3.0**

---

## 🔌 1. Brancher le Kinect au Raspberry Pi
### Kinect v1 (Xbox 360)
1. Connecter le Kinect à son **adaptateur secteur**.
2. Relier le câble USB du Kinect à un **port USB du Raspberry Pi**.

### Kinect v2 (Xbox One)
1. Utiliser l’**adaptateur officiel Kinect v2**.
2. Brancher l’adaptateur sur une **alimentation externe**.
3. Relier le Kinect via USB au Raspberry Pi.

---

## 🛠️ 2. Installer les pilotes OpenKinect
Les pilotes **libfreenect** permettent d’utiliser le Kinect v1 sous Linux.

### Étapes d’installation
1. Mettre à jour le Raspberry Pi :
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```
2. Installer les dépendances requises :
   ```bash
   sudo apt install git cmake libusb-1.0-0-dev libturbojpeg0-dev libglfw3-dev
   ```
3. Cloner le dépôt de **libfreenect** :
   ```bash
   git clone https://github.com/OpenKinect/libfreenect.git
   ```
4. Compiler et installer :
   ```bash
   cd libfreenect
   mkdir build && cd build
   cmake ..
   make
   sudo make install
   sudo ldconfig
   ```

---

## 🏃 3. Tester le Kinect
Vérifier si le Kinect est bien détecté :
```bash
lsusb
```
Vous devriez voir une ligne contenant **Microsoft Corp. Xbox NUI Camera**.

Tester l’affichage vidéo :
```bash
freenect-glview
```
Si tout fonctionne, une fenêtre avec l’image de la caméra devrait apparaître.

---

## 📌 Remarques
- **Kinect v2** nécessite **libfreenect2**, qui est plus complexe à installer sur Raspberry Pi.
- Pour le **suivi des squelettes**, il faut utiliser OpenNI ou NiTE (plus difficile à configurer sur Pi).

📢 **Si vous voulez utiliser Kinect pour un projet spécifique, n’hésitez pas à poser vos questions !** 😊
