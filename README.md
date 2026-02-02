# 🔐 Face Recognition Locking System

A comprehensive face recognition system with real-time detection, enrollment, live recognition, and advanced face tracking capabilities. Built with Python, OpenCV, MediaPipe, and ArcFace.

---

## ✨ Features

### 🎯 Core Modules

- **📸 Face Enrollment** - Register new identities with multiple face samples
- **👁️ Live Recognition** - Real-time face matching against enrolled database
- **🔒 Face Locking** - Lock onto a specific person and track their actions:
  - Face movement (left/right)
  - Eye blinks
  - Smiles/laughs
  - Action history logging

### 🛠️ Technical Features

- **Robust Detection** - Hybrid Haar + MediaPipe face detection
- **High Accuracy** - ArcFace embeddings (512-dimensional)
- **Real-time Performance** - 8-12 FPS with full pipeline
- **Flexible Threshold** - Adjustable recognition sensitivity
- **Auto-capture Mode** - Hands-free enrollment

---

## 📋 Requirements

- **Python**: 3.8 or higher
- **Operating System**: Windows, macOS, or Linux
- **Camera**: Webcam or external camera
- **Storage**: ~200 MB for models and data

---

## 🚀 Quick Start

### Step 1: Installation

**Windows:**

```batch
setup.bat
```

**macOS/Linux:**

```bash
bash setup.sh
```

This will:

- Create a virtual environment
- Install all dependencies
- Set up project directories

### Step 2: Download Model

```bash
python download_model.py
```

This downloads the ArcFace model (~170 MB) required for face embeddings.

### Step 3: Verify Installation

Run these tests to ensure everything works:

```bash
# Test camera access
python -m src.camera

# Test face detection
python -m src.detect

# Test landmarks
python -m src.landmarks

# Test alignment
python -m src.align

# Test embeddings
python -m src.embed
```

✅ **Each test should display green indicators and run smoothly.**

---

## 📖 Usage Guide

### 1️⃣ Enroll New Faces

Register people in your system:

```bash
python -m src.enroll
```

**Interactive Steps:**

1. **Enter name** when prompted (e.g., "Alice", "Bob")
2. **Position face** in front of camera
3. **Capture samples**:
   - Press `SPACE` to manually capture one sample
   - Press `A` to toggle auto-capture mode
4. **Collect 15+ samples** for best accuracy
5. **Press `S`** to save enrollment
6. **Press `Q`** to quit

**Pro Tips:**

- Vary your facial expressions
- Capture from different angles
- Ensure good lighting
- Move slightly between captures

**Controls:**
| Key | Action |
|-----|--------|
| `SPACE` | Capture one sample |
| `A` | Toggle auto-capture mode |
| `S` | Save enrollment (requires 3+ samples) |
| `R` | Reset new samples (keep existing) |
| `Q` | Quit |

---

### 2️⃣ Live Recognition

Recognize enrolled faces in real-time:

```bash
python -m src.recognize
```

**What You'll See:**

- Green boxes around recognized faces with names
- Red boxes around unknown faces
- Confidence scores (lower distance = higher confidence)
- Real-time FPS counter

**Controls:**
| Key | Action |
|-----|--------|
| `Q` | Quit |
| `R` | Reload database (refresh enrolled identities) |
| `+` or `=` | Increase threshold (more accepts) |
| `-` | Decrease threshold (stricter matching) |

**Understanding the Distance:**

- **0.00 - 0.25**: Perfect match
- **0.25 - 0.35**: Good match (typical threshold)
- **0.35 - 0.50**: Possible match
- **> 0.50**: Likely different person

---

### 3️⃣ Face Locking & Action Tracking

Lock onto a specific person and track their actions:

```bash
python -m src.lock
```

**Interactive Steps:**

1. **View enrolled identities** (displayed as a numbered list)
2. **Enter the EXACT NAME** of the person to track

   ⚠️ **IMPORTANT**: You must enter the **name**, not the number!

   **Example:**

   ```
   Enrolled identities:
     1. hackim
     2. hackim2
     3. lanez
     4. lanez2

   Enter the name of the identity to lock (exact match): lanez
   ```

   ✅ **Correct**: `lanez`  
   ❌ **Wrong**: `3` (this will cause an error)

3. **System locks** when the person appears on camera
4. **Actions are tracked** and logged:

   - Face moved left
   - Face moved right
   - Eye blink
   - Smile or laugh

5. **History saved** to `data/history/` folder

**Tracked Actions:**

- **Movement**: Detects left/right face movement (>10 pixels)
- **Blinks**: Monitors eye aspect ratio (EAR < 0.2)
- **Smiles**: Tracks mouth width increase (>1.25x baseline)

**Lock Release:**

- System unlocks if face is not detected for 30 consecutive frames
- Press `Q` to manually quit

**History File Example:**

```
# Face Lock history: Alice
# Started: 2026-01-15 14:32:10
# Format: timestamp  action_type  description
# ---
1737824532.45  face_moved_left  face moved left
1737824534.12  eye_blink  eye blink
1737824536.78  smile  smile or laugh
1737824538.23  face_moved_right  face moved right
```

---

## 🔧 Configuration

Edit `src/config.py` to customize behavior:

### Camera Settings

```python
CAMERA_INDEX = 0  # Change to 1 for external camera
```

### Recognition Threshold

```python
DEFAULT_DISTANCE_THRESHOLD = 0.34
# Lower (0.25-0.30): Stricter, fewer false accepts
# Higher (0.35-0.45): More lenient, fewer false rejects
```

### Enrollment Settings

```python
SAMPLES_NEEDED_FOR_ENROLLMENT = 15     # Minimum samples
AUTO_CAPTURE_INTERVAL_SECONDS = 0.25   # Auto-capture delay
```

### Face Detection

```python
HAAR_MIN_SIZE = (80, 80)  # Minimum face size in pixels
```

### Action Detection (Face Lock)

```python
LOCK_MOVEMENT_THRESHOLD_PX = 10       # Movement sensitivity
LOCK_EAR_BLINK_THRESHOLD = 0.2        # Blink detection
LOCK_SMILE_MOUTH_RATIO = 1.25         # Smile detection
LOCK_ACTION_COOLDOWN_FRAMES = 15      # Prevent duplicate detections
LOCK_RELEASE_FRAMES = 30              # Frames before unlock
```

---

## 🗂️ Project Structure

```
face-recognition-5pt/
├── src/
│   ├── config.py          # All configuration settings
│   ├── camera.py          # Camera test utility
│   ├── detect.py          # Haar face detection
│   ├── landmarks.py       # MediaPipe 5-point landmarks
│   ├── align.py           # Face alignment (similarity transform)
│   ├── embed.py           # ArcFace embedding extraction
│   ├── haar_5pt.py        # Robust hybrid detector
│   ├── enroll.py          # Face enrollment module
│   ├── recognize.py       # Live recognition module
│   ├── lock.py            # Face locking & action tracking
│   └── evaluate.py        # Threshold optimization
├── data/
│   ├── db/
│   │   ├── face_db.npz    # Enrolled face embeddings (binary)
│   │   └── face_db.json   # Metadata (names, timestamps)
│   ├── enroll/            # Raw enrollment images
│   │   ├── Alice/
│   │   ├── Bob/
│   │   └── ...
│   └── history/           # Face lock action logs
│       ├── alice_history_20260115143210.txt
│       └── ...
├── models/
│   └── w600k_r50.onnx     # ArcFace model
├── requirements.txt       # Python dependencies
├── download_model.py      # Model download utility
├── setup.bat              # Windows setup script
├── setup.sh               # macOS/Linux setup script
└── README.md              # This file
```

---

## 🐛 Troubleshooting

### Common Issues

#### ❌ **"Camera cannot be opened"**

**Solutions:**

- Check camera permissions in system settings
- Try changing `CAMERA_INDEX` in `config.py` (0 or 1)
- Ensure no other app is using the camera
- Test with: `python -m src.camera`

#### ❌ **"No face detected"**

**Solutions:**

- Move closer to the camera
- Improve lighting (face the light)
- Ensure face is fully visible
- Lower `HAAR_MIN_SIZE` in `config.py`

#### ❌ **"Model not found"**

**Solution:**

```bash
python download_model.py
```

#### ❌ **"No enrolled identities"**

**Solution:**

```bash
python -m src.enroll
# Follow enrollment process
```

#### ❌ **Face Lock Error: "'3' not in database"**

**Problem:** You entered the number instead of the name.

**Solution:** Enter the **EXACT NAME** as shown in the list:

```
Enrolled identities:
  1. hackim
  2. hackim2
  3. lanez      ← Enter "lanez", not "3"
  4. lanez2

Enter the name of the identity to lock (exact match): lanez
```

#### ❌ **Too many false accepts (wrong people recognized)**

**Solutions:**

- Press `-` during recognition to lower threshold
- Edit `DEFAULT_DISTANCE_THRESHOLD` in `config.py` (try 0.28-0.32)
- Re-enroll with more samples (20+)

#### ❌ **Too many false rejects (correct people not recognized)**

**Solutions:**

- Press `+` during recognition to increase threshold
- Edit `DEFAULT_DISTANCE_THRESHOLD` in `config.py` (try 0.36-0.40)
- Enroll with varied expressions and angles

#### ❌ **Low FPS / Laggy performance**

**Solutions:**

- Close other applications
- Reduce camera resolution in `config.py`
- Use a computer with better CPU/GPU
- Expected FPS: 8-12 (normal for this pipeline)

#### ❌ **Actions not detected in Face Lock**

**Solutions:**

- **For blinks**: Blink more deliberately
- **For smiles**: Make exaggerated expressions
- **For movement**: Move head more noticeably (>10 pixels)
- Adjust thresholds in `config.py`:
  - `LOCK_EAR_BLINK_THRESHOLD` (default: 0.2)
  - `LOCK_SMILE_MOUTH_RATIO` (default: 1.25)
  - `LOCK_MOVEMENT_THRESHOLD_PX` (default: 10)

---

## 📊 Performance

### Expected Frame Rates

| Module        | FPS      | Bottleneck         |
| ------------- | -------- | ------------------ |
| Camera only   | 30       | None               |
| + Detection   | 20-25    | Haar cascade       |
| + Landmarks   | 20-25    | MediaPipe          |
| + Alignment   | 25-30    | Minimal            |
| + Embedding   | **8-12** | **ONNX inference** |
| Full pipeline | **8-10** | ArcFace model      |

### Optimization Tips

- ArcFace embedding is the slowest step (~80ms per face)
- Consider GPU acceleration for ONNX Runtime
- Skip embedding on every frame (process every 2-3 frames)
- Use smaller camera resolution

---

## 🔒 Privacy & Security

### Data Storage

- All face data stored **locally** in `data/` folder
- No cloud uploads or external connections
- Face embeddings are **not reversible** to original images

### Recommendations

- Do NOT share `data/db/face_db.npz` (contains biometric data)
- Keep `data/enroll/` folder private (original face images)
- Add `data/` to `.gitignore` if using version control
- Delete enrollment images after creating database (optional)

---

## 🧪 Technical Details

### Pipeline Architecture

```
Camera Frame (BGR Image)
    ↓
Haar Cascade Detection → Bounding Box
    ↓
MediaPipe FaceMesh → 5 Facial Landmarks
    ↓
Similarity Transform → Aligned 112×112 Face
    ↓
ArcFace ONNX Model → 512-dim Embedding (L2 normalized)
    ↓
Cosine Distance → Compare with Database
    ↓
Threshold Decision → Name or "Unknown"
```

### Face Embedding

- **Model**: ArcFace (w600k_r50)
- **Dimension**: 512
- **Distance Metric**: Cosine distance (1 - cosine similarity)
- **Normalization**: L2 normalized vectors

### Action Detection (Face Lock)

- **Movement**: Tracks face centroid horizontal displacement
- **Blink Detection**: Eye Aspect Ratio (EAR) from 6 landmarks per eye
- **Smile Detection**: Mouth width ratio compared to baseline

---

## 📚 FAQ

**Q: How many samples do I need to enroll?**  
A: Minimum 3, recommended 15+. More samples = better accuracy.

**Q: Can I recognize multiple people at once?**  
A: Yes! The system detects all faces in frame simultaneously.

**Q: How do I delete an enrolled person?**  
A: Manually edit `data/db/face_db.npz` or delete and re-enroll everyone. (A management tool may be added in future versions)

**Q: What's the difference between `recognize.py` and `lock.py`?**  
A:

- `recognize.py`: Recognizes ALL faces in frame continuously
- `lock.py`: Locks onto ONE specific person and tracks their actions

**Q: Can I use this for security/authentication?**  
A: This is an educational project. For production security:

- Add liveness detection (prevent photo spoofing)
- Implement secure storage encryption
- Use multiple factors (face + password/PIN)

**Q: Does this work with masks or glasses?**  
A: Partial occlusions reduce accuracy. The system works best with clear, unobstructed faces.

**Q: How do I change the camera?**  
A: Edit `CAMERA_INDEX` in `src/config.py`:

- `0`: Built-in webcam
- `1`: First external camera
- `2+`: Additional cameras

---

## 📝 Version History

**v1.0.0** - January 2026

- Initial release
- Core face recognition pipeline
- Enrollment, recognition, and locking modules
- Action tracking (movement, blinks, smiles)
- Hybrid Haar + MediaPipe detection

---

## 🙏 Acknowledgments

- **InsightFace**: ArcFace model and embeddings
- **MediaPipe**: Face detection and landmark extraction
- **OpenCV**: Computer vision framework
- **ONNX Runtime**: Model inference

---

## 📧 Support

For issues or questions:

1. Check the **Troubleshooting** section above
2. Review `QUICKSTART.md` for quick reference
3. Inspect `config.py` for adjustable parameters

---

## ⚖️ License

Educational use only. Not intended for commercial deployment without proper security hardening.

---

**Built with ❤️ for Computer Vision Education**

---

## 🎯 Next Steps

1. ✅ Complete installation and model download
2. ✅ Test each module with validation commands
3. ✅ Enroll yourself and a friend
4. ✅ Try live recognition with threshold adjustments
5. ✅ Experiment with face locking and action tracking
6. 🔧 Customize settings in `config.py`
7. 📊 Optimize performance for your hardware

**Happy Face Recognizing! 👤🔍**
#   F a c e L o c k i n g  
 