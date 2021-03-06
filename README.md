# CoreLM 

CoreLM is a flexible and reusable feed-forward neural network which can be used to train neural language models and joint models (Devlin et. al, 2014), and interface with popular SMT systems like [MOSES](http://www.statmt.org/moses/). It is implemented in Python using [Theano](http://deeplearning.net/software/theano/), which makes is easy-to-use and modify. 

## Features

* Implementation of self-normalized log-likelihood (Devlin et. al, 2014)  and noise contrastive estimation (NCE) loss functions, to train fast neural language models.
* Decoder Integration with MOSES using NeuralLM and BilingualLM feature functions in MOSES. Also, rescoring MOSES n-best lists using neural language models. 
* Efficient and optimized implementation using Theano, capable of using GPU support for faster training and decoding. 
* The neural network architecture is flexible. Multiple hidden layers and various activation function, multiple sets of features with different embeddings etc.
* The training is also flexible, with layer specific and adjustable learning rates, using various cost functions like log-likelihood and NCE and regularizations (L1 and L2). 
* Preprocessing scripts for monolingual language modeling and bilingual language modeling. 

## Getting Started

### Prerequisites
* Python Version 2.7 
* Theano (See [installation instructions](http://deeplearning.net/software/theano/install_ubuntu.html)) with CUDA support (to use GPU)

### Installation
1. Download and unzip CoreLM package in your local machine.  Alternatively, you can clone using GIT.
	```
	git clone https://github.com/nusnlp/corelm /path/to/corelm
	```

2. Add the CoreLM directory to PYTHONPATH environment variable. For bash users, add the following line to ~/.bashrc : 
```
export PYTHONPATH="${PYTHONPATH}:/path/to/corelm/"
```

## Using CoreLM


### Preprocessing

The preprocessing scripts can be found in [dlm/preprocess/](dlm/preprocess) directory. The following scripts are available. For detailed help, run the required script with `--help` option. 

* **[monolingual.py](dlm/preprocess/monolingual.py)** : This script preprocesses a text file for monolingual language modeling. The text file must contain one sentence per line.

* **[bilingual.py](dlm/preprocess/bilingual.py)** : This script preprocesses sentence aligned parallel corpora for bilingual language modeling. 

* **[features.py](dlm/preprocess/features.py)** : This script can be used for sequence labeling tasks. The input text file must have one sentence per line, and one per-word feature is accepted.  An example is shown below:
```
word1_feature1 word2_feature2 word3_feature3 ... wordN_featureN 
```
* **[convert_to_memmap.py](dlm/preprocess/convert_to_memmap.py)** : Custom inputs can be converted to input. The input must be a text file, with each line representing a training instance. The words or features must be replaced by corresponding indices according to the vocabulary file supplied. The format is as shown below:
```
word_index_11 word_index_12  ... word_index_1M output_word_index_1
...
...
word_index_N1 word_index_N2  ... word_index_NM output_word_index_N
```
where M is the number of input words and N is the number of training instances.


### Training
Training the neural network is done using the [train.py](train.py) script. The script takes in a memory-memory mapped file which is generated by the pre-processing scripts. Use `--help` for detailed list of options. 


### Testing
Evaluation of the neural network can be done using [test.py](test.py) script. It prints the perplexities and log-likelihood of the models on the test set. It optionally outputs the predicted labels. To predict lables of custom test instances use the [classify.py](classify.py) script. See --help for each script. 

### Integration with Moses
Integration of language and joint models trained using CoreLM is achieved by two methods, re-ranking n-best hypothesis and decoder integration.
* **Re-ranking** : To perform re-ranking of SMT n-best lists (in Moses format) using CoreLM models, first the weight of the new feature is to be trained using the [dlm/reranker/train.py](dlm/reranker/train.py). This can be done using MERT or PRO, which can be set using command-line options. After training the weights, the re-ranking can be done using [dlm/reranker/rerank.py](dlm/reranker/rerank.py). Refer to `--help` for these scripts for the list of options. 

* **Decoder Integration** : Currently, CoreLM uses the NPLM interface to Moses for integration. CoreLM models can be converted to NPLM format using [corelm_to_nplm.py](dlm/misc/corelm_to_nplm.py) script. This can be integrated using `NeuralLM` and `BilingualLM` feature functions in Moses (See [Moses documentation](http://www.statmt.org/moses/?n=FactoredTraining.BuildingLanguageModel)).




## License
This project is licensed under the MIT license - see the [LICENSE.md](LICENSE.md) file for details

