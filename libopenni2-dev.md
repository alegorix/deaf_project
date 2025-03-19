## 🔹 **1. OpenNI + NiTE** (Meilleur pour le suivi squelettique)
👉 **Pourquoi ?** OpenNI permet d’accéder aux flux de profondeur et RGB du Kinect et, avec NiTE, de suivre les squelettes et détecter les mouvements des mains.  

### 📌 **Installation**
```bash
sudo apt install libopenni2-dev
```

```

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


