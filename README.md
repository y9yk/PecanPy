# PecanPy: A parallelized, efficient, and accelerated _node2vec_ in Python

Learning low-dimensional representations (embeddings) of nodes in large graphs is key to applying machine learning on massive biological networks. _Node2vec_ is the most widely used method for node embedding. PecanPy is a fast, parallelized, memory efficient, and cache optimized Python implementation of [node2vec](https://github.com/aditya-grover/node2vec). It uses cache-optimized compact graph data structures and precomputing/parallelization to result in fast, high-quality node embeddings for biological networks of all sizes and densities. The implementation and the optimizations, along with benchmarks, are described in this [preprint](https://doi.org/10.1101/2020.07.23.218487) `bioRxiv doi.org/10.1101/2020.07.23.218487`.

## Requirements

```
python 3.7.4
gensim 3.8.0
numpy 1.17.4
numba 0.46.0 (you might get segmentation faults if your numba is outdated)
```

## Usage

PecanPy operates in three different modes – `PreComp`, `SparseOTF`, and `DenseOTF` – that are optimized for networks of different sizes and densities; `PreComp` for networks that are small (≤10k nodes; any density), `SparseOTF` for networks that are large and sparse (>10k nodes; ≤10% of edges), and `DenseOTF` for networks that are large and dense (>10k nodes; >10% of edges). These modes appropriately take advantage of compact/dense graph data structures, precomputing transition probabilities, and computing 2nd-order transition probabilities during walk generation to achieve significant improvements in performance.

### Example

To run *node2vec* on Zachary's karate club network using `SparseOTF` mode, execute the following command from the project home directory:

```bash
python -m n2v --input demo/karate.edg --output demo/karate.emb --mode SparseOTF
```

### Demo

Execute the following command for full demonstration:

```bash
sh demo/run_n2v
```

### Mode

As mentioned above, PecanPy contains three different modes, each of which is better optimized for different network sizes/densities:
| Mode | Network size/density | Optimization |
|:-----|:---------------------|:-------------|
| `PreComp` (default) | ≤10k nodes; any density | Precompute second order transition probabilities, using CSR graph |
| `SparseOTF` | >10k nodes; ≤10% of edges | Transition probabilites computed on-the-fly, using CSR graph |
| `DenseOTF` | >10k nodes; >10% of edges | Transition probabilities computed on-the-fly, using dense matrix |

### Options

Check out the full list of options available using:
```bash
python -m n2v --help
```

### Input

The supported input is a network file as an edgelist `.edg` file (node id could be int or string):

```
node1_id node2_id <weight_float, optional>
```

Another supported input format (only for `DenseOTF`) is the numpy array `.npz` file. Run the following command to prepare a `.npz` file from a `.edg` file.

```bash
python -m n2v --input $input_edgelist --output $output_npz --task todense
```

### Output

The output file has *n+1* lines for graph with *n* vertices, with a header line of the following format:

```
num_of_nodes dim_of_representation
```

The following  next *n* lines are the representations of dimension *d* following the corresponding node ID:

```
node_id dim_1 dim_2 ... dim_d
```

## Additional Information
### Support
For support please contact [Remy Liu](https://twitter.com/RemyLau3) at liurenmi@msu.edu.

### License
See [LICENSE.md](https://github.com/krishnanlab/pecanpy/blob/master/LICENSE.md) for license information for all data used in this project.

### Citation
If you use this work, please cite:  
Liu R, Krishnan A (2020) PecanPy: a fast, efficient, and parallelized Python implementation of _node2vec_. _bioRxiv_ doi.org/10.1101/2020.07.23.218487.

### Authors
Renming Liu, Arjun Krishnan*

*General correspondence should be addressed to AK at arjun@msu.edu.

### Funding
This work was primarily supported by US National Institutes of Health (NIH) grants R35 GM128765 to AK and in part by MSU start-up funds to AK.

### Acknowledgements
We thank Christopher A. Mancuso, Anna Yannakopoulos, and the rest of the [Krishnan Lab](https://www.thekrishnanlab.org) for valuable discussions and feedback on the software and manuscript.

### References

**Original _node2vec_**
* Grover, A. and Leskovec, J. (2016) node2vec: Scalable Feature Learning for Networks. ArXiv160700653 Cs Stat.
Original _node2vec_ software and networks
  * https://snap.stanford.edu/node2vec/ contains the original software and the networks (PPI, BlogCatalog, and Wikipedia) used in the original study (Grover and Leskovec, 2016).

**Other networks**
* Stark, C. et al. (2006) BioGRID: a general repository for interaction datasets. Nucleic Acids Res., 34, D535–D539.
  * BioGRID human protein-protein interactions.

* Szklarczyk, D. et al. (2015) STRING v10: protein–protein interaction networks, integrated over the tree of life. Nucleic Acids Res., 43, D447–D452.
  * STRING predicted human gene interactions.

* Greene, C.S. et al. (2015) Understanding multicellular function and disease with human tissue-specific networks. Nat. Genet., 47, 569–576.
  * GIANT-TN is a generic genome-scale human gene network. GIANT-TN-c01 is a sub-network of GIANT-TN where edges with edge weight below 0.01 are discarded.

BioGRID (Stark et al., 2006), STRING (Szklarczyk et al., 2015), and GIANT-TN (Greene et al., 2015) are available from https://doi.org/10.5281/zenodo.3352323.

* Law, J.N. et al. (2019) Accurate and Efficient Gene Function Prediction using a Multi-Bacterial Network. bioRxiv, 646687.
  * SSN200 is a cross-species network of proteins from 200 species with the edges representing protein sequence similarities. Downloaded from https://bioinformatics.cs.vt.edu/~jeffl/supplements/2019-fastsinksource/.
