# ONNX Runtime for Intel macOS

This repository contains only a manually started build of the public upstream
ONNX Runtime 1.31.0 source for macOS 14+ and x86-64 Python 3.12. It does not
contain an application, private models, keys, settings, or user data.

The workflow verifies the upstream revision, builds a wheel, verifies the native
extension architecture, retags it for x86-64, imports it, and saves it as a
short-lived public GitHub Actions artifact. Everyone can view the workflow,
logs, and resulting artifact. See the upstream project and license at
https://github.com/microsoft/onnxruntime.

The job is restricted to a public repository and uses the standard
`macos-15-intel` GitHub-hosted runner. The wheel is uploaded before the final
import check, so a failed smoke test does not discard the compiled result.
A saved artifact alone does not mean the import check passed.

The optional `arm_nchwc` manual input builds a separate native ARM64 verification
candidate from the same upstream revision with the upstream
`onnxruntime_USE_ARM_NEON_NCHWC=ON` option. It uses the free standard `macos-15`
runner and skips the Intel job. This candidate is not an accepted application
runtime: model-output compatibility must be checked separately. No application
sources, model weights, model outputs or private repository keys are used here.

The optional `arm_reduction8` input applies an explicitly recorded generic MLAS
kernel patch to the pinned upstream ARM64 source. It retains NCHWc16 storage and
splits each channel reduction into two eight-channel partial sums. This changes
floating-point operation ordering and selects the C++ NEON kernel for that entry
point. Bias and activation remain after the complete reduction. The upstream
source hash, patched source hash, build option and patch are recorded. This is an
experimental compatibility candidate, not an accepted runtime; complete numeric
and performance validation is required separately. The patch contains no
application code, model data or reference model outputs. The existing Intel
build and unpatched ARM64 input remain available.

The optional `arm_layernorm` input combines reduction8 with an explicitly
recorded generic LayerNorm/RMSNorm arithmetic candidate. It uses a four-lane
double-precision mean, an eight-lane centered fused variance reduction, and
the corresponding scale/bias arithmetic order. It is selected only on ARM64
with `MLAS_ARM_AVX2_LAYERNORM=1`; other builds retain upstream dispatch.
This experimental option is not accepted until complete model-level and
performance checks pass. The patch contains no application data or model
parameters. Both upstream patches and their exact source hashes are recorded.

The optional `arm_layernorm16` input applies the same generic ordered
normalization to FP16 input/output, using the original MLAS half conversions.
Its full-model numerical acceptance is still required separately. This option
retains the prior F32 candidate as a separate input and records the complete
generic source patch. Public upstream compiler objects now use a compiler-keyed
ccache capped at 2 GB, containing no application files or model data. Cache
billing settings are not changed. Standard public runners remain in use.
