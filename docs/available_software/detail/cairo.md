---
hide:
- toc
json_ld:
  '@context': https://schema.org
  '@type': SoftwareApplication
  applicationCategory: DeveloperApplication
  description: Cairo is a 2D graphics library with support for multiple output devices.
    Currently supported output targets include the X Window System (via both Xlib
    and XCB), Quartz, Win32, image buffers, PostScript, PDF, and SVG file output.
    Experimental backends include OpenGL, BeOS, OS/2, and DirectFB
  license: Not confirmed
  name: cairo
  offers:
    '@type': Offer
    price: 0
  operatingSystem: LINUX
  review:
    '@type': Review
    author:
      '@type': Organization
      name: EESSI
    reviewBody: Application has been successfully made available on all architectures
      supported by EESSI
    reviewRating:
      '@type': Rating
      ratingValue: 5
  softwareRequirements: See https://www.eessi.io/docs/ for how to make EESSI available
    on your system
  softwareVersion: '[''cairo/1.17.4-GCCcore-12.2.0'', ''cairo/1.17.8-GCCcore-12.3.0'',
    ''cairo/1.18.0-GCCcore-13.2.0'']'
  url: https://cairographics.org
---

cairo
=====


Cairo is a 2D graphics library with support for multiple output devices. Currently supported output targets include the X Window System (via both Xlib and XCB), Quartz, Win32, image buffers, PostScript, PDF, and SVG file output. Experimental backends include OpenGL, BeOS, OS/2, and DirectFB

https://cairographics.org
# Available modules


The overview below shows which cairo installations are available per target architecture in EESSI, ordered based on software version (new to old).

To start using cairo, load one of these modules using a `module load` command like:

```shell
module load cairo/1.18.0-GCCcore-13.2.0
```

*(This data was automatically generated on {{ generated_time }})*

| |aarch64/generic|aarch64/neoverse_n1|aarch64/neoverse_v1|aarch64/nvidia/grace|x86_64/generic|x86_64/amd/zen2|x86_64/amd/zen3|x86_64/amd/zen4|x86_64/intel/cascadelake|x86_64/intel/haswell|x86_64/intel/icelake|x86_64/intel/sapphirerapids|x86_64/intel/skylake_avx512|
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
|cairo/1.18.0-GCCcore-13.2.0|x|x|x|x|x|x|x|x|x|x|x|x|x|
|cairo/1.17.8-GCCcore-12.3.0|x|x|x|x|x|x|x|x|x|x|x|x|x|
|cairo/1.17.4-GCCcore-12.2.0|x|x|x|x|x|x|x|x|x|x|x|x|x|
