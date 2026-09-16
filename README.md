# Unblur Text Image

GitHub Action that takes a **direct image URL**, enhances black text on a white background (works great for Dutch / any language document-style text), and uploads the cleaned result as an artifact.

## How to use

1. Go to the **Actions** tab of this repo.
2. Select **Unblur Text Image**.
3. Click **Run workflow**.
4. Paste a **direct** link to the image (must end in `.png`, `.jpg`, `.jpeg`, etc. or be a raw image URL).
5. After the run finishes (~20–40 seconds), download the artifact named **unblurred-image**.

## What it does

- Downloads the image from the URL you provide
- Converts to grayscale
- Mild denoising
- CLAHE contrast boost
- Unsharp-mask sharpening
- Otsu binarization → clean black text on pure white

Perfect for slightly blurred screenshots, scans, or photos of printed Dutch text.

## Local PowerShell equivalent

```powershell
python -m pip install opencv-python pillow numpy

python -c "
import cv2, numpy as np, sys
img = cv2.imread(sys.argv[1], cv2.IMREAD_GRAYSCALE)
den = cv2.fastNlMeansDenoising(img, h=8)
clahe = cv2.createCLAHE(clipLimit=2.5, tileGridSize=(8,8))
enh = clahe.apply(den)
blur = cv2.GaussianBlur(enh, (0,0), 1.5)
sharp = cv2.addWeighted(enh, 1.8, blur, -0.8, 0)
_, bin_img = cv2.threshold(sharp, 0, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU)
cv2.imwrite(sys.argv[2], bin_img)
print('Saved', sys.argv[2])
" "blurry.png" "unblurred.png"
```
