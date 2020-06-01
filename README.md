jointcal test data
==================

This repository contains data to test the performance of the [jointcal](http://github.com/lsst/jointcal) product. Test data for jointcal needs to have multiple visits of the same field, processed with the LSST DM stack to get VisitInfo metadata and source catalogs.
See the note in the dependency file `ups/testdata_jointcal.table` regarding the obs package dependencies of this product: this package has no explicit dependencies, but does require the appropriate obs packages to be `setup` in order to use the contained butler repositories.

Individual sets of testing data should be placed in their own directories, as butler-accessible repositories. They can then be loaded in testing code via the butler and the catalogs fed to jointcal. The image HDUs in the calexps in each repo have been set to identically zero and then compressed to save space, since the calexps are only needed for their metadata.

The directories contained in this repository are listed below, with a description of their contents.

The `cfht`, `decam`, and `hsc` directories each contain a `ref_cats/` directory with `sdss-dr9-fink-v5b`, `gaia_dr2_20191105` and `ps1_pv3_3pi_20170110` reference catalogs in indexed HTM format.
The sdss refcats were copied from their respective `validation_data_*/` repositories' `ref_cats/` directory.
The Gaia and PS1 refcats were extracted from the respective refcats on lsst-dev using the `scripts/extract-refcat-shards.py` script.

cfht
----

Source catalogs, metadata, and zeroed+compressed images derived from [validation_data_cfht](https://github.com/lsst/validation_data_cfht), processed with the w_2020_20 weekly.

The script `scripts/compress_jointcal_cfht_test_data.py` extracted the relevant files from the `validation_data_cfht` repository, removed the pixel-level data, and gzip compressed the catalogs to make their size reasonable. I saved a skyMap to `deepCoadds/`, generated by running:

```
makeDiscreteSkyMap.py cfht --output cfht/deepCoadd --id visit=850587^840375 ccd=12 --configfile makeSkyMapConfig.py
```

and this makeSkyMapConfig.py,

```
config.skyMap.projection='TAN'

# dimensions of inner region of patches (x,y pixels)
config.skyMap.patchInnerDimensions=[4000, 4000]

# nominal pixel scale (arcsec/pixel)
config.skyMap.pixelScale=0.185
```

decam
-----

Source catalogs, metadata, and zeroed+compressed images derived from [validation_data_decam](https://github.com/lsst/validation_data_decam), processed with the w_2020_20 weekly.

NOTE: this data has serious errors in the astrometry for `visit=0176837`.
This data does not represent good quality inputs into jointcal, but may be useful for testing how jointcal handles fitting failures.

The script `scripts/compress_jointcal_decam_test_data.py` extracted the relevant files from the `validation_data_decam` repository, removed the pixel-level data, and gzip compressed the catalogs to make their size reasonable. I saved a skyMap to `deepCoadds/`, generated by running:

```
makeDiscreteSkyMap.py cfht --output data/deepCoadd --id visit=0176837^0176846 --configfile makeSkyMapConfig.py
```

and this makeSkyMapConfig.py:


```
config.skyMap.projection='TAN'

# dimensions of inner region of patches (x,y pixels)
config.skyMap.patchInnerDimensions=[4000, 4000]

# nominal pixel scale (arcsec/pixel)
config.skyMap.pixelScale=0.263
```

hsc
---

Source catalogs, metadata, and zeroed+compressed images taken from the `w_2020_14`processing run of the HSC RC dataset, available at `lsst-dev:/datasets/hsc/repo/rerun/RC/w_2020_14/DM-24359-sfm`.
The included `scripts/compress_jointcal_hsc_test_data.py` file copies the necessary data from the butler repo, removes pixel-level data from the calexps and compresses the source catalogs, and extracts the relevant portions of the sqlite3 registry.
Finally, the src catalogs have been converted to sourceTable_visit parquet tables for quick ingest.  The included `scripts/make_sourcetables_jointcal_hsc_test_data.sh` file runs the appropriate transformation tasks and cleans up temporary files.

Git LFS
-------

To clone and use this repository, you'll need Git Large File Storage (LFS).

Our [Developer Guide](http://developer.lsst.io/en/latest/tools/git_lfs.html)
explains how to set up Git LFS for LSST development.
