# refx fast.ai course 1

## basic training workflow

1. Enable data augmentation, and `precompute=True`
2. Use `lr_find()` to find highest learning rate where loss is still clearly improving
3. Train last layer from precomputed activations for 1-2 epochs
4. Train last layer with data augmentation (i.e. `precompute=False`) for 2-3 epochs with `cycle_len=1`
5. Unfreeze all layers
6. Set earlier layers to 3x-10x lower learning rate than next higher layer
7. `Use lr_find()` again
8. Train full network with `cycle_mult=2` until over-fitting


