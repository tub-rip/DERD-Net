# DERD-Net: Learning Depth from Event-based Ray Densities

Official repository for [DERD-Net: Learning Depth from Event-based Ray Densities](https://arxiv.org/pdf/2504.15863), by [Diego de Oliveira Hitzges](https://www.linkedin.com/in/diego-de-oliveira-hitzges-410943276/), [Suman Ghosh](https://www.linkedin.com/in/suman-ghosh-a8762576/), and [Guillermo Gallego](https://sites.google.com/view/guillermogallego), preprint published at arXiv.

## [Paper](https://arxiv.org/pdf/2504.15863) | [Video]()

If you use this work in your research, please cite it as follows:

```bibtex
@article{hitzges2025derd,
  title={DERD-Net: Learning Depth from Event-based Ray Densities},
  author={Hitzges, Diego de Oliveira and Ghosh, Suman and Gallego, Guillermo},
  journal={arXiv preprint arXiv:2504.15863},
  year={2025}
}
```

## Framework

<div align="center">
  <img src="assets/system_pipeline_thicker_arrows.png" alt="Alt Text">
</div>

### Data-Preprocessing

The events are processed into disparity space images (DSIs), which represent the potential depth of each pixel across multiple disparity levels by counting the rays passing through each voxel, projected from the pixel where an event was triggered. In stereo event vision, DSIs from two or more cameras can be fused, eliminating the need for event synchronization between cameras. This reduces complexity and enables more robust depth estimation. This approach was originally proposed in the [MC-EMVS](https://onlinelibrary.wiley.com/doi/10.1002/aisy.202200221) paper. To construct DSIs, refer to the associated repository, [dvs_mcemvs](https://github.com/tub-rip/dvs_mcemvs).

### Input

To ensure reliable depth estimation, we apply an adaptive Gaussian threshold filter to each DSI, filtering for pixels with a sufficiently high ray count. For each selected pixel, a local sub-area around it is extracted from the DSI and normalized. These extracted regions, or Sub-DSIs, serve as the input for the neural network.

### Neural Network

The neural network architecture is a 3D-Convolutional GRU. Each Sub-DSI first undergoes a 3D-convolution to capture geometric patterns and enhance generalization. Following this, each depth layer is fed sequentially to a GRU to integrate information and reduce the dimension along the depth axis. Because the Sub-DSI consists of ray counts projected from the representative camera position into space, each depth layer depends on the previous one. Consequently, the last hidden state captures the embedding of all 3D geometric data in a 2D matrix. Finally, two fully connected layers map this embedding to the network output.

<div align="center">
  <img src="assets/neural_net.png" alt="Alt Text">
</div>

### Output

We present two versions of the network, identical in architecture except for their last layer. In the single-pixel version, the network estimates the depth at the selected pixel, positioned centrally within the Sub-DSI. In the multi-pixel version, the output is a 3x3 grid, providing depth estimates for the selected pixel as well as its adjacent pixels.
