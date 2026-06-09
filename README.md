# Quick deploy Minio & Polaris 

This project provides a sample custom docker-compose.yml file and a setup.sh to quickly spin up Minio and Polaris REST catalog containers on the same Linux server. The objective of this project is to allow quick deployment of Minio and Polaris REST catalog services set up and ready for lab testing with Iceberg with minimum user intervention.


## Files

1.  **docker-compose.yml**: Deploys and configures the following containers - 
    - Minio container with service listening on port 9000 and UI on port 9001, default credentials(minioadmin/minioadmin) and default bucket(dataiku). 
    - Postgres database (polaris) to support Polaris REST catalog listening on port 5432.  
    - Polaris REST catalog container with API endpoint listening on port 8181 and management on port 8282, with connectivities configured to the Postgres database and Minio service, and a transient polaris-setup-worker to run setup.sh to create the following resources in the catalog.

2.  **setup.sh**: Executed by transient setup worker to provision the Polaris REST catalog. The key resources created 
    - warehouse/catalog = dataiku_catalog
    - principal user = dataiku (with CLIENT_ID and CLIENT_SECRET writtin into polaris_creds.env) with associated roles 
    - namespace = dataiku  

3.  **polaris_creds.env**: This file gets overwritten with the Polaris REST catalog user credentials. Docker-compose is not able to create this file - ensure this file already exists in the folder with write permissions.  

## How It Works

Ensure docker compose is installed on the server:

*  Place all three files (docker-compose.yml, setup.sh and polaris_credts.env) in the same folder and run `docker compose up -d`


## Additional Notes

The polaris-setup-worker spins up on `network_mode=host` in order to be on the same network as the host machine and setup.sh will then try to retrieve the host's machine IP address in order to configure the Polaris REST catalog to connect with the Minio service also listening on the same IP address. This process should work fine in most situations but not thoroughly validated.
