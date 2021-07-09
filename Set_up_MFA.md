# Set up MFA

## Setting up a Python environment with MFA
Follow the [instruction from the MFA document](https://montreal-forced-aligner.readthedocs.io/en/latest/installation.html) The installation of Kaldi is slightly more complicated. You can find an instruction [here](Install_Kaldi).

## Download resources required for alignment
After setting up MFA. We still need to download some more resources before we can start alignment. Using English as an example:
1. Download the pre-trained acoustic model by running ```mfa download acoustic english```.
2. Download the pronunciation dictionary by running ```mfa download dictionary english```.
