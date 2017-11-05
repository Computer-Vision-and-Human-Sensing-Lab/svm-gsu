# svm-gsu

*A C++ framework for training/testing the Support Vector Machine with Gaussian Sample Uncertainty (SVM-GSU).*

This is the implementation code for the Support Vector Machine with Gaussian Sample Uncertainty (SVM-GSU), whose linear variant (LSVM-GSU) was first proposed in [1], and its kernel version, i.e., Kernel SVM with Isotropic Gaussian Sample Uncertainty (KSVM-iGSU), was first proposed in [2]. If you want to use one of the above classifiers, please consider citing the appropriate [papers](#references).

Below, there are given detailed guidelines on how to [build](#0-prerequisites-and-build-guidelines) the code,  [prepare](#1-files-format) the input data files to the appropriate format (example files are given accordingly), and [use](#2-usage) the built binaries for training and/or testing SVM-GSU.  A [toy example](#toy-example) is given as a +++ .... A [Visualization tool](#visualization-of-lsvm-gsuksvm-igsu) written in Matlab is also given, along with some illustrative 2D toy examples. Short presentations of [LSVM-GSU](#a-linear-svm-with-gaussian-sample-uncertainty-lsvm-gsu-1) and [KSVM-iGSU ](#b-kernel-svm-with-isotropic-gaussian-sample-uncertainty-ksvm-igsu-2) are given below. For more detailed discussion of the above classifiers, please refer to the corresponding [papers](#references).



## 0. Prerequisites and build guidelines

This framework is built in [C++11](https://en.wikipedia.org/wiki/C%2B%2B11) using the [Eigen](http://eigen.tuxfamily.org/index.php?title=Main_Page) library. In order to build the code, you need to +++ ... 

```
- Eigen ??.??
- ???
```

### Linux

The framework was originally built in GNU/Linux (and has been tested on ArchLinux, Debian, and Ubuntu) using the [QtCreator IDE](https://www.qt.io/qt-features-libraries-apis-tools-and-ide/#ide). You may use QtCreator project files in order to build and/or modify the code. Otherwise, you may build code using +++ ...

### Windows

*Not available yet.*



## 1. Files format

The training set of SVM-GSU consists of the following three parts:

- A set of vectors that correspond to the **mean vectors** of the input data (input Gaussian distributions),
- A set of matrices that correspond to the **covariance matrices** of the input data (input Gaussian distributions), and 
- A set of binary **ground truth** labels that correspond to input data class labels.

We adopt a [libsvm](https://www.csie.ntu.edu.tw/~cjlin/libsvm/)-like file, plain-text format for the input data files. More specifically, for the above data files, we follow the formats described below. 

### Mean vectors file format

Each line of the mean vectors file consists of an n-dimensional feature vector, identified by a unique document id (`doc_id`), given in the following format: 

```
<doc_id> 1:<value> 2:<value> ... j:<value> ... n:<value>\n
```

The framework supports sparse representation for the feature vectors, i.e., a zero-valued feature can be omitted. For instance, the following line

~~~
feat_i 1:0.1 4:0.25 16:0.6
~~~

corresponds to a 16-dimensional feature vector that is associated with the document id *feat_i*. An example mean vectors file can be found [here]().

### Ground truth file format

Each line of the ground truth file consists of a binary `label` (in {+1,-1}) associated with a document id (`doc_id`):

~~~
<doc_id> <label>\n
~~~

An example ground truth file can be found [here]().

### Covariance matrices file format

Each line of the covariance matrices file consists of a matrix, which is identified by a unique document id (`doc_id`), and whose entries are given in the following format:

~~~
<doc_id> 1,1:<value> ... 1,j:<value> ... 1,n:<value> ... i,1:<value> ... i,j:<value> ... i,n:<value> ... n,1:<value> ... n,j:<value> ... n,n:<value>
~~~

Similarly to the mean vectors file, the framework adopts a sparse representation format for the covariance matrices. For instance, the following line

~~~
feat_i 1,1:0.125 2,2:0.5 3,3:2.25
~~~

corresponds to a 3x3 diagonal matrix that is associated with the document id *feat_i*. An example covariance matrices file can be found [here]().

**Note:** The document id (`doc_id`) that accompanies each line of the above data files, is used to identify each input datum (i.e., a triplet of a mean vector, a covariance matrix, and a truth label that describes an annotated multi-variate Gaussian distribution). In that sense, there is no need to put inout mean vectors, covariance matrices, and truth labels in correspondance. Furthermore, the framework will find the intersection between the given `doc_id`'s (i.e., the given mean vectors, covariance matrices, and truth labels) and will construct the training set appropriately. As an example, if the given data files are as follows

Mean vectors:

~~~
doc_1 1:0.13 3:0.12
doc_3 1:-0.21 2:0.1 3:-0.43
doc_5 1:0.11 2:-0.21
~~~

Ground truth labels:

~~~
doc_5 -1
doc_3 +1
doc_6 -1
~~~

Covariance matrices

~~~
doc_3: 1,1:0.25 2,2:0.01 3,3:0.1
doc_5: 1,1:0.25 2,2:0.01 3,3:0.1
~~~

then the framework will consider only the input data with ids `doc_3` and `doc_5`. Obviously, for a binary classification problem, the training set should include at least two training examples with different truth labels.



## 2. Usage

The framework consists of two basic components, one for training a SVM-GSU model [(gsvm-train)](#gsvm-train), and one for evaluating a trained SVM-GSU model on a given dataset [(gsvm-predict)](#gsvm-predict). Their basic usage is described below. In any case, information about their basic usage can also be obtained using the `-h` command line argument (i.e., `gsvm-train -h` and `gsvm-predict -h`).

### gsvm-train

Usage:

~~~
gsvm-train [options] <mean_vectors> <ground_truth> <covariance_matrices> <model_file>
~~~

Options:

~~~
+v <verbose_mode>: Verbose mode (default: 0)
+t <kernel_type>: Set type of kernel function (default 0)
	0 -- Linear kernel
    2 -- Radial Basis Function (RBF) kernel
+d <cov_mat>: Select covariance matrices type (default: 0)
	0 -- Full covariance matrices
	1 -- Diagonal covariance matrices
	3 -- Isotropic covariance matrices
+l <lambda>: Set the parameter lambda of SVM-GSU (default 1.0)
+g <gamma>: Set the parameter gamma (default 1.0/dim)
+T <iter>: Set number of SGD iterations
+k <k>: Set SGD sampling size
~~~



### gsvm-predict

Usage:

~~~
gsvm-predict [options] <mean_vectors> <model_file> <output_file>
~~~

Options:

~~~
-v <verbose_mode>: Verbose mode (default: 0)
-t <ground_truth>: Select ground truth file
-m <evaluation_metrics>: Evaluation metrics output file
~~~



### Toy example

In [toy_example]()






## A. Linear SVM with Gaussian Sample Uncertainty (LSVM-GSU) [1]

### Motivation

In our method we consider that our training examples are multivariate Gaussian distributions with known means and covariance matrices, each example having a different covariance matrix expressing the uncertainty around its mean. This is illustrated in the figure below

<p align="center">
  <img src="images/svmgsu_motivation.jpg" width="300" alt="SVM-GSU's motivation"/>
</p>

where the shaded regions are bounded by iso-density loci of the Gaussians, and the means of the Gaussians for examples of the positive and negative classes are located at "x" and "o" respectively. A classical linear SVM formulation (**LSVM**) would consider only the means of the Gaussians as training examples and, by optimizing the soft margin using the hinge loss and a regularization term, would arrive at the separating hyperplane depicted by the dashed line. In our formulation (**LSVM-GSU**), we optimize for the soft margin using the same regularization but the *expected* value of the hinge loss, where the expectation is taken under the given Gaussians. By doing so, we take into consideration the various uncertainties and arrive at a drastically different decision border, depicted by the solid line.  For a detailed presentation of LSVM-GSU, please refer to [1].



## B. Kernel SVM with Isotropic Gaussian Sample Uncertainty (KSVM-iGSU) [2]

*Not available yet.*



## Visualization of LSVM-GSU/KSVM-iGSU

A visualization tool build in Matlab is available under XXX/





## References

[1] Tzelepis, Christos, Vasileios Mezaris, and Ioannis Patras. "Linear Maximum Margin Classifier for Learning from Uncertain Data." *IEEE Transactions on pattern analysis and machine intelligence* XX.YY (2017): pppp-pppp.

[2] Tzelepis, Christos, Vasileios Mezaris, and Ioannis Patras. "Video event detection using kernel support vector machine with isotropic gaussian sample uncertainty (KSVM-iGSU)." *International Conference on Multimedia Modeling*. Springer, Cham, 2016.
