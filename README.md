### Timbre Transfer using Nonnegative Matrix Factorization
**📄**  **Click here to read the full Project Report (PDF)**

#### Project Overview
This project implements an algorithm to transfer the timbre of a specific source audio to a target audio signal, relying solely on signal processing methods [1]. The primary technique explored is **Nonnegative Matrix Factorization (NMF)** to achieve a "quick and dirty" timbre transfer without the use of large and sophisticated neural networks. 

We engineered a custom signal processing pipeline to map the sonic characteristics of noise-like sources onto structured musical targets:
*   **Sources:** Bees_Buzzing.wav, Wind_Blowing.wav, and Chainsaw_Sawing.wav.
*   **Targets:** Jingle_Bells_Boogie.wav, Have_Yourself.wav, Blue_Christmas.wav, and White_Christmas.wav.

#### Methodology

##### 1. Signal Processing & STFT
We processed the raw audio by converting it into the time-frequency domain to extract structural rhythmic and spectral properties:
*   **STFT Computation:** Extracted the complex-domain Short-Time Fourier Transform (STFT) with a sampling frequency of 22050 Hz, a window length of 4960, and a hop size of 1024.
*   **Magnitude Spectrograms:** Separated the magnitude from the phase to map the spectral envelope and frequencies of both the source and target signals.

##### 2. Nonnegative Matrix Factorization (NMF)
To achieve the timbre transfer, we utilized NMF to derive non-negative matrices $W$ (templates/frequencies) and $H$ (activations over time):
*   **Fixed Template Matrix ($W$):** We constrained and initialized $W$ using the normalized magnitude STFT of the source track. Fixing $W$ ensures that the spectral patterns of the source dominate the final representation.
*   **Learned Activation Matrix ($H$):** We initialized $H$ with random values and updated it iteratively (using scikit-learn) so that the combination of $W$ and $H$ matches the magnitude STFT of the target audio.

##### 3. Audio Re-Synthesis
Because the NMF process discards phase information, we implemented two alternative ways to reconstruct the time-domain waveform:
*   **Naive Inverse STFT (ISTFT):** Applies the newly calculated magnitude to the original phase of the source signal.
*   **Griffin-Lim Algorithm:** An iterative technique that mathematically reconstructs a signal from its magnitude by iteratively improving the phase to minimize the reconstruction error.

#### Key Results
*   **Reconstruction Quality:** The Griffin-Lim algorithm produced significantly smoother and more natural waveforms compared to the ISTFT method, which suffered from inconsistencies and audible artifacts due to phase/magnitude mismatches.
*   **Parameter Optimization:** Keeping the template matrix ($W$) fixed proved crucial [8]. If $W$ is not fixed, the NMF adapts too much to the target's original sounds, leading to a much weaker timbre transfer.
*   **Timbre Evaluation:** Metric evaluation validated the transfer. The Spectral Centroid of the processed tracks shifted higher (e.g., from 858.5 Hz to 1720.3 Hz), and Spectral Flatness increased, indicating the successful introduction of the source's brighter, noisier textures.
*   **Structural Preservation:** Despite the distinct change in timbre, the Cosine Similarity between the Mel-Frequency Cepstral Coefficients (MFCCs) of the target and the processed track remained very high (0.9537), confirming that the core rhythmic and spectral identity of the original target song was preserved.

#### How to Run
1. Clone the repository and extract the `audio.zip` folder into the main directory.
2. Install dependencies by running `pip install sklearn tqdm` (and ensure `librosa` and `soundfile` are installed).
3. Open and run the `Homework2.ipynb` Jupyter Notebook to execute the pipeline and generate the combined `.wav` files in the `audio/outputs` folder.
