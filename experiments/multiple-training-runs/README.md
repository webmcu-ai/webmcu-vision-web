
Multiple Training Runs: Environmental Consistency TestThis experiment evaluates the training speed and classification consistency of a 2-layer CNN vision model across five distinct environmental conditions. The primary variable is the background used for the 0blank class, testing how different visual noise levels affect model performance.Experiment ParametersModel Architecture: 2-Layer Convolutional Neural Network (CNN).Dataset: 30 images per class.Classes:0blank: Environment-dependent background.1cup: Target object 1.2pen: Target object 2.Assets per Test: Each experiment folder contains:A header/ folder and an image/ folder.A detailed log.txt of the training session.Three real-world test images featuring heat maps and inference results.Training Environments & ResultsEnvironmentDescriptionBackground (Class 0)NotesBareMinimalist setupLight green paperBaseline for "clean" training.DesktopStandard workspaceArea beside computerTypical office lighting and clutter.FloorGround levelFloor surfaceUnique perspective/texture.ShadeLow lightDark roomTests model sensitivity to underexposure.BusyHigh noiseWorkbench with paint splattersSignificant visual artifacts and "busy" textures.Directory StructureEach experiment listed above is stored in its own sub-directory within the multiple-training-runs folder.Plaintext/experiments/multiple-training-runs/
└── [environment-name]/
    ├── header/
    ├── image/
    ├── log.txt
    └── test_result_1_heatmap.png
    └── test_result_2_heatmap.png
    └── test_result_3_heatmap.png
ObservationsThe heat maps included in each folder provide visual confirmation of which features the model prioritized during training in each specific environment. This data is crucial for understanding how "busy" backgrounds (like the paint-splattered workbench) influence weight distribution compared to the "bare" green paper baseline.
