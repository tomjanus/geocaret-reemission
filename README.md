<div id="top"></div>

<!-- PROJECT SHIELDS -->
[![Contributors][contributors-shield]][contributors-url]
[![Forks][forks-shield]][forks-url]
[![Stargazers][stars-shield]][stars-url]
[![Issues][issues-shield]][issues-url]
[![GPL-3.0 License][license-shield]][license-url]

# GeoCARET-ReEmission with Docker <i class="fa-brands fa-docker"></i>

<!-- ABOUT THE PROJECT -->
## About The Library

This repository provides instructions for running two software packages: [GeoCARET](https://github.com/reservoir-research/geocaret) and [RE-Emission](https://github.com/tomjanus/reemission) from their respective [Docker images](https://www.techtarget.com/searchitoperations/definition/Docker-image). 

[GeoCARET](https://github.com/reservoir-research/geocaret) is an open-source software for geospatial analysis of reservoirs and catchments. [RE-Emission](https://github.com/tomjanus/reemission) is an open-source tool for estimating, reporting and visualising reservoir emissions. These tools can be used sequentially to estimate emissions from multiple reservoirs. GeoCARET processes geospatial datasets to generate input data for emission models, which RE-Emission then uses to calculate, report and visualize emissions. 

Both tools are written in Python and can be installed as Python packages. To simplify and speed up the installation process, we also provide their Docker images (see [here](https://github.com/Reservoir-Research/geocaret/pkgs/container/geocaret) for GeoCARET and [here](https://github.com/tomjanus/reemission/pkgs/container/reemission) for RE-Emission) which enable running both tools via command-line in isolated environments. 

This repository outlines how to run both packages as Docker containers and provides the folder structure required to facilitate data exchange between your local file system and the containerized applications.

## Pre-Requisites

### Docker Desktop, Docker Engine & Docker Compose

Before running the software, you need a working Docker installation. If you are using Windows or macOS, you should install [Docker Desktop](https://www.docker.com/products/docker-desktop/), which provides an easy-to-use interface for managing Docker on desktop operating systems. Please visit [Docker's official documentation](https://docs.docker.com/get-docker/) and follow the appropriate instructions for installing Docker Desktop on your system. Linux users also have the option of installing [Docker Engine](https://docs.docker.com/engine/) and [Docker Compose](https://docs.docker.com/compose/install/) as an alternative.

Docker can be set to start automatically at system startup. If you choose not to enable this option, you’ll need to launch it manually before use. To check if Docker is running, open your console or terminal and type `docker -v`.

### Earth Engine Account Setup

Additionally, to run GeoCARET, you will need to set up an account with Google Earth Engine / Google Cloud and request access to the private assets hosted in our project folder. Detailed instructions on how to do this are available in the following documentation pages:

- [Setting up Earth Engine and Google Cloud](https://reservoir-research.github.io/geocaret/installation/account_setup.html)
- [Requesting access to private assets](https://reservoir-research.github.io/geocaret/installation/access_to_assets.html)

<p align="right">(<a href="#top">back to top</a>)</p>

## Fetching / Building Docker Images

It is recommended to fetch the Docker images directly from the GitHub Container Registry (GHCR) using the following commands:

```bash
docker pull ghcr.io/reservoir-research/geocaret
docker pull ghcr.io/tomjanus/reemission
```

Alternatively, you can build the images from source by downloading the source code for each package from their respective GitHub repositories. Instructions for building GeoCARET can be found [here](https://reservoir-research.github.io/geocaret/installation/building_image.html), and for RE-Emission [here](https://tomjanus.github.io/reemission/installation/building_image.html).

## Folder structure

Running both applications requires a specific folder structure that maps directories on your local file system to corresponding locations inside the Docker containers, enabling data exchange, e.g. reading inputs and saving outputs. This mapping is defined in two separate `compose.yaml` files—one for each application.

The folder structure includes an `demo.csv` input file for GeoCARET demonstration run and a `test_input.json` input file for testing RE-Emission. You can also create your own input files for both GeoCARET and RE-Emission, placing them in the appropriate folders as shown in the file tree below.

``` bash
geocaret-reemission (root)
├── geocaret
│   ├── auth
│   ├── compose.yml
│   ├── data
│   │   ├── demo.csv
│   │   └── README.md
│   └── outputs
└── reemission
    ├── compose.yaml
    ├── examples
    │   └── test_input.json
    └── outputs
```

The `geocaret/data` and `geocaret/outputs` directories are used to store the input and output data for GeoCARET calculations, respectively. The `geocaret/auth` folder holds authentication credentials for Earth Engine and Google Cloud. Similarly, `reemission/examples` and `reemission/outputs` store the input and output data for RE-Emission.

<p align="right">(<a href="#top">back to top</a>)</p>

## Usage

### Running GeoCARET

To test everything is working correctly, you should first run the following command from inside the GeoCARET workspace folder `geocaret`:

```bash
docker compose run --rm geocaret
```

<div class="warning" style='background-color:#f3ffe6; color: #284d00; border-left: solid #428000 4px; border-radius: 5px; padding:0.1em; font-size:Small; margin-bottom:1em'>
<span>
<p style='margin-top:0.4em; text-align:center'>
<b>Note <i class='far fa-bell'></i></b></p>
<p style='margin-left:1em;'>
You should see the following message: <i>You must specify a command to run. See https://Reservoir-Research.github.io/geocaret/running_geocaret/running_docker.html for details</i> and GeoCARET will exit.
</p></span>
</div>

More information about running GeoCARET with Docker can be found in the [GeoCARET's documentation](https://reservoir-research.github.io/geocaret/running_geocaret/running_docker.html).

### Running RE-Emission

To test if the RE-Emission Docker image is working, run RE-Emission using `docker compose`, similar to how you ran GeoCARET. Start from the `reemission` folder:

```bash
docker compose run --rm reemission
```

You should see a short usage guide 

```
Usage: reemission [OPTIONS] COMMAND [ARGS]...
```
This will be followed by additional details about the available options and commands for the RE-Emission command-line interface (CLI). More information on running RE-Emission with Docker can be found [here](https://tomjanus.github.io/reemission/running_docker.html). 

#### Simple analysis

You can run a test analysis using the input data provided in `./examples/test_input.json`. From within the `reemission` folder, enter the following command:

```bash
docker compose run --rm reemission reemission calculate examples/test_input.json -o outputs/test_output.json -o outputs/test_output.xlsx
```

The analysis should complete successfully and you should find two output files - `test_output.json` and `test_output.xlsx` - in the `outputs` folder.

<div class="warning" style='background-color:#f7e8c8; color: #573c06; border-left: solid #a6730f 4px; border-radius: 5px; padding:0.1em; font-size:Small; margin-bottom:1em'>
<span>
<p style='margin-top:0.4em; text-align:center'>
<b>Atttention <i class="fas fa-exclamation-triangle"></i></b></p>
<p style='margin-left:1em;'>
The two instances of "reemission" in the command are intentional. The first refers to the name of the running container while the second invokes the Re-Emission's CLI.
</p></span>
</div>

The output files include the input, intermediate, and final output variables in both `.json` and `Excel` formats.

<div class="warning" style='background-color:#f3ffe6; color: #284d00; border-left: solid #428000 4px; border-radius: 5px; padding:0.1em; font-size:Small; margin-bottom:1em'>
<span>
<p style='margin-top:0.4em; text-align:center'>
<b>Note <i class='far fa-bell'></i></b></p>
<p style='margin-left:1em;'>
RE-Emission also supports generating output reports in a PDF format. However, this feature requires a working LaTeX installation, which is not currently included in the RE-Emission Docker Image. To generate a PDF report, provided that LaTeX is installed in your OS, add the <b>-o [output-filename].pdf</b> flag to the remisison calculation command. You can view an example PDF report in the RE-Emission documentation <a href=https://tomjanus.github.io/reemission/reporting.html>here</a>.
</p></span>
</div>

#### ReEmission Demo

You can run a short demo that demonstrates how RE-Emission processes tabular input data, along with reservoir and catchment delineations from GeoCARET, to calculate reservoir emissions and visualize them on an interactive map. More details about the demo can be found on the [corresponding page in RE-Emission's documentation](https://tomjanus.github.io/reemission/demo.html). To run the demo from within the `reemission` folder, use the following command:

```bash
docker compose run --rm reemission reemission run-demo examples
```
where `examples` is the directory where all input and output files will be stored.

First, pre-calculated outputs from GeoCARET will be fetched from an online source. This includes tabular outputs with input data for the emission model and geospatial data with delineations (reservoir, river, and catchment) in `.shp` format—one for each analyzed dam.

Once the analysis is complete, you will find several files and folders in the `examples` directory, including the fetched input data. The structure of the `examples` folder should look like this:

``` bash
examples
├── demo_interactive_map
├── geocaret_outputs
├── reemission_demo_dam_db
├── reemission_demo_delineations
├── reemission_outputs
└── test_input.json
```

They contents of each folder are explained below:

* `reemission_demo_delineations` : Contains geospatial outputs from GeoCARET, including reservoir and catchment delineations, impounded river segments, and tabular outputs with emission input parameters in `output_parameters.csv`. Each folder contains outputs from a single simulation run. This folder is fetched from a remote source.
* `reemission_demo_dam_db` : Contains a shapefile of hydroelectric dams, represented as points with associated data about dam and turbine properties. This folder is also fetched from a remote source.
* `geeocaret_outputs` : Contains merged shapefiles from `reemission_demo_delineations`, updated with emission estimates calculated by RE-Emission. This folder also includes the `reemission_inputs.json` file, created by merging and converting multiple `output_parameters.csv` files.
* `reemission_outputs` : Contains the output data generated by RE-Emission in both `.json` and `.xlsx` formats.
* `demo_interactive_map` : Contains an interactive map that visualizes the delineated reservoirs and their estimated emissions.

An animated GIF demonstrating how to run the demo (albeit not from within the Docker container) is shown below. Running the demo using the Docker image should look the same, except for the initial command.

![demo-22-05-24](https://github.com/user-attachments/assets/b8875111-d19d-4e86-828f-57cbe51ce42e)

The interactive map can also be accessed at [https://tomjanus.github.io/reemission_demo_map](https://tomjanus.github.io/reemission/demo_map).

<p align="right">(<a href="#top">back to top</a>)</p>

## Running GeoCARET/ReEmission calculations

Here, we will demonstrate how to run GeoCARET and RE-Emission sequentially. We will begin with dam locations and dam heights/full supply levels, then use GeoCARET to delineate the respective reservoirs and catchments, and calculate reservoir- and catchment-level inputs for the emission model. Finally, we will calculate reservoir emissions using RE-Emission.

The demo illustrates the process for a batch of three reservoirs.

<div class="warning" style='background-color:#f3ffe6; color: #284d00; border-left: solid #428000 4px; border-radius: 5px; padding:0.1em; font-size:Small; margin-bottom:1em'>
<span>
<p style='margin-top:0.4em; text-align:center'>
<b>Note <i class='far fa-bell'></i></b></p>
<p style='margin-left:1em;'>
To run the analysis you need to be authenticated with GeoCARET, have a Google Cloud project folder set up an have at least one assets directory within your GCloud project folder. For details how to do this, please refer to the <a href=https://reservoir-research.github.io/geocaret/installation/account_setup.html>Account Setup</a> section of the GeoCARET documentation. For this example, we will assume that the project folder is named <b>geocaret-demo</b> and inside the project folder we create assets folder called <b>emissions</b>.
</p></span>
</div>

<div class="warning" style='background-color:#f7e8c8; color: #573c06; border-left: solid #a6730f 4px; border-radius: 5px; padding:0.1em; font-size:Small; margin-bottom:1em'>
<span>
<p style='margin-top:0.4em; text-align:center'>
<b>Attention <i class="fas fa-exclamation-triangle"></i></b></p>
<p style='margin-left:1em;'>
An internet connection needs to present throughout the process of running the GeoCARET calculations. Short interruptions in connectivity are permissible.
</p></span>
</div>

#### Running Times

The majority of the processing time is spent in GeoCARET, where reservoirs and catchments are delineated and their respective characteristics computed. The computational time varies depending on the size of the reservoirs and catchments, as well as the availability of Google’s servers and their current workload. In our experience, it took **19 minutes** to analyze three dams during this GeoCARET demonstration run, using an Earth Engine account with unpaid usage. Please note that this time does not include the optional export of delineations to Google Drive, which took an additional **2.5 minutes**. The run times for the subsequent steps are measured in seconds.

### GeoCARET-ReEmission Demo

Here, we will outline the steps necessary to calculate the reservoir emissions associated with the construction of three dams. The process involves computing the reservoir and catchment characteristics for each dam and utilizing this data to estimate greenhouse gas (GHG) emissions.

#### Step 1 - Run GeoCARET

Assuming your project is callled **geocaret-demo** and you choose a **standard** simulation scenario (see [Export Settings and Outputs in GeoCARET's documentation](https://reservoir-research.github.io/geocaret/ghg_emissions/output_data_exports.htmlb) for options) you can run geocaret from within the `geocaret` folder using the following command:

```bash
docker compose run --rm geocaret python heet_cli.py data/demo.csv geocaret-demo demo standard
```

This run delineates the reservoirs and catchments for three dam locations and calculates the characteristics of each that are relevant for estimating reservoir GHG emissions with ReEmission.

#### Step 2 - Copy `output_parameters.csv` between `geocaret` and `reemission` folders

After the analysis completes you should see a subfolder in the `outputs` folder with the designation `DEMO_[yyyymmdd]-[hhmm]`, where `[yyyymmdd]` represents the date of the simulation (e.g. 20240924) and `[hhmm]` indicates the simulation start time when (e.g. 1914). The file we are interested in is `output_parameters.csv`, which contains the input data for RE-Emission. You will need to copy this file to the `examples` folder within the `reemission` directory, either manually or programmatically. In the Linux Teminal you can do the following (from within the `geocaret` folder):

```bash
mkdir ../reemission/examples/demo
cp outputs/DEMO_[yyyymmdd]-[hhmm]/output_parameters.csv  ../reemission/examples/demo/output_parameters.csv
```

The equivalent commands in Windows Command Prompot (CMD) are:

```bash
mkdir ..\reemission\examples\demo
copy outputs\DEMO_[yyyymmdd]-[hhmm]\output_parameters.csv ..\reemission\examples\demo\output_parameters.csv
```

#### Step 3 - Convert `output_parameters.csv` to ReEmission input file in **JSON** format

The tabular output data from GeoCARET needs to be converted into the JSON file format required by RE-Emission. This involves using two conversion scripts in RE-Emission: the first script imputes any missing data that cannot be sourced from known geospatial datasets, and the second converts the tabular data into the appropriate JSON format.

##### Add missing columns (variables)

Three input variables for ReEmission cannot be derived from geospatial datasets and must be entered manually. These variables are: *level of wastewater treatment in the catchment*, *landuse intensity in the catchment* and *reservoir's purpose/type* represented by: `c_treatment_factor`, `c_landuse_indensity` and `type`, respectively. Here, we will apply default values for these parameters (across all reservoirs) using a script in ReEmission. You can adjust them later in `output_parameters_upgraded.csv`. To run the script, navigate to the `reemission` folder and execute the following command:

```bash
docker compose run --rm reemission reemission-geocaret process-tab-outputs \
  -i examples/demo/output_parameters.csv \
  -o examples/demo/output_parameters_upgraded.csv \
  -cv 'c_treatment_factor' 'primary (mechanical)' \
  -cv 'c_landuse_intensity' 'low intensity' \
  -cv 'type' 'unknown'
```

The allowed values for each of the three variables are listed below.

| c_landuse_intensity  | c_treatment_factor            | type           |
|----------------------|-------------------------------|----------------|
| low intensity        | no treatment                  | hydroelectric  |
| high intensity       | primary (mechanical)          | multipurpose   |
|                      | secondary biological treatment| potable        |
|                      | tertiary                      | irrigation     |
|                      |                               | flood control  |
|                      |                               | unknown        |

##### Convert CSV to JSON

We then convert the tabular data, updated with the missing input variables, into a JSON representation that conforms to RE-Emission's input data specification.

```bash
docker compose run --rm reemission reemission-geocaret tab-to-json \
  -i examples/demo/output_parameters_upgraded.csv \
  -o examples/demo/input_data_demo.json
```

#### Step 4 - Run ReEmission

Finally, we estimate the reservoir emission in ReEmission.

```bash
docker compose run --rm reemission reemission calculate \
  examples/demo/input_data_demo.json \
  -o outputs/output_data_demo.json \
  -o outputs/output_data_demo.xlsx
```

The generated files, `outputs/output_data_demo.json` and `outputs/output_data_demo.xlsx`, contain the inputs, the calculated intermediate values and emissions. Both files contain equivalent numerical information. `JSON` files are intended for data transfer and processing via scripts, e.g. in Python or JavaScript, while `XLSX` files are suitable for quick examination and manipulation using Microsoft Excel or LibreOffice Calc.

### Exporting delineations to GDrive - <a style="color: skyblue">OPTIONAL</a>

We have currently configured GeoCARET to output only the tabular values. However, you can also optionally export the other generated files, including reservoir and catchment delineations (polygons), dam locations (points), and impounded river sections (polylines) in shapefile format. These files will be exported to your Google Drive. To perform the file export, use the following command, where `DEMO_[yyyymmdd]-[hh-mm]` corresponds to the output folder generated during the GeoCARET run and `geocaret-demo` is the project folder. The `--drive-folder` flag sets the the path on your Google Drive where the results will be exported to. All arguments: `--results-path`, `--drive-folder` and `--project` are required.

```bash
docker compose run --rm geocaret python heet_export_cli.py \
  --results-path projects/geocaret-demo/assets/emissions/XHEET/DEMO_[yyymmdd]-[hh-mm] \
  --drive-folder DEMO_[yyymmdd]-[hh-mm] \
  --project geocaret-demo
```

You can download the folder manually from your Google Drive, either via a web browser or using a Google Drive client.

## Known Issues <i class="fa fa-bug" aria-hidden="true"></i>

### 1. Docker in Rootless mode

You may encounter permission issues when writing to volumes (the created folder structure) if your Docker is configured to run in rootless mode, which is not the default option. While using Docker in rootless mode is recommended for critical server-side applications that require increased security, we discourage its use with our applications. However, if you choose to run Docker in rootless mode and encounter permission issues—such as being unable to write files into folders—you can refer to [this article](https://joeeey.com/blog/rootless-docker-avoiding-common-caveats/) for potential fixes.

<p align="right">(<a href="#top">back to top</a>)</p>

## License
[GPL-3.0](https://choosealicense.com/licenses/gpl-3.0/)

<!-- CONTACT -->
## :mailbox_with_mail: Contact
- [Tomasz Janus (Work)](mailto:tomasz.janus@manchester.ac.uk)
- [Tomasz Janus (Personal)](mailto:tomasz.k.janus@gmail.com)

### Resources
* [Best README Template](https://github.com/othneildrew/Best-README-Template)
* [Choose an Open Source License](https://choosealicense.com)
* [Img Shields](https://shields.io)
* [GitHub Pages](https://pages.github.com)

<p align="right">(<a href="#top">back to top</a>)</p>

## Contributors ✨

<!-- ALL-CONTRIBUTORS-LIST:START - Do not remove or modify this section -->
<!-- prettier-ignore-start -->
<!-- markdownlint-disable -->
<table>
  <tr>
    <td align="center"><a href="https://github.com/tomjanus"><img src="https://avatars.githubusercontent.com/tomjanus" width="100px;" alt=""/><br /><sub><b>Tomasz Janus</b></sub></a><br /><a href="https://github.com/tomjanus/geocaret-reemission/commits?author=tomjanus" title="Code">💻</a><a href="https://github.com/tomjanus/geocaret-reemission/commits?author=tomjanus" title="Tests">⚠️</a> <a href="https://github.com/tomjanus/geocaret-reemission/issues/created_by/tomjanus" title="Bug reports">🐛</a><a href="#design-TJanus" title="Design">🎨</a><a href="" title="Documentation">📖</a></td>
    <td align="center"><a href="https://github.com/tomjanus"><img src="https://avatars.githubusercontent.com/sinnottj-rse" width="100px;" alt=""/><br /><sub><b>James Sinnott</b></sub></a><br /><a href="https://github.com/tomjanus/geocaret-reemission/commits?author=sinnottj-rse" title="Code">💻</a><a href="https://github.com/tomjanus/geocaret-reemission/commits?author=sinnottj-rse" title="Tests">⚠️</a> <a href="https://github.com/tomjanus/geocaret-reemission/issues/created_by/sinnottj-rse" title="Bug reports">🐛</a><a href="#design-JSinnott" title="Design">🎨</a><a href="" title="Documentation">📖</a></td>
  </tr>
</table>


<!-- markdownlint-restore -->
<!-- prettier-ignore-end -->
<!-- ALL-CONTRIBUTORS-LIST:END -->

This project follows the [all-contributors](https://github.com/all-contributors/all-contributors) specification. Contributions of any kind are welcome!

<p align="right">(<a href="#top">back to top</a>)</p>

<!-- MARKDOWN LINKS & IMAGES -->
<!-- https://www.markdownguide.org/basic-syntax/#reference-style-links -->
[contributors-shield]: https://img.shields.io/github/contributors/tomjanus/geocaret-reemission.svg?style=plastic
[contributors-url]: https://github.com/tomjanus/geocaret-reemission/graphs/contributors
[forks-shield]: https://img.shields.io/github/forks/tomjanus/geocaret-reemission.svg?style=plastic
[forks-url]: https://github.com/tomjanus/geocaret-reemission/network/members
[stars-shield]: https://img.shields.io/github/stars/tomjanus/geocaret-reemission.svg?style=plastic
[stars-url]: https://github.com/tjanus/geocaret-reemission/stargazers
[issues-shield]: https://img.shields.io/github/issues/tomjanus/geocaret-reemission.svg?style=plastic
[issues-url]: https://github.com/tomjanus/geocaret-reemission/issues
[license-shield]: https://img.shields.io/github/license/tomjanus/geocaret-reemission.svg?style=plastic
[license-url]: https://github.com/tomjanus/geocaret-reemission/blob/main/LICENSE.txt
