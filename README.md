# Airflow-OpenLineage-Watsonx.DataIntelligence-Demo
This project provides an example of how to capture OpenLineage events from Airflow using watsonx.data intelligence.

**Prerequisites**
**1. An instance of Airflow:**
It mmust either be available (running on any OS or environment) or can be installed using the instructions below that describe how to install an instance of Airflow (https://airflow.apache.org/) using Astonomer's (https://www.astronomer.io/) Astro CLI (https://www.astronomer.io/docs/cli/v1.44/overview) on macOS running on Podman (https://podman.io/). This example uses Airflow v3.3.0+astro.2 with Podman v6.1.0.

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

**2. A watsonx.data intelligence environment.**
For this example I am using this TechZone instance. Note that the watsonx. data intelligence instance only allows three sources to be scanned for lineage, so you'll probably want to use a new instance of this environment for this demo.

**3. A PostgreSQL database.**
If a SaaS version of watsonx.data intelligence is used, as in this example, the PostgreSQL database must be accessible using a public IP or hostname in order to be reachable by the metadata import process. If a software version of watsonx.data intelligence is used, a public IP may not be necessary.

This project uses an instance of Postgres hosted by aiven, using their free plan. Other providers of online free PostgreSQL are Neon and Supabase.

Here is a screenshot of my aiven-based PostgreSQL connection properties:
<img width="1504" height="785" alt="Screenshot 2026-10-08 at 4 57 14 PM" src="https://github.com/user-attachments/assets/f8ae59fe-3952-498e-8fe7-da51db61607f" />

**Create the PostgreSQL database resources**
Execute the sql/airline_disruption_setup.sql script in the PostgreSQL database's public schema using your database tool of choice (I used DBeaver: https://dbeaver.io/)

**Connect to Dbeaver:**
<img width="1469" height="866" alt="Screenshot 2026-10-08 at 5 21 40 PM" src="https://github.com/user-attachments/assets/a43d5777-3a50-4eb1-90e0-8583bcbe429b" />

When the connection will be successful, it will look like this:
<img width="1469" height="866" alt="Screenshot 2026-10-08 at 5 21 40 PM" src="https://github.com/user-attachments/assets/c2c2e4a5-5d5c-413d-a7b1-a6bc2fad4c00" />

The following resources will be created:

Tables

crew_members
disruption_events
disruption_summary
flights

Views

vw_crew_disruption_detail
vw_flight_disruptions

**Add a watsonx.data intelligence DSD for the PostgreSQL database**
Create a new Data Source Definition (DSD) for your PostgreSQL database.

Select Data > Connectivity > Data source definitions > New data source definition:

<img width="1424" height="669" alt="Screenshot 2026-10-08 at 7 31 34 PM" src="https://github.com/user-attachments/assets/97ac81db-0851-4a24-9628-fc6afad6e3c2" />

add credentials:

<img width="1490" height="757" alt="Screenshot 2026-10-08 at 7 35 40 PM" src="https://github.com/user-attachments/assets/328f5d2e-1787-455a-904c-472f81cb71e3" />

test connection:

<img width="1488" height="678" alt="Screenshot 2026-10-08 at 7 37 38 PM" src="https://github.com/user-attachments/assets/65a47767-4de9-4f0c-b875-de4cf452883c" />

**Add a watsonx.data intelligence Platform Connection for your PostgreSQL database**
Create a new Platform Connection for your PostgreSQL database:
<img width="1812" height="864" alt="project-connect-to-data-source" src="https://github.com/user-attachments/assets/c6751a68-1aae-4b61-b5ba-7965f90b4190" />

**Create a watsonx.data intelligence project**
Create a watsonx.data intelligence project named airflow-lineage (the name is not critical).

**Create a project level connection using the Platform Connection**
Within the project, choose New Asset and pick "Connect to a data source":
<img width="1812" height="864" alt="project-connect-to-data-source" src="https://github.com/user-attachments/assets/c6751a68-1aae-4b61-b5ba-7965f90b4190" />

<img width="2306" height="1352" alt="project-select-platform-connection" src="https://github.com/user-attachments/assets/4e4de725-2368-4dd2-a341-fda1cf4419e8" />

Click Next and then click Create.

**Create a Data Quality SLA**
Create a Data Quality SLA on these tables: crew_members, disruption_events, flights with overall data quality of at least 99%:

**Run a metadata import for the PostgreSQL database resources**
Within the project, create and run a metadata import for the PostgreSQL database resources. Use these settings:

Select both Import asset metadata and Import lineage metadata:
Select your PostgreSQL DSD and Connection:
<img width="1479" height="768" alt="Screenshot 2026-10-08 at 7 52 54 PM" src="https://github.com/user-attachments/assets/6c6332bd-6a4a-4615-9739-4feaabb1928b" />
Click the edit button for the Scope of the Asset Metadata and select the four tables and two views:
Click the edit button for the Scope of the Lineage Metadata and select the public schema:
<img width="1404" height="488" alt="Screenshot 2026-10-08 at 7 53 00 PM" src="https://github.com/user-attachments/assets/684f5a7b-ef43-474f-b45c-0348b91827d8" />

Run the Metadata Import Job

**Run a metadata enrichment for the PostgreSQL database resources**
Within the project, create and run a metadata enrichment for the PostgreSQL database resources. Use these settings:

Select the metadata import from the the previous step:
<img width="2160" height="660" alt="metadata-enrichment-select-import" src="https://github.com/user-attachments/assets/43387bf2-e400-44da-9ad9-1f6382016484" />

Select all the enrichment objectives:
<img width="2882" height="1288" alt="metadata-enrichment-select-objectives" src="https://github.com/user-attachments/assets/117040d0-7b45-4832-a8af-c0402d2e24ff" />

Select the [uncategorized] category for both Scope & Primary category and and basic sampling:
<img width="2106" height="1242" alt="metadata-enrichment-sampling-options" src="https://github.com/user-attachments/assets/21c292e5-3616-4263-a694-636935ac8a12" />

Run the Metadata Enrichment Job

**Import lineage for the mock dashboard**
This project contains openlineage event files for a mock dashboard that reads the disruption_summary table.

Before you can import these events into .data intelligence, you must make sure they have:

A unique run id
Up-to-date timestamps.
Database host and port values that match an endpoint in your DSD created earlier
Here are the steps to edit the event files:

Clone this project to your local machine.

Switch to the project's ./openlineage-events/mock-dashboard directory in a terminal session.

If you have a local Python3 environment, make the script refresh-mock-dashboard-events.sh executable:

$ chmod +x refresh-mock-dashboard-events.sh

Execute the script:

$ ./refresh-mock-dashboard-events.sh

You should see output like this:

	aarushikaushik@Aarushis-MacBook-Pro mock-dashboard % ./refresh-mock-dashboard-events.sh
	runId:    30b12403-b27f-4c00-8cbf-0ab582a6ea36
	start:    2026-07-29T03:17:05.000000+00:00
	complete: 2026-07-29T03:17:08.421000+00:00
	Refreshed: ./dashboard-start-event.json
	Refreshed: ./dashboard-complete-event.json
If you do not have a local Python3 environment:

Edit the dashboard-start-event.json file, search for all occurrences of 2026-07-29T03:17:05 and replace all of them with a current timestamp

Edit the dashboard-complete-event.json file, search for all occurrences of 2026-07-29T03:17:08 and replace all of them with a current timestamp that is 3 seconds later than the timestamps in the dashboard-start-event.json file.

Note that the "eventTime" attribute is a long format like 2026-07-29T03:17:05.000000+00:00 and the "nominalStartTime" and "nominalEndTime" attributes are shorter formats, like "2026-07-29T03:17:05+00:00".

Finally, find and edit all occurrences of "postgres://172.31.10.79:5432" in both openlineage event files to refer to the hostname or IP and port number of your postgres instance. For example, my URL is "postgres://pg-6ef3527-onefoursix.b.aivencloud.com:17143"

Once you have completed those edits to the mock-dashboard openlineage events, you can push them to .data intelligences' openlineage endpoint:

**Generate an IBM Cloud API key** for your account and set it as an environment variable in a terminal session:
IBM_CLOUD_API_KEY="<your IBM Cloud Key>"

**Generate a bearer token:**
TOKEN=$(curl -s -X POST \
  'https://iam.cloud.ibm.com/identity/token' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -d "grant_type=urn:ibm:params:oauth:grant-type:apikey&apikey=${IBM_CLOUD_API_KEY}" \
  | python3 -c "import sys,json; print(json.load(sys.stdin)['access_token'])")

**Post the dashboard-start-event.json file to the openlineage endpoint (edit the path to the file for your environment):
**
curl -v \
  "https://api.ca-tor.dai.cloud.ibm.com/gov_lineage/v2/lineage_events/openlineage" \
  -X POST \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d @dashboard-start-event.json
You should receive an HTTP 201 response code

**Post the dashboard-complete-event.json file to the openlineage endpoint (edit the path to the file for your environment):**

curl -v \
  "https://api.ca-tor.dai.cloud.ibm.com/gov_lineage/v2/lineage_events/openlineage" \
  -X POST \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d @dashboard-complete-event.json
You should receive an HTTP 201 response code

Confirm the lineage events are successfully processed by .data intelligence:

<img width="2312" height="1052" alt="mock-dashboard-events-processed" src="https://github.com/user-attachments/assets/acd8c61c-eda2-4008-9a9c-63d28f53a5ec" />

**Inspect the lineage and see the gap**
After the Metadata import Job completes, create a Lineage graph including the vw_crew_disruption_detail view and the disruption_summary table. We can see full lineage for where the view vw_crew_disruption_detail gets its data, and we can see that the dashboard report gets data from the disuruption_summary table but we do not see where the disuruption_summary table gets its data from:
<img width="3364" height="1038" alt="lineage-gap-before-airflow" src="https://github.com/user-attachments/assets/22aa90e3-ce37-4d1f-97f5-c42d0d775c62" />

To close that gap, we'll configure Airflow to send OpenLineage events to watsonx.data integration when the Dag that populates the disruption_summary table executes.

**Install Airflow using the Astro CLI**
For this example, I'll install Airflow using Astonomer's Astro CLI.

**Step 1 — Install Podman**
See the docs here for instructions on installing Podman. If you are using Windows, you'll need to have WSLv2 installed. Make sure to give your podman machine at least 4 vCPUs and 4 GB of memory.

**Step 2 — Install Astro CLI**
Run this command in a terminal session to install the astro cli:

brew install astro

**Step 3 — Configure astro to use podman**
Run this command in a terminal session to configure astro to use podman:

astro config set container.binary podman -g

**Step 4 — Create and initialize the Airflow environment**
Run these commands in a terminal session to create a home directory for astro and to init the astro environment:

mkdir ~/airflow
cd ~/airflow
astro dev init

**Step 5 — Create requirements.txt**
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
➤ Postgres Database: postgresql://localhost:15344/postgres
➤ The default Postgres DB credentials are: postgres:postgres


**Connect to the Airflow UI with your browser**
Point your browser to the Airflow UI URL printed in the previous step and you should see the UI:

Add a PostgreSQL Database Connection to Airflow
In the Airflow UI, choose Admin > Connections > Add Connection and create a PostgreSQL connection to your own database. Set the Connection type to Postgres, set the Connection ID to postgres, and fill in the other properties to connect to your database. For example, my settings look like this :
<img width="1509" height="818" alt="Screenshot 2026-10-09 at 9 18 40 PM" src="https://github.com/user-attachments/assets/558dd9ce-f650-41db-942a-753bed99a79b" />

**Execute the Dag**
Click the Dags button in the Airflow UI, and confirm the airline_crew_disruption Dag appears:
Click into the Dag, click its Trigger button and then confirm the trigger action:
<img width="1502" height="428" alt="Screenshot 2026-10-09 at 9 20 33 PM" src="https://github.com/user-attachments/assets/1391503f-8401-491b-8bba-d2d46f2cfddc" />

Confirm the Dag completed successfully:
Confirm the PostgreSQL table disruption_summary now has data in it:(Check your postgres dbeaver database, if the data gets populated)

**Confirm that OpenLineage events were sent from Airflow**
Run a command like this to confirm that Airflow successfully emitted its openlineage events:

podman logs -f "$(podman ps --filter name=scheduler --format '{{.Names}}')" 2>&1 | \
  grep --line-buffered -iE "Successfully emitted OpenLineage|401" | \
  sed -u -E 's/^([0-9TZ :.+-]+) \[([a-z]+) *\] (.*) \[[A-Za-z0-9_.]+\].*/\1  \2  \3/'

You should see confirmation that the START and COMPLETE events were emitted without any errors:
<img width="1244" height="223" alt="Screenshot 2026-10-09 at 9 22 21 PM" src="https://github.com/user-attachments/assets/e0075594-596d-4e39-8a99-af48bac2eccc" />

**Confirm that OpenLineage events were received by watsonx.data intelligence**
Navigate to Data > Data lineage > Map lineage > Processed OpenLineage events to see the START and COMPLETE events received by watsonx.data intelligence:

<img width="1482" height="805" alt="Screenshot 2026-10-09 at 9 24 36 PM" src="https://github.com/user-attachments/assets/af1348da-d38d-417b-9df8-0e4f49598e02" />


**View the completed lineage**
The lineage now shows the path of the data that lands in the disruption_summary table: the data was read from the view vw_crew_disruption_detail by the Airflow Dag airline_crew_disruption which processed the data and then wrote to the disruption_summary table.

<img width="1481" height="699" alt="Screenshot 2026-10-09 at 9 24 03 PM" src="https://github.com/user-attachments/assets/76234e39-30e5-4571-ba96-be4abd4d32ff" />

Make sure to also note the data quality scores and Data Quality SLA violation shown on the tables, and to explore the column level lineage!

**Final note**
Hopefully this example helps describe the setup needed to integrate Airflow with watsonx.data intelligence.









