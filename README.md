# Positional Embeddings for Vision Transformers (ViT)
Implemented Positional Encodings: <strong>No Position</strong>, <strong>Learnable</strong>, <strong>Sinusoidal (Absolute)</strong>, <strong>Relative</strong>, and <strong>Rotary (RoPe)</strong>.
<ul>
  <li>Works by splitting dimensions into two parts and implements 1D positional embeddings on each part.</li>
  <li>One part uses the x-positions sequence, and the other uses y-positions. Check below for more details.</li>
  <li>Classification token is handled differently in all methods. Check below for more details.</li>
  <li>Network used here in a scaled-down version of the original ViT with only 800k parameters</a>. </li>
  <li>Works with small datasets by using a smaller patch size of 4.</li>
</ul>  
<br>

## Run commands (also available in <a href="scripts.sh">scripts.sh</a>): <br>
Different positional embeddings can be chosen using the <strong>pos_embed</strong> argument. Example:
<table>
  <tr>
    <th>Positional Embedding Type</th>
    <th>Run command</th>
  </tr>
  <tr>
    <td>No Position</td>
    <td>python main.py --dataset cifar10 --pos_embed <strong>none</strong></td>
  </tr>
  <tr>
    <td>Learnable</td>
    <td>python main.py --dataset cifar10 --pos_embed <strong>learn</strong></td>
  </tr>
  <tr>
    <td>Sinusoidal (Absolute)</td>
    <td>python main.py --dataset cifar10 --pos_embed <strong>sinusoidal</strong></td>
  </tr>
  <tr>
    <td>Relative</td>
    <td>python main.py --dataset cifar10 --pos_embed <strong>relative</strong> --max_relative_distance 2</td>
  </tr>
  <tr>
    <td>Rotary (Rope) </td>
    <td>python main.py --dataset cifar10 --pos_embed <strong>rope</strong></td>
  </tr>
</table>
Relative Positional Embedding uses a "max_relative_distance" hyper-parameter to clamp distances between -max_relative_distance and max_relative_distance (referred to as k in paper).
<br>
The dataset can be changed using the dataset argument.
<br>
<br>

## Results
Results on test sets of different datasets when ViT is trained using different positional embeddings. 
<table>
  <tr>
    <th>Positional Encoding Type</th>
    <th>FashionMNIST</th>
    <th>SVHN</th>
    <th>CIFAR10</th>
    <th>CIFAR100</th>
  </tr>
  <tr>
    <td>No Position</td>
    <td></td>
  </tr>
  <tr>
    <td>Learnable</td>
    <td></td>
  </tr>
  <tr>
    <td>Sinusoidal (Absolute)</td>
    <td></td>
  </tr>
  <tr>
    <td>Relative</td>
    <td></td>
  </tr>
  <tr>
    <td>Rotary (Rope) </td>
    <td></td>
  </tr>
</table>
<br>

## Splitting X and Y-axis to Multiple 1D Positonal Embeddings:

## Handling Classification Token:
Many of the Positional Embeddings were designed to work without classification tokens. When a classification token is present, some techniques (Sinusoidal, Relative, and Rotary) must be adapted.
<table>
  <tr>
    <th>Positional Encoding Type</th>
    <th>Classification Token</th>
  </tr>
  <tr>
    <td>No Position</td>
    <td>No positional embedding added to the Classification Token.</td>
  </tr>
  <tr>
    <td>Learnable</td>
    <td>Learnable Classification Token also learns its positional embedding.</td>
  </tr>
  <tr>
    <td>Sinusoidal (Absolute)</td>
    <td>No positional embedding is provided to the Classification Token. Instead, the Classification Token learns its positional embedding.</td>
  </tr>
  <tr>
    <td>Relative</td>
    <td>One solution is not to use the Classification Token. Instead, a separate index (used 0 here) in the embedding lookup tables represents distances to the Classification token.</td>
  </tr>
  <tr>
    <td>Rotary (Rope) </td>
    <td>Sequences of patches start at 1 (instead of 0), and 0 represents the position of the Classification Token. Using a 0 index for Classification Token results in no change/rotation.</td>
  </tr>
</table>
<br>

## Parameters Comparison:
Comparison of additional learned parameters added by different positional embeddings. 
<table>
  <tr>
    <th>Positional Encoding Type</th>
    <th>Additional Parameters Explaination</th>
    <th>Parameters Count</th>
  </tr>
  <tr>
    <td>No Position</td>
    <td>N/A</td>
    <td>0</td>
  </tr>
  <tr>
    <td>Learnable</td>
    <td>Number of Patches * Embed dim</td>
    <td>64 x 128 = 8192</td>
  </tr>
  <tr>
    <td>Sinusoidal (Absolute)</td>
    <td>No learned parameters</td>
    <td>0</td>
  </tr>
  <tr>
    <td>Relative</td>
    <td> (2 * max_relative_distance + 1 + 1) * Embed dim * 2 per encoder block</td>
    <td> (2 x 2 + 1 + 1) x 128 x 2 x 6 = 9216 </td>
  </tr>
  <tr>
    <td>Rotary (Rope) </td>
    <td>No learned parameters</td>
    <td>0</td>
  </tr>
</table>
<br>

## Base Transformer Config:
Below are the base training and network details used in the experiments.
<table>
  <tr>
    <td>Input Size</td>
    <td> 3 X 32 X 32  </td>
    <td></td>
    <td></td>
    <td>Epochs</td>
    <td> 200  </td>
  </tr>
  <tr>
    <td>Patch Size</td>
    <td>4</td>
    <td></td>
    <td></td>
    <td>Batch Size</td>
    <td> 128  </td>
  </tr>
  <tr>
    <td>Sequence Length</td>
    <td>8*8 = 64</td>
    <td></td>
    <td></td>
    <td>Optimizer</td>
    <td> AdamW  </td>
  </tr>
  <tr>
    <td>Embedding Dim </td>
    <td>128</td>
    <td></td>
    <td></td>
    <td>Learning Rate</td>
    <td> 5e-4  </td>
  </tr>
  <tr>
    <td>Num of Layers </td>
    <td>6</td>
    <td></td>
    <td></td>
    <td>Weight Decay</td>
    <td> 1e-3  </td>
  </tr>
  <tr>
    <td>Num of Heads </td>
    <td>4</td>
    <td></td>
    <td></td>
    <td>Warmup epochs</td>
    <td> 10 </td>
  </tr>
  <tr>
    <td>Forward Multiplier </td>
    <td>2</td>
    <td></td>
    <td></td>
    <td>Warmup schedule</td>
    <td> Linear  </td>
  </tr>
  <tr>
    <td>Dropout </td>
    <td>0.1</td>
    <td></td>
    <td></td>
    <td>Learning Rate Decay Schedule</td>
    <td> Cosine  </td>
  </tr>
  <tr>
    <td>Parameters </td>
    <td>820k</td>
    <td></td>
    <td></td>
    <td>Minimum Learning Rate</td>
    <td> 1e-5  </td>
  </tr>
</table>
<br>

Note: This repo is built upon the following GitHub repo: <a href="https://github.com/s-chh/PyTorch-Scratch-Vision-Transformer-ViT">Vision Transformers from Scratch in PyTorch</a>
<br>

### Citations
```
@article{vaswani2017attention,
  title={Attention is all you need},
  author={Vaswani, Ashish and Shazeer, Noam and Parmar, Niki and Uszkoreit, Jakob and Jones, Llion and Gomez, Aidan N and Kaiser, {\L}ukasz and Polosukhin, Illia},
  journal={Advances in neural information processing systems},
  volume={30},
  year={2017}
}
@inproceedings{dosovitskiy2020image,
  title={An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale},
  author={Dosovitskiy, Alexey and Beyer, Lucas and Kolesnikov, Alexander and Weissenborn, Dirk and Zhai, Xiaohua and Unterthiner, Thomas and Dehghani, Mostafa and Minderer, Matthias and Heigold, Georg and Gelly, Sylvain and others},
  booktitle={International Conference on Learning Representations},
  year={2020}
}
@article{shaw2018self,
  title={Self-attention with relative position representations},
  author={Shaw, Peter and Uszkoreit, Jakob and Vaswani, Ashish},
  journal={arXiv preprint arXiv:1803.02155},
  year={2018}
}
@article{su2024roformer,
  title={Roformer: Enhanced transformer with rotary position embedding},
  author={Su, Jianlin and Ahmed, Murtadha and Lu, Yu and Pan, Shengfeng and Bo, Wen and Liu, Yunfeng},
  journal={Neurocomputing},
  volume={568},
  pages={127063},
  year={2024},
  publisher={Elsevier}
}
```
