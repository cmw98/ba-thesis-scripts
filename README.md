# Deep Learning-based Compression of Synthetic Images - Scripts

This is a repository that features all relevant scripts for my (Clemens Wansch) bachelor thesis titled "Deep Learning-based Compression of Synthetic Images".

Please be aware that due to moving files around on and off the VSC, and my old and new notebook, file paths etc are unfortunately by no means consistent and need to be adapted to your local machine and setup. Brief instructions on how to get things up and running are given below.

## Step 1: set up Jupyter Lab with an appropriate conda environment

Packages needed include, but are not limited to, pytorch, CompressAI, PIL, matplotlib...

Once you have your Jupyter up and running, your next station are the notebooks.

## Step 2: download datasets

Unfortunately it has become hard to impossible to open the original compression.cc website where the ZIP files were provided back in the day. There is an entry in the TensorFlow docs on how to retrieve the dataset with their code but no download link - (https://www.tensorflow.org/datasets/catalog/clic)[https://www.tensorflow.org/datasets/catalog/clic].

The GTA+ dataset is provided in this repository as `gtaplus-dataset.zip`.

## Step 3: Train compression models

Do this step once with the GTA+ dataset, and once with the CLIC dataset.

Open up `Training_final.ipynb`. Adapt all paths to your liking (cell 11). Either adapt the WandB code or remove it. Execute the script to train your models, the model snapshots will be saved in `model_dir`. Quality settings 4, 6, and 8 are the ones used originally (as is coded in the for loop of the last code cell).

## Step 4: Run entire test set through Balle network and save the PNGs

For this step use `Load_compression_net.ipynb`. Beware: this script will save the resulting images in the same format as the input, so it is advisable to first convert everything to PNG. Also you'll again need to adapt the paths and point it to the network snapshots and the datasets from the previous step.

The script will run the entire test set through the network snapshots provided (from the previous step) and create a directory for each network with the coded images, for the purpose of investigating and evaluating visual quality (BPP is not saved here.)

For the combined pipeline approach, create a downsampled version of the test set and run the same script on it, so you can upsample them afterwards using the super-resolution network.

## Step 5: create statistics for compression

`Inference_final.ipynb` will perform a test run and plot a chart with the quantitative results. Note that there is only one chart created with either PSNR or MS-SSIM on the Y-axis. Save one plot, then change the metric for the Y-axis in the last Jupyter cell, and run it again, to obtain both a PSNR and an MS-SSIM chart.

It is advisable to save the statistics as CSV for use with `comp_sr_eval.ipynb`, so you can combine and plot multiple runs and also combine it with the pipeline approach (compression and super-resolution).

## Step 6: evaluate AMD FidelityFX super-resolution samples

The AMD FidelityFX samples are given in the `fsr-samples` subdirectory. `{i}-native.png` is the original resolution screenshot, `{i}-q2` to `{i}-q5` with and without `-sharpened` are the different scaling factors with and without the sharpening step (q2 being x2 scaling factor, q3 x1.7, q4 x1.5, and q5 x1.3). `{i}-native_x2_SR.png` is the native image downsampled and then reconstructed with the RCAN network - of course, feel free to reconstruct these on your own instead.

Use `Benchmark_fsr.ipynb` to evaluate these screenshots - the paths should already be matching.

## Step 7: set up the super-resolution network

Disclaimer: this is not part of the repository. Please check out https://github.com/sanghyun-son/EDSR-PyTorch and set it up with its own Conda environment (there are specific requirements, refer to the README of that repository). Of course, the model trained for this paper is neither EDSR nor MDSR, but RCAN, which is also implemented in this repo though it's a bit hidden.

Navigate to src/demo.sh, you will find a series of command lines for different models and configs. The one I used is line 47-48.

I'll be upfront, I ran this script only a few times successfully on the VSC because it's really resource-hungry, I can't provide all the details anymore because the VSC3 cluster simply no longer exists and hence I am unable to look up everything, I only saved the data from the CSVs I generated and the rendered PNGs.

Anyways, after successful training of the network with the GTA+ training set, you should run inference on the test set as well as the decompressed renderings from the MBT network (Step 4) to receive all the inference needed.

Alas, I can't even tell you the specific command lines anymore, however if you just run `python main.py` without args (or some jibberish) you should be presented a usage listing with the possible CLI args.

## Step 8: calculate statistics from the inferred super-resolution versions

use `calc_stats_from_renderings.ipynb` to calculate PSNR and MS-SSIM for all the rendered PNGs against the originals. Note the directory structure in cell 3 (`for dir in [...]`). bicubic_supersampled should contain the super-sampled PNGs without compression, q4-q8 stands for the quality setting used as explained in Step 3, mse and msssim are of course the distortion functions optimised towards, and fullres and x2res means with/without super-resolution. Again, adapt all paths in here. You should end up with a table of PSNR, ssim and ms-ssim statistics, which you can save as CSV.

## Step 9: plot CSVs

Use `comp_sr_eval.ipynb` to create a plot from the CSV. For an example what the csv should look like, refer to  `compression_sr_results.csv`.

## Optional step 10: create sample comparison graphics (as seen in the thesis)

For this, use `Sample_img_compression.ipynb` to create a side-by-side comparison of just the different lambdas and distortion functions of the compression network side-by-side, and `Sample_img_pipeline.ipynb` to have the same times two - with and without super-resolution. this requires all the rendered PNGs from just like Step 8, with the same directory structure.