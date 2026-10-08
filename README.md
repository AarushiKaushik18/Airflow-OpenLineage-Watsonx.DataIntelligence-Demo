# Airflow-OpenLineage-Watsonx.DataIntelligence-Demo
This project provides an example of how to capture OpenLineage events from Airflow using watsonx.data intelligence.

An instance of Airflow must either be available (running on any OS or environment) or can be installed using the instructions below that describe how to install an instance of Airflow (https://airflow.apache.org/) using Astonomer's (https://www.astronomer.io/) Astro CLI (https://www.astronomer.io/docs/cli/v1.44/overview) on macOS running on Podman (https://podman.io/). This example uses Airflow v3.3.0+astro.2 with Podman v6.1.0.

If you are installing Airflow on macOS, you'll need to install Homebrew first.

**#Install Homebrew for Mac:**
https://brew.sh/
Run command in terminal: /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

****#Install Podman**:
https://podman.io/docs/installation

Install Airflow using the Astro CLI
For this example, I'll install Airflow using Astonomer's Astro CLI.

**#Step 1 — Install Podman**
See the docs here for instructions on installing Podman. If you are using Windows, you'll need to have WSLv2 installed. Make sure to give your podman machine at least 4 vCPUs and 4 GB of memory.

**Step 2 — Install Astro CLI**
Run this command in a terminal session to install the astro cli:

brew install astro
