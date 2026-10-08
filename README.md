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

**#Step 3 — Configure astro to use podman**
Run this command in a terminal session to configure astro to use podman:

astro config set container.binary podman -g
**Step 4 — Create and initialize the Airflow environment**
Run these commands in a terminal session to create a home directory for astro and to init the astro environment:

mkdir ~/airflow
cd ~/airflow
astro dev init

**Step 5 — Create requirements.txt** (• Mac: Press Command + Space, type TextEdit, and press Enter.
)
In a text editor, create the file ~/airflow/requirements.txt with this content:

apache-airflow-providers-postgres[openlineage]

**Step 6 - Create .env**
Create the file ~/airflow/.env with the following text, including your own IBM_API_KEY on line 2. Edit the value on line 4 if you are running watsonx.data inteligence in a region other than ca-tor.

AIRFLOW__OPENLINEAGE__NAMESPACE=airflow
OPENLINEAGE__TRANSPORT__AUTH__APIKEY=<YOUR IBM CLOUD API KEY>
OPENLINEAGE__TRANSPORT__TYPE=http
OPENLINEAGE__TRANSPORT__URL=https://api.ca-tor.dai.cloud.ibm.com
OPENLINEAGE__TRANSPORT__ENDPOINT=gov_lineage/v2/lineage_events/openlineage
OPENLINEAGE__TRANSPORT__AUTH__TYPE=jwt
OPENLINEAGE__TRANSPORT__AUTH__TOKEN_ENDPOINT=https://iam.cloud.ibm.com/identity/token
OPENLINEAGE__TRANSPORT__AUTH__GRANT_TYPE=urn:ibm:params:oauth:grant-type:apikey
OPENLINEAGE__TRANSPORT__AUTH__RESPONSE_TYPE=cloud_iam

**Step 7 - Import the example Dag**
Copy the file dags/airline_disruption_dag.py to the ~/airflow/dags directory.

**Step 8 - Start Airflow**

astro dev start

You should see output like this:

$ astro dev start
✔ Project image has been updated
✔ Project started
➤ Airflow UI: http://airflow.localhost:6563
➤ Postgres Database: postgresql://localhost:5432/postgres
➤ The default Postgres DB credentials are: postgres:postgres

**#Connect to the Airflow UI with your browser**
Point your browser to the Airflow UI URL printed in the previous step and you should see the UI:

<img width="1508" height="933" alt="Screenshot 2026-10-08 at 4 46 43 PM" src="https://github.com/user-attachments/assets/f4784ecf-bd7b-4965-9095-5ee3064aec4b" />

