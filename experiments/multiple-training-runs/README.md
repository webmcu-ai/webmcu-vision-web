# Multiple Training Runs: Environmental Consistency Test

This experiment evaluates the training speed and classification consistency of a **2-layer CNN vision model** across five distinct environmental conditions. The primary goal is to determine how the visual environment of the `0blank` class affects model robustness.

### Experiment Parameters
* **Model:** 2-Layer CNN Vision Model.
* **Data Split:** 30 images per class.
* **Classes:**
    * `0blank`: Environment-dependent background.
    * `1cup`: Target object.
    * `2pen`: Target object.
* **Folder Contents:** Each test directory includes:
    * `/header/` and `/image/` folders.
    * A text-based training log.
    * Three real-world test images featuring **heat maps** and inference results.

### Training Environments

| Environment | Background (Class 0) | Description |
| :--- | :--- | :--- |
| **Bare** | Light green paper | A clean, solid-colored baseline. |
| **Desktop** | Computer desk | Standard workspace area beside a computer. |
| **Floor** | Floor surface | Real-world floor textures and lighting. |
| **Shade** | Dark room | Low-light conditions to test sensor sensitivity. |
| **Busy** | Paint-splattered workbench | High visual noise with significant artifacts. |

### Directory Structure
Results for each environment are organized as follows:

```text
/experiments/multiple-training-runs/
└── [environment-name]/
    ├── header/
    ├── image/
    ├── log.txt
    ├── test_01_heatmap.jpg
    ├── test_02_heatmap.jpg
    └── test_03_heatmap.jpg
