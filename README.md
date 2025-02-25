# OSPSuite-R

  <!-- badges: start -->

[![AppVeyor build status](https://ci.appveyor.com/api/projects/status/github/Open-Systems-Pharmacology/OSPSuite-R?branch=develop&svg=true)](https://ci.appveyor.com/project/open-systems-pharmacology-ci/ospsuite-r)
[![codecov](https://codecov.io/gh/Open-Systems-Pharmacology/OSPSuite-R/branch/develop/graph/badge.svg)](https://codecov.io/gh/Open-Systems-Pharmacology/OSPSuite-R)

  <!-- badges: end -->

# Overview
The **ospsuite** R-package provides the functionality of loading, manipulating, and simulating the simulations created in the Open Systems Pharmacology Software tools PK-Sim and MoBi.

# Installation
**As of now, R 4.x.x is not supported by ospsuite! The latest supported R version is 3.6.3.**

**ospsuite** requires following packages to be installed:

- [rClr](https://github.com/Open-Systems-Pharmacology/rClr/releases/latest)
- [R6](https://github.com/r-lib/R6)

## Under Windows
The release version of the package comes as a binary *.zip and can be downloaded from [here](https://github.com/Open-Systems-Pharmacology/OSPSuite-R/releases). The package `rClr` can be downloaded from [here](https://github.com/Open-Systems-Pharmacology/rClr/releases/latest) and installed from binary *.zip. The package `R6` can be installed from CRAN.

The package also requires the Visual C++ Runtime that is installed with OSPS and can be manually downloaded [here](https://aka.ms/vs/16/release/vc_redist.x64.exe).

```
# Install dependencies
install.packages(pathTorCLR.zip, repos = NULL)
install.packages('R6')
# Install ospsuite-r from local file
install.packages(pathToOSPSuite.zip, repos = NULL)
```

## Under Linux
The **ospsuite** package has been tested under Linux distributions CentOS 7 and Ubuntu 18. Some functionality, such as creating individuals, is not availble under Linux. Installation under Linux requires several prerequisites, the detailed instructions can be found in the [Wiki](https://github.com/Open-Systems-Pharmacology/OSPSuite-R/wiki/Setup-ospsuite-R-on-Ubuntu).
For other Linux distributions Docker containers can be used (Dockerfiles based on CentOS 7 and Ubuntu 18 are available under https://github.com/Open-Systems-Pharmacology/OSPSuite-R/releases )

## Build from source
You can clone the GIT repository and build the package from source.

### How to update dependencies from nuget?
- `git submodule update --init --recursive` to install all submodules
- Make sure you have [ruby](https://www.ruby-lang.org/de/downloads/) install and that it is available in your path
- Run `rake postclean` or simply double click on `postclean.bat`. This will update all nuget packages and copy the dependencies in the package `inst/lib` folder.

# Usage
In general, every workflow starts with loading a simulation that has been exported to the `*.pkml` format. The method `loadSimulation()`  returns the corresonding simulation that is used as input of other methods. The then can change values of parameters and initial conditions, run the simulation, and retrieve the simulated results.

```{r loadSim}
library(ospsuite)

# Load a simulation
dataPath <- file.path(path.package("ospsuite", quiet = FALSE), "extdata", fsep = .Platform$file.sep)
simFilePath <- file.path(dataPath, "Aciclovir.pkml", fsep = .Platform$file.sep)
sim <- loadSimulation(simFilePath)

# Get the parameter "Dose"
doseParamPath <- "Applications|IV 250mg 10min|Application_1|ProtocolSchemaItem|Dose"
doseParam <- getParameter(doseParamPath, sim)

# Change the dose to 350mg. The values has to be converted to base unit, first
newValue <- toBaseUnit(quantity = doseParam, values = 350, unit = "mg")
setParameterValues(parameters = doseParam, values = newValue)

# Simulate
simResults <- runSimulation(simulation = sim)
# Retrieve the results
simulatedValues <- getOutputValues(simulationResults = simResults)

# Plot time-concentration profile
plot(simulatedValues$data$Time, simulatedValues$data$`Organism|PeripheralVenousBlood|Aciclovir|Plasma (Peripheral Venous Blood)`,
type = "l",
xlab = "Time [min]",
ylab = "Concentration [µmol/l]")
```
![](man/figures/README-example-1.png)<!-- -->

More detailes description of the methods and the typical workflows can be found in the vignettes. You can see the list of all vigenttes available for **ospsuite** by calling

```
vignette(package = "ospsuite")
```

To open a specific vignette, call

```
# Insert the name of the vignette you want to view as the argument
vignette("introduction-ospsuite")
```

## Code of conduct

Everyone interacting in the Open Systems Pharmacology community (codebases, issue trackers, chat rooms, mailing lists etc...) is expected to follow the Open Systems Pharmacology [code of conduct](https://github.com/Open-Systems-Pharmacology/Suite/blob/master/CODE_OF_CONDUCT.md).

## Contribution

We encourage contribution to the Open Systems Pharmacology community. Before getting started please read the [contribution guidelines](https://github.com/Open-Systems-Pharmacology/Suite/blob/master/CONTRIBUTING.md). If you are contributing code, please be familiar with the [coding standards](https://github.com/Open-Systems-Pharmacology/Suite/blob/master/CODING_STANDARDS_R.md).

## License

OSPSuite-R is released under the [GPLv2 License](LICENSE).

All trademarks within this document belong to their legitimate owners.
