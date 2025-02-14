# Salient Modifications
The Dockerfile has been updated with multiple targets:

* base: A base layer for other targets to build on.
* test: Adds the VIC sample data and packages required to run tests.
* vic: The default target, just includes VIC.

### Test Image
To build the test image, from the VIC top level directory, run:

```bash
docker build -f ci/Dockerfile --target test -t salient/vic_test:canary .
```

To run the tests:
```bash
docker run --rm salient/vic_test:canary
```

### VIC Image
To build the default VIC container image, from the VIC top level directory, run:

```bash
docker build -f ci/Dockerfile -t salient/vic:canary .
```

To run a sample:
```bash
docker run --rm -it salient/vic:canary bash

# Get the sample data
apt install -y git
git clone https://github.com/UW-Hydro/VIC_sample_data.git /VIC_sample_data

# Configure global parameter file
cat /VIC_sample_data/image/Stehekin/parameters/Stehekin_image_test.global.txt | \
    sed 's|${VIC_SAMPLE_DATA}|/VIC_sample_data|' | \
    sed 's|${VIC_SAMPLE_RESULTS}|/VIC_sample_data|' > \
    /VIC_sample_data/image/Stehekin/parameters/Stehekin_image_test.global2.txt

# Make output directory
mkdir /VIC_sample_data/sample_image/

# Execute the VIC image driver
/VIC/vic/drivers/image/vic_image.exe -g /VIC_sample_data/image/Stehekin/parameters/Stehekin_image_test.global2.txt

# Alternatively, execute the VIC image driver in parallel
mpiexec \
    --allow-run-as-root \
    -np 3 \
    /VIC/vic/drivers/image/vic_image.exe \
    -g /VIC_sample_data/image/Stehekin/parameters/Stehekin_image_test.global2.txt
```

# Variable Infiltration Capacity (VIC) Model

| VIC Links & Badges              |                                                                             |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| VIC Documentation      | [![Documentation Status](https://readthedocs.org/projects/vic/badge/?version=latest)](http://vic.readthedocs.org/en/latest/)                                                                             |
| VIC Users Listserve    | [![VIC Users Listserve](https://img.shields.io/badge/VIC%20Users%20Listserve-Active-blue.svg)](https://mailman.u.washington.edu/mailman/listinfo/vic_users)                                              |
| Developers Gitter Room | [![Join the chat at https://gitter.im/UW-Hydro/VIC](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/UW-Hydro/VIC?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge) |
| License                | [![GitHub license](https://img.shields.io/badge/license-MIT-blue.svg)](https://raw.githubusercontent.com/UW-Hydro/VIC/master/LICENSE.txt)                                                              |
| Current Release DOI    | [![DOI](https://zenodo.org/badge/7766/UW-Hydro/VIC.svg)](https://zenodo.org/badge/latestdoi/7766/UW-Hydro/VIC) |

----------

This repository serves as the public source code repository of the **Variable Infiltration Capacity Model**, better known as **VIC**. VIC documentation can be read on the [VIC documentation website](http://vic.readthedocs.org).

The Variable Infiltration Capacity (VIC) macroscale hydrological model (MHM) has been developed over the last two decades at the [University of Washington](http://uw-hydro.github.io/) and [Princeton University](http://hydrology.princeton.edu) in collaboration with a large number of other researchers around the globe. Development and maintenance of the official version of the VIC model is currently coordinated by the [UW Hydro | Computational Hydrology group](http://www.hydro.washington.edu) in the [Department of Civil and Environmental Engineering](http://www.ce.washington.edu) at the [University of Washington](http://www.washington.edu). All development activity is coordinated via the [VIC github page](https://github.com/UW-Hydro/VIC), where you can also find all archived, current, beta, and development versions of the model.

A skeletal first version of the VIC model was introduced to the community by [Wood et al. [1992]](http://dx.doi.org/10.1029/91JD01786) and a greatly expanded version, from which current variations evolved, is described by [Liang et al. [1994]](http://dx.doi.org/10.1029/94jd00483). As compared to other MHMs, VIC’s distinguishing hydrological features are its representation of subgrid variability in soil storage capacity as a spatial probability distribution to which surface runoff is related, and its parameterization of base flow, which occurs from a lower soil moisture zone as a nonlinear recession. Movement of moisture between the soil layers is modeled as gravity drainage, with the unsaturated hydraulic conductivity a function of the degree of saturation of the soil. Spatial variability in soil properties, at scales smaller than the grid scale, is represented statistically, without assigning infiltration parameters to specific subgrid locations. Over time, many additional features and representations of physical processes have been added to the model. VIC has been used in a large number of regional and continental scale (even global) hydrological studies. In 2016, VIC version 5 was released. This was a major update to the VIC source code focusing mainly on infrastructure improvements. The development of VIC-5 is detailed in [Hamman et al. 2018](https://doi.org/10.5194/gmd-11-3481-2018). A selection of VIC applications can be found on the [VIC references page](http://vic.readthedocs.org/en/latest/Documentation/References/).

Every new application addresses new problems and conditions that the model may not currently be able to handle, and as such the model is always under development. The VIC model is an open source development project, which means that contributions are welcome, including to the VIC documentation.

By placing the original source code archive on GitHub, we hope to encourage a more collaborative development environment. A tutorial on how to use the VIC git repository and how to contribute your changes to the VIC model can be found on the [working with git page](http://vic.readthedocs.org/en/latest/Development/working-with-git/). The most stable version of the model is in the `master` branch, while beta versions of releases under development can be obtained from the `development` branch of this repository.

VIC is a research model developed by graduate students, post-docs and research scientists over a long period of time (since the early 1990s). Every new VIC application addresses new problems and conditions which the model may not currently be able to handle. As a result, the model is always under development. Because of the incremental nature of this development, not all sections of the code are equally mature and not every combination of model options has been exhaustively tested or is guaranteed to work. While you are more than welcome to use VIC in your own research endeavors, ***the model code comes with no guarantees, expressed or implied, as to suitability, completeness, accuracy, and whatever other claim you would like to make***. In addition, the model has no graphical user interface, nor does it include a large set of analysis tools, since most people want to use their own set of tools.

While we would like to hear about your particular application (especially a copy of any published paper), we cannot give you individual support in setting up and running the model. The [VIC documentation website](http://vic.readthedocs.org) includes reasonably complete instructions on how to run the model, as well as the opportunity to sign up for the VIC Users Email List. The [VIC listserve](https://mailman.u.washington.edu/mailman/listinfo/vic_users) should be used for questions about model setup and application. It is basically VIC users helping other VIC users. All other exchanges about VIC source code are managed through the [VIC github page](https://github.com/UW-Hydro/VIC).

If you make use of this model, please acknowledge [Liang et al. [1994]](http://dx.doi.org/10.1029/94jd00483) and [Hamman et al. [2018]](https://doi.org/10.5194/gmd-11-3481-2018) plus any other references appropriate to the features you used that are cited in the [model overview](http://vic.readthedocs.org/en/latest/Overview/ModelOverview/).
