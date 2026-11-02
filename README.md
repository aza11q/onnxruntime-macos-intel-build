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
