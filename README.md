# Anomaly Detection for Time-series (PyADT)

[![Build Status](https://travis-ci.com/larryshaw0079/PyADT.svg?branch=master)](https://travis-ci.com/larryshaw0079/PyADT)
[![Documentation Status](https://readthedocs.org/projects/pyadt/badge/?version=latest)](https://pyadt.readthedocs.io/en/latest/)
[![Coverage Status](https://coveralls.io/repos/github/larryshaw0079/PyADT/badge.svg?branch=master&service=github)](https://coveralls.io/github/larryshaw0079/PyADT?branch=master)
[![PyPI](https://img.shields.io/pypi/v/adtk)](https://pypi.org/project/PyADT/)
[![Downloads](https://pepy.tech/badge/pyadt)](https://pepy.tech/project/PyADT)
[![Code style: black](https://img.shields.io/badge/code%20style-black-000000.svg)](https://github.com/psf/black)
[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/larryshaw0079/PyADT/master?filepath=notebooks%2Fdemo.ipynb)

`PyADT` is aimed at accelerating the workflow of time series anomaly detection for researchers. It contains various utilities for data loading, pre-processing, detector construction, detector ensemble, evaluation and etc. `PyADT` can help you to write less boilerplate on following parts:

- Preparing dataset & pre-processing
- Feature extraction (Optional)
- Model training
- Ensemble (Optional)
- Evaluation

**Table of Contents**:

- [Anomaly Detection for Time-series (PyADT)](#anomaly-detection-for-time-series--pyadt-)
  * [Installation](#installation)
  * [Quick Start](#quick-start)
  * [Other Utilities](#other-utilities)
  * [Implemented Algorithms](#implemented-algorithms)

## Installation
To install the package locally, run:

```bash
cd <pyadt_dir>
pip install .
```

## Quick Start

You can find the [Documentation](https://pyadt.readthedocs.io/en/latest/) here.

### Fetch the dataset

`PyADT` contains various built-in datasets. To utilize them:

```python
from pyadt.datasets.repository import get_nab_nyc_taxi

data = get_nab_nyc_taxi(root_path='<the_path_of_nab_dataset>')
```

All components of the dataset are organized as a dict:

`{'value': value, 'label': label, 'timestamp': timestamp, 'datetime': datetime}`

### Pre-processing

It's important to pre-process the time series before training. `PyADT` offered three types of pre-processing methods including:

- Rearrangement: Sort the values along with the timestamp and reconstruct the timestamp to discover missing values. (return a dict and append an attribute `missing`)
- Normalization: Normalize the time series
- Imputation: Impute the time series.

```python
from pyadt.datasets.preprocessing import series_impute, series_normalize, series_rearrange

data_processed = series_rearrange(**data)

data_processed['value'] = series_normalize(data_processed['value'], mask=data_processed['missing'], method='zscore')

data_processed['value'] = series_impute(data_processed['value'], missing=data_processed['missing'], method='linear')
```

### Feature extraction

Extracting manual features is essential for some anomaly detection approaches. `PyADT` offered various options for extracting features including:

- Simple features: logarithm, difference, second-order difference, ...
- Window-based features: window mean value, window std value, ...
- Decomposition features: STL decomposition, ...
- Frequency domain features: wavelet features, spectral residual, ...
- Regression features: SARIMA regression residual, Exponential Smoothing residual, ...

```python
from pyadt.datasets.feature_extraction import FeatureExtractor

feature_extractor = FeatureExtractor()
```

### Train the model

Different anomaly detection algorithms should be utilized to tackle different scenarios. `PyADT` contains various algorithms including supervised-, unsupervised-, nonparametric-methods (you can refer the full list of [implemented algorithms](#implemented-algorithms)).

```python
from pyadt.model import ThresholdDetector

train_x = data['value']
detector = ThresholdDetector()
pred_y = detector.fit_predict(train_x)
```

### Ensemble

TODO

### Evaluation

It's easy to evaluate your algorithms using `PyADT`'s built-in metrics:

```python
from pyadt.evaluation.metrics import roc_auc

train_y = data['label']
roc = roc_auc(pred_y, train_y, delay=7)
```

In real-world applications, the delay of anomaly alerts is acceptable. So `PyADT` offered the `delay` argument for all metrics.

<img src="https://i.loli.net/2020/08/12/shGMx2QqjcP8tTe.png" style="zoom: 50%;" />

### The pipeline

TODO

## Other Utilities

### Visualization

You can visualize your data with a single line of code:

```python
from pyadt.datasets.utils import plot_series

fig = plot_series(value=data['value'], label=data['label'], datetime=data['datetime'], plot_vline=True)
fig.show()
```

The example visualization:

<img src="https://i.loli.net/2020/08/12/j78NoQsZHtR5lnv.png" style="zoom: 67%;" />

### Generate synthetic data

TODO

## Implemented Algorithms

### Supervised Approaches

- Random Forest
- SVM
- Deep Neural Network

### Unsupervised Approaches

#### Non-parametric

- SR
- Threshold
- Quantile
- K​-Sigma

#### Statistic-based

- SPOT
- DSPOT
- Autoregression
- ESD
- S-ESD
- S-H-ESD

#### Machine learning-based

- LOF
- Isolation Forest
- OCSVM

#### Deep-based

- Autoencoder
- RNN Autoencoder
- Donut

## TODO

- Fix bugs and reformat code
- Complete the models
- Incorporating Numba
- Implement the pipeline
- Synthetic data generator
