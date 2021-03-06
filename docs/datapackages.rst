===============================================================================
Published Data Packages
===============================================================================

We've chosen `tabular data packages <https://frictionlessdata.io/specs/tabular-data-package/>`__ as the main distribution format for PUDL because they:

* are based on a free and open standard that should work on any platform,
* are relatively easy for both humans and computers to understand,
* are easy to archive and distribute,
* provide rich metadata describing their contents,
* do not force users into any particular platform.

We our hope this will allow the data to reach the widest possible audience.

.. seealso::

    The `Frictionless Data <https://frictionlessdata.io/>`__ software and
    specifications, a project of
    `the Open Knowledge Foundation <https://okfn.org>`__

We intend to publish tabular data packages on a quarterly basis, containing the
full outputs from the PUDL ETL pipeline via `Zenodo <https://zenodo.org>`__,
and open data archiving service provided by CERN. The most recent release can
always be found through this concept DOI:
`10.5281/zenodo.3653158 <https://doi.org/10.5281/zenodo.3653158>`__. Each
individual version of the data releases will be assigned its own unique DOI.

Users who aren't working with Python, or who don't want to set up and run the
data processing pipeline themselves can download and use the data packages
directly. We provide scripts alongside the published data packages that will
load them into a local SQLite database or for the larger datasets, convert them
into `Apache Parquet <https://parquet.apache.org/>`__ datasets on disk which
can be read directly into
`Pandas <https://pandas.pydata.org>`__,
`Dask <https://dask.org>`__, or
`R dataframes <https://www.rdocumentation.org/packages/base/versions/3.6.2/topics/data.frame>`__.

We archive the original input data and provide a one-line script which should
allow users to replicate the entire ETL process, generating byte-for-byte
identical outputs. See the documentation published with the data releases for
details on how to load or reproduce the data packages.

We also curate the
`Catalyst Cooperative Community on Zenodo <https://zenodo.org/communities/catalyst-cooperative/>`__
which lists all of the archived products generated by our projects.

The these archives and the DOIs associated with them should be permanently
accessible, and are suitable for use as references in academic and other
publications.

-------------------------------------------------------------------------------
Using Data Packages
-------------------------------------------------------------------------------

Once you've downloaded or generated your own tabular data packages you can use
them to do analysis on almost any platform. For now, we are primarily using
the data packages to populate a local SQLite database.

`Open an issue on Github <https://github.com/catalyst-cooperative/pudl/issues>`__ and let us know if you have another example we can add.

SQLite
^^^^^^

If you want to access the data via SQL, we have provided a script that loads
several data packages into a local :mod:`sqlite3` database. Note that these
data packages **must** have all been generated by the **same** ETL run, or they
will be considered incompatible by the script. For example, to load three
data packages generated by our example ETL configuration into your local SQLite
DB, you could run the following command from within your PUDL workspace:

.. code-block:: console

    $ datapkg_to_sqlite \
        -o datapkg/pudl-example/pudl-merged \
        datapkg/pudl-example/ferc1-example/datapackage.json \
        datapkg/pudl-example/eia-example/datapackage.json \

The path after the ``-o`` flag tells the script where to put the merged
data package, and the subsequent paths to the various ``datapackage.json``
files indicate which data packages should be merged and loaded into SQLite.

Apache Parquet
^^^^^^^^^^^^^^

The :ref:`data-epacems` data approaches 100 GB in size, which is too large to
work with directly in memory on most systems, and take a very very long time
to load into SQLite. Instead, we recommend converting the Hourly Emissions
table into an `Apache Parquet <https://parquet.apache.org>`__ dataset which is
stored on disk locally, and either reading in only parts of it using pandas,
or using `Dask dataframes <https://dask.org>`__, to serialize or distribute
your analysis tasks. Dask can also speed up processing for in-memory tasks,
especially if you have a powerful system with multiple cores, a solid state
disk, and plenty of memory.

If you have generated an EPA CEMS data package, you can use the
``epacems_to_parquet`` script to convert the hourly emissions table like this:

.. code-block:: console

    $ epacems_to_parquet datapkg/pudl-example/epacems-eia-example/datapackage.json

The script will automatically generate a Parquet Dataset which is partitioned
by year and state in the ``parquet/epacems`` directory within your workspace.
Run ``epacems_to_parquet --help`` for more details.

Microsoft Access / Excel
^^^^^^^^^^^^^^^^^^^^^^^^^

If you'd rather do spreadsheet based analysis, here's how you can pull the
data packages into Microsoft Access for use with Excel and other Microsoft
tools:

.. todo::

    Document process for pulling data packages or datapackage bundles into
    Microsoft Access / Excel. If you've gotten this to work and would like to
    contribute an example, please let us know!

Other Platforms
^^^^^^^^^^^^^^^

Because the data packages we're publishing right now are designed as
well normalized relational database tables, pulling them directly into e.g.
Pandas or R dataframes for interactive use probably isn't the most useful
thing to do. In the future we intend to generate and publish data packages
containing denormalized tables including values derived from analysis of the
original data, post-ETL. These packages would be more suitable for direct
interactive use.

Want to submit another example? Check out :doc:`the documentation on
contributing <CONTRIBUTING>`. Wish there was an example here for your favorite
data analysis tool, but don't know what it would look like? Feel free to
`open a Github issue <https://github.com/catalyst-cooperative/pudl/issues>`__
requesting it.
