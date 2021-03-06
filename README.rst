Adapter for `Nucleic Acid Database <http://ndbserver.rutgers.edu/>`_
====================================================================

.. image:: https://img.shields.io/pypi/status/ndb_adapter.svg
    :target: https://pypi.python.org/pypi/ndb_adapter

.. image:: https://img.shields.io/pypi/v/ndb_adapter.svg
    :target: https://pypi.python.org/pypi/ndb_adapter

.. image:: https://img.shields.io/pypi/dm/ndb_adapter.svg
        :target: https://pypi.python.org/pypi/ndb_adapter

.. image:: https://img.shields.io/pypi/l/ndb_adapter.svg
        :target: https://pypi.python.org/pypi/ndb_adapter

.. image:: https://img.shields.io/pypi/pyversions/ndb_adapter.svg
    :target: https://pypi.python.org/pypi/ndb_adapter

Adapter for easy access to NDB resources directly from python (3.*).

Usage
-----

Adapter offers 4 types of search: summary, advanced, dna and rna.

Summary
~~~~~~~

To get summary of structure, type:

.. code-block:: python

    >>> from ndb_adapter import NDB
    >>> res = NDB.summary('4Z4B')
    >>> res.ndb_id
    '4Z4B'
    >>> res.title
    '2-PYRIDYL HOECHST - A NEW GENERATION DNA-BINDING RADIOPROTECTOR'
    >>> res.description
    "DNA (5'-D(*CP*GP*CP*AP*AP*AP*TP*TP*TP*GP*CP*G)-3')"
    >>> res.get_dict()
    {'Molecular Description': "DNA (5'-D(*CP*GP*CP*AP*AP*AP*TP*TP*TP*GP*CP*G)-3')", ...}

As you see some properties are available for summary result. Full list of them is
`here <http://michsior14.github.io/ndb_adapter/ndb_adapter.html#module-ndb_adapter.summary_result>`_.

Advanced search
~~~~~~~~~~~~~~~

To perform advanced search you should use `AdvancedOptions
<http://michsior14.github.io/ndb_adapter/ndb_adapter.html#module-ndb_adapter.advanced_search_options>`_
object and pass it as argument to `advanced_search
<http://michsior14.github.io/ndb_adapter/ndb_adapter.html#ndb_adapter.ndb.NDB.advanced_search>`_ function from NDB,
however it's not required. That object has a lot of `setters
<http://michsior14.github.io/ndb_adapter/ndb_adapter.html#ndb_adapter.advanced_search_options.AdvancedSearchOptions.set_author>`_
for modify search query. Look at them to search what you want. It's also possible to
change result `report type <http://michsior14.github.io/ndb_adapter/ndb_adapter.html#ndb_adapter.enums.ReportType>`_,
just pass wanted type into constructor.

.. code-block:: python

    >>> from ndb_adapter import *
    # options preparation
    >>> opt = AdvancedSearchOptions()   # default report type NDBStatus
    >>> opt.set_crystal_structure(yes_no_ignore=YesNoIgnore.Yes)    # default and_or=AndOr.And
    >>> opt.set_dna(and_or=AndOr.Or, yes_no_ignore=YesNoIgnore.Yes)

    # search
    >>> res = NDB.advanced_search(opt)
    >>> print(res.count)
    4695
    >>> print(res.report)
    [<ndb_adapter.search_report.NDBStatusReport object at 0x00000248F9E02128>, ...]

    # working on result
    >>> first = res.report[0] # first structure from search
    # if you want have annotations in IDE (i.e. Pycharm) do something like this:
    >>> first = res.report[0] # type: ReportType.NDBStatus
    >>> print(first.title)
    "2-Pyridyl Hoechst - a New Generation DNA-Binding Radioprotector"

Every report type result is different - you can examine theirs `properties
<http://michsior14.github.io/ndb_adapter/ndb_adapter.html#module-ndb_adapter.search_report>`_ or use typing.

Some of them has `statistics <http://michsior14.github.io/ndb_adapter/ndb_adapter.html#module-ndb_adapter.statistics>`_
also, for example:

.. code-block:: python

    >>> from ndb_adapter import *

    >>> opt = AdvancedSearchOptions(ReportType.RNABasePairRelFreq)
    >>> opt.set_hybrid(yes_no_ignore=YesNoIgnore.Yes)

    >>> res = NDB.advanced_search(opt)
    >>> print(res.statistics)
    Min: {'Relative cWW': 0.5, 'Relative tWW': 0.0, 'Relative cHS': 0.0, 'Relative tWS': 0.0, ...}
    Max: {'Relative cWW': 1.0, 'Relative tWW': 0.05, 'Relative cHS': 0.06, 'Relative tWS': 0.06, ...}
    Mean: {'Relative cWW': 0.8, 'Relative tWW': 0.01, 'Relative cHS': 0.01, 'Relative tWS': 0.01, ...}
    Standard Deviation: {'Relative cWW': 0.12, 'Relative tWW': 0.01, 'Relative cHS': 0.02, 'Relative tWS': 0.02, ...}
    >> print(res.statistics.min)
    {'Relative tWS': 0.0, 'Relative tWW': 0.0, 'Relative cHH': 0.0, 'Relative cWW': 0.5, ...}

Dna search
~~~~~~~~~~

`Dna search <http://michsior14.github.io/ndb_adapter/ndb_adapter.html#ndb_adapter.ndb.NDB.dna_search>`_
is very similar to advanced search, but with `DnaSearchOptions
<http://michsior14.github.io/ndb_adapter/ndb_adapter.html#module-ndb_adapter.dna_search_options>`_ and smaller
amount of setters.

.. code-block:: python

    >>> from ndb_adapter import *
    # options preparation
    >>> opt = DnaSearchOptions()
    >>> opt.set_structural_features(StructuralFeatures.A_DNA)

    # search
    >>> result = NDB.dna_search(opt)
    >>> print(result.count)
    393
    >>> str(result.report)
    [<ndb_adapter.search_report.SimpleReport object at 0x00000152D0FCB438>, ...]

    # working on result
    >>> first = result.report[0]   # type: SimpleReport
    >>> print(first.title)
    "Crystal structure of 60-mer BFDV Capsid Protein ..."

Rna search
~~~~~~~~~~

`Rna search <http://michsior14.github.io/ndb_adapter/ndb_adapter.html#ndb_adapter.ndb.NDB.rna_search>`_
is just like dna search. Options: `RnaSearchOptions
<http://michsior14.github.io/ndb_adapter/ndb_adapter.html#ndb-adapter-rna-search-options-module>`_.

.. code-block:: python

    >>> from ndb_adapter import *
    # search
    >>> result = NDB.rna_search()   # default is empty RnaSearchOptions object
    >>> print(result.count)
    3014
    >>> str(result.report)
    [<ndb_adapter.search_report.SimpleReport object at 0x0000018B3C877E48>, ...]

    # working on result
    >>> first = result.report[0]   # type: SimpleReport
    >>> print(first.title)
    "Crystal structure of the bacterial A1408C-mutant ..."

Structure Download
~~~~~~~~~~~~~~~~~~

Almost on every result of search you can download related files `(.pdb, .cif, .xml etc.)
<http://michsior14.github.io/ndb_adapter/ndb_adapter.html#ndb_adapter.ndb_download.DownloadType>`_
to buffer or save.

.. code-block:: python

    >>> from ndb_adapter import NDB
    >>> res = NDB.summary('4Z4B')
    >>> res.download()  # .pdb is default
    "HEADER DNA 01-APR-15 4Z4B ..."

    >>> from ndb_adapter import DownloadType
    >>> res.download(download_type=DownloadType.Cif)
    'data_4Z4B\n# \n_entry.id   4Z4B ...'

    >>> res = NDB.advanced_search()
    >>> res.download(save=True) # saves n files in current directory
    >>> res.download(save=True, target_dir='/home/user/Downloads/')  # saves n files in ~/Downloads/

    >>> res.report[0].download(save=True) # saves first structure file in current directory

You can also search and download in one line:

.. code-block:: python

    >>> from ndb_adapter import NDB
    >>> res = NDB.download('4Z4B')   # like above .pdb is default
    "HEADER DNA 01-APR-15 4Z4B ..."
    # save and target dir is also available

Requirements
------------

- python 3.*
- `requests <https://pypi.python.org/pypi/requests>`_
- `xlrd <https://pypi.python.org/pypi/xlrd>`_

Installation
------------

To install, simply:

.. code-block:: bash

    $ pip install ndb_adapter

Documentation
-------------

Documentation is available at https://michsior14.github.io/ndb_adapter/.

Licence
-------

Software is distributed under the MIT Licence.
