# meta-fbvuln

An OpenEmbedded layer containing a class for collecting vulnerability management metadata for continuous vulnerability scanning of target images.

The processing performed by this class is derived from the `cve-check.bbclass` in [oe-core](https://github.com/openembedded/openembedded-core).

## Usage

Add this layer to your bblayers.conf:
```sh
echo 'BBLAYERS += "/path/to/meta-fbvuln"' >> conf/bblayers.conf
```

Interit this class in local.conf:
```sh
echo 'INHERIT += "fbvuln-manifest"' >> conf/local.conf
```

Build an image as you normally would:
```sh
bitbake core-image-minimal
```

Once your build has completed you can locate the generated vulnerability tracking files:
```sh
ls -1 tmp/deploy/images/*/*.fbvuln.csv
```

You will find one vulnerability tracking file per image built for the target.  Within this file are comma-separated lines containing six fields: vendor, product, version, software environment, hardware environment and patched vulnerabilities.

As an example, Cairo may appear as:
```
,cairo,1.16.0,linux_kernel,x64,CVE-2017-7475 CVE-2018-19876 CVE-2019-6461 CVE-2019-6462
```

The above snippet shows that no vendor is needed to disambiguate the cairo CPE.  The software has been compiled to run under Linux on an x86-64 system and has had four vulnerabilities addressed through patching.

Another representative example is Chromium (from [meta-browser](https://github.com/OSSystems/meta-browser)):
```
google,chrome,80.0.3987.132,linux_kernel,x64,
chromium,chromium,80.0.3987.132,linux_kernel,x64,
```

In the case of Chromium, you'll see that two tracking entries are produced: one for Chrome official releases and one for Chromium (vulnerabilities are often tracked against one, but not the other).

Collected tracking data should be regularly fed through a vulnerability analysis pipeline while your software images are deployed and supported.  You may wish to consider the [nvdtools project](https://github.com/facebookincubator/nvdtools) for this scanning, using the csv2cpe and cpe2cve tools to process your data from a scheduled job.

## Configuration

You'll very likely want to set the CPE target hardware and software environments in your generated vulnerability tracking files.  You can do so by setting the CPE_TARGET_SW and CPE_TARGET_HW variables (for example, in your local.conf - using x86-64) as follows:

```sh
echo 'CPE_TARGET_SW ?= "linux_kernel"' >> conf/local.conf
echo 'CPE_TARGET_HW ?= "x64"' >> conf/local.conf
```

There's no easily searchable source of valid target hardware (or software!) enumerations available.  If you leave either of these out you risk hitting false positives, but if you get either of these wrong you risk false negatives.  Your best bet it to search the NIST NVD database for example values.

## License

meta-fbvuln licensed under the MIT license, as found in the [LICENSE](LICENSE) file.
