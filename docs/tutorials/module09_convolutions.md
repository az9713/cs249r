# Module 09 – Convolution Test Tutorial

## What you learn
Convolution tests (`tinytorch/tests/09_convolutions/test_convolutions_core.py` and `test_convolutions_gradient_flow.py`) enforce weight layout, padding, stride, and gradient propagation for Conv2d layers plus pooling ops. You learn to reason about spatial tensor shapes and kernel alignment.

## How the tests teach it
- Core tests feed synthetic images through Conv2d to verify output sizes follow `(H_out = (H + 2p - k) / s + 1)` and that filters actually respond to features.
- Gradient flow tests backprop random tensors to ensure convolutional weights and biases receive gradients and activations propagate to earlier layers.
- Progressive suites combine multiple convs and pooling stages to demonstrate how receptive fields grow as networks deepen.

## Why this matters in 2026
Convolution is still dominant in vision, audio, and parts of multimodal stacks. Hardware accelerators expose tiling tricks you can only exploit if you trust your math. Mastering these tests prepares you to debug padding mistakes or optimize fused kernels for embedded boards.

## Try it yourself
- Implement dilated convolution support and adapt the existing output-shape assertions to cover the new receptive fields.
- Compare gradient magnitudes across `test_convolutions_gradient_flow.py` to understand how initialization impacts deep CNN stability.

