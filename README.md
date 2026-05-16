# 🧠 Brain Tumor Segmentation

A classical image processing pipeline that automatically locates and outlines brain tumors in MRI scans — no deep learning required. The project includes a full segmentation pipeline, a contour-based tumor detection approach, and a desktop GUI application that any non-technical user can operate.

---

## 📌 Project Overview

This project focuses on **image segmentation** — not classifying what type of tumor it is, but precisely identifying **where** the tumor is located within an MRI scan. It was built entirely using classical computer vision techniques, demonstrating a strong understanding of image processing fundamentals.

The system processes raw MRI images through a multi-step pipeline:
- Removes the skull to isolate brain tissue
- Highlights tumor regions using morphological transforms
- Delineates precise tumor boundaries using Watershed segmentation
- Displays results through an interactive desktop GUI

---

## 🗂️ Project Structure

```
Brain-Tumor-Segmentation/
│
├── segmentation_pipeline.ipynb     # Full skull stripping + watershed pipeline
├── contour_detection.ipynb         # Simplified tumor detection with contour drawing
├── gui_app.py                      # Tkinter desktop GUI application
└── README.md
```

---

## ⚙️ How It Works

### Part 1 — Full Segmentation Pipeline

The pipeline processes each MRI image through the following stages:

#### 1. Skull Stripping
The skull is bright and prominent in MRI scans. Without removing it first, any segmentation algorithm would confuse the skull bone for tumor tissue. Skull stripping is done in three steps:

- **Otsu Thresholding** — automatically calculates the optimal brightness threshold to separate brain tissue from background, without needing a hardcoded value
- **Morphological Closing** — fills small internal holes in the binary mask to create solid regions
- **Erosion** — shrinks all white regions inward from their edges using a large disk (radius 14). Since the skull is a thin outer ring, erosion removes it entirely while the larger brain tissue region survives

#### 2. White Top-Hat Transform
Applied to the skull-stripped image to enhance tumor visibility. The top-hat transform extracts small bright regions that stand out from their surroundings — exactly the appearance of tumors against uniform brain tissue. This makes the tumor region brighter while suppressing the rest.

#### 3. Binarization
A second round of Otsu thresholding is applied to the top-hat output, producing a clean binary image where white pixels represent potential tumor regions.

#### 4. Watershed Segmentation
The most precise stage of the pipeline:
- **Distance Transform** — calculates how far each white pixel is from the nearest background pixel. Pixels at the center of regions receive the highest values
- **Peak Local Max** — finds the peaks of the distance map, which become the seed points for segmentation
- **Watershed** — floods outward from each seed, drawing precise boundaries wherever two regions meet

#### 5. Morphological Cleanup
- **Closing** — fills small holes within the segmented regions (smooths interior)
- **Opening** — removes small noise and rough protrusions from region edges (smooths exterior)

---

### Part 2 — Contour-Based Tumor Detection

A faster, simpler alternative approach:

- Thresholds the image at 70% of maximum brightness
- Labels all connected white regions using connected component analysis
- Filters regions by **Solidity** (> 0.5) — tumors are compact and dense
- Selects the largest high-solidity region as the tumor candidate
- Dilates the region slightly and draws its **contour in yellow** on the original MRI

This approach gives quick visual results and is useful as a lightweight detection step before full segmentation.

---

### Part 3 — Desktop GUI Application

Built with **Tkinter**, Python's built-in GUI framework. The application is designed for non-technical users such as radiologists:

- Click **"Upload Image"** to select any MRI scan from your file system
- The image is automatically processed through the pipeline
- Results are displayed side by side — original MRI on the left, tumor contour highlighted in **red** on the right
- Click **"Clear"** to reset and process a new image

No code interaction required.

---

## 🛠️ Technologies Used

| Tool | Purpose |
|---|---|
| Python | Core programming language |
| OpenCV (cv2) | Image reading, grayscale conversion, contour drawing |
| Scikit-image (skimage) | Full image processing pipeline — thresholding, morphology, watershed, region analysis |
| SciPy (ndimage) | Distance transform for watershed preparation |
| NumPy | Array operations and pixel manipulation |
| Matplotlib | Visualizing intermediate and final results |
| Tkinter | Desktop GUI application |
| Pillow (PIL) | Image format conversion for Tkinter display |

---

## 📦 Installation

```bash
# Clone the repository
git clone https://github.com/yourusername/brain-tumor-segmentation.git
cd brain-tumor-segmentation

# Install dependencies
pip install opencv-python scikit-image scipy numpy matplotlib Pillow
```

---

## 🚀 Usage

### Running the Notebook Pipeline
Open `segmentation_pipeline.ipynb` or `contour_detection.ipynb` in Jupyter Notebook or Google Colab and update the image directory path:

```python
folder_path = "path/to/your/mri/images"
```

Then run all cells.

### Running the GUI Application
```bash
python gui_app.py
```

Click **"Upload Image"**, select an MRI scan, and view the segmentation result instantly.

---

## 🔬 Pipeline Visualization

Each image is processed and visualized across 16 stages:

| Stage | Description |
|---|---|
| Original Image | Raw MRI input |
| Thresholded Image | Binary mask from Otsu thresholding |
| Filled Image | After morphological closing |
| Eroded Image | After skull removal via erosion |
| Skull Stripped | Brain tissue isolated |
| Top-Hat Transform | Tumor region enhanced |
| Binarized Image | Binary tumor candidate mask |
| Distance Map | Distance transform visualization |
| Labeled Markers | Watershed seed points |
| Segmented Image | Watershed output |
| Closed Image | Post-watershed closing |
| Opened Image | Final cleaned segmentation |

---

## 🧠 Key Concepts

**Image Segmentation** — identifying and delineating specific regions within an image at the pixel level, as opposed to classification which only assigns a label to the whole image.

**Skull Stripping** — preprocessing step in neuroimaging to remove non-brain tissue from MRI scans before analysis.

**Otsu Thresholding** — an automatic thresholding method that finds the optimal threshold by minimizing intra-class variance of pixel intensities.

**Morphological Operations** — mathematical operations on binary images:
- *Erosion* — shrinks white regions from their boundaries
- *Dilation* — expands white regions outward
- *Closing* — dilation followed by erosion (fills holes)
- *Opening* — erosion followed by dilation (removes noise)

**White Top-Hat Transform** — extracts small bright features from an image by subtracting the result of a morphological opening from the original image.

**Watershed Segmentation** — treats the image as a topographic surface and simulates flooding from seed points to find region boundaries.

---
**[Your Name]**  
Computer Science / Biomedical Imaging  
[LinkedIn] • [GitHub]
