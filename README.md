# webmcu-vision-web
WebSerial Connection to a on-device XIAO ML Kit Vision Model 



Active WebSerial Page at  [https://webmcu-ai.github.io/webmcu-vision-web/index.html](https://webmcu-ai.github.io/webmcu-vision-web/index.html)

Needs Seeedstudio XIAO ML Kit connected through webSerial

Proof of concept for on-device training pagper github at  <a href="https://github.com/webmcu-ai/on-device-vision-ai">https://github.com/webmcu-ai/on-device-vision-ai</a>





webmcu-vision-web
Browser-based training, management, and deployment companion for the XIAO ESP32-S3 Sense.
Single HTML file. No installation. Open in Chrome or Edge, plug in your ESP32, and train a visual classifier in under 90 minutes on $15–40 USD hardware. All data stays on your local machine.
Part of the webmcu-ai series — a set of open templates for TinyML on microcontrollers, designed to be adapted by LLM-assisted workflows for your specific hardware, sensors, and problem.
---
Quick Start
Download `index.html` from this repository (or clone it).
Open `index.html` in Chrome or Edge (not Firefox, not Safari — WebSerial is required).
Plug your XIAO ESP32-S3 Sense into USB.
Click Flash Device (Section 1) to flash the firmware — no Arduino IDE needed.
Click Connect ESP32 (Section 2) to open the serial connection.
Collect images, train, export weights. Done.
> **No Python. No pip. No npm. No build step.** The only requirement is Chrome or Edge.
---
The TinyML Dataset Principle
Before diving into the workflow, one concept is worth internalising:
TinyML works best when your dataset matches your deployment exactly.
This is different from general computer vision. You are not trying to build a model that works everywhere. You are building a model that works in your environment, with your objects, under your lighting. That means:
Capture images where you will deploy the device. Not in a different room, not under different light, not at a different distance.
15–40 images per class is often enough if they are clean and representative. 200 images collected in the wrong conditions will perform worse than 30 collected correctly.
Background matters as much as the object. If every image of Class 1 has a blue table in the background, the model may be classifying the table, not the object. Vary positions or use a consistent neutral background.
Use the confusion matrix and the heatmap to diagnose problems. If the heatmap shows activation on the background, collect more varied images. Do not collect more images of the same thing.
The capture interface in Section 3 is designed around this workflow. The camera is live. Position your object. Check the preview. Capture. Repeat until your class counts are balanced.
---
Section-by-Section Workflow
Section 1 — Flash Firmware
Flash pre-compiled ESP32 firmware directly from the browser. No Python or Arduino IDE needed.
Option A — Sketch only (recommended for re-flashing):
Flashes `flash.ino.bin` to address `0x10000`
Preserves bootloader and NVS (saved settings)
Faster; use this for all normal re-flashing
Option B — Full merged binary (for fresh chips or partition changes):
Flashes `flash.ino.merged.bin` to address `0x0`
Writes bootloader + partition table + application in one pass
Use this when first setting up a new device, or if Option A fails
To use your own compiled binary: click Browse next to either option and select your `.bin` file.
> ⚠ **Refresh the page before flashing.** This clears the previous WebSerial state. After flashing, unplug and replug the ESP32.
Finding your compiled binary in Arduino IDE 2.x:
Enable `File → Preferences → Show verbose output during: compilation`
The build folder path is printed at the end of the compilation log
`Sketch → Export Compiled Binary` also works
---
Section 2 — WebSerial Monitor & SD Card Browser
Connecting
Click Connect ESP32. Select the USB serial port from the browser dialog. Firmware boot messages will appear in the serial monitor. If nothing appears, check that `USB CDC On Boot: Enabled` is set in Arduino IDE board settings.
Serial Monitor
Displays all firmware output in real time
Quick-send buttons: `t` = next menu item, `l` = select/toggle, `1`–`5` = direct menu actions
Free-text command field for any serial command
Copy Log and Clear Log buttons
SD Card Browser
Browse, preview, edit, and delete files on the ESP32 SD card without ejecting it.
Navigate: click folder names; use breadcrumb links; ↑ Up button
Preview images: click any `.jpg` file to view it inline
Edit text files: click any `.json`, `.h`, `.txt`, or `.csv` to open it in an editable textarea. Click Save changes to SD to write it back.
Download: any file can be downloaded to your PC
Delete: select a file, click 🗑 Delete Selected
Syncing config.json
Click ⟳ Refresh config.json from SD to read `/header/config.json` from the SD card and populate all browser training fields: class names, learning rate, batch size, epochs, augmentation, grayscale, and validation image count.
This means you can edit `config.json` in the SD browser (or download it, edit it, re-upload it), save it back to the SD card, and the firmware will pick up the new settings at next boot — no recompile.
The `config.json` schema:
```json
{
  "version": 68,
  "inputSize": 64,
  "numClasses": 3,
  "classLabels": ["0Blank", "1Cup", "2Pen"],
  "learningRate": 0.0003,
  "batchSize": 6,
  "targetEpochs": 20,
  "useAugmentation": true,
  "useGrayscale": false,
  "imagesToPsram": true,
  "validationImages": 3,
  "weightsFile": "myWeights.bin"
}
```
`classLabels` — change these to your own class names. They drive both the ESP32 OLED menu and the browser capture rows. Names beginning with a digit sort correctly on the SD card (e.g., `"0Background"`, `"1Widget"`, `"2Fault"`).
---
Section 3 — Camera & Data Collection
Before you start
Set these before clicking Start Camera & Brain — they cannot be changed once the model is built:
Grayscale mode: 3× smaller model, needs more data. Good for high-contrast problems (QR codes, on/off states). Leave unchecked for colour-dependent problems.
Input Resolution: 64×64 is the default and recommended starting point. Higher resolutions give more spatial detail but slow inference on the ESP32. The live hint shows the downstream effect on model dimensions.
Camera source
Webcam: select from the dropdown. Any browser-accessible camera works.
ESP32 camera: select "ESP32 via Serial" from the dropdown (appears when connected). Use 📷 Single Frame for one shot, or ▶ Start Stream for continuous capture at a set interval.
Capturing images
For each class:
Name the class in the text field (or sync from `config.json`)
Position your object under your deployment lighting
Click 📷 Capture to add a frame to the buffer
Repeat until you have 15–40 samples per class
Balance your classes. Training works best when all classes have similar sample counts. The sample counters update live.
Project folder (recommended)
Click 📂 Choose Project Folder to set a folder on your PC. Every Capture will automatically save a JPEG there, mirroring the SD card directory structure:
```
project_folder/
  images/
    0Blank/     ← img_0001.jpg, img_0002.jpg, ...
    1Cup/
    2Pen/
  header/       ← myWeights.bin, myWeights.h, config.json saved here
```
Once a project folder is set:
📂 Load images from folder reloads all images from disk into the training buffer (useful after a page refresh)
📂 Load model from folder reloads `header/myWeights.bin` into the live TFJS model (requires model to be built first)
RAM window
The RAM window per class setting controls how many images are held in browser memory for training. The disk folder holds all images regardless. If you hit browser memory limits (usually at 500+ images per class at high resolution), reduce this number.
---
Section 4 — Training
Click Start Camera & Brain to build the model and start the training loop.
The model is built once, locked at the resolution and channel mode you selected. Training and live inference run simultaneously on a 150ms tick.
Training settings
Setting	Default	Notes
Batch size	6	Keep as a multiple of number of classes
Learning rate	0.0003	Reduce to 0.0001 for fine-tuning
Dropout	0.3	Reduces overfitting on small datasets
Target epochs	20	20–40 is usually sufficient for 3 classes
Min samples	10	Training won't start until each class has this many
Reading the activity log
The log prints a line every 10 batches:
```
Batch 120 | Loss 0.2341 ▓▓▓░░░░░ | Converging
```
And a validation line at each epoch boundary:
```
▶ Val @ Epoch 3 | Overall 83.3% | 0Blank: 3/3 (100%) | 1Cup: 2/3 (67%) | 2Pen: 3/3 (100%)
```
The validation uses the last N images per class as a held-out set (controlled by `validationImages` in config.json). If a class is consistently underperforming in validation, collect more images for that class, varying position and background.
Confusion matrix
Click Update Confusion Matrix at any time to see per-class accuracy. Green diagonal = correct; red off-diagonal = confusion between classes. A class that is consistently confused with another class needs more clearly distinct training images.
Epoch modes
Use All Data (default): systematic epoch ordering, all images used each epoch. Better for small datasets.
Random batch mode: uniform random sampling. Useful for exploratory training on large buffers.
Auto-save
Check Auto-save weights every 10 batches to continuously save `myWeights.bin` and `myWeights.h` to your project folder during training. Leave unchecked if you want to protect a well-trained model from being overwritten during continued training.
---
Section 5 — Export Weights & Config
myWeights.bin (recommended for device deployment)
Click Export myWeights.bin to save trained weights. If a project folder is set, saves to `project_folder/header/myWeights.bin`. Otherwise saves to PC Downloads.
Copy this file to `/header/myWeights.bin` on the SD card. The firmware loads it automatically at next boot.
The browser can also write directly to the SD card via WebSerial: click the SD export button (requires ESP32 connected). This takes ~25 seconds for an 83 KB file.
myWeights.h (for baked-in firmware weights)
Generates a C header file containing all weights as `float` arrays. Copy to your Arduino sketch folder and uncomment `#define USE_BAKED_WEIGHTS` in the firmware to compile the weights directly into the binary (no SD card required at runtime).
config.json
The config textarea is kept live in sync with all training fields. Edit it directly, or use the fields above and let it update automatically. Click Save config.json to Downloads or write directly to the SD card.
Loading weights back into the browser
Load TFJS Model — load a previously saved TensorFlow.js model (JSON + binary files from Save TFJS Model).
Load myWeights.bin from SD — read `myWeights.bin` from the ESP32 SD card over WebSerial into the live TFJS model.
📂 Load model from folder — read `myWeights.bin` from your PC project folder. Requires the model to be built first (click Start Camera & Brain).
---
Heatmap
The live Conv2 activation heatmap shows which parts of the image the model is paying attention to.
Live Heatmap (ESP32): streams activation maps from the running ESP32. Requires the ESP32 to be connected and in inference mode. Pauses browser inference while active.
Browser Heatmap: extracts Conv2 activations from the TFJS model in the browser. Works with webcam or ESP32 stream. No impact on ESP32 FPS.
How to use it diagnostically:
Hold your target object in front of the camera
Red/yellow = high activation. Blue = low activation.
Good: activation cluster follows the object as you move it
Bad: activation is on the background, not the object — you need more background-varied training images
---
Offline / Restricted Network Use
The three JavaScript libraries (TensorFlow.js, JSZip, esptool-js) are loaded from CDN. For offline use:
Download the three files:
`https://cdn.jsdelivr.net/npm/@tensorflow/tfjs@4.22.0/dist/tf.min.js`
`https://cdnjs.cloudflare.com/ajax/libs/jszip/3.10.1/jszip.min.js`
`https://unpkg.com/esptool-js@0.5.7/dist/bundle.js`
Place them in the same folder as `index.html`
Update the three `<script src="...">` lines at the top of `index.html` to local paths
This is the only change required for fully offline operation.
---
Adapting with an LLM
This repository is designed for LLM-assisted modification. The single-file structure and consistent naming make it straightforward to describe a specific change to Claude, ChatGPT, Gemini, or any similar tool.
Examples of things you can ask:
"Change the number of classes from 3 to 5 and update all relevant parts of the file"
"Replace the webcam capture with a different camera API"
"Add a fourth input modality alongside the camera"
"Change Conv1 to 8 filters and Conv2 to 16 filters and update the ESP32 firmware defines to match"
"Add a section that logs validation accuracy to a CSV file after each epoch"
Paste the relevant section of `index.html` into the LLM context along with your request. The function names and section numbers in this README correspond directly to the code.
When adapting the weight transposition logic (for filter count changes), refer to Section 5 of the companion paper for the exact ESP32 ↔ TensorFlow.js layout mapping.
---
Hardware
Item	Cost	Notes
Seeed Studio XIAO ESP32-S3 Sense	~$15–20 USD	With camera and microphone
XIAO ML Kit expansion board	~$22 USD	Adds MicroSD, OLED, IMU, buttons
MicroSD card (any size)	~$5 USD	FAT32 formatted
USB-C cable	—	Data cable, not charge-only
The XIAO ESP32-S3 Sense includes an OV2640 camera and 8 MB PSRAM. The ML Kit expansion board adds the SD card slot, OLED display, and IMU used by the firmware.
---
Repository Structure
```
webmcu-vision-web/
  index.html            ← The entire application (single file)
  flash.ino.bin         ← Pre-compiled sketch binary (Option A flash)
  flash.ino.merged.bin  ← Pre-compiled merged binary (Option B flash)
  README.md             ← This file
  paper/
    paper2-webmcu-vision-web.pdf   ← Companion arXiv paper
    paper2-webmcu-vision-web.tex   ← LaTeX source
    *.png / *.jpg                  ← Paper figures
```
---
Related Repositories
Repository	Paper	Description
on-device-vision-ai	Paper 1	C++ on-device CNN training and inference firmware
webmcu-vision-web	Paper 2	This repository — browser companion
webmcu-audio-web (coming)	Paper 3	Audio classification with I2S microphone
webmcu-imu-web (coming)	Paper 4	IMU gesture recognition
---
License
MIT License. See LICENSE.
Contributions welcome as GitHub issues and pull requests.
---
By Jeremy Ellis · High School Robotics Educator, British Columbia, Canada






