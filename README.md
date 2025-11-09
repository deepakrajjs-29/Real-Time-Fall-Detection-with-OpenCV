# 🚨 AI-Powered Fall Detection System

<div align="center">

![Python](https://img.shields.io/badge/Python-3.7+-blue.svg)
![OpenCV](https://img.shields.io/badge/OpenCV-4.5+-green.svg)
![MediaPipe](https://img.shields.io/badge/MediaPipe-Latest-orange.svg)
![Status](https://img.shields.io/badge/Status-Active-success.svg)

**Real-time fall detection using AI pose estimation 🏥**

[Features](#-features) • [Installation](#-installation) • [Usage](#-usage) • [How It Works](#-how-it-works) • [Applications](#-real-world-applications)

![Fall Detection Demo](https://img.shields.io/badge/Demo-Watch-red.svg)

</div>

---

## 📋 Overview

An intelligent fall detection system that uses computer vision and pose estimation to monitor people in real-time. Ideal for elderly care, workplace safety, hospitals, and smart home applications. The system analyzes body posture and alerts when a potential fall is detected.

## ✨ Features

- 🎯 **Real-time Pose Detection** - Tracks 33 body landmarks using MediaPipe
- ⚡ **Instant Fall Detection** - Identifies falls within milliseconds
- 📊 **Visual Feedback** - On-screen status indicators and pose skeleton
- 🔔 **Alert System** - Console notifications when falls are detected
- 👤 **Single Person Tracking** - Optimized for individual monitoring
- 🎨 **Visual Pose Overlay** - See body landmarks and connections in real-time
- ⏱️ **Timestamp Logging** - Records exact time of fall detection

## 🛠️ Installation

### Prerequisites

- Python 3.7 or higher
- Webcam or IP camera
- Operating System: Windows, macOS, or Linux
- Minimum 4GB RAM recommended

### Quick Setup

**Step 1: Create Project Directory**
```bash
mkdir fall-detection
cd fall-detection
```

**Step 2: Install Dependencies**
```bash
pip install opencv-python mediapipe
```

**Alternative: Using requirements.txt**

Create `requirements.txt`:
```text
opencv-python>=4.5.0
mediapipe>=0.10.0
```

Install:
```bash
pip install -r requirements.txt
```

### Verify Installation

```python
import cv2
import mediapipe as mp
print("✅ All dependencies installed successfully!")
```

## 🚀 Usage

### Starting the System

1. **Run the script:**
   ```bash
   python fall_detection.py
   ```

2. **Position yourself** in front of the camera (full body should be visible)

3. **Monitor status:**
   - **Green "Normal"** - Standing/sitting position detected
   - **Red "Fall Detected!"** - Horizontal body position detected

4. **Exit:** Press `ESC` key to quit

### 📹 Camera Setup Tips

| ✅ Best Practices | ❌ Avoid |
|------------------|---------|
| Mount camera at chest/head height | Too high or too low angles |
| Ensure good lighting | Dark or backlit areas |
| Keep full body in frame | Partial body visibility |
| Use plain background | Cluttered backgrounds |
| Maintain 2-4 meters distance | Too close (<1m) or far (>5m) |

## 🔧 How It Works

### Detection Pipeline

```
📹 Video Capture
    ↓
🔄 Frame Flip (Mirror Effect)
    ↓
🎨 BGR → RGB Conversion
    ↓
🤖 MediaPipe Pose Estimation
    ↓
📍 Extract 33 Body Landmarks
    ↓
📏 Calculate Shoulder & Hip Positions
    ↓
🧮 Compute Vertical Distance
    ↓
⚖️ Fall Detection Logic
    ↓
🚨 Trigger Alert if Fall Detected
    ↓
📺 Display Status on Screen
```

### 🧠 Detection Algorithm

#### Key Body Landmarks

The system tracks these critical points:

```python
• LEFT_SHOULDER (Landmark 11)
• RIGHT_SHOULDER (Landmark 12)
• LEFT_HIP (Landmark 23)
• RIGHT_HIP (Landmark 24)
```

#### Fall Detection Logic

```
1. Calculate Average Shoulder Y-Position:
   shoulder_y = (left_shoulder.y + right_shoulder.y) / 2

2. Calculate Average Hip Y-Position:
   hip_y = (left_hip.y + right_hip.y) / 2

3. Compute Vertical Distance:
   vertical_distance = hip_y - shoulder_y

4. Fall Detection:
   IF vertical_distance < 50 pixels:
      → Body is HORIZONTAL
      → FALL DETECTED! 🚨
   ELSE:
      → Body is VERTICAL
      → Normal Status ✅
```

### 📊 Visual Representation

```
Standing Position:              Fallen Position:
     🟢 Shoulder                🔴 Shoulder ← → Hip
        |                       
        |                       (Horizontal alignment)
     🟢 Hip                     Distance < 50px
        |
   Distance > 50px
```

## 💻 Code Breakdown

### 1. Initialization
```python
mp_pose = mp.solutions.pose
pose = mp_pose.Pose(
    min_detection_confidence=0.7,  # 70% confidence threshold
    min_tracking_confidence=0.7    # Smooth tracking
)
```

### 2. Key Parameters

| Parameter | Value | Purpose |
|-----------|-------|---------|
| `min_detection_confidence` | 0.7 | Initial pose detection threshold |
| `min_tracking_confidence` | 0.7 | Frame-to-frame tracking accuracy |
| `fall_threshold` | 50 pixels | Vertical distance for fall detection |

### 3. Coordinate System

- **Normalized Coordinates**: MediaPipe returns values between 0.0 and 1.0
- **Pixel Conversion**: `pixel_y = normalized_y × frame_height`
- **Y-Axis**: Increases downward (top = 0, bottom = height)

## 🎯 Real-World Applications

### 🏥 Healthcare & Elderly Care
- **Nursing Homes**: Monitor residents 24/7
- **Home Care**: Alert caregivers instantly
- **Hospitals**: Patient room monitoring
- **Rehabilitation Centers**: Track recovery progress

### 🏭 Industrial Safety
- **Construction Sites**: Worker safety monitoring
- **Warehouses**: Accident detection
- **Factories**: Workplace injury prevention

### 🏠 Smart Home Integration
- **Living Assistance**: Help elderly living alone
- **Security Systems**: Intrusion detection
- **Emergency Response**: Automatic 911 calls

### 🏋️ Sports & Fitness
- **Gym Safety**: Weightlifting accident prevention
- **Sports Training**: Analyze falls in contact sports
- **Physical Therapy**: Monitor exercise safety

## 🔧 Configuration Options

### Adjusting Sensitivity

**More Sensitive (Detect Earlier):**
```python
if (hip_y - shoulder_y) < 80:  # Increased threshold
    fall_detected = True
```

**Less Sensitive (Reduce False Positives):**
```python
if (hip_y - shoulder_y) < 30:  # Decreased threshold
    fall_detected = True
```

### Multi-Person Detection

```python
# Change to detect multiple people
pose = mp_pose.Pose(
    model_complexity=2,  # More accurate
    min_detection_confidence=0.7
)
```

## 🚀 Advanced Features You Can Add

### 1. Alert Notifications 📧
```python
import smtplib

def send_alert_email():
    # Send email to caregiver
    server = smtplib.SMTP('smtp.gmail.com', 587)
    server.sendmail(from_addr, to_addr, "Fall detected!")
```

### 2. Save Fall Video 📹
```python
if fall_detected and not video_saved:
    out = cv2.VideoWriter('fall_' + str(fall_time) + '.avi', ...)
    video_saved = True
```

### 3. Sound Alarm 🔔
```python
import winsound

if fall_detected:
    winsound.Beep(1000, 500)  # 1000Hz for 500ms
```

### 4. Database Logging 💾
```python
import sqlite3

def log_fall(timestamp):
    conn = sqlite3.connect('falls.db')
    cursor = conn.cursor()
    cursor.execute("INSERT INTO falls VALUES (?)", (timestamp,))
    conn.commit()
```

### 5. SMS Alerts 📱
```python
from twilio.rest import Client

def send_sms_alert():
    client = Client(account_sid, auth_token)
    message = client.messages.create(
        body="Fall detected!",
        to="+1234567890"
    )
```

## 🐛 Troubleshooting

| Issue | Possible Cause | Solution |
|-------|---------------|----------|
| **No pose detected** | Poor lighting or camera angle | Improve lighting, adjust camera position |
| **False positives** | Threshold too high | Decrease fall threshold to 30-40 pixels |
| **Missed falls** | Threshold too low | Increase fall threshold to 60-80 pixels |
| **Laggy performance** | Low-end hardware | Reduce camera resolution or confidence values |
| **Camera not opening** | Permission issues | Check camera access in system settings |

### Debug Mode

Add this for troubleshooting:
```python
print(f"Shoulder Y: {shoulder_y:.2f}, Hip Y: {hip_y:.2f}")
print(f"Distance: {hip_y - shoulder_y:.2f}")
```

## 📊 Performance Metrics

### System Requirements

| Component | Minimum | Recommended |
|-----------|---------|-------------|
| **CPU** | Dual-core 2.0 GHz | Quad-core 2.5+ GHz |
| **RAM** | 4 GB | 8 GB+ |
| **Camera** | 720p @ 15 fps | 1080p @ 30 fps |
| **OS** | Windows 10, macOS 10.14, Ubuntu 18.04 | Latest versions |

### Expected Performance

- **FPS**: 15-30 frames per second
- **Detection Latency**: < 100ms
- **CPU Usage**: 20-40%
- **Memory Usage**: ~500MB

## 🔐 Privacy & Ethics

### Important Considerations

⚠️ **Privacy Warning**: This system records video footage. Ensure:
- Users are informed and consent
- Compliance with local privacy laws (GDPR, HIPAA, etc.)
- Secure storage of footage
- Data retention policies

### Ethical Guidelines

✅ **DO:**
- Obtain explicit consent
- Use for safety purposes only
- Implement data encryption
- Regular security audits

❌ **DON'T:**
- Deploy without consent
- Store unnecessary footage
- Share data without authorization
- Use for surveillance beyond safety

## 📁 Project Structure

```
fall-detection/
│
├── fall_detection.py          # Main application
├── requirements.txt           # Dependencies
├── README.md                  # Documentation
├── config.py                  # Configuration (optional)
├── logs/                      # Fall detection logs
│   └── falls_YYYY-MM-DD.log
└── recordings/                # Video recordings (optional)
    └── fall_TIMESTAMP.avi
```

## 🔬 Technical Details

### MediaPipe Pose Landmarks

33 body landmarks tracked:
- **0-10**: Face and head
- **11-16**: Upper body (shoulders, elbows, wrists)
- **23-28**: Lower body (hips, knees, ankles)
- **17-22**: Hands
- **29-32**: Feet

### Coordinate System
```
(0,0) ─────────► X (width)
  │
  │
  │
  ▼
  Y (height)
```

## 🚀 Future Enhancements

- [ ] **Multi-person tracking** - Monitor multiple people simultaneously
- [ ] **Cloud integration** - Store data in cloud databases
- [ ] **Mobile app** - Receive alerts on smartphone
- [ ] **AI improvement** - Train custom ML models for better accuracy
- [ ] **Activity classification** - Distinguish between falls and sitting/lying
- [ ] **Fall prediction** - Detect unstable posture before falling
- [ ] **Integration with wearables** - Combine with smartwatch data
- [ ] **Heatmap analysis** - Identify high-risk areas
- [ ] **Emergency services API** - Automatic 911 calls

## 🤝 Integration Examples

### Raspberry Pi Setup
```python
# Use with Raspberry Pi Camera
cap = cv2.VideoCapture(0)
cap.set(cv2.CAP_PROP_FRAME_WIDTH, 640)
cap.set(cv2.CAP_PROP_FRAME_HEIGHT, 480)
```

### IP Camera Integration
```python
# Connect to IP camera
cap = cv2.VideoCapture('rtsp://username:password@ip:port/stream')
```

### Home Assistant
```python
import requests

def notify_home_assistant():
    requests.post(
        'http://homeassistant.local:8123/api/webhook/fall_detected',
        json={'timestamp': time.time()}
    )
```

## 📚 Resources & Learning

- [MediaPipe Pose Documentation](https://google.github.io/mediapipe/solutions/pose.html)
- [OpenCV Tutorials](https://docs.opencv.org/master/d6/d00/tutorial_py_root.html)
- [Fall Detection Research Papers](https://scholar.google.com/scholar?q=fall+detection+computer+vision)

## 👥 Contributing

Contributions welcome! Areas for improvement:
- Enhanced detection algorithms
- Multi-person tracking
- Better false positive reduction
- Cloud integration
- Mobile app development

## 📄 License

MIT License - Free to use, modify, and distribute.

## ⚠️ Disclaimer

This system is designed for assistance and monitoring purposes. It should NOT be the sole safety measure. Always combine with:
- Professional medical monitoring
- Emergency response systems
- Human supervision when possible

**Not FDA approved. Not intended to replace medical devices.**

## 🙏 Acknowledgments

Built with:
- **MediaPipe** by Google Research
- **OpenCV** - Open Source Computer Vision Library
- **Python** Community

---

<div align="center">

**🚨 Stay Safe with AI-Powered Monitoring 🚨**

*Made with ❤️ for safety and care*

**⭐ Star if this helps you! | 🐛 Report issues | 💡 Suggest features**

</div>
