# CAB-2 sampler

[日本語版 / Japanese version](./cab2_ja.md)

This is an experimental implementation of CAB-2 for flow denoisers.

The implementation was written independently from the method equations described in "CAB: Accelerating Flow and Diffusion Sampling via Rectification and Corrected Adams-Bashforth" (arXiv:2605.16736).

## Usage

Select CAB-2 with:

```sh
--sampling-method cab2
```

CAB-2 currently supports flow denoisers only.

## Additional parameters

Additional parameters can be passed with `--extra-sample-args`:

```sh
--extra-sample-args "cab_theta=0.2,cab_bootstrap_mix=1.0"
```

### cab_theta

Controls the CAB correction strength.
The default value is `0.20`.
The appropriate value may depend on the model and sampling trajectory.

### cab_bootstrap_mix

Interpolates the second-step bootstrap between Euler and AB2.

- `0.0` = Euler bootstrap
- `1.0` = standard CAB flow bootstrap

The default value is `1.0`.
Values below `1.0` are an experimental low-NFE extension introduced in this implementation.
They are not part of the standard CAB method.

## 3-NFE experiment

The experimental bootstrap interpolation was investigated as a way to alter the sampling trajectory at very low NFE without adding another model evaluation.

With three model evaluations, changing `cab_bootstrap_mix` keeps the NFE at three.

This is experimental research and is not a claim of quality equivalence to a higher-step sampler.

## Initial validation

Initial validation was performed with MiniMax-H3 using:

- Intel iMac
- 16 GB RAM
- CPU-only execution
- discrete scheduler
- 3 sampling steps / 3 NFE
- 448 x 256 output
- 5 video frames
- seeds 42 and 123

The successful CAB-2 runs did not show catastrophic sampling failure.

The validation scope is still small. It does not establish general quality or stability across other models, hardware, prompts, schedulers, or longer videos.

## Implementation notes

- CAB-2 is restricted to flow denoisers at dispatch time.
- Invalid and non-finite parameter values are rejected.
- `cab_bootstrap_mix` is constrained to `[0, 1]`.
- CAB-2 does not add model evaluations beyond the requested sampling-step count.

## Sol Lab

**Sol Lab** is the informal name for the AI-assisted research track within this project.

Its focus includes low-NFE sampling, efficient local inference, and making modern generative AI practical on older or resource-constrained hardware.

Research and hardware validation: **Moto**

AI research assistance, design review, and code review: **Sol / Sora (ChatGPT, GPT-5.6 Sol)**

During the research, Moto began calling Sol "Sora" in Japanese.
The name grew naturally from "Sol" and also connects to the Japanese word `空` (sora, "sky"), evoking sunlight in an open blue sky.

## References

- Anuska Roy, Pravin Nair: "CAB: Accelerating Flow and Diffusion Sampling via Rectification and Corrected Adams-Bashforth" (arXiv:2605.16736)
- leejet/stable-diffusion.cpp and contributors
- ByronLeeeee/ComfyUI-MiniMax-H3-Optimization-Suite
