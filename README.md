[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/mesmith75/ic-teach-kstmumu-public/main?filepath=starter_notebook.ipynb)

# Imperial College Third Year Problem Solving

Welcome to the 3rd year physics problem solving course. The aim of the course is to perform a simplified version of a real High Energy Physics analysis carried out by members of the LHCb collaboration here at Imperial.

## The LHCb collaboration

The [LHCb collaboration](http://lhcb-public.web.cern.ch/) is formed of approximately 1000 physicists from institutes all over the world, including a strong presence [here at Imperial](https://www.imperial.ac.uk/high-energy-physics/research/experiments/lhcb/). Together they contribute to the successful running of the LHCb detector and analyses the vast quantities of data it produces. The LHCb experiment is one of the 4 large experiments at the LHC particle collider operating at CERN, near Geneva. 

## Useful links - example code

- An example of some fit code can be found [here](https://github.com/mesmith75/ic-teach-kstmumu-public/blob/main/starter_notebook.ipynb).
- The data is loaded into a [`pandas.DataFrame`](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.html).
- Python arrays can be readily manipulated with [`numpy`](https://numpy.org/)
- To minimise the likelihood the example code uses the [`iminiuit`](https://pypi.org/project/iminuit/) package.
- Plots are made with [`matplotlib.pyplot`](https://matplotlib.org/stable/api/_as_gen/matplotlib.pyplot.html).

## Example code

An simple example of a fit to some toy data is provided. The toy data is located in the [kstarmumu_toy_data](kstarmumu_toy_data) folder. You can download it to run it locally or use the binder link here: [![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/mesmith75/ic-teach-kstmumu-public/main?filepath=starter_notebook.ipynb).

The most pertinent part is the calculation of the negative log-likelihood and the minimisation.

The likelihood is calculated in the function below:
```python
def log_likelihood(fl, afb, _bin):
    """
    Returns the negative log-likelihood of the pdf defined above
    :param fl: f_l observable
    :param afb: a_fb observable
    :param _bin: number of the bin to fit
    :return:
    """
    _bin = bins[int(_bin)]
    ctl = _bin['ctl']
    normalised_scalar_array = d2gamma_p_d2q2_dcostheta(fl=fl, afb=afb, cos_theta_l=ctl)
    return - np.sum(np.log(normalised_scalar_array))
```
This uses `numpy` to apply the PDF function, `d2gamma_p_d2q2_dcostheta`, with some values of *FL* and *AFB* to the data array and sum it efficiently. This is then passed to `minuit` that minimises the negative log-likelihood by varying *FL* and *AFB*:
```python
    m = Minuit(log_likelihood, fl=starting_point[0], afb=starting_point[1], _bin=i)
    m.fixed['_bin'] = True  # fixing the bin number as we don't want to optimize it
    m.limits=((-1.0, 1.0), (-1.0, 1.0), None)
    m.migrad()
    m.hesse()
```
Here `migrad` is the operation that searches for the minimum of the `log_likelihood` function. Having found it, `hesse` is run to find the uncertainties on the fit parameters at the minimum.

## Predictions

A set of Standard Model predictions for the angular observables are located in the [predictions](predictions) folder along with a small notebook to load and read them.

## Data

The meanings of the variables in the data are described in the [Branches](Branches/README.md) folder. A list of the data files are in the [samples](samples/README.md) folder.

## Software versions

The data is in a format that does not depend on the python version. The versions of the packages in the skeleton code are listed below that the binder image uses are listed below. In general the previse version numbers should not matter - any fairly recent version of these packages should suffice.

```yml
name: python 3.8
dependencies:
  - python=3.8
  - numpy=1.21.2
  - pandas=1.3.2
  - matplotlib=3.4.3
  - pip
  - pip:
      - iminuit==2.8.2
      - sklearn
```
