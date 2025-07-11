The issue is that Raspberry Pi cameras use different backends than regular USB webcams. The cv2.CAP_DSHOW backend I used is Windows-specific and won't work on Raspberry Pi.
Here are the steps to troubleshoot and fix your Raspberry Pi camera:
1. Enable the Camera Interface
bashsudo raspi-config

Go to Interface Options → Camera → Enable
Reboot: sudo reboot

2. Check Camera Detection
bash# For older Raspberry Pi OS
vcgencmd get_camera

# For newer Raspberry Pi OS (with libcamera)
libcamera-hello --list-cameras
3. Check Camera Connection

Ensure the ribbon cable is properly connected
The blue side should face the USB ports on the Pi

4. Install Required Packages (if needed)
bash# For older systems
sudo apt update
sudo apt install python3-opencv

# For newer systems with libcamera
sudo apt install python3-picamera2
5. Alternative: Use PiCamera Library
If OpenCV still doesn't work, here's a version using the PiCamera library:
python# Install with: pip install picamera2
from picamera2 import Picamera2
import cv2
import numpy as np

def start_picamera_tracking():
    picam2 = Picamera2()
    config = picam2.create_preview_configuration(main={"size": (640, 480)})
    picam2.configure(config)
    picam2.start()
    
    while True:
        frame = picam2.capture_array()
        # Convert from RGB to BGR for OpenCV
        frame = cv2.cvtColor(frame, cv2.COLOR_RGB2BGR)
        
        # Your existing eye tracking code here...
        # process_frame(frame)
        
        cv2.imshow('Raspberry Pi Camera', frame)
        if cv2.waitKey(1) & 0xFF == ord('q'):
            break
    
    picam2.stop()
    cv2.destroyAllWindows()
The updated code I provided will try multiple backends (V4L2, GStreamer) that work better with Raspberry Pi cameras. Try running it and let me know what error messages you get - this will help pinpoint the exact issue!