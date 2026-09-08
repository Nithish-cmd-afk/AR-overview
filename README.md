# AR - Augmented Reality (Universal WebAR 3D System)

A production-quality, browser-based **WebAR application** that allows users to upload/select 3D `.glb` models, preview and configure spatial parameters, generate compact dual-purpose AR QR markers, scan with any mobile smartphone camera, and view 3D models **spatially anchored directly above physical QR codes** in full 6 Degrees of Freedom (6DOF).

> **Universal AR Scanning**: Open the AR viewer **once** (`ar.html`), point your camera at any QR code marker, and the 3D model automatically loads and switches on the fly without page reloads or closing tabs.

> **Strict Web Standards**: Built with HTML5, CSS3, JavaScript (ES6+), Three.js, WebGL, and WebRTC. **Zero Unity. Zero Native App Installs.**

---

## 🚀 Key Features

- **Universal Multi-Model Scanning**: Point your phone camera at different QR codes in real-time — the 3D viewer instantly identifies the marker and swaps models on the fly with zero-latency memory caching.
- **Dual-Purpose Compact QR Code**: Encodes short, clean URLs (`ar.html?m=car`, etc.) and simultaneously serves as the physical computer-vision tracking marker.
- **Physical 6DOF Pose Estimation**: Calculates real-time 3D camera-relative position $(X, Y, Z)$ and orientation $(\text{Roll}, \text{Pitch}, \text{Yaw})$ so you can walk around the physical marker in 360° to view the model from front, sides, top, and back.
- **One-Euro (1€) Jitter Filter**: Eliminates hand jitter while preserving zero-latency response during rapid camera movement.
- **Three.js PBR Pipeline**: ACES Filmic tone mapping, ambient hemisphere & directional key/fill lights, and shadow maps to ensure models render with vibrant fidelity.
- **Built-in 3D Assets**: Pre-loaded with models (Tactical Helicopter, Quadcopter Drone, Cyber Sentinel Robot, Cybercar, Taj Mahal, Eiffel Tower, Spider, Spiderman, Earth, Tower House).
- **Studio Configurator & Multi-Model Gallery**: Real-time 3D OrbitControls preview, scale/height/offset tuning, 0–50 MB `.glb` upload, and a Multi-Model QR Gallery for side-by-side scanning.
- **Dark Glassmorphism HUD**: Responsive status indicators (`SEARCHING`, `QR DETECTED`, `TRACKING`, `QR LOST`), dynamic model switch toasts, scale stepper, auto-rotation toggle, and fullscreen mode.
- **Zero-Build Deployment**: Works out of the box on GitHub Pages or any static HTTPS host.

---

## 📂 Project Structure

```text
ar-augmentedreality/
├── index.html                 # Modern landing page showcasing features & demo launchers
├── generator.html             # AR experience studio & Multi-Model QR Gallery
├── ar.html                    # Universal fullscreen mobile-optimized AR camera tracking & 3D view
│
├── css/
│   ├── style.css              # Global dark futuristic theme & glassmorphic layout
│   ├── generator.css          # Creator studio styling & Multi-Model Gallery grid
│   └── ar.css                 # Clean AR HUD overlay, tracking status pills & toast alerts
│
├── js/
│   ├── config.js              # AR config schema, model catalogue & default parameters
│   ├── tracker.js             # 6DOF Coplanar PnP / Homography pose solver & 1€ filter
│   ├── model-loader.js        # Three.js GLTFLoader manager with PBR lighting & animations
│   ├── generator.js           # 3D preview viewport, GLB upload handling, QR generator & gallery
│   ├── ar.js                  # Universal AR camera loop, dynamic model switcher & spatial anchor
│   ├── procedural-models.js   # Procedural GLB binary models (helicopter, drone, robot, car)
│   ├── glb-builder.js         # Pure JS glTF 2.0 binary builder
│   ├── libs/jsQR.js           # High-precision QR computer vision detector
│   └── main.js                # Landing page interactivity & quick launch modal
│
├── package.json               # Node & Vite configuration
└── README.md                  # Complete documentation
```

---

## 🌐 Live Production Deployment (GitHub Pages)

Your WebAR application is hosted live at:
```text
https://nithish-cmd-afk.github.io/ar-augmentedreality/
```

- **Landing Page**: `https://nithish-cmd-afk.github.io/ar-augmentedreality/`
- **Universal AR Scanner**: `https://nithish-cmd-afk.github.io/ar-augmentedreality/ar.html`
- **AR Studio & Multi-Model Gallery**: `https://nithish-cmd-afk.github.io/ar-augmentedreality/generator.html`

---

## 📦 How to Add Custom 3D Models

### Step 1: Add your `.glb` file
Place your binary GLB file in the `models/` folder:
```text
models/my-spacecraft.glb
```

### Step 2: Register in `js/config.js`
Open `js/config.js` and add an entry under `AR_CONFIG.models`:
```javascript
spacecraft: {
  name: "Interstellar Spacecraft",
  category: "Sci-Fi",
  file: "models/my-spacecraft.glb",
  scale: 0.8,
  height: 0.20,
  offsetX: 0.0,
  offsetZ: 0.0,
  rotationY: 0,
  autoRotate: false,
  description: "Deep space explorer vessel."
}
```

### Step 3: Use in Generator / AR
- In the generator, select your model or navigate directly to `ar.html?id=spacecraft`.

---

## 🖨️ Physical QR Marker Printing & Setup Guide

For optimal 6DOF tracking performance:

1. Open `generator.html`, select your 3D model, and click **Download PNG**.
2. **Print Size**:
   - **Recommended**: $10\text{ cm} \times 10\text{ cm}$ (or $15\text{ cm} \times 15\text{ cm}$).
   - Larger printed markers provide higher tracking distance and stability.
3. **Physical Surface**:
   - Place the printed QR code on a flat table or floor.
   - Avoid glossy surfaces with strong glare or reflections.
   - Ensure even ambient lighting.

---

## 📱 How to Experience AR on Mobile

1. Open your phone's native Camera app or QR scanner.
2. Scan the printed QR code → Tap the URL prompt to open the webpage.
3. Tap **Allow Camera** when prompted by Safari / Chrome.
4. Point the camera at the physical QR code on your table.
5. The 3D model appears anchored directly on top of the QR code!
6. Move around the table to view the model from **front, left, back, and top**.

---

## 🔬 How the 6DOF QR Marker Tracking Works

```text
[ Physical QR Code ] ──> [ Mobile Camera (getUserMedia) ]
                                 │
                         (30-60 FPS Frame)
                                 │
                         [ jsQR Corner Finder ]
                         Top-Left, Top-Right,
                         Bottom-Right, Bottom-Left
                                 │
                   [ Perspective Camera Matrix K ]
                     fy = (H/2) / tan(FOV_y/2)
                     fx = fy,  cx = W/2, cy = H/2
                                 │
                 [ Coplanar Homography Decomposition ]
                   H = K · [ r1  r2  t ]
                   λ = 2 / (||K⁻¹ h1|| + ||K⁻¹ h2||)
                   r1 = λ K⁻¹ h1,  r2 = λ K⁻¹ h2
                   r3 = r1 × r2,   t = λ K⁻¹ h3
                                 │
                 [ SVD / Polar Orthonormalization ]
                   Guarantees pure SO(3) Rotation
                                 │
                 [ One-Euro (1€) Adaptive Filter ]
                   Stationary: Heavy low-pass cutoff (no jitter)
                   Moving: High dynamic cutoff (zero lag)
                                 │
               [ Three.js markerGroup Matrix Transform ]
               Position: (X, Y, Z)  Rotation: Quaternion
                                 │
                  [ 3D GLB Model Spatially Anchored ]
```

---

## 💡 Troubleshooting & FAQ

| Issue | Cause | Solution |
| :--- | :--- | :--- |
| **Camera won't open / Black screen** | Non-HTTPS connection or permissions blocked | Ensure site is running via `https://` or `localhost`. Check browser site settings to allow camera. |
| **Model appears upside down / tilted** | Marker plane orientation mismatch | The tracker includes automatic normal re-orientation (`-Math.PI / 2` around X) so standing models point upwards towards the ceiling. |
| **Jitter when holding phone still** | Optical sensor noise | The built-in One-Euro filter automatically suppresses high-frequency corner noise. |
| **Model looks dark/black** | Missing PBR lighting or tone mapping | The built-in `ModelLoader` configures ACES Filmic tone mapping and a 3-point hemisphere + directional light rig. |

---

## 📜 License

MIT License © 2026 Custom WebAR Project.
