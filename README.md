# Low cusp volume verification

This repository contains the verification code for the paper "Hyperbolic 3-manifolds of low cusp volume."
Along with the data, this code provides a proof of Theorems {} and {} of the paper. 

# Installation

There are no dependencies of this code outside of the standard C++ library.
There are four binaries that need to be made from the `src` directory:

```
make rootcat
make verify
make identify
make test_float
```
One should run `test_float` from the `bin` directory before and after any verification code to make sure that your system correctly catchest any overflow and underflow.
To run the proof of Theorem {}, from the `bin` direcotry execute:
```
./rootcat data_folder | ./verify ""
```
Note, this process is long and take 10-15 hours to finish.
Similarly, the proof of Theorem {}, from the `bin` directory:
```
./rootcat data_folder | ./identify ""
```
This runs in about 5 minutes.
For ease of use, a script is provided to runn all of these commands.

# Code

## Arithmetic

The arithmetic used in this repository is idenitcal to the one published in "Homotopy hyperbolic 3-manifolds are hyperbolic."
In fact, aside from fixing two minor typos and adding a missing library to `test_float.C`, the following files are identical to the versions published there:
```
Complex.h Complex.C Complex.inline
ACJ.h ACJ.C ACJ.inline
SL2ACJ.h SL2ACJ.C
test_float.C
```
The fixed typos are contained in the patch file `arithmetic.patch`.
Note, in "Homotopy hyperbolic 3-manifolds are hyperbolic," the authors use `CWEB` to generate the source code.
Here, we chose to leave the code more readble and discuss details in the appendix of the main paper.

## Parameter space code

We use the arithmetic above to verify a binary tree the leaf nodes of which represent (sub)boxes in the parameter space. See our paper for deals.

### rootcat

This program reads the binary tree as stored in a collection of text `.out` files, or compressed `.out.tar.gz` versions, and prints the tree out to `stdin` in depth-first format.
This format was chosen because the dataset is quite large.
Each internal node is represented by an `X` and each leaf node includes an elinimation criterion that will be checked by `verify` or `identify`.

The code for `rootcat` is entirely contained in `rootcat.c`.
It is written in `C` an should catch any and all reading errors if the data is corrupt.

### verify

This program verifies Theorem {}.
It expects to recieve the complete data tree in depth-first order (e.g. the output of `rootcat`)
At a leaf node, the program will take the binary coodrinate of the node and use it to construct the parameters corresponding to that box.
See the relevant code in `box.h` and `box.c`.
Validity of this code is discussed in the paper.

The program then rigorously validates the the condition encoded in the leaf node elimination criterion holds over the entire box.
Conditions 0-6 are bounrady condisitons that check if the box lies entirely outside of the valid parameter space on interest.
Lettered condisions are of the form `t(words)` where `t` encodes the type of condition and `words` is a word or list of words.
There are four types of lettered contitions, see the paper for details.
The code for verifying these conditions can be found in: `elimination.h` and `elimination.c`.

### test_float

This program checks whether your system correctly reports that roundoff error has occured.
Note, the roundoff checking function has been updated from the version in "Homotopy hyperbolic 3-manifolds are hyperbolic" to only include testing for x64 machines. 
