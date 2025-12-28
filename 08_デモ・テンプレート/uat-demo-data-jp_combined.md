# uat-demo-data-jp Documentation
Generated from: uat-demo-data-jp



<!-- SOURCE_START: uat-demo-data-jp/.github\workflows\in-development\README.md -->
## File: uat-demo-data-jp/.github\workflows\in-development\README.md

## Introduction

Welcome to the **Field Engineering Demo Library** repository! This repository is designed to provide a common library of Cognite Data Fusion (CDF) demo modules that can be used, improved, and tailored to support different use cases and industries. Deployment from this environment does not require coding experience. The foundation of this demo environment is the **Valhall dataset** from Open Industrial Data. Additional data sources may be added as separate modules to extend or enhance the demos.

### Overview & Key Terms

- **Github Actions:** You can deploy to CDF through the Github UI using actions configured in the `Actions` tab in this repository.  You are not required to set up CDF toolkit on your own.

- **Github Environments:** All secrets and environmental variables are managed through the Github UI using a Github feature called Environments. You can access Github Environments in the `Settings` tab in this repository and then selecting `Environments`. Instructions on creating your environment are provided in the [Deployment Walkthrough](#deployment-walkthrough) section below.

- **Modules:** Each demo is organized into a "module." A module is a bundle of CDF resources, such as data models, time series, files, and Streamlit apps. While modules are not entirely self-contained, the number of inter-module dependencies should be minimized and documented. Each module should include its own `README.md` file with instructions and details.

- **Industry Models:** A key feature of this repository is the inclusion of **industry models**, located under the `cog-demo/modules/industry_models` folder. These models use the Valhall dataset as a base but modify asset and time series names to simulate demos for different industries, such as energy, chemicals, or biotech. This approach maintains compatibility with existing CDF artifacts created for the Valhall demo while offering industry-specific flexibility.

### Getting Started

To set up and deploy this demo environment, you will need to be an administrator in GitHub and have the appropriate permissions to enable deployment from a GitHub Action. 

### Toolkit Documentation

If you are interested in learning more about toolkit and developing your own modules, visit the [Toolkit section](https://docs.cognite.com/cdf/deploy/cdf_toolkit/) on Cognite Docs for additional information. The [YAML reference library](https://docs.cognite.com/cdf/deploy/cdf_toolkit/references/resource_library) is particularly helpful to understand how YAML files are configured for each CDF artifact type, which will enable you to create your own modules. 


## Deployment Walkthrough
### Pre-requisites
These pre-requisites assume you are using Azure.
1. If you need to create a CDF environment (organization + project), follow the steps outlined [here](https://cognitedata.atlassian.net/wiki/spaces/AUTH/pages/3375923233/CDF+Project+and+Organization+Management) to create an internal environment. If you already have one, you can skip this step.
2. In Entra ID, create an App Registration or use a Cognite internal application registration. Generate a secret and take note of both client ID and secret.
![alt text](images/my_organization.png "CDF app registration")
3. Add that App Registration to the CDF admin group for your organization, or manually create a group in CDF that gives the App Registration permissions comparable to the default `oidc-admin-group`. If using a Cognite internal application registration, you must create a new temp admin group (groups and projects full capabilities) and assign that app registration to the new group.
![alt text](images/admin.png "Admin")
4. Cognite Functions is not activated by default for a first time CDF deployment. If Cognite Functions is not activated before you try to deploy from GitHub, the deployment will fail. Go into the CDF web UI and access the Cognite Functions page to activate it. If this is a new CDF with no permissions added, you will need to manually add permissions for your user account so it has "read/write" for functions so you can access the page. You must wait for Cognite Functions to finish activating before running the GitHub action. Cognite Functions activation takes an hour or so.
5. Change the Data Modeling status of your CDF to "DATA_MODELING_ONLY" by submitting a ticket [here](https://cognitedata.atlassian.net/servicedesk/customer/portal/44/create/636). You can continue before this has been completed, but you will need to have this completed before you upload your 3D model.
6. A Github Environment with variables and secrets must be created in this repository for your CDF:
   - In this repository, go to GitHub: `Settings > Environments`.
   - Familiarize yourself with an example, like `demo`.
   - Create your environment using the **same name** as your CDF project.
   - Add your environment variables and secrets

###### Environment Secrets
- FUNCTION_CLIENT_SECRET
- IDP_CLIENT_SECRET
- TRANSFORMATION_CLIENT_SECRET

###### Environment Variables 
required environment variables with examples, on the left for CDF app registrations and on the right for Entra ID app registrations:

- CDF_CLUSTER
- CDF_PROJECT
- FUNCTION_CLIENT_ID
- IDP_CLIENT_ID
- IDP_SCOPES (None or https://<cluster>.cognitedata.com/.default)
- IDP_TENANT_ID
- IDP_TOKEN_URL (https://auth.cognite.com/oauth2/token or https://login.microsoftonline.com/**tenantID**/oauth2/v2.0/token)
- LOGIN_FLOW  (client_credentials)
- PROVIDER  (cdf or entra_id)
- TRANSFORMATION_CLIENT_ID

![alt text](images/secrets_image.png "Secrets")
![alt text](images/variable_image.png "variables")


### Deployment Steps

1. **Prepare Deployment YAML File**  
   - You can use a programming IDE (like VS Code) or the GitHub UI to create your own deployment YAML file. The steps are similar.
   - Navigate to the `cog-demos` folder.  
   - Download `config.demo.yaml` to use as a template, and start customizing the parameters as needed for your environment. You will customize the modules in the next step.\
   - The `config.demo.yaml` contains all modules for a basic Valhall deployment. Add additional modules if desired.
   - Name the file `config.<Your_CDF_Project>.yaml`
   - Create your own branch and check your YAML deployment file into GitHub.
   - Create a pull request and merge the branch back into `main`.

2. **Run the Deployment**  
   - Create your own branch and check your YAML deployment file into GitHub.
   - Create a pull request and merge the branch back into `main`.

2. **Run the Deployment**  
   - Go to the GitHub Actions tab.  
   - Select the deployment action `Deploy CDF Modules` and specify the branch (if not `main`) and the environment name corresponding to your YAML deployment file.
   - The first deployment could fail, but permissions will be created. Run the deployment a second time. If it still fails, try locally to run `poetry run cdf auth init` to set up your environment (we will fix that soon hopefully)

![alt text](images/run_workflow.png "Secrets")


### Summary video
https://github.com/user-attachments/assets/078ec3ca-ed2b-4d32-bf5d-d6998fff1993

### Post-Deployment Steps for First Time Deployment
1. In your CDF's Data Management workspace, go to Configure > 3D, create a scene and link it to your location and attach and upload the Valhall 3D model [downloaded from here](https://drive.google.com/drive/folders/1JQdDRdlCzwn8IpFl1TPHQfgnGqrVDvx2?usp=sharing). After the model is uploaded, publish the model. NOTE: If your CDF is not set to "DATA_MODELING_ONLY", your 3D model will not be uploaded correctly. Ensure your CDF is set to "DATA_MODELING_ONLY" per the pre-requisites.



## Local run

1. Clone the repository

2. Follow the prerequisites above 

In your terminal: 

3. install dependencies using poetry.lock `poetry install --no-root`

4. Run `poetry run cdf auth init` to set up your environment. Alternatively, you can edit .env file with the right details based on [.env template CDF authentication](.env_tmpl.cdf) or [.env template Entra authentication](.env_tmpl.entra) and run `poetry run cdf auth init` afterwards. Have a look at the [.env template CDF authentication](.env_tmpl.cdf) or [.env template Entra authentication](.env_tmpl.entra) files, since they set up additional environment variables that are used to run [local scripts](scripts) (datapoints replication and wipe of CDF project) and run functions locally.

![alt text](images/auth_init.png "auth init command")

In Fusion:

5. Check that the app registration for toolkit belongs to the newly created toolkit group, if not add it manually, temp admin group can be deleted. If you used the app registration from Entra ID, you can skip this step, verify the groupID is linked to the newly created toolkit group. 

![alt text](images/toolkit_app.png "Toolkit app registration")

6. Create your config in [cog-demos](cog-demos), config-<your environment>.yaml, for example copy and rename [config.demo.yaml](cog-demos/config.demo.yaml). 

In your terminal: 

7. Build the templates `poetry run cdf build --env=<your environment>` (replace <your environment> with your environment)

8. Deploy the templates `poetry run cdf deploy --env=<your environment>` (replace <your environment> with your environment)

9. Run `poetry run cdf run workflow --external-id=wf_population_valhall` (if not successful, you might have to run the workflow manually from the UI, you need to enable it un unleash first)

Alternatively, you can run the [script](full_deploy.sh), for example like `./full_deploy.sh <your environment>` to deploy everything. Adapt the script if necessary to remove some compononents, like workflows you are not deploying.


## Additional scripts

- [run workflow in CDF](scripts/run_workflow.py) to run a workflow in CDF.
Run with `poetry run run_workflow` OR `poetry run run_workflow --external_id <external_id> [--confirm]`

- [replication script - DM project](scripts/replication.py) to run locally. this will replicate datapoints from publicdata (CDM) to your own project.
Run with `poetry run replication` OR `poetry run replication --start_time <start_time> --days_offset <days_offset> (--start_over) (--confirm)`

- [run 3D contextualization in CDF](scripts/three_d_contextualization.py) to run 3D contextualization in CDF.
Run with `poetry run three_d_contextualization` OR `poetry run three_d_contextualization --start_time <start_time> --days_offset <days_offset> (--start_over) (--confirm)`

- [wipe data in CDF - DM project](scripts/wipe_cdf_dm.py) to run locally. this will delete all data in CDF. Open a selection window and asks for confirmation
Run with `poetry run wipe`

## FAQ

### What is a module?
A module is a bundle of CDF resources, such as data models, time series, files, and Streamlits. A general practice is to create a module for each use case. While some dependencies between modules may exist, it is a good idea to use modules to minimize these dependencies.

### What is an industry model?
An industry model is a module under the `cog-demo/modules/industry_model` folder that applies industry-specific asset and time series names to the Valhall dataset. All external IDs remain the same, so this should make it interoperable with many CDF artifacts that were created against Valhall demo.

### Where do I go for help?
Ask for help in the `#uat-and-demo-projects` channel or reach out to @gaetan-h


## Very quick deploy
You can create GitHub environments, including variables and secrets, using the GitHub CLI 

- Install gh: If you haven't already, install the GitHub CLI from https://cli.github.com/, eg `brew install gh`

- Authenticate: Log in to your GitHub account using `gh auth login`

- Run the [script](create_environment.sh) to create the environment, variables and secrets based on the .env file, environment name is CDF_PROJECT

- The script will then create the toolkit app registration and add it to the toolkit group

- The script will then trigger the workflow to deploy all the workflow modules

```
./create_environment.sh
```

Prequisites:
- Have requested the CDF project
- Have created an admin group in CDF (only required full capabilities on groups and projects) and added the toolkit app registration to it (this group can be deleted after the toolkit group has been created)
- Have setup the .env file with the right details based on [.env template CDF authentication](.env_tmpl.cdf) or [.env template Entra authentication](.env_tmpl.entra)

<!-- SOURCE_END: uat-demo-data-jp/.github\workflows\in-development\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\common\atlas_ai\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\common\atlas_ai\README.md

Name: Atlas AI data models
Description: Create the required data models for Atlas AI
Type: Atlas AI

<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\common\atlas_ai\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\common\cdf_apm_base\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\common\cdf_apm_base\README.md

# APM base data models
APM base data models
Type: Industrial Applications

<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\common\cdf_apm_base\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\common\cdf_scene\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\common\cdf_scene\README.md

# CDF scene data model
CDF scene data model
Type: 3D

<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\common\cdf_scene\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\common\demo_initializer\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\common\demo_initializer\README.md

# Valhall Demo Initializer
Valhall Demo Initializer
Type: Streamlit

<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\common\demo_initializer\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\common\demo_reset\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\common\demo_reset\README.md

# Reset Manager
Reset Manager
Type: Streamlit

<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\common\demo_reset\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\common\doc_classifier\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\common\doc_classifier\README.md

Name: Document Classifier
Description: This demo is designed to read different types of documents (P&IDs, manuals, assembly diagrams, etc), classify the documents, and pull out metadata from the documents like drawing number and title. You can then add this information as CDF metadata to each document to make it easier to find the information you're looking for (this is manual for now). There is a separate demo of Doc Parsing included, with a data model and corresponding PDF that you can parse to show the power of generative AI in CDF.
Type: Streamlit


**Author:** Kevin Caputo

**Description:** This demo is designed to read different types of documents (P&IDs, manuals, assembly diagrams, etc), classify the documents, and pull out metadata from the documents like drawing number and title. You can then add this information as CDF metadata to each document to make it easier to find the information you're looking for (this is manual for now). There is a separate demo of Doc Parsing included, with a data model and corresponding PDF that you can parse to show the power of generative AI in CDF.

**Demo Notes:** The prompt provides some hints to give a more reliable result and lists the options for classification (for consistency). Try tweaking the prompt in the Streamlit if you are not getting the results you want. 


<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\common\doc_classifier\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\common\file_annotations\functions\file_annotations\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\common\file_annotations\functions\file_annotations\README.md

### - File annotations
Creates file annotations

<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\common\file_annotations\functions\file_annotations\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\common\file_annotations\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\common\file_annotations\README.md

Name: File annotations
Description: Function to create file annotations
Type: Contextualization

<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\common\file_annotations\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\common\foundation\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\common\foundation\README.md

Name: Foundation Module
Description: Foundation module that contains the basic building blocks for other modules, including RAW data for Valhall.
Type: Foundation

Author: Kevin Caputo

This module contains:
- A "superuser" group with full permissions to everything
- RAW tables containing assets, work orders, and time series mappings for the Valhall dataset. 


<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\common\foundation\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\common\ops_summarizer\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\common\ops_summarizer\README.md

# Pipeline Monitor
Pipeline Monitor
Type: Streamlit

<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\common\ops_summarizer\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\common\pipeline_monitor\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\common\pipeline_monitor\README.md

# Pipeline Monitor
This shows a map of Pump Stations along a pipeline and displays the number of open work orders for each station.
Type: Streamlit

**Author:** Torgrim Aas

**Description:** This demo is typically for midstream customers. It shows a map of Pump Stations along a pipeline and displays the number of open work orders for each station.

**Demo Notes:** There are two hardcoded URLs in the library.py file. You will need to update those once you deploy to your environment.


<br>
<img width="437" alt="Screenshot 2025-01-10 145344" src="https://github.com/user-attachments/assets/f45d639d-8dd2-4dea-8eea-3b0bce2636fc" />


<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\common\pipeline_monitor\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\common\robot_garden\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\common\robot_garden\README.md

# Robot Garden
This is a self-contained module that has a 3D Drone image based photogrammetry model, along with equipment data sheets and an asset hierarchy.
Type: Industrial Applications

Steps:
1. Run the rg-tr-assets transformation to create the assets.
2. Upload the 3D model (stored in Google Drive as RobotGarden_adjusted_cleaned2.laz)
3. Contextualize the 3D model (not done by this module)

<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\common\robot_garden\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\common\streamlit_mgr\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\common\streamlit_mgr\README.md

# Streamlit Manager
This Streamlit app lets you send Streamlit applications directly from Cognite Data Fusion (CDF) to the `cognitedata/cognite-samples` GitHub repository. It simplifies the process of turning your Streamlits into toolkit modules—no knowledge of toolkit module structures required. The app converts your Streamlit code into `.py` and `.yaml` files, which you can then load into your preferred IDE and manage using GitHub as a version control system.
Type: Streamlit

## Features
- **Read Existing Modules**: Scans the `cog-demos/modules/` directory in the specified branch to list existing modules.
- **Update or Create Modules**: Choose an existing module to update or create a new one by entering a custom path (e.g., `common/mymodule`).
- **Field Validation**: Includes validation to ensure all required fields (app, branch, module, commit message) are valid before publishing.

## Usage
1. **Launch the App**: Deploy this Streamlit in your CDF environment (see deployment instructions in the root README).
2. **Select a Streamlit App**: Choose a Streamlit from CDF using the "Select Streamlit Application" dropdown.
3. **Enter Your GitHub PAT**: Provide a Personal Access Token (PAT) with repository write access under "GitHub Personal Access Token (PAT)".
4. **Specify a Branch**: Input an existing branch name (e.g., `my-branch`) under "Existing Branch Name". Wait up to 10 seconds for module options to load.
5. **Choose or Create a Module**: Select an existing module from the dropdown or pick "Create New..." and enter a new module name.
6. **Add a Commit Message**: Write a brief commit message describing your changes.
7. **Publish to GitHub**: Click "Publish to GitHub" to push your Streamlit as `.py` and `.yaml` files to the `cognitedata/cognite-samples` repo.

## Notes
- **GitHub PAT**: Required for authentication. Generate one with `repo` scope in your GitHub settings.
- **Branch Management**: Create and manage branches via the GitHub web UI at [https://github.com/cognitedata/cognite-samples/branches](https://github.com/cognitedata/cognite-samples/branches). The `main` branch is protected and cannot be used directly. Streamlit Manager will not create branches for you.
- **Target Repository**: Fixed to `cognitedata/cognite-samples` (this repository).
- **Deployment**: To redeploy this Streamlit Manager to your CDF environment, follow the instructions in the root README of this repository on deploying one module.

### Screenshot:
<img width="547" alt="Screenshot 2025-01-10 145344" src="https://github.com/user-attachments/assets/f33dde3c-5015-4862-a37e-e9bb5217c862"/>


<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\common\streamlit_mgr\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\common\super_emitter\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\common\super_emitter\README.md

# Super Emitter
Super Emitter app
Type: Streamlit

<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\common\super_emitter\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\common\three_d_contextualization\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\common\three_d_contextualization\README.md

# 3D contextualization
Contextualization of 3D models in Cognite Data Fusion
Type: 3D

## 3D prerequisites (manual)
- Make sure your project is "DM only"
- Create 3D model and upload it, source is [here](https://drive.google.com/file/d/1iAqzFOAwWkt2t2CcG8qkBw0Ik6y-teQ6/view?usp=sharing)
- Create a scene and link it the location
- Get model ID and revision ID after upload
- Run the [function](../three_d_contextualization/functions/3d_annotations_valhall), but you may want to run it locally through [local_run.ipynb](3d_annotations_valhall/local_run.ipynb) or [local_run.py](3d_annotations_valhall/local_run.py). it will use values from .env to run.
'Example data input: `data={"model_id":"<3D model id>","revision_id":"<3D revision id>","project":"<project_name>","asset_space":"<asset_space>","three_d_space":"<3d_space>"}'`
You probably will need to run it locally using the [local run file](functions/3d_annotations_valhall/local_run.py)



<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\common\three_d_contextualization\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\common\time_series_ann\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\common\time_series_ann\README.md

# Time Series Annotation
Time Series Annotation
Type: Streamlit

<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\common\time_series_ann\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\common\valhall_dm\LICENSE.md -->
## File: uat-demo-data-jp/cog-demos\modules\common\valhall_dm\LICENSE.md

# License for the Asset Performance Management (APM) data set

Please see [Terms of Use](./Open%20Industrial%20Data%20-%20Terms%20of%20Use.pdf) for the APM data set.

The Valhall compressor operational states Jupyter Notebook is licensed under Apache-2.


<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\common\valhall_dm\LICENSE.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\common\valhall_dm\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\common\valhall_dm\README.md

Dependencies: This module must be deployed with the "foundation" module and one of the industry models.

Module contains CDF artifacts to support the Valhall dataset:
- Data model spaces and UOMs
- P&IDs and manuals for Valhall
- Function that automatically syncs time series datapoints with Open Industrial Data
- `valhall-dm` location filter
- Three transformations that convert work orders and assets from RAW tables into the Core DM. Additionally, it maps time series to assets.


<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\common\valhall_dm\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\common\work_pkg_gen\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\common\work_pkg_gen\README.md

# Work Package Generator
Work Package Generator
Type: Streamlit

<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\common\work_pkg_gen\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\hw_reference\hw_canvas\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\hw_reference\hw_canvas\README.md

# HW Canvas
Demo Toolkit - Hello World reference for CDF Canvas
Type: Streamlit

<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\hw_reference\hw_canvas\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\in-development\cdf_p_and_id_annotation\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\in-development\cdf_p_and_id_annotation\README.md

# P&ID annotation
The module creates a simple data pipeline for processing files from your location. The processing here is related to the annotation/contextualization mapping of tags in P&ID documents to assets and files.
Type: Contextualization

Key features are:

- Update of metadata and aliases for Files and Assets/Tags.
  - Configuration in extraction pipeline matching the names and structure
    of data modeling. See list examples list and description in list for
    annotation function.
  - DEBUG mode
    - Extensive logging
    - Processing one file name provided as input in configuration
  - Metadata and aliases for files
    - Using AI / LMM endpoint in CDF to generate a summary that will be used
      as a description (if no description exist for document)
    - Summary is used to generate tags – if processing diagrams are found in
      the text, the tag PID is added with other keywords as tags to the
      document metadata
    - Generation different alias versions of the file name used for matching
      between P&ID diagrams. Full file name containing version and revision
      is usually not used in the diagrams referring to other diagrams. For
      this reason aliases of the file name is created to reflect this an make
      the matching more precise.
  - Alias for Assets/Tags
    - Creating asset alias without system numbers, making matching with this
      more precise.
    - Process uses a raw table to store State, preventing processing of items
      already processed.

NOTE this code should be updated to reflect the project need for file name
and asset matching depending on naming standards in the company’s P&ID
documents

- Run P&ID annotation process
  - Configuration in extraction pipeline matching the names and structure of
    data modeling, including configuration of:
    - Instance spaces – where your data are stored
    - Schema spaces – schema definition of the used data model
    - External ID for the view/type – as your extended Cognite Asset type
    - Version of your view/type
    - Search property – within you type what you use for matching
    - Property for filtering and list of possible values, ex: list of tag values
  - DEBUG mode
    - Extensive logging
    - Processing one file name provided as input in configuration
  - Delete functionality.
    - IF updating / changing the thresholds for automatic approval/suggestions
      you should clean up and remove existing annotations.
    - Annotations removed are only related to annotations created by this
      process (i.e. annotations manually created, or by a different process
      will not be deleted)
    - Without delete – the external ID for the annotations prevent creation of
      duplicate annotations.
  - Using state store for incremental support.
    - Use sync api against data modeling for only processing new/updated files
    - Store state/cursor in RAW db/table (as provided in configuration)
  - Run ALL mode
    - Clean out status an logged status from previous runs in RAW, and process
      all P&ID files.
    - NOTE: to also clean / delete previous annotations also add:
      cleanOldAnnotations =  True in configuration
  - Annotation process
    - Optional: Use configuration for filter property to find Files and/or
      Assets
    - Use search property to match from P&ID to files and assets
    - If matching process fails on batch:
      - First retry 3 times.
      - If still failing - then try processing files individually
      - If individual processing fails, log and skip file
    - Report all matches by writing matches to a table in RAW for doc to tag
      and for doc to doc matches (db/table as configured)
    - Use threshold configuration to automatically approve or suggest annotations
    - Create annotation in DM service
    - Log status from process to extraction pipeline log

## Managed resources

This module manages the following resources:

1. auth:
   - Name: `files.processing.groups`
     - Content: Authorization group used for processing the files,
       running transformation, function (contextualization) and updating files

2. data set:
   - ID: `ds_files_{{location_name}}`
     - Content: Data lineage, used Links to used functions, extraction
       pipelines, transformation, and raw tables

3. extraction pipeline:
   - ID: `ep_ctx_files_{{location_name}}_{{source_name}}_pandid_annotation`
     - Content: Documentation and configuration for a CDF function running
       P&ID contextualization/annotation (see function for more description)
   - ID: `ep_ctx_file_metadata_update`
     - Content: Documentation and configuration for a CDF function updating
       aliases for asset & files (see function for more description)

4. function:
   - ID: `fn_dm_context_files_{{location_name}}_{{source_name}}_annotation`
     - Content: reads new/updated files using the SYNC api. Extracts all
       tags in P&ID that match tags from Asset & Files to create CDF
       annotations used for linking found objects in the document to
       other resource types in CDF
   - ID: `fn_dm_context_{{location_name}}_{{source_name}}_alias_update`
     - Content: Reads new/updated assets and files to create & update the
       alias property.

5. raw: in database : ds_files_{{source_name}}_{{location_name}}
   - ID: documents_docs
     - Content: DB with table for with all

6. workflow
   - ID: `wf_{{location_name}}_files_annotation`
     - Content: Start Function:
       `fn_dm_context_{{location_name}}_{{source_name}}_alias_update` and then
       Function:
       `fn_dm_context_files_{{location_name}}_{{source_name}}_annotation`

## Variables

The following variables are required and defined in this module:

> - function_version:
>   - Version ID for your Function metadata
> - location_name:
>   - The location for your data, the name used in all resource types related
>     to the data pipeline
> - source_name:
>   - The name of the source making it possible to identify where the data
>     originates from, ex: 'workmate', 'sap', 'oracle',..
> - files_dataset:
>   - The name of the data set used for extraction pipeline and RAW DB
> - schemaSpace:
>   - Namespace within a data model where schemas are defined and managed
> - viewVersion:
>   - Version of the views / types defined and used
> - fileInstanceSpace:
>   - Namespace or context within a data model where instances (or entities)
>     are defined and managed
> - equipmentInstanceSpace:
>   - Instance space related to equipment (if used)
> - assetInstanceSpace:
>   - Instance space related to assets / tags / functional locations
> - functionClientId:
>   - Environment variable that contains the value for Application ID for
>     system account used to run function
> - functionClientSecret:
>   - Environment variable that contains the value for Secret Value for the
>     Application used for the system account running the function
> - files_location_processing_group_source_id:
>   - Object ID from Azure AD for used to link to the CDF group created

## Usage

  poetry shell
  cdf build
  cdf deploy

### P&ID configuration

Annotation configuration example:

- debug
  - write DEBUG messages and only process one file if True
- debugFile
  - if debug is True, process only this file name
- runAll
  - if True run on all found documents, if False only run on document not
    updated since last  annotation
- cleanOldAnnotations
  - if True remove all annotations for file created by function before
    running the process (useful for testing different annotations Thresholds)
- rawdb
  - Raw database where status Information is stored
- rawTableDocTag
  - Raw table to store found documents tags relationships in the P&ID
- rawTableDocDoc
  - Raw table to store found documents to documents relationships in the P&ID
- rawTableState
  - Raw table to store state related to process
- autoApprovalThreshold
  - Threshold for auto approval of annotations
- autoSuggestThreshold
  - Threshold for auto suggestion of annotations

- annotationView
  - View to store annotations in
- annotationJob
  - Job configuration for annotation

- schemaSpace
  - Schema space for the views, same or different for each view
- instanceSpace
  - Instance space ( where data is stored) for the views, same or different
    for each view
- externalId
  - External id of the view
- version
  - Version of the view
- searchProperty
  - Property to search for in the view that is used to create the annotation
    link, typically alias
- type
  - Type of the link to create in the annotation, either diagrams.FileLink
    or diagrams.AssetLink

```yaml
Example:

config:
  parameters:
    debug: False
    debugFile: 'PH-ME-P-0156-001.pdf'
    runAll: False
    cleanOldAnnotations: False
    rawDb: 'ds_files_{{location_name}}_{{source_name}}'
    rawTableState: 'files_state_store'
    rawTableDocTag: 'documents_tags'
    rawTableDocDoc: 'documents_docs'
    autoApprovalThreshold: 0.85
    autoSuggestThreshold: 0.50
  data:
    annotationView:
      schemaSpace: {{ schemaSpace }}
      externalId: CogniteDiagramAnnotation
      version: {{ viewVersion }}
    annotationJob:
      fileView:
        schemaSpace: {{ schemaSpace }}
        instanceSpace: {{ fileInstanceSpace }}
        externalId: CogniteFile
        version: {{ viewVersion }}
        searchProperty: aliases
        type: diagrams.FileLink
        filterProperty: tags
        filterValues: ["PID", "ISO", "PLOT PLANS"]
      entityViews:
        - schemaSpace: {{ schemaSpace }}
          instanceSpace: {{ equipmentInstanceSpace }}
          externalId: CogniteEquipment
          version: {{ viewVersion }}
          searchProperty: aliases
          type: diagrams.FileLink
        - schemaSpace: {{ schemaSpace }}
          instanceSpace: {{ assetInstanceSpace }}
          externalId: CogniteAsset
          version: {{ viewVersion }}
          searchProperty: aliases
          type: diagrams.AssetLink
          filterProperty: tags
          filterValues: ["PID"] 
```

#### Running functions locally

You may run locally:

To run `fn_dm_context_files_LOC_SOURCE_annotation`,
simply call the `handler.py` with a local `.env`file that contain env variable
for logging on to your CDF project

#### Cognite Function runtime

Using Cognite Functions to run workloads will be limited by the underlying
resources in the cloud provider functions. Hence processing many P&ID
documents will not be optimal in a CDF function since it will time
out and fail. One solution for this is to do the initial one-time job locally
and let the function deal with all new and updated files.


<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\in-development\cdf_p_and_id_annotation\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\in-development\contextualization_pct\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\in-development\contextualization_pct\README.md

# Dashboard for Contextualization Metrics
Dashboard for Contextualization Metrics
Type: Streamlit

<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\in-development\contextualization_pct\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\in-development\custom_access_management\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\in-development\custom_access_management\README.md

# Additional access management
Additional access management for additional readonly/readwrite groups
Type: Foundation

<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\in-development\custom_access_management\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\in-development\demoable_dm\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\in-development\demoable_dm\README.md

# Demoable Data Model
The deployment creates a data model that is simple enough to be demoed in the Knowledge Graph visualization. It does not do anything else, but can be useful as an introduction to show how data is represented in the Knowledge Graph in an easily understandable way. This type of demo may be relevant for digital / IT / data scientist customers.
Type: Data Model

Demo instructions:
1. Go to Data management > Data models and click on this data model.
2. Ensure the "Knowledge Graph" feature is enabled using the lightning bolt icon (experimental as of Feb 2025).
3. Start on LYB_Unit container. This shows the relationship of the unit to the parent.
4. Click on HX-001 to show all objects linked to the heat exchanger (work orders, P&IDs, time series)
5. Click PID_001.pdf to show all objects linked to the P&ID
6. Click HX-002 to expand all objects linked to HX-002.


<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\in-development\demoable_dm\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\in-development\live_weather_data\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\in-development\live_weather_data\README.md

# Hosted extractor
Hosted extractor to pull live weather data
Type: Data Integration

- Creates an hosted REST extractor to pull live weather data
- Transformation to update the timeseries and assign an asset and unit

<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\in-development\live_weather_data\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\in-development\mqtt_logistics\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\in-development\mqtt_logistics\README.md

# MQTT Logistics
Hosted extractor to pull live logistics data from an MQTT broker
Type: Data Integration

<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\in-development\mqtt_logistics\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\in-development\simulated_data\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\in-development\simulated_data\README.md

# Simulation of data
This will run daily through a workflow and creates some simulated data for activities (maintenance orders and operations) in RAW.
Type: Data Simulation

NB: Depending on the data model you choose, you may need to update the transformations.yaml

for example in [tr_maintenance_orders_simulated.Transformation.yaml](tr_maintenance_orders_simulated.Transformation.yaml):

from
```
  dataModel:
    space: '{{ company_process_industries_extensions }}'
    externalId: '{{ company_process_industries_extensions_external_id }}'
    version: v1
    destinationType: {{ company_process_industries_extensions_external_id }}MaintenanceOrder
  instanceSpace: {{ default_location }}-maintenance-orders
```

to 
```
  dataModel:
    space: cdf_idm
    externalId: CogniteProcessIndustries
    version: v1
    destinationType: CogniteMaintenanceOrder
  instanceSpace: {{ default_location }}-maintenance-orders
```

<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\in-development\simulated_data\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\in-development\timeseries_sync\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\in-development\timeseries_sync\README.md

# Timeseries synchronization
Timeseries subscription with publicdatacdm
Type: Timeseries

You may need to obtain a secret for publicdatacdm to run the synchronization and deploy the function

<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\in-development\timeseries_sync\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\in-development\valhall_dm_extension\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\in-development\valhall_dm_extension\README.md

# Valhall data model extension

The deployment creates: 
- [containers/spaces/views/data models](data_models) with CDM/IDM extensions. Extension of CogniteAsset into <company>Asset/<company>Storage, of CogniteEquipment into <company>Equipment, CogniteFile into <company>File, CogniteMaintenanceOrder into <company>MaintenanceOrder, CogniteOperation into <company>Operation, CogniteSourceSystem into <company>SourceSystem, CogniteTimeSeries into <company>TimeSeries. We do not import CDM views in the extended data model. 
- [files](files) with P&ID information
- 1 [hosted REST extractor](hosted_extractors), to get live datapoints (weather data)
- simulated data in [raw](raw) for operations and storage tables
- [transformations](transformations) to populate the data for assets, equipment, files, maintenance orders, operations, source system, timeseries and storage categories in the extended data model
- 1 [workflow](workflows) to orchestrate the transformations and function after deployment - you might need to use unleash to enable the workflow UI, it can also be trigger by the toolkit 
`poetry run cdf run workflow --external-id=wf_population_valhall` (if not successful, you might have to run the workflow manually from the UI)

<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\in-development\valhall_dm_extension\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\in-development\valhall_dm_idm\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\in-development\valhall_dm_idm\README.md

# Valhall IDM - Process Industries Data Model

The deployment creates: 
- [spaces](data_models) for instances
- [files](files) with P&ID information
- 1 [function](functions), for file annotations
- simulated data in [raw](raw) for operations tables
- 1 [location filter](locations), to filter the data
- [transformations](transformations) to populate the data in IDM
- 1 [workflow](workflows) to orchestrate the transformations and function after deployment - you might need to use unleash to enable the workflow UI, it can also be trigger by the toolkit 
`poetry run cdf run workflow --external-id=wf_population_valhall` (if not successful, you might have to run the workflow manually from the UI)


<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\in-development\valhall_dm_idm\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\industry_models\bulk_chem\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\industry_models\bulk_chem\README.md

Bulk Chemical Industry Model
Bulk Chemical Industry Model

<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\industry_models\bulk_chem\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\industry_models\gene_therapies\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\industry_models\gene_therapies\README.md

Gene Therapies
Gene therapies industry model

<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\industry_models\gene_therapies\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\industry_models\offshore_og\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\industry_models\offshore_og\README.md

Offshore Oil & Gas
Offshore Oil & Gas Industry Model is a data model that represents the offshore oil & gas industry.

Dependencies: Should be deployed with "foundation" and "valhall-dm" modules.

This module creates time series tags created in the Core DM.


<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\industry_models\offshore_og\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\industry_models\refining\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\industry_models\refining\README.md

Refining
Refining Industry Model is a data model that represents the refining industry.This type of demo may be relevant for customers in the refining industry.

<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\industry_models\refining\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\infield\cdf_infield_common\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\infield\cdf_infield_common\README.md

# CDF Infield Common
This module contains shared configurations across multiple locations for Infield.
Type: Industrial Applications

<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\infield\cdf_infield_common\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\infield\cdf_infield_location\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\infield\cdf_infield_location\README.md

# CDF Infield location
This module contains location specific configurations for Infield. This is the default location.
Industrial Applications

The support multiple locations, copy this module and modify the configurations. Remember to
rename the module name to e.g. `infield_location_<default_location>`.

## Defaults

The module has been set up to use data from the `examples/cdf_oid_example_data` module. That module
loads data into RAW, and a transformation to move data
into the classic asset hierarchy in CDF.

This module only uses the data from the classic asset hierarchy to populate assets in data models, and
from the RAW tables to make activities from the `workorders` table in RAW.

In [./default.config.yaml](default.config.yaml), you will find default values that are adapted to the
`cdf_oid_example_data` data set.

## app_config

This module creates a configuration instance of Infield by populating the APM_Config data model with a configuration. This
configuraiton ties together groups, spaces, and the root asset externalId for a specific Infield location.

## Auth

The module creates four groups that need four matching groups in the identity provider that the CDF
project is configured with. The groups are:

* Normal role for regular Infield users
* A viewer role for read/only users
* A template administrator role
* A checklist administrator role

The source ids from the groups in the identity provider should be set in [./default.config.yaml](default.config.yaml).

## Data models

There are two spaces created in this module: one space for Infield to store app data and one space for
data from source systems, like assets, activities/work orders etc.

## Transformations

You will find three transformations in this module:

* [Assets hierarchy to data models](./transformations/sync_assets_from_hierarchy_to_apm.sql) - This transformation
  will duplicate the asset hierarchy into the APM data models. It uses the default location source data space to
  store the instances. If you have multiple locations, you need one such transformation per asset hierarchy/location.
  This transformation should run before everything else.
* [Assets hierarchy parent relationships to data models](./transformations/sync_asset_parents_from_hierarchy_to_apm.sql)
   -- This transformation should run after the asset hierarchy has been copied the APM data models as it
   requires the assets to exist.
* [Work orders to activities](./transformations/sync_workorders_to_activities.sql) - This transformation will copy
  work orders from the RAW data model to the APM data model. This transformation relies on the `examples/apm_simple` module
  and the RAW workorders table. In a customer deployment, you will adapt this transformation to populate APM _Activity
  daya model with work orders from the customer's source system.

## Implementation notes

* APM_Activity requires startDate and endDate set to a time range viewed from Infield (for the sample, we hard code these).
* APM_Activity source cannot be null, it must be set to something (don't use APP as this is reserved for Infield).
* APM_Activity rootLocation must exactly match the externalId of the configured root asset.


<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\infield\cdf_infield_location\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\infield\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\infield\README.md

# Infield
Infield setup
Industrial Applications

You may need to obtain a secret for publicdatacdm to run the synchronization and deploy the function

<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\infield\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\maintain\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\maintain\README.md

# Maintain
Maintain
Industrial Applications

<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\maintain\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\testing\infield\cdf_infield_common\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\testing\infield\cdf_infield_common\README.md

# cdf_infield_common

This module contains shared configurations across multiple locations for Infield.


<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\testing\infield\cdf_infield_common\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\testing\infield\cdf_infield_location\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\testing\infield\cdf_infield_location\README.md

# cdf_infield_location

This module contains location specific configurations for Infield. This is the default location.
The support multiple locations, copy this module and modify the configurations. Remember to
rename the module name to e.g. `infield_location_<default_location>`.

## Defaults

The module has been set up to use data from the `examples/cdf_oid_example_data` module. That module
loads data into RAW, and a transformation to move data
into the classic asset hierarchy in CDF.

This module only uses the data from the classic asset hierarchy to populate assets in data models, and
from the RAW tables to make activities from the `workorders` table in RAW.

In [./default.config.yaml](default.config.yaml), you will find default values that are adapted to the
`cdf_oid_example_data` data set.

## app_config

This module creates a configuration instance of Infield by populating the APM_Config data model with a configuration. This
configuraiton ties together groups, spaces, and the root asset externalId for a specific Infield location.

## Auth

The module creates four groups that need four matching groups in the identity provider that the CDF
project is configured with. The groups are:

* Normal role for regular Infield users
* A viewer role for read/only users
* A template administrator role
* A checklist administrator role

The source ids from the groups in the identity provider should be set in [./default.config.yaml](default.config.yaml).

## Data models

There are two spaces created in this module: one space for Infield to store app data and one space for
data from source systems, like assets, activities/work orders etc.

## Transformations

You will find three transformations in this module:

* [Assets hierarchy to data models](./transformations/sync_assets_from_hierarchy_to_apm.sql) - This transformation
  will duplicate the asset hierarchy into the APM data models. It uses the default location source data space to
  store the instances. If you have multiple locations, you need one such transformation per asset hierarchy/location.
  This transformation should run before everything else.
* [Assets hierarchy parent relationships to data models](./transformations/sync_asset_parents_from_hierarchy_to_apm.sql)
   -- This transformation should run after the asset hierarchy has been copied the APM data models as it
   requires the assets to exist.
* [Work orders to activities](./transformations/sync_workorders_to_activities.sql) - This transformation will copy
  work orders from the RAW data model to the APM data model. This transformation relies on the `examples/apm_simple` module
  and the RAW workorders table. In a customer deployment, you will adapt this transformation to populate APM _Activity
  daya model with work orders from the customer's source system.

## Implementation notes

* APM_Activity requires startDate and endDate set to a time range viewed from Infield (for the sample, we hard code these).
* APM_Activity source cannot be null, it must be set to something (don't use APP as this is reserved for Infield).
* APM_Activity rootLocation must exactly match the externalId of the configured root asset.


<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\testing\infield\cdf_infield_location\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\valhall_ac\LICENSE.md -->
## File: uat-demo-data-jp/cog-demos\modules\valhall_ac\LICENSE.md

# License for the Asset Performance Management (APM) data set

Please see [Terms of Use](./Open%20Industrial%20Data%20-%20Terms%20of%20Use.pdf) for the APM data set.

The Valhall compressor operational states Jupyter Notebook is licensed under Apache-2.


<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\valhall_ac\LICENSE.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/cog-demos\modules\valhall_ac\README.md -->
## File: uat-demo-data-jp/cog-demos\modules\valhall_ac\README.md

# Valhall Asset Centric Data Module
The module has a basic data set that can be used as example data for many other modules and
purposes. It is used by other packages and modules like cdf_infield_location.

## About the module

The module has a basic data set that can be used as example data for many other modules and
purposes. It is used by other packages and modules like cdf_infield_location.

## About the data

This data is from [Open Industrial Data](https://hub.cognite.com/open-industrial-data-211/what-is-open-industrial-data-994).

Below is a snippet from the Cognite Hub website describing the data:

> The data originates from a single compressor on Aker BP’s Valhall oil platform in the North Sea.
> Aker BP selected the first stage compressor on the Valhall because it is a subsystem with
> clearly defined boundaries, rich in time series and maintenance data.
>
> AkerBP uses Cognite Data Fusion to organize the data stream from this compressor. The data set available in Cognite
> Data Fusion includes time series data, maintenance history, and
> Process & Instrumentation Diagrams (P&IDs) for Valhall’s first stage compressor and associated process equipment:
> first stage suction cooler, first stage suction scrubber, first stage
> compressor and first stage discharge coolers. The data is continuously available and free of charge.
>
>By sharing this live stream of industrial data freely, Aker BP and Cognite hope to accelerate innovation within
data-heavy fields. This includes predictive maintenance, condition
> monitoring, and advanced visualization techniques, as well as other new, unexpected applications. Advancement in these
> areas will directly benefit Aker BP’s operations and will also
>improve the health and outlook of the industrial ecosystem on the Norwegian Continental Shelf.

## Managed resources

This module manages the following resources:

1. Datasets for each resource type in the data set named based on the location name (default:oid).
2. A set of Process & Instrumentation diagrams to be uploaded as files.
3. Pre-structured data in RAW for assets, timeseries, workorders, and workitems that are suitable for creating relationships
   between the different resources. These are structured into one database per source system.
4. A set of timeseries (but no datapoints) for the different assets.
5. A transformation that moves assets from the asset RAW table into the classic asset hierarchy.

## Pre Requisites for this module:

You must execute the cog-demos/common/foundation module before you execute this one. The foundation module ingests all the relevant data into the Staging environment in Fusion which will then be used by this module.


## Variables

The following variables are required and defined in this module:

| Variable | Description |
|----------|-------------|
| default_location| The default location name to use for the data set. We use default oid (Open Industrial Data) |
| source_asset| The name of the source system where assets originate from, default: workmate|
| source_workorder| The name of the source system where the workorders orginate from, default workmate|
| source_files| The name of the source system where the files originate from, default: workmate|
| source_timeseries| The name of the source system where the timeseries originate from, default: pi|

## Usage

If you want to create a project with example data, you can either specify this module in your `environment.yml` file or
you can copy it to `custom_modules`, change the name (remove the cdf_ prefix), and replace the data with your own in the
various sub-directories.


<!-- SOURCE_END: uat-demo-data-jp/cog-demos\modules\valhall_ac\README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/README.md -->
## File: uat-demo-data-jp/README.md

## Introduction

Welcome to the **Field Engineering Demo Library** repository! This repository is designed to provide a common library of Cognite Data Fusion (CDF) demo modules that can be used, improved, and tailored to support different use cases and industries. Deployment from this environment does not require coding experience. The foundation of this demo environment is the **Valhall dataset** from Open Industrial Data. Additional data sources may be added as separate modules to extend or enhance the demos.

### Overview & Key Terms

- **Github Actions:** You can deploy to CDF through the Github UI using actions configured in the `Actions` tab in this repository.  You are not required to set up CDF toolkit on your own.

- **Github Environments:** All secrets and environmental variables are managed through the Github UI using a Github feature called Environments. You can access Github Environments in the `Settings` tab in this repository and then selecting `Environments`. Instructions on creating your environment are provided in the [Deployment Walkthrough](#deployment-walkthrough) section below.

- **Modules:** Each demo is organized into a "module." A module is a bundle of CDF resources, such as data models, time series, files, and Streamlit apps. While modules are not entirely self-contained, the number of inter-module dependencies should be minimized and documented. Each module should include its own `README.md` file with instructions and details.

- **Industry Models:** A key feature of this repository is the inclusion of **industry models**, located under the `cog-demo/modules/industry_models` folder. These models use the Valhall dataset as a base but modify asset and time series names to simulate demos for different industries, such as energy, chemicals, or biotech. This approach maintains compatibility with existing CDF artifacts created for the Valhall demo while offering industry-specific flexibility.

### Getting Started

To set up and deploy this demo environment, you will need to be an administrator in GitHub and have the appropriate permissions to enable deployment from a GitHub Action. 

### Toolkit Documentation

If you are interested in learning more about toolkit and developing your own modules, visit the [Toolkit section](https://docs.cognite.com/cdf/deploy/cdf_toolkit/) on Cognite Docs for additional information. The [YAML reference library](https://docs.cognite.com/cdf/deploy/cdf_toolkit/references/resource_library) is particularly helpful to understand how YAML files are configured for each CDF artifact type, which will enable you to create your own modules. 

## Deployment Walkthrough
### Pre-requisites
These pre-requisites assume you are using Azure.
1. If you need to create a CDF environment, follow the steps outlined [here](https://cognitedata.atlassian.net/wiki/spaces/AP1/pages/4717740585/Creating+a+Demo+CDF) to create an internal environment. Don't create your own IdP organization if this is an internal demo (i.e. create the project directly under cog-demos).
2. In Entra ID, create an App Registration.
3. Add that App Registration to the CDF admin group for your organization, or manually create a group in CDF that gives the App Registration permissions comparable to the default `oidc-admin-group`.
4. Cognite Functions is not activated by default for a first time CDF deployment. If Cognite Functions is not activated before you try to deploy from GitHub, the deployment will fail. Go into the CDF web UI and access the Cognite Functions page to activate it. If this is a new CDF with no permissions added, you will need to manually add permissions for your user account so it has "read/write" for functions so you can access the page. You must wait for Cognite Functions to finish activating before running the GitHub action. Cognite Functions activation takes an hour or so.
5. Change the Data Modeling status of your CDF to "DATA_MODELING_ONLY" by submitting a ticket [here](https://cognitedata.atlassian.net/servicedesk/customer/portal/44/create/636). You can continue before this has been completed, but you will need to have this completed before you upload your 3D model.
6. A Github Environment with variables and secrets must be created in this repository for your CDF:
   - In this repository, go to GitHub: `Settings > Environments`.
   - Familiarize yourself with an example, like `valhall-demo`.
   - Create your environment using the same name as your CDF project.
   - Add your environment variables and secrets.  

### Deployment Steps

1. **Prepare Deployment YAML File**  
   - You can use a programming IDE (like VS Code) or the GitHub UI to create your own deployment YAML file. The steps are similar.
   - Navigate to the `cog-demos` folder.  
   - Download `config.template.yaml` to use as a template, and start customizing the parameters as needed for your environment. You will customize the modules in the next step.\
   - The `config.template.yaml` contains all modules for a basic Valhall deployment. Add additional modules if desired.
   - Name the file `config.<Your_CDF_Project>.yaml`
   - Create your own branch and check your YAML deployment file into GitHub.
   - Create a pull request and merge the branch back into `main`.

2. **Run the Deployment**  
   - Go to the GitHub Actions tab.  
   - Select the deployment action `Deploy modules to CDF project` and specify the branch (if not `main`) and the environment name corresponding to your YAML deployment file.
   - The first deployment will fail, but permissions will be created. Run the deployment a second itme. If it still fails, something else is wrong - see the troubleshooting section.

### Post-Deployment Steps for First Time Deployment
1. If you are deploying the Valhall dataset for the first time, you will need to run all three transformations, e.g. for `valhall-dm`:
   - tr_assets_to_cdm
   - tr_ts_to_cdm
   - tr_events_to_cdm
3. In your CDF's Data Management workspace, go to Configure > 3D and upload the Valhall 3D model [downloaded from here](https://drive.google.com/drive/folders/1JQdDRdlCzwn8IpFl1TPHQfgnGqrVDvx2?usp=sharing). After the model is uploaded, publish the model. NOTE: If your CDF is not set to "DATA_MODELING_ONLY", your 3D model will not be uploaded correctly. Ensure your CDF is set to "DATA_MODELING_ONLY" per the pre-requisites.
4. Go to Build Solutions > Functions and run the "Valhall 3D - Annotations" and "Valhall Files - Annotations" functions to contextualize P&IDs and the 3D model. Under the function details, you will find a data input example that tells you how to structure the input to the functions.

## Troubleshooting
- **My deployment run failed and I don't know why**: In the Github UI, click on the "workflow run" to view the log. This should give you an indication where it failed and why.
- **My deployment run failed because of an AuthorizationError**: On the first run, the deployment might fail due to insufficient permissions. Permissions are automatically added during this process. Re-run the deployment, and if everything else is correct, it should succeed. If it still doesn't work, there is a different problem and you should verify you have met the pre-requisites.
- **My deployment run was successful but the one or more functions says it failed to deploy**: Functions may fail to deploy for unknown reasons. If the deployment action succeeded but the function has a "Failed to deploy" error in the CDF UI, delete the function and re-run the deployment.
- **My deployment run was successful but the "Valhall Datapoints - OID Sync" function is timing out**:
  This is expected at first and temporary. `valhall_dm` contains a function that will backfill up to a month for each time series tag. Due to the volume of data during the initial backfill, it will time out for the initial few executions as it makes its way through the entire of list of tags. After a few timeouts, it will get caught up and succeed. 
  

## FAQ

### What is a module?
A module is a bundle of CDF resources, such as data models, time series, files, and Streamlits. A general practice is to create a module for each use case. While some dependencies between modules may exist, it is a good idea to use modules to minimize these dependencies.

### What is an industry model?
An industry model is a module under the `cog-demo/modules/industry_model` folder that applies industry-specific asset and time series names to the Valhall dataset. All external IDs remain the same, so this should make it interoperable with many CDF artifacts that were created against Valhall demo.

### Where do I go for help?
Ask for help in the `#americas-field-engineering` channel.


<!-- SOURCE_END: uat-demo-data-jp/README.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/scripts\readme.md -->
## File: uat-demo-data-jp/scripts\readme.md

This folder is for scripts that are not part of the Cognite Toolkit deployment template.

- [replication script - DM project](replication.py) to run locally. this will replicate datapoints from publicdata (CDM) to your own project.
Run with `poetry run replication`

- [wipe data in CDF - DM project](wipe_cdf_dm.py) to run locally. this will delete all data in CDF. Open a selection window and asks for confirmation
Run with `poetry run wipe`

- [run workflow in CDF](run_workflow.py) to run a workflow in CDF.
Run with `poetry run run_workflow`

- [run 3D contextualization in CDF](three_d_contextualization.py) to run 3D contextualization in CDF.
Run with `poetry run three_d_contextualization`

<!-- SOURCE_END: uat-demo-data-jp/scripts\readme.md -->

================================================================================


<!-- SOURCE_START: uat-demo-data-jp/streamlit_create_environment\README.md -->
## File: uat-demo-data-jp/streamlit_create_environment\README.md

## Streamlit application to create environment

This Streamlit application is designed to help users create a new environment in the Field Engineering Demo Library repository. The application guides users through the process of creating a new environment, including setting up the necessary secrets and variables in the GitHub repository.

### How to create the application in CDF

1. Navigate to Data Management > Build Solutions > Streamlit apps

![](streamlit.png)

You might need to update your capabilities and add `files:acl`.

![](admin.png)

2. Create the app

![](create_app.png)

You might get this message, click on "Run the application"

![](warning.png)

Delete everything in the code editor and paste the code from [streamlit_code.py](streamlit_code.py)

![](app.png)

Click on Rerun on the right, you should see the Streamlit application.
If you want, you can publish the app, but also you can use it directly here

Populate the necessary value 

![](populate.png)

Click on "Generate JSON" and "Copy to clipboard"

![](generate_json.png)

You are now ready to use the [workflow to create your environment](../.github/workflows/in-development/create_env.yaml)


<!-- SOURCE_END: uat-demo-data-jp/streamlit_create_environment\README.md -->

================================================================================
