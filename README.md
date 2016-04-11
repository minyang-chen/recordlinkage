# recordlinkage

This **recordlinkage** package is a library to link records in or between data sources. The package provides most of the tools needed for record linkage. The package contains indexing methods, functions to compare records and classifiers. The package is developed for research and linking of small or medium sized files. 

This project is inspired by the [Freely Extensible Biomedical Record Linkage (FEBRL)](https://sourceforge.net/projects/febrl/) project, which is a great project. This project has one big difference, it uses ``pandas`` and ``numpy`` for data handling and computations. The use of ``pandas``, a flexible and powerful data analysis and manipulation library for Python, makes the record linkage process much easier and faster. A lot of built-in ``pandas`` methods can be used to integrate your record linkage directly into existing data manipulation projects.  

One of the aims of this project is to make an extensible record linkage framework. It is easy to include your own indexing algorithms, comparison/similarity measures and classifiers. 

## Basic linking example
Import the ``recordlinkage`` module with all important tools for record linkage and import the data manipulation framework **pandas**. 

```python
import recordlinkage
import pandas
```
For examples, you try to link two datasets with personal information like name, sex and date of birth. Load these datasets into a pandas ``DataFrame``.
```python 
dfA = pandas.DataFrame(YOUR_FIRST_DATASET)
dfB = pandas.DataFrame(YOUR_SECOND_DATASET)
```

Comparing all record can be computationally intensive. Therefore, we make smart set of candidate links with one of the built-in indexing techniques like **blocking**. Only records pairs that agree on the surname are included. 

```python
index = recordlinkage.Index(dfA, dfB)
candidate_links = index.block('surname')
```

For each candidate link, compare the pair of records with the Compare class and the available comparison/similarity functions.
```python
compare = recordlinkage.Compare(candidate_links, dfA, dfB)

compare.fuzzy('name', 'name', method='jarowinkler', threshold=0.85)
compare.exact('sex', 'gender')
compare.exact('dob', 'date_of_birth')
compare.fuzzy('streetname', 'streetname', method='damerau_levenshtein', threshold=0.7)
compare.exact('place', 'placename')
compare.exact('haircolor', 'haircolor', missing_value=9)

# The comparison vectors
compare.vectors
```

This record linkage package contains several classification alogirthms. Plenty of the algorithms need trainings data (supervised learning) while others are unsupervised. An example of supervised learning:

```python

true_linkage = pandas.Series(YOUR_GOLDEN_DATA, index=pandas.MultiIndex(YOUR_MULTI_INDEX))

logrg = recordlinkage.LogisticRegressionClassifier()
logrg.learn(compare.vectors[true_linkage.index], true_linkage)

logrg.predict(compare.vectors)
```

and an example of unsupervised learning (the well known ECM-algorithm):
```python

ecm = recordlinkage.BernoulliEMClassifier()
ecm.learn(compare.vectors)

```

## Main Features
The main features of the **recordlinkage** package are:

  - Clean and standardise data
  - Make pairs of records with several indexing methods such as **blocking** and **sorted neighbourhood indexing**
  - Compare records with a large number of comparison and similarity functions (including the jaro-winkler and levenshtein metrics)
  - Several classifications algorithms, both supervised and unsupervised algorithms. 

## Documentation, dependencies, installation and license
The most recent documentation can be found at [recordlinkage.readthedocs.org](http://recordlinkage.readthedocs.org/en/latest/).

The following packages are required. You probably have it already ;)
- [numpy](http://www.numpy.org): 1.7.0 or higher
- [pandas](https://github.com/pydata/pandas): 0.17.0 or higher
- [scipy](https://www.scipy.org/)
- [sklearn](http://scikit-learn.org/)

The following packages are recommanded
- [jellyfish](https://github.com/jamesturk/jellyfish): Needed for approximate string comparison. Version 0.5.0 or higher.

It is not possible to install the package with ``pip`` (coming soon). You can download or clone the **recordlinkage** project and install it in the normal way

```sh
python setup.py install
```
The license for this record linkage tool is GPLv3.
