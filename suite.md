# 📌 Connecter un Kinect à un Raspberry Pi pour la Traduction de la Langue des Signes

## 🎯 Objectif
Utiliser un Kinect avec un Raspberry Pi pour analyser une personne parlant la langue des signes et traduire les gestes en phrases en français.

---

## 🎯 Matériel requis
- **Kinect v1 (Xbox 360) ou Kinect v2 (Xbox One)**
- **Adaptateur secteur Kinect** (obligatoire pour les modèles Xbox 360 et One)
- **Raspberry Pi 4 ou supérieur**
- **Carte microSD avec Raspberry Pi OS installé**
- **Câble USB 2.0 ou 3.0**

---

## 💪 1. Brancher le Kinect au Raspberry Pi
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

## 🔍 4. Acquisition et traitement des données
1. **Acquisition des données** :
   - Utiliser le **Kinect** pour capturer les mouvements des mains et du corps.
   - Extraire les coordonnées des articulations via **OpenNI** ou **libfreenect**.

### 📌 Extraction avec libfreenect (Kinect v1)
libfreenect permet d’accéder aux données de profondeur mais ne gère pas directement le suivi du squelette.

#### **Activer le capteur de profondeur**
```bash
freenect-glview
```

#### **Récupérer les données brutes en Python**
```python
import freenect
import cv2
import numpy as np

def get_depth():
    depth, _ = freenect.sync_get_depth()
    depth = depth.astype(np.uint8)  # Conversion pour affichage
    return depth

while True:
    depth_frame = get_depth()
    cv2.imshow('Kinect Depth', depth_frame)
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

cv2.destroyAllWindows()
```

---

### 📌 Extraction avec OpenNI (suivi des articulations)
OpenNI permet le suivi des squelettes avec NiTE.

#### **Installation d’OpenNI et NiTE**
```bash
sudo apt install libopenni2-dev
```

#### **Code Python pour récupérer les articulations**
```python
from openni import openni2
import numpy as np

openni2.initialize()
dev = openni2.Device.open_any()
depth_stream = dev.create_depth_stream()
depth_stream.start()

while True:
    frame = depth_stream.read_frame()
    frame_data = frame.get_buffer_as_uint16()
    depth_array = np.array(frame_data).reshape(480, 640)
    print("Profondeur à un point (320, 240) :", depth_array[240, 320])
```
---

## 🔄 5. Conversion des gestes en texte via un moteur NLP

### **1. Mappage des signes à des mots**
```python
gesture_to_text = {
    0: "Bonjour",
    1: "Merci",
    2: "Je t’aime",
    3: "Comment ça va ?",
}

def translate_gesture(predicted_class):
    return gesture_to_text.get(predicted_class, "Geste inconnu")
```

### **2. Assemblage des mots en phrases**
```python
recognized_gestures = [0, 3, 1]  # "Bonjour", "Comment ça va ?", "Merci"
translated_words = [translate_gesture(g) for g in recognized_gestures]
sentence = " ".join(translated_words) + "."
```

### **3. Amélioration avec un modèle NLP**
```python
import spacy

nlp = spacy.load("fr_core_news_sm")

def refine_sentence(sentence):
    doc = nlp(sentence)
    return " ".join([token.text for token in doc]).capitalize()

refined_sentence = refine_sentence(sentence)
```

### **4. Synthèse vocale (facultatif)**
```python
import os
os.system(f'espeak "{refined_sentence}"')
```

---

## 📚 Remarques
- **Kinect v2** nécessite **libfreenect2**, plus complexe à installer sur Raspberry Pi.
- Le **suivi des squelettes** avec OpenNI/NiTE est plus précis mais plus lourd à configurer.
