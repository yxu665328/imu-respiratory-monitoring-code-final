# IMU Respiratory Monitoring — Analysis Code

This folder contains the analysis code for Yucheng Xu's Honours project at the University of Tasmania.

> **You'll need the original data to run the experimental analyses.** This package includes the code and plotting support files, but not the raw recordings or saved results. Add the matching original recordings before trying to reproduce the thesis results.

## Files and folders

The main folder is `Code Yucheng Xu`. The two MATLAB files are directly inside it; the Python scripts are in the two folders below.

| Location | Main script | What it does |
| --- | --- | --- |
| Main folder | `RR_estimate_comparison.m` | Compares paired IMU and Graseby recordings at 20, 35 and 50 breaths/min. |
| Main folder | `paul_compare_5postures.m` | Compares breathing-rate estimates across five postures. |
| `Nine position map` | `process_nine_imu.py` | Processes P1–P9 and creates maps, figures, tables and a composite video. |
| `24 point model` | `paul_24_raw_to_heatmap.py` | Processes the P1–P24 inputs and builds a 3D amplitude heatmap. |

Keep `positions_new.csv`, `baby_paul_new.jpg` and `requirements.txt` beside the nine-position script. Keep `paul_estimated_model.py` and `paul_upper_body_heatmap_24.py` beside the 24-point script; they build and plot the model.

## Setup

Open PowerShell in the main folder and install the Python packages:

```powershell
python -m pip install -r ".\Nine position map\requirements.txt"
```

These requirements cover both Python folders. Use the same Python environment when you run the scripts. In VS Code, you can right-click a folder and choose **Open in Integrated Terminal**.

For MATLAB, set the **Current Folder** to the main folder. The IMU–Graseby script needs **Signal Processing Toolbox** and **Statistics and Machine Learning Toolbox**.

## Running the analyses

The input folders below aren't included in this code-only package. Create them when you add the data. Keep the original column names, timestamps and acceleration units, and check that each file matches the intended point or posture.

### IMU–Graseby comparison

Put these six original CSV files beside `RR_estimate_comparison.m`:

| Setting | IMU file | Graseby file |
| --- | --- | --- |
| 20 breaths/min | `20260805_003820_imu.csv` | `20260805_003820_graseby.csv` |
| 35 breaths/min | `20260805_004005_imu.csv` | `20260805_004005_graseby.csv` |
| 50 breaths/min | `20260805_004147_imu.csv` | `20260805_004147_graseby.csv` |

Run in MATLAB:

```matlab
run('RR_estimate_comparison.m');
```

Results go into `Results/IMU_Graseby_Comparison_20260805_v5_trimmed` in the main folder.

### Nine-position maps

Inside `Nine position map`, create these two input folders and put the matching `P1.csv`–`P9.csv` files in each:

- `analysis_inputs/RR35_normal`
- `analysis_inputs/RR35_single_lung`

Open PowerShell **inside `Nine position map`** and run:

```powershell
python .\process_nine_imu.py run --input-dir .\analysis_inputs\RR35_normal --expected-rr-bpm 35 --condition RR35_normal --output-dir results

python .\process_nine_imu.py run --input-dir .\analysis_inputs\RR35_single_lung --expected-rr-bpm 35 --condition RR35_single_lung --output-dir results
```

The known setting of 35 breaths/min helps select the PCA component. Results go into `results/RR35_normal` and `results/RR35_single_lung` inside this folder. The video combines recordings taken one position at a time, aligned by breathing phase. With the default settings, it lasts 21 seconds; its playback speed does not represent the measured breathing rate.

### Five-posture comparison

Put the five posture recordings, `P1.csv`–`P5.csv`, in the main folder. In order, they should be supine, 45° left, 45° right, 135° left and 135° right.

Run in MATLAB:

```matlab
result = paul_compare_5postures(pwd, 35);
disp(result.OutputFolder);
```

Here, `35` is the reference rate for comparison; it doesn't set the estimated breathing rate. Each run creates a new results folder, and the second line prints its location.

### 24-point 3D heatmap

Inside `24 point model`, create `raw_24` and add the matching `P1.csv`–`P24.csv` inputs. Use acceleration in **mg**, with columns such as `ax_mg`, `ay_mg` and `az_mg`, and retain the device timestamps.

Open PowerShell **inside `24 point model`** and run:

```powershell
python .\paul_24_raw_to_heatmap.py .\raw_24 --output-dir .\paul_24_output
```

The `paul_24_output` folder will contain a values CSV, an interactive HTML heatmap and a PNG preview. Opening the HTML needs internet access to load Plotly. The model geometry is approximate, and the colours show acceleration amplitude with interpolation between points. The model does not estimate respiratory volume.

The geometry helper can also run without recordings, and the heatmap helper uses demo values if no values CSV is supplied. These modes let you view the model; they don't reproduce experimental results.

Running an analysis again with the same output path overwrites its results. The five-posture script is the exception: it creates a new folder each time.

