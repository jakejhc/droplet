#Microfluidic Pipe and Droplet Analyzer with OpenCV

##Place The Pipe horizontally

This is a complete, single-file HTML application built with OpenCV.js for real-time computer vision processing directly in the browser. You can save this file as index.html and deploy it straight to GitHub Pages.  It implements the precise horizontal line detection logic using OpenCV's Canny edge detector and Hough Line Transform, automatically calculates scale from the $2\text{ mm}$ pipe inner diameter, fits ellipses to detected droplets inside the pipe, overlays real-time HUD annotations, displays a live measurements table, and lets you export the data directly to CSV.

Features Built Into This ScriptClient-Side Computer Vision: Powered by OpenCV.js via CDN, requiring no backend servers or Python installations.  Automatic Parallel Line Calibration: Uses cv.Canny and cv.HoughLinesP to filter horizontal lines, selects the two longest parallel lines, and auto-scales the live pixel distance to $2.0\text{ mm}$.Spatial Droplet Filtering: Restricts droplet detection exclusively to contours located inside the pipe bounds.Morphological Analysis: Fits an ellipse (cv.fitEllipse) to calculate Major Axis, Minor Axis, Surface Area ($\text{mm}^2$), Circularity, and Aspect Ratio in real time.Live Export: Exports snapshot measurements to a structured .csv file.

Please view demo at: [jakejhc.github.io/droplet](https://jakejhc.github.io/droplet/)

##Prompt for Gemini

write a single html for me to deploy on github: use webcam to analyze close up drawing/images consisting a pipe (inner diameter assumed to be 2mm) and a few droplet shapes. overlay realtime identified contour and length/width measurements of the droplets (assume pipe inner  diameter is 2mm) to the webcam video. 

Instead of looking for the pipe contour, detect two nearly parallel lines using:

Canny edge detector & Hough Line Transform

Then:
Find the two longest parallel lines: pipe is always horizontal, and droplets always inside pipe.

Measure the distance between them.

Declare that distance = 2 mm.

Compute pixels/mm.

Measure every droplet.

## Advantages of this approach

Robust.

Works even if only part of the pipe is visible.
Less sensitive to lighting.
Works well with hand drawings and images.
the goal is analyzing microfluidic sketches and images.
 
✅ Automatic pipe edge detection (Hough transform)

✅ Automatic calibration from the 2 mm pipe ID

✅ Automatic droplet detection

✅ Ellipse fitting for droplets (instead of bounding boxes)

✅ Major axis

✅ Minor axis

✅ Area

✅ Perimeter

✅ Circularity

✅ Aspect ratio

✅ Save measurements to CSV

✅ Live measurement table beside the webcam
This version would be significantly more accurate and better suited for analyzing hand-drawn or printed microfluidic diagrams.

##Yes, the Hough line and contour analysis algorithm designed for hand-drawn sketches **can work on real-world microfluidic images**, but its reliability will depend heavily on lighting, physical optical distortions, and surface reflections.

While hand-drawn sketches feature high-contrast, clean black lines on white paper, real microfluidic channels and physical droplets present distinct computer vision challenges:

---

### Real-World Challenges & How to Adapt

#### 1. Channel Edge Detection (Canny & Hough Transform)

* 
**The Reality:** In real physical devices (such as PDMS, glass, or acrylic chips), channel edges are translucent or reflective. Instead of two clean edges, Canny edge detection might pick up refraction lines, bubbles, or dust.


* **The Fix:** * Ensure **backlighting** (a diffused LED light panel behind the microfluidic channel) to create crisp, high-contrast borders.
* Adjust the Canny low and high threshold parameters dynamically using a trackbar instead of fixed values (`50, 150`).



#### 2. Droplet Fitting & Ellipse Approximation

* **The Reality:** Real fluidic droplets are transparent, meaning the center of the droplet is light while only the interface/meniscus creates a shadow ring. Otsu thresholding  might segment only the dark ring rather than a solid blob.


* **The Fix:**
* Apply morphological operations like **Closing** (dilation followed by erosion using `cv.morphologyEx`) to fill in the dark outer ring into a solid contour before running `cv.fitEllipse`.


* If droplets move rapidly, consider applying background subtraction (`cv.createBackgroundSubtractorMOG2()`) so only moving fluid bodies are isolated from static background noise.



---

### Recommended OpenCV Processing Pipeline for Physical Chips

| Microfluidic Stage | Sketch Logic (Current) | Physical Image Adaptation |
| --- | --- | --- |
| **Calibration** | Simple line detection (`cv.HoughLinesP`) 

 | ROI cropping + horizontal edge gradient filtering to ignore reflections |
| **Pre-processing** | Direct Gaussian Blur + Canny 

 | Adaptive Thresholding or Background Subtraction to isolate fluids |
| **Segmentation** | Otsu Binary Thresholding 

 | Morphological Closing to seal droplet borders |
| **Morphology** | Ellipse Fitting (`cv.fitEllipse`) 

 | Unchanged (ellipse fitting handles rotated, squished plugs effectively) 

 |

---

### How to Upgrade the Code?

If you plan to test this live with physical microfluidic devices (such as drops dispensed from an **Open Foundry** setup), the current single-file HTML script can be updated with:

1. **Interactive Threshold Sliders:** Tune Canny and Otsu sensitivity directly in the UI.
2. **Region-of-Interest (ROI) Selection:** Manually crop the channel area to filter out external equipment or tubing.
