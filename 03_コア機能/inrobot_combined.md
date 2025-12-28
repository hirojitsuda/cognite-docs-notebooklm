# INROBOT Documentation
Generated from: docs.cognite.com (inrobot)



<!-- SOURCE_START: docs.cognite.com/docs\cdf\inrobot\guides\admin.mdx -->
## File: docs.cognite.com/docs\cdf\inrobot\guides\admin.mdx

---
title: Configure InRobot
description: Configure robot credentials, set up robot access, upload 3D models, and grant network access to connect robots to InRobot.
content-type: procedure
audience: dataEngineer
experience-level: 100
lifecycle: use
article-type: article
---
<Warning>
The features described in this section are currently in [beta](/cdf/product_feature_status) testing and are subject to change.
</Warning>

You must set up robots and upload 3D models or point cloud in Cognite Data Fusion ([CDF](/cdf)) to connect robots to [Cognite InRobot](https://inrobot.cogniteapp.com).

<Tip>
You can also use the [Cognite Toolkit](/cdf/deploy/cdf_toolkit/guides/inrobot_toolkit) to use pre-built configurations to set up InRobot.
</Tip>

## Before you start

Make sure you have the following:

- A Cognite project you can use to access [CDF](https://fusion.cognite.com).
- A 3D model or point cloud of the location where the robot inspection will take place.
- One of the following:
  - Access to create groups and app registrations in [Azure](https://portal.azure.com) and access to create groups and data sets in CDF.
  - Or a client ID and a secret of a service principal with the [correct access](#set-up-robot-access-in-cognite-data-fusion).
- Access to the InRobot application. A Cognite or CDF project administrator can activate the application by contacting [Cognite Support](https://cognite.zendesk.com/hc/en-us).

## Set up robot credentials

The robot's _device agent_ (software connecting the robot, CDF, and InRobot) needs client credentials to connect to the **Cognite Robotics API**. To create robot credentials in the [Azure portal](https://portal.azure.com):

<Steps>
<Step title="Register an application">
[Register an application](/cdf/access/entra/guides/register_custom_webapp). Follow the steps to register an application. Copy and store the <span class="ui-element">Application (client) ID</span> (`clientId`) and the <span class="ui-element">Directory (tenant) ID</span> (`tenantID`) values securely.
</Step>

<Step title="Create a client secret">
[Create a client secret](/cdf/access/entra/guides/add_service_principal#create-a-client-secret-in-azure-ad). Follow the steps to create a client secret in Azure AD.

<Info>
Make sure you copy this value now. This value will be hidden after you leave this page.
</Info>
</Step>

<Step title="Create a group">
[Create a group](/cdf/access/entra/guides/create_groups_oidc#step-1-create-a-group-in-azure-ad). Follow the steps to create a group in Microsoft Entra ID. Copy and store the <span class="ui-element">Object Id</span> of the created group securely.
</Step>

<Step title="Add the application to the group">
[Add the application to the group](/cdf/access/entra/guides/add_service_principal#add-the-service-principal-to-a-cdf-group). Link your newly created application in Microsoft Entra ID to a group in CDF.
</Step>
</Steps>

## Set up robot access in Cognite Data Fusion
To set up a robot's access in [CDF](https://fusion.cognite.com):

<Steps>
<Step title="Create a data set">
Create a data set. Under <span class="ui-element">Manage</span>, select <span class="ui-element">Manage data catalog</span> > <span class="ui-element">+Create a data set</span>. The robot will only have access to this data set, and this data set will identify the robot. Learn more about [data sets](/cdf/data_governance/concepts/datasets).
</Step>

<Step title="Create a group and add capabilities">
[Create a group and add capabilities](/cdf/access/guides/capabilities#create-a-group-and-add-capabilities). Follow the steps to create a new group and assign [specified capabilities](/cdf/access/guides/capabilities#configure-inrobot) to the group.

<Note>
The service principal needs to have access to one and only one data set, as this is used to identify the robot. Refrain from reusing the <span class="ui-element">Object Id</span> as it can create issues.
</Note>
</Step>
</Steps>

## Upload a 3D model or point cloud

Integrating a 3D model of a context map lets you view the map's 3D model in InRobot. Once you upload both maps, you can combine them in InRobot. Learn more about [3D models](/cdf/3d).

To upload a 3D model or a point cloud of a robot's context (physical) map in CDF:

<Steps>
<Step title="Upload a 3D model file">
[Upload a 3D model file](/cdf/3d/guides/3dmodels_upload#upload-a-3d-model).
</Step>

<Step title="Upload a revision to the 3D model">
[Upload a revision to the 3D model](/cdf/3d/guides/3dmodels_upload#upload-a-revision-to-an-existing-3d-model).
</Step>

<Step title="Publish the model">
[Publish](/cdf/3d/guides/3dmodels_edit#publish-unpublish-or-delete-a-revision) the model.

<Tip>
Copy and save the 3D <span class="ui-element">Model Id</span> and <span class="ui-element">Revision Id</span> values to use further in the configuration. Model ID and Revision ID are used to identify a 3D model in certain application configurations.
</Tip>
</Step>
</Steps>

## Grant access to URLs and ports

To access InRobot and the robots on your network, [add URLs to your allowlist](/cdf/admin/allowlist).

<br/>

___

<br/>

You've completed the basic configuration for all robots. For a more technical and robot-specific configuration, select one of the following:

- [Configuration for Spot](/cdf/inrobot/guides/spot)
- [Configuration for ANYMal](/cdf/inrobot/guides/anymal)


<!-- SOURCE_END: docs.cognite.com/docs\cdf\inrobot\guides\admin.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\cdf\inrobot\guides\anymal.mdx -->
## File: docs.cognite.com/docs\cdf\inrobot\guides\anymal.mdx

---
title: Set up ANYmal
description: Configure ANYmal-specific settings to connect ANYmal to InRobot.
content-type: procedure
audience: dataEngineer
experience-level: 200
lifecycle: use
article-type: article
---
Once you completed the general setup, configure the ANYmal-specific settings to connect ANYmal to InRobot. Before you begin, ensure you have [internet access](https://help.ubuntu.com/stable/ubuntu-help/net-wireless-connect.html.en) on your PC or WM and that the ANYmal API is available and installed.

## Download ANYmal integration

To download the ANYmal integration:

<Steps>
<Step title="Sign in">
Sign in to [Cognite Data Fusion](https://fusion.cognite.com) (CDF).
</Step>

<Step title="Navigate to Extractors">
Go to <span class="ui-element">Data management</span> > <span class="ui-element">Integrate</span> > <span class="ui-element">Extractors</span>.
</Step>

<Step title="Download the ANYmal Integration extractor">
Select and download the <span class="ui-element">ANYmal Integration</span> extractor. The extracted folder must have the compressed Docker images, `anymal_agent.tar.gz` and `webrtc_controller.tar.gz`, the `docker-compose.yaml` file, and the `.env_template` template file for the required environment variables.
</Step>

<Step title="Load the Docker images">
Load the Docker images into your Docker runtime:

```bash
docker load -i anymal_agent.tar.gz
docker load -i webrtc_controller.tar.gz
```
</Step>
</Steps>

Learn more about [data extraction](/cdf/integration/concepts/extraction#download-installation-files-and-documentation).

## Deploy ANYmal agent

### Set environment variables

Rename the **.env_template** to **.env** and fill in the environment variables.

**Cognite**

| Parameter               | Description                                |
| ----------------------- | ------------------------------------------ |
| `COGNITE_CLIENT_ID`     | Enter client ID.                           |
| `COGNITE_CLIENT_SECRET` | Enter client secret.                       |
| `COGNITE_TOKEN_URL`     | Enter the token URL.  |
| `COGNITE_TENANT_ID`     | Enter Microsoft Entra tenant ID instead of setting `COGNITE_TOKEN_URL` if you're using Microsoft Entra ID. The `COGNITE_TENANT_ID` will be used if you set `COGNITE_TOKEN_URL` and `COGNITE_TENANT_ID`.                 |
| `COGNITE_SCOPES`        | Enter the scopes to expand or restrict the access.                   |
| `COGNITE_PROJECT`       | Enter the name of the Cognite project. |
| `COGNITE_CLUSTER`       | Enter the name of the Cognite cluster. |

**ANYmal**

| Parameter                              | Description                                                                                   |
| -------------------------------------- | --------------------------------------------------------------------------------------------- |
| `ANYMAL_SERVER`                        | Enter the ANYmal server to connect to, for example, `172.21.0.1:58050`.                   |
| `ANYMAL_ROBOT_NAME`                    | Enter the name of the robot to connect to.                                                    |
| `ANYMAL_STATE_UPDATE_PERIOD_MILLISECS` | Enter the rate at which states are sent from the ANYymal device agent, for example, 100". |
| `ANYBOTICS_CREDENTIALS_DIR`            | Enter the path to the ANYymal credentials folder (with `ads-cli.crt` and `ads-cli.pem`). |
| `COGNITE_ANYMAL_CLIENT_NAME`           | Enter the name of the Cognite ANYymal Data Service client, for example `cognite_anymal_agent`.   |
| `DATA_FOLDER_PATH`                     | Enter the path to an existing folder on the computer the Cognite agent is running on. The data folder is used to temporarily store inspection data before uploading it to CDF. |
| `CONFIG_FOLDER_PATH`                   | Enter the path to an existing folder on the machine the Cognite agent is running on. The configuration folder can contain an ANYymal-to Cognite-asset mapping. See [Upload an ANYmal environment](#upload-an-anymal-environment) and [Parse ANYmal missions](#parse-anymal-missions). |
| `LOG_LEVEL`                            | Cognite agent log level. Possible level values are `DEBUG`, `INFO`, `WARNING`, `ERROR`. |
| `LOG_LEVEL_ANYMAL`                     | ANYymal client log level. Possible level values are `DEBUG`, `INFO`, `WARNING`, `ERROR`.  |

**Video stream**

| Parameter                              | Description                                                                                   |
| -------------------------------------- | --------------------------------------------------------------------------------------------- |
| `ANYMAL_CERTIFICATE`                   | Enter the path to the `ads-cli.crt` file (`"/usr/share/ads/credentials/ads-cli.crt"`). |
| `ANYMAL_CERTIFICATE_KEY`               | Enter the path to the `ads-cli.pem` file (`"/usr/share/ads/credentials/ads-cli.crt"`). |
| `ANYMAL_ROOT_CA`                       | Enter the path to a root certificate. You can skip it if `ANYMAL_SKIP_AUTH_VERIFY=true`. |
| `ANYMAL_SKIP_AUTH_VERIFY`              | Enter `true` or `false` if the agent skips verifying the root certificate. |
| `ANYMAL_PREFERRED_STREAM`              | Enter `"zoom_camera"` or `"thermal_camera"`. The agent currently doesn't support online changing of streams. |
| `ANYMAL_INSECURE`                      | Enter `true` when connecting to the ANYbotics simulator, otherwise, enter `false`. |
| `WEBRTC_CONFIG`                        | Select the correct configuration file: set to `"config-anymal-twilio"`. |

**Environments and missions**

| Parameter            | Description                                                                                           |
| -------------------- | ----------------------------------------------------------------------------------------------------- |
| `MISSION_FOLDER`     | Enter the path to the folder to upload ANYmal [mission](#parse-anymal-missions) files.            |
| `ENVIRONMENT_FOLDER` | Enter the path to the folder to upload ANYmal [environment](#upload-an-anymal-environment) files. |

### Run the ANYmal agent

From a folder that contains the `.env` file and the `docker-compose.yaml` file, run the following commands:

<Tabs>
<Tab title="Run all images">
```sh
docker-compose up -d
```
</Tab>

<Tab title="Stop the container">
```sh
docker compose down
```
</Tab>

<Tab title="Run device agent only">
```sh
docker compose up -d anymal-agent
```
</Tab>

<Tab title="Run video agent">
```sh
docker compose up -d webrtc-controller
```
</Tab>
</Tabs>

#### Upload an ANYmal environment

The `anymal-environment-parser` parses ANYmal environment files and converts them to an InRobot waypoint map and location. The location will have the same external ID as the environment file name (without extension), and the map will have the name `<filename>_inspection_points`. For example, if the file's name is `my_environment.yaml`, the location's name is `my_environment`, and the corresponding waypoint map's name is `my_environment_inspection_points`.

The environment parser isn't running continuously as part of the `docker-compose.yaml` file. To upload an environment, run the environment parser container with the following command in the same folder as the `.env` file:

```sh
docker run --env-file=.env --name anymal-environment-parser --restart unless-stopped -v ${ENVIRONMENT_FOLDER}:/environments cognite/anymal-environment-parser:latest
```

If your `.env` file is in another folder, update the `--env-file` path. Set the `ENVIRONMENT_FOLDER` environment variable to the path where the environment files are stored in your terminal session. The command will connect to the running container. Once you're done, stop the container, select the following shortcut: Mac - <kbd>CMD+C</kbd> and Windows - <kbd>Ctrl+C</kbd>.

Find an ANYmal environment file and rename it to `<environment_name>.yaml`, where the `environment_name` is the name for the _location_ in InRobot. Add that file to the folder specified by the `ENVIRONMENT_FOLDER` environment variable. This makes the environment parser process the environment file and create a location, frame, and a waypoint map in CDF.

For example, if you have an environment file called `my_environment.yaml`, add it to the folder specified by the `ENVIRONMENT_FOLDER` environment variable. The location in InRobot will be called `my_environment`, and the corresponding waypoint map will be called `my_environment_inspection_points`.

```sh
environments/
    my_environment.yaml
```

If you'd like to move the new map to another location, update the map's location using Postman to update in the `robotics/maps/update`:

```json
{
  "items": [
    {
      "externalId": "my_environment_inspection_points",  // The externalId of the map
      "update": {
        "locationExternalId": {
          "set": "my_other_location" // The externalId of the location
        }
      }
    }
  ]
}
```

#### Parse ANYmal missions

`anymal-mission-parser` parses ANYmal missions. The parser creates InRobot missions from the ANYmal mission files. The parser isn't running continuously as part of the `docker-compose.yaml` file. To upload missions, run the mission parser container with the following command in the same folder as the `.env` file:

```sh
docker run --env-file=.env --name anymal-mission-parser --restart unless-stopped -v ${MISSION_FOLDER}:/missions cognite/anymal-mission-parser:latest
```

If your `.env` file is in another folder, update the `--env-file` path. Set the `MISSION_FOLDER` environment variable to the path where the mission files are stored in your terminal session. The command will connect to the running container. Once you're done, stop the container, select the following shortcut: Mac - <kbd>CMD+C</kbd> and Windows - <kbd>Ctrl+C</kbd>.

After the environment has been parsed and uploaded to CDF, you can upload ANYmal missions trained in that environment. To parse and upload ANYmal missions, add a folder containing all the missions in the environment to the folder specified by the `MISSIONS_FOLDER` environment variable. The name of the folder added to the `MISSIONS_FOLDER` must be the `environment_name`. 

Here are the examples of mission files:

```sh
missions/
    testenvironment1/
        mission1.yaml
        mission2.yaml
    testenvironment2/
        mission3.yaml
```

ANYmal is deployment ready; there's no need to add sensors, tags, or other physical adjustments to your environment. ANYmal uses onboard sensors to navigate without tags or fiducials.

## Link ANYmal AssetId to CDF Asset external ID

If a mission is triggered through the ANYmal native application and not through InRobot, linking data from inspection events to the correct asset in CDF isn't possible. To solve this, add an `Anymal_AssetId: CDF_AssetInternalId` in a configuration file. The file should be defined as `$CONFIG_FOLDER_PATH/anymal_asset_id_to_cdf_asset_id_mapping.json`, where `$CONFIG_FOLDER_PATH` is set in the `.env` file. The file format is:

```json
{
    "Anymal_AssetId1": "CDF_AssetInternalId1",
    "Anymal_AssetId2": "CDF_AssetInternalId2",
}
```


<!-- SOURCE_END: docs.cognite.com/docs\cdf\inrobot\guides\anymal.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\cdf\inrobot\guides\autowalk.mdx -->
## File: docs.cognite.com/docs\cdf\inrobot\guides\autowalk.mdx

---
title: Autowalk missions
description: Record robot maps, upload them to CDF, and align waypoints with 3D models for autonomous missions.
content-type: procedure
audience: dataEngineer
experience-level: 100
lifecycle: use
article-type: article
---
Use Spot **Autowalk** to record robot maps (missions) and replay them with Spot walking autonomously. A robot map is a set of navigation waypoints that confine the space where the robot can operate. You must create and upload a robot map to [Cognite Data Fusion (CDF)](/cdf) to let a robot perform missions autonomously within the InRobot application. With the tablet provided and the **Autowalk** functionality, you can:

- Record a map by placing waypoints.
- Replay the recorded movements.
- Upload the scanned area to CDF.

<Tip>
Test the **Autowalk** functionality by creating a test robot map before going on-site.
</Tip>

<a id="connect-to-robot"></a>
## Connect to a robot

Use a tablet to navigate Spot and create maps. To connect the tablet to the robot:

<Steps>
<Step title="Turn on the robot and tablet">
Confirm that you've turned on the robot and the tablet.
</Step>

<Step title="Open the Spot application">
Open the **Spot** application, which is pre-loaded onto the tablet.
</Step>

<Step title="Select the appropriate network">
Select the appropriate network as directed by your system administrator. For most initial configurations, this is Spot's built-in access point.
</Step>

<Step title="Add the robot">
Select the robot from the list of available WiFi networks or select <span class="ui-element">Add robot</span> to add the robot to the network.
</Step>

<Step title="Enter credentials">
Enter a username and password. You can find the credentials on the robot where you've placed the battery.
</Step>

<Step title="Power on the robot">
Select <span class="ui-element">Power On</span> on the tablet, then turn on the motor power.
</Step>
</Steps>

Learn more about [Spot start procedure](https://support.bostondynamics.com/s/article/Start-Spot-49955).

<a id="create-robot-map"></a>
## Create a robot map

Creating a robot map means manually controlling the robot while it records its movement for the mission by placing waypoints. After the recording stops, you can replay the map, and the robot will walk autonomously from waypoint to waypoint. Remember to upload all recorded maps to CDF to create custom missions with contextualized data and actions in InRobot.

<a id="record-map"></a>
### Record a map

Before you start recording a robot's map, make sure that:

- The area is without obstacles.
- The docking station has its associated fiducial.
- There's a fiducial at the map's starting location. Place more fiducials on the route with little information, such as long white walls, for the robot to locate itself. Learn more about [placing fiducials](/cdf/inrobot/guides/spot#print-and-place-fiducials).

<Tip>
- Record maps as loops so that the robot sees the same fiducial at the start and end of the map. You can also use the docking station fiducial as the beginning and end of the map.
- Power cycle Spot at the beginning of the map recording if the robot has been on for a while. Power cycle resets Spot to its internal position.
- Don't pose Spot while creating the map. It's not supported in InRobot and will require creating a new map.
</Tip>

To record a robot's map:

<Steps>
<Step title="Optional. Take control">
Take control over the robot.
</Step>

<Step title="Undock and position the robot">
**Undock** the robot and move it to the position where its cameras can read the starting fiducial. All maps must start at a fiducial. Record maps as loops so that the robot sees the same fiducial at the start and end of the map.
</Step>

<Step title="Optional. Select Walk mode">
Select <span class="ui-element">Walk</span> to navigate the robot. If the robot is in the <span class="ui-element">Stand</span> mode, you can move the robot only in place.
</Step>

<Step title="Start recording">
In the top left corner, select <span class="ui-element">Drive</span> > <span class="ui-element">Autowalk</span> > <span class="ui-element">Record</span>.
</Step>

<Step title="Name the map">
Name the map and select <span class="ui-element">Continue</span>. This name will also be the name of the map uploaded to Cognite Data Fusion. You can't edit the map once you confirmed it.
</Step>

<Step title="Record and navigate">
Select <span class="ui-element">Start recording</span> and navigate the robot with the controllers. During the recording, the robot places navigation waypoints along its path every 2 meters.

<Tip>
- To visit a specific location in the route, rotate the robot 90 degrees and back. It's not required but is helpful for gauge inspection routes.
- Avoid visiting the same location twice and walk the map in one go.
- If necessary, make the robot go back and forth several times to increase the accuracy of the waypoints placed on the map.
</Tip>
</Step>

<Step title="Optional. Perform PTZ actions">
For hard-to-reach inspection points, stop the robot, make sure that the object is in line of sight, and perform a PTZ action. Don't pose as posing isn't saved, and you might lose line of sight.
</Step>

<Step title="Finish recording">
Once you're ready to stop recording, select <span class="ui-element">Finish recording</span>. You can also tap the red (<span class="ui-element">+</span>) button and select <span class="ui-element">Dock & finish recording</span> if you want to dock the robot.
</Step>

<Step title="Optional. Replay the map">
Select <span class="ui-element">Play now</span> to replay the recorded map and verify that the robot can follow the route autonomously.

<Check>
You've recorded the map and can upload it to CDF.
</Check>
</Step>
</Steps>

<a id="upload-robot-map-to-cdf"></a>
## Upload a robot map to CDF
To upload a map to CDF:

<Steps>
<Step title="Select Drive">
Select <span class="ui-element">Drive</span> from the dropdown list.
</Step>

<Step title="Open menu">
Select the red (<span class="ui-element">+</span>) button.
</Step>

<Step title="Upload the mission">
Select <span class="ui-element">Upload mission to CDF</span>.
</Step>

<Step title="Confirm the upload">
Leave the map name as is and <span class="ui-element">Confirm</span> the upload. The name isn't used anywhere.

<Check>
The map uploads to CDF and becomes available in the InRobot application.
</Check>
</Step>
</Steps>

You can confirm the map's upload to CDF in two ways:

<Tabs>
<Tab title="In CDF">
<Steps>
<Step title="Sign in to CDF">
Sign in to CDF and select <span class="ui-element">Explore data</span>.
</Step>

<Step title="Find the zip file">
Under <span class="ui-element">Files</span>, find a zip file of the uploaded map.
</Step>
</Steps>
</Tab>

<Tab title="In InRobot">
<Steps>
<Step title="Open Missions">
Select the <span class="ui-element">Missions</span> tab, and then select <span class="ui-element">3D</span>.
</Step>

<Step title="Find the map">
On the <span class="ui-element">Controls</span> panel on the right, find the name of the created map in the <span class="ui-element">Robot map</span> dropdown list.

<Info>
You can also see a mission created with the same name in the <span class="ui-element">Missions</span> list.
</Info>
</Step>
</Steps>
</Tab>
</Tabs>

<a id="dock-robot"></a>
## Dock a robot

After you've uploaded the robot map to CDF, you can dock it. To dock the robot:

<Steps>
<Step title="Select Drive">
Select <span class="ui-element">Drive</span> from the dropdown list.
</Step>

<Step title="Select Dock">
Select the red (+) button, and then select <span class="ui-element">Dock</span>.
</Step>

<Step title="Navigate to the docking station">
Navigate the robot to the green docking fiducial, tap it, and select <span class="ui-element">Dock here</span>.
</Step>

<Step title="Release the emergency stop">
Release the emergency stop from the tablet once you finish controlling the robot.
</Step>
</Steps>

<a id="align-map-in-inrobot"></a>
## Align a map in InRobot
Aligning the map you've recorded and its navigation waypoints with the 3D map in InRobot lets you verify that the robot has no issues performing autonomous walking. You can also have a real-time visual feedback in the 3D view of the robot's position in relation to your 3D / 2D maps.

<Tip>
Before you begin, make sure that you've uploaded the robot map to CDF for it to be available in InRobot.
</Tip>

To align the map with waypoints:

<Steps>
<Step title="Open 3D ">
In InRobot, select the <span class="ui-element">3D</span> screen.
</Step>

<Step title="Select the map">
Select <span class="ui-element">Waypoint map</span> and select the robot map to work with.
</Step>

<Step title="Optional. Slice the model">
Select <span class="ui-element">Slice model</span> to remove or add layers on the map. It helps you find waypoints, the robot, and assets on the map.
</Step>

<Step title="Open alignment settings">
Select <span class="ui-element">Align waypoint map</span>. If you don't see this panel, you still need to upload a map to CDF.
</Step>

<Step title="Align the waypoints">
Update the values and use sliders to align the waypoints in the map with the 3D model.

- <span class="ui-element">Position X</span>: Move waypoints right or left. The higher the number, the more you move waypoints to the left on the map.
- <span class="ui-element">Position Y</span>: Move waypoints up and down. The higher the number, the higher the waypoints are on the map.
- <span class="ui-element">Position Z</span>: Move waypoints closer to or farther from the map.

<Tip>
Select <span class="ui-element">Drag and drop</span> to move all waypoints from right to left and up and down.
</Tip>

- <span class="ui-element">Rotation X</span>: Move waypoints horizontally.
- <span class="ui-element">Rotation Y</span>: Move waypoints vertically.
- <span class="ui-element">Rotation Z</span>: Move waypoints clockwise and counterclockwise.
</Step>

<Step title="Save the alignment">
Select <span class="ui-element">Save alignment</span> once you've completed the alignment.
</Step>
</Steps>


<!-- SOURCE_END: docs.cognite.com/docs\cdf\inrobot\guides\autowalk.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\cdf\inrobot\guides\spot.mdx -->
## File: docs.cognite.com/docs\cdf\inrobot\guides\spot.mdx

---
title: Set up Spot
description: Configure Spot-specific settings to connect Spot to InRobot.
content-type: procedure
audience: dataEngineer
experience-level: 200
lifecycle: use
article-type: article
---
Once you completed the general setup, configure the Spot-specific settings to connect Spot to InRobot. For better experience with the InRobot functionality, use Spot Enterprise with Spot Cam + IR and [Core I/O](https://www.bostondynamics.com/sites/default/files/inline-files/spot-core-io.pdf) with the internet access or [Spot EAP 2](https://www.bostondynamics.com/sites/default/files/inline-files/spot-eap-2.pdf).

## Connect Spot Core to the internet

Learn about the Spot CORE I/O [specifications](https://support.bostondynamics.com/s/article/Spot-CORE-I-O-User-Guide) and how to [connect](https://support.bostondynamics.com/s/article/Spot-CORE-I-O-User-Guide#FiveGsetup) the Spot CORE I/O to the internet.

## Download Spot integration

To download the Spot CORE I/O integration:

<Steps>
<Step title="Sign in">
Sign in to [Cognite Data Fusion](https://fusion.cognite.com) (CDF).
</Step>

<Step title="Open Extractors">
In <span class="ui-element">Data management</span>, select <span class="ui-element">Integrate</span> > <span class="ui-element">Extractors</span>.
</Step>

<Step title="Download the integration">
Select and download the <span class="ui-element">Cognite Boston Dynamics Spot Integration</span> extractor.
</Step>
</Steps>

Learn more about [data extraction](/cdf/integration/concepts/extraction#download-installation-files-and-documentation).

## Deploy Spot device agent to your Boston Dynamics Spot
### Set environment variables

Copy the **`.env`** template and follow the guidance below to fill in the environment variables.

<AccordionGroup>
<Accordion title=".env template">

```
#Deployment environment variables

TAG=<tag>
REPOSITORY=cognite/

# Cognite
COGNITE_CLIENT_ID=abc1234
COGNITE_CLIENT_SECRET=abc1234
COGNITE_TENANT_ID=abc1234
COGNITE_PROJECT=officerobotics
COGNITE_CLUSTER=greenfield

# Should not be set in most cases, only useful for debugging
# COGNITE_LOG_LEVEL=INFO
# LOG_LEVEL=WARNING
# ENABLE_LOGGING_TO_INROBOT=false
# UPLOAD_AGENT_LOGS_TO_CDF=true

# Enable sending metrics to Cognite.
# ENABLE_REPORT_METRICS=false

# Boston dynamics
BOSDYN_HOSTNAME=192.168.50.3
BOSDYN_CLIENT_USERNAME=user
BOSDYN_CLIENT_PASSWORD=<password>

MISSION_PARSER_SERVICE_PORT=5533
DATA_UPLOAD_SERVICE_PORT=5534
VIDEO_RECORDER_SERVICE_PORT=5535
SERVICE_HOST_IP=192.168.50.5
ROBOT_NAME="Spot Robot 1"
SPOT_DOCK_ID=520
ENABLE_ESTOP_HIJACK=False or True
ESTOP_TIMEOUT=9.0
MAX_SPEED_MS=0.5

# Video streaming
STREAM_TARGET_BITRATE=100
STREAM_FRAME_REFRESH_INTERVAL=30
STREAM_IDR_INTERVAL=30

VIDEO_STREAM_ID=5
WEBRTC_JANUS_ADDRESS=<janus instance|EU,US>
WEBRTC_JANUS_ROOM=<janus room id>
WEBRTC_JANUS_FORWARD_VIDEO_PORT=<forward port>
WEBRTC_JANUS_FORWARD_SECRET=<room admin password>
WEBRTC_CONFIG="config-twilio" or "config-janus"
```

</Accordion>
</AccordionGroup>

**Docker images**

| Parameter    | Description                                                         |
| ------------ | ------------------------------------------------------------------- |
| `TAG`        | Enter the latest version of the device agent, for example, `0.5.0`. |
| `REPOSITORY` | Enter `cognite/`.                                                   |

**Cognite**

| Parameter               | Description                                |
| ----------------------- | ------------------------------------------ |
| `COGNITE_CLIENT_ID`     | Enter client ID.                           |
| `COGNITE_CLIENT_SECRET` | Enter client secret.                       |
| `COGNITE_TENANT_ID`     | Enter Microsoft Entra tenant ID.                 |
| `COGNITE_PROJECT`       | Enter the name of the CDF project. |
| `COGNITE_CLUSTER`       | Enter the name of the CDF cluster. |

**Debugging**

Setting up these variables is useful for debugging only.

| Parameter               | Description                                |
| ----------------------- | ------------------------------------------ |
| `COGNITE_LOG_LEVEL`     |  Enter the log level of the logs sent to InRobot. Default value is `info`. |
| `LOG_LEVEL`             | Enter the log level of the logs send to `std::out`. Default value is `info`. |
| `ENABLE_LOGGING_TO_INROBOT` | Allow the forward of all logs to InRobot log window. |
| `UPLOAD_AGENT_LOGS_TO_CDF`  | Leave the default value to upload the agent logs to CDF. |

**Metrics**

| Parameter               | Description                                |
| ----------------------- | ------------------------------------------ |
| `ENABLE_REPORT_METRICS` |  Allow sending metrics to CDF.    |

**Boston Dynamics**

| Parameter                     | Description                                                                                                                                            |
| ----------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `BOSDYN_HOSTNAME`             | Enter the IP address of the robot found on the Spot CORE, for example, `192.168.50.3`.                                                             |
| `BOSDYN_CLIENT_USERNAME`      | Enter the `Spot` username, for example, _user_.                                                                                                        |
| `BOSDYN_CLIENT_PASSWORD`      | Enter the `Spot` password.                                                                                                                             |
| `MISSION_PARSER_SERVICE_PORT` | Leave the default value of 5533.                                                                                                                       |
| `DATA_UPLOAD_SERVICE_PORT`    | Leave the default value of 5534.                                                                                                                       |
| `SERVICE_HOST_IP`             | Enter the IP address of the Spot CORE on the robot network, for example, 192.168.50.5.                                                             |
| `SPOT_DOCK_ID`                | Enter the ID of the fiducial on the docking station, for example 520. **If the robot has no docking station, remove this parameter.**                  |
| `ENABLE_ESTOP_HIJACK`         | Allow the device agent to take control over the emergency stop from any other entity (tablet, other agents running on Spot). Default value is `False`. |
| `ESTOP_TIMEOUT` | Enter the number of seconds of the timeout for the services that keep the estop alive. |
| `MAX_SPEED_MS` | Enter the maximum walk speed of the robot in m/s. The maximum allowed value is 2.0. |

**Video streaming**

| Parameter                   | Description                                  |
| --------------------------- | -------------------------------------------- |
| `STREAM_TARGET_BITRATE` | Enter the compression level in target BPS. |
| `STREAM_FRAME_REFRESH_INTERVAL` | Enter the number of frames for how often the entire feed should be refreshed. |
| `STREAM_IDR_INTERVAL` | Enter the number of frames for how often an IDR message should be sent. |
| `VIDEO_STREAM_ID`           | Enter the video stream ID.                   |
| `WEBRTC_VERSION_TAG`        | Enter the version of the video docker image. |
| `WEBRTC_ADDRESS`            | Enter the address to the instance.           |
| `WEBRTC_ROOM`               | Enter the room ID.                           |
| `WEBRTC_FORWARD_VIDEO_PORT` | Enter the video port.                        |
| `WEBRTC_FORWARD_SECRET`     | Enter the room admin password.               |

### Install Device Agent on Spot CORE I/O
Copy the **`.env`** file to the Spot CORE I/O. Run the following command via the robot Ethernet or Wi-Fi:

```sh
scp -P 20022 ${path/to/.env} spot@${spots_ip}:~/
ssh -p 20022 spot@${spots_ip}
sudo mkdir -p /data/cognite/
sudo cp ~/.env /data/cognite/
```

Make sure you're standing close to the robot, and in the Spot CORE I/O interface, navigate to the <span class="ui-element">Extensions</span> page and upload the `CogniteRobot.spx` file. The extension starts running.

<Frame>
<img src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/inrobot/device_agent_extension.png" alt="Device agent extension"/>
</Frame>

## Print and place fiducials

Spot uses [fiducials](https://support.bostondynamics.com/s/article/About-Fiducials), QR code-like images, to align its internal map with the surroundings. Fiducials are essential at the start of each mission and should be placed along the entire mission path. Each point of Spot's route should have a visible fiducial. The default size for fiducial images is 146 mm x 146 mm, but you can adjust it in the robot's admin panel if needed.

<Note>
Use 500-series fiducials to identify the docking stations only.
</Note>

### Place fiducials

Recommendations to follow when positioning fiducials:

- Place one fiducial at the mission's starting location and one at the docking station.
- Place fiducials at ground level at the starting location.
- Tape fiducials securely against a wall in a location where they will persist. If fiducials shift after recording, the mission may no longer replay.
- Place fiducials low on the wall, with the top of the fiducial image at a human knee height, around 45-60 cm (18-24 in.) from the ground.
- Place fiducials in areas that are feature deserts, for example, a span of over 3m of a featureless white wall.
- Place fiducials on glass walls so that the robot avoids walking into the glass. You can also enhance glass walls with markers, such as AprilTags, post-it notes, or A4 sheets to create identifiable "features."
- Place fiducials at intersections to mark the area the robot can pass through several times. These loops can improve the quality of the map.

Things to avoid when positioning fiducials:

- Avoid using the same fiducial multiple times in a single mission. Each fiducial in a mission must be unique. However, you can use the same fiducial in other missions.
- Don't place fiducials in areas with inconsistent lighting. Shadowed or unevenly lit fiducials can have unreliable detections.
- Don't place fiducials on bright surfaces, such as windows.
- Don't place fiducials where they can be blocked, damaged, moved, or removed.

## Train a robot map and upload it to CDF
Learn how to train robot maps with the [Spot Autowalk functionality](/cdf/inrobot/guides/autowalk).

### Set up VPN (optional)

Setting up a VPN on the robot can be beneficial for an easier upgrade. VPN lets you upgrade the device agent and the robot remotely. You will need a VPN setup or host it yourself.


<!-- SOURCE_END: docs.cognite.com/docs\cdf\inrobot\guides\spot.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\cdf\inrobot\guides\usage.mdx -->
## File: docs.cognite.com/docs\cdf\inrobot\guides\usage.mdx

---
title: Use InRobot
description: Control robots remotely, create and run missions, schedule autonomous operations, and review collected mission data.
content-type: procedure
audience: dataEngineer
experience-level: 100
lifecycle: use
article-type: article
---
<a id="access-inrobot"></a>
## Access InRobot
Sign in to InRobot to start planning missions and navigating a robot.

<Steps>
<Step title="Navigate to InRobot">
Navigate to [https://inrobot.cogniteapp.com/](https://inrobot.cogniteapp.com).
</Step>

<Step title="Sign in with your credentials">
Enter your <span class="ui-element">Customer ID</span> and sign in with your credentials.
</Step>
</Steps>

Once you've signed in, you get access to the InRobot application and the robot connected to it. With InRobot, you can work with everyday tasks, such as:

- **Operator rounds**: An operator navigates the robot, inspects assets, and collects data.
- **Auto-scans**: A robot scans the area with a 360-view camera without the operator's navigation.
- **Condition monitoring**: A robot performs autonomous condition monitoring of several equipment pieces, collects the data, and sends it to Cognite Data Fusion ([CDF](/cdf)).

<a id="basic-inrobot-functionality"></a>
## Basic InRobot functionality

Before you plan missions, you should familiarize yourself with the actions you can perform with the robot. Within the InRobot application, depending on the robot's equipment and capabilities, you can:

<a id="take-photos"></a>
### Take photos

- **Pan-tilt-zoom (PTZ)**: The camera can turn left to right, tilt up and down, and zoom in and out of a scene.
- **360-degree**: The camera can capture every visual angle in the horizontal plane.
- **Pan-tilt-zoom infrared (PTZ IR)**: The camera can turn left to right, tilt up and down, and zoom in and out of a scene. It can also measure temperature and give a picture and video of an object.

<a id="record-videos"></a>
### Record videos

The robot can record videos up to 60 seconds with infrared, 360-degree, and pant-tilt-zoom cameras.

<a id="read-gauges"></a>
### Read gauges

- **Dial**: The dial gauge accurately measures minor differences in the height and length of mechanical components and displacements.
- **Digital**: The digital gauge checks a liquid's or gas's pressure and provides a direct reading on a digital display.
- **Level**: The level gauge measures the level of liquid or gasses inside a tank or a storage container.
- **Valve**: The ball valve controls the flow of fluids and gasses with the help of a rotating ball.

<a id="manage-controls"></a>
### Manage controls

You can find the alignment controls at the top right of the <span class="ui-element">3D</span> screen. On this panel, you can:

- <span class="ui-element">Select waypoint map</span>: Select the waypoint map to work with from the <span class="ui-element">Waypoint map</span> dropdown list. Set the map to default to see it every time you sign in to InRobot.

- <span class="ui-element">Align robot map</span>: Update the alignment of the existing waypoints:
  1. Select <span class="ui-element">Align robot map</span> and update the values.
  2. Select <span class="ui-element">Save alignment</span> to save the changes.
- <span class="ui-element">Slice model</span>: To remove or add layers on the map, use the <span class="ui-element">Slicing plane</span> slider or an input field. It helps you find waypoints, the robot, and assets on the map.
- <span class="ui-element">Move map to model</span>: Move the waypoints to the 3D model. Adjust the position of waypoints if needed.
- <span class="ui-element">Drag and drop map</span>: Move all waypoints from right to left and up and down.
- <span class="ui-element">Align 2D map</span>: Update the alignment of a 2D map to a 3D map by changing the 2D map coordinates, and then <span class="ui-element">Save 2D alignment</span>.

<a id="interact-with-video-stream"></a>
### Interact with video stream

Select the <span class="ui-element">Video room</span> screen to interact with the robot's video stream. In the top right, you can select the camera type and robot actions. Select the area on the screen where you want the robot to look or go.

<a id="navigate-the-robot"></a>
### Navigate the robot

- <span class="ui-element">Undock</span>: The robot can detach itself from the docking station.
- <span class="ui-element">Dock</span>: The robot will return to the docking station when it has completed the mission or requires a recharge.
- <span class="ui-element">Sit</span>: The robot will rest and save energy if you need to pause or don't need the robot.
- <span class="ui-element">Stand</span>: The robot starts moving.
- <span class="ui-element">Self-right</span>: The robot can get back on its feet if it has lost its balance.
- <span class="ui-element">Reboot robot</span>: The robot turns off and then turns on.
- <span class="ui-element">Emergency stop</span>: The robot's power turns off without docking the robot. Use the emergency stop if you can't stop the robot with the other commands or the operation is no longer safe.

<a id="control-the-robot-remotely"></a>
## Control the robot remotely

With InRobot, you can run automated missions, navigate the robot, and inspect assets. Before you operate the robot, make sure that:

- The robot is connected to the internet.
- The robot is fully charged.
- The robot is in a safe location.
- People aren't too close to the robot, and they're aware of the robot.

To control the robot remotely:

<Steps>
<Step title="Connect to the robot">
Select the robot and select <span class="ui-element">Control</span> to connect to the robot. The power will turn on automatically.
</Step>

<Step title="Navigate the robot">
In the upper right, select <span class="ui-element">More options</span> (&vellip;) to navigate the robot.
</Step>

<Step title="Optional. Adjust controls and navigate">
On the <span class="ui-element">Control</span> tab:
- In the <span class="ui-element">Navigation</span> section, select an asset to inspect and then select <span class="ui-element">Navigate</span>. You can also select a waypoint on the map.
- In the <span class="ui-element">Payload controls</span>, adjust the settings of a PTZ camera, dial gauge reading, etc., or take an image of the selected asset.
</Step>

<Step title="Dock the robot">
When the robot completes the mission, select <span class="ui-element">Dock</span> to return the robot to the docking station.
</Step>

<Step title="Optional. Release control">
On the <span class="ui-element">Control</span> tab, select <span class="ui-element">Release control</span> to let another operator control the robot.
</Step>
</Steps>

On the <span class="ui-element">Control</span> and <span class="ui-element">Mission</span> tabs, you can see the status of the robot when it's in control, running the mission, docked, stuck, etc. You can also check the battery charge and see when the robot is being charged. On the home page, you can see statuses for all your robots.

<a id="create-a-mission"></a>
## Create a mission

You need to plan and create missions for a robot to run without an operator's control.

To plan a mission

<Steps>
<Step title="Create a new mission">
Select <span class="ui-element">Mission</span> > <span class="ui-element">+ Create mission</span>. You don't need to control the robot to create missions.
</Step>

<Step title="Configure the mission">
Name the mission, select a robot type and a map from the dropdown list, and select <span class="ui-element">Create</span>. [Learn how to create robot maps](/cdf/inrobot/guides/autowalk).
</Step>

<Step title="Add an inspection point">
Select <span class="ui-element">+ Add inspection point</span> to create a task for the robot. An inspection point connects a waypoint, an action, and, optionally, an asset. It tells the robot where the asset or the waypoint is and what action to perform.
</Step>

<Step title="Optional. Select an asset">
Select an asset. The closest waypoint will be selected automatically if the asset is contextualized in the 3D model.
</Step>

<Step title="Select a waypoint">
Select a waypoint the robot navigates to to perform an action.
</Step>

<Step title="Optional. Edit the waypoint">
Select <span class="ui-element">Edit selected waypoint</span> to change the waypoint. This change applies only for this inspection point and doesn't permanently change the waypoint associated with the selected asset.
</Step>

<Step title="Select the robot's action">
For every inspection point, select the robot's action for this asset or waypoint.

<Note>
Add the same asset as many times as the number of actions you want the robot to perform on it, but keep the number of assets and actions within the recommended mission duration of 45 minutes.
</Note>
</Step>

<Step title="Save the mission">
Select <span class="ui-element">Save</span>.

<Check>
You've now created the mission and can view, edit, delete, or [run](#run-a-mission) it on the <span class="ui-element">Mission</span> tab.
</Check>
</Step>
</Steps>

<a id="run-a-mission"></a>
## Run a mission

Once you've created your first mission, you can assign the robot to perform independent asset inspection.

To run a mission:

<Steps>
<Step title="Take control and open Mission">
Take control of the robot and select <span class="ui-element">Mission</span>.
</Step>

<Step title="Find the mission">
In the <span class="ui-element">Search</span> field, enter the mission name or select one from the list.
</Step>

<Step title="Review and edit (optional)">
Optional. To review and edit inspection points, select the mission or select <span class="ui-element">More options</span> (&vellip;) > <span class="ui-element">Edit</span>.
</Step>

<Step title="Run the mission">
Select <span class="ui-element">More options</span> (&vellip;) > <span class="ui-element">Run</span>. You can follow the robot on the map via the robot cameras or logs.

When the mission is over, the robot navigates back to the docking station and sends the collected data to CDF.
</Step>
</Steps>

<a id="schedule-a-mission"></a>
## Schedule a mission

You can schedule the robot to perform single or recurring missions autonomously.

<Warning>
Make sure that nobody controls the robot before the start of the scheduled mission. Otherwise, the mission won't start.
</Warning>

To schedule a mission:

<Steps>
<Step title="Open the Schedule tab">
Select the <span class="ui-element">Schedule</span> tab > <span class="ui-element">+Schedule mission</span>.

or

Select the <span class="ui-element">Mission</span> tab > <span class="ui-element">More options</span> (&vellip;) next to the mission you want to schedule > <span class="ui-element">Schedule</span>.
</Step>

<Step title="Configure the schedule">
Schedule a single mission or recurring mission runs.

<Check>
Once you've scheduled the mission, you can edit or delete runs or skip a mission run without editing the schedule.
</Check>
</Step>
</Steps>

<a id="review-mission-data"></a>
## Review mission data

The data collected by the robot during its mission reaches CDF in one of three ways:

- The robot sends the data every 5 seconds.
- The robot stores the data collected and sends it at the end of its mission.
- If there's a connection issue, the robot stores the data it collects and sends it after reaching the docking station and connecting to the internet.

The data collected are available in InRobot and in [Cognite Data Fusion](https://fusion.cognite.com/).

In **InRobot**, the <span class="ui-element">Reports</span> tab lets you view the robot's mission details. You can also view the details of a specific run within a mission or get a closer look at the robot's actions, such as a measured value of a gauge or an infrared image of an asset.

In **CDF**, you can review the data with:

- [Charts](/cdf/explore/charts): Analyze and troubleshoot time series data from an asset reading, for example, temperature changes. Using the Equipment tag and Time Series ID tabs, you can find the time series associated with the asset.
- [Data Explorer](/cdf/integration/guides/exploration/data_explorer): Find, verify, visualize, and learn about the data collected by the robot.
- [Image and video management](/cdf/integration/guides/exploration/images_videos/): View data of asset readings of images and videos taken by the robot.

Based on the review, you can improve your robotics missions and ensure good-quality data capture.


<!-- SOURCE_END: docs.cognite.com/docs\cdf\inrobot\guides\usage.mdx -->

================================================================================


<!-- SOURCE_START: docs.cognite.com/docs\cdf\inrobot\index.mdx -->
## File: docs.cognite.com/docs\cdf\inrobot\index.mdx

---
title: About Cognite InRobot
description: Deploy and manage robots to automate data collection and equipment inspection.
content-type: concept
mode: wide
audience: dataEngineer
experience-level: 100
lifecycle: use
article-type: map
---
<Warning>
The features described in this section are currently in [beta](/cdf/product_feature_status) testing and are subject to change.
</Warning>

Cognite InRobot helps you plan, perform, and automate activities, such as running missions (operator rounds), scanning and monitoring equipment, and collecting different types of data.

The application integrates with:

- [Spot](https://www.bostondynamics.com/products/spot): A robot platform by Boston Dynamics.
- [ANYmal D](https://www.anybotics.com/anymal-autonomous-legged-robot/): An autonomous legged robot platform by ANYbotics.
- [ANYmal X](https://www.anybotics.com/anymal-ex-proof-inspection-robot/): An ex-proof inspection robot platform by ANYbotics.

## Get started

<CardGroup cols={3}>
  <Card 
    title="Record robot maps" 
    icon="map" 
    href="/cdf/inrobot/guides/autowalk"
  >
    Create and upload navigation waypoints, and align them with 3D models for autonomous mission planning.
  </Card>

  <Card 
    title="Operate robots and missions" 
    icon="gamepad" 
    href="/cdf/inrobot/guides/usage"
  >
    Control robots remotely, schedule autonomous operations, and review collected mission data in InRobot and CDF.
  </Card>

  <Card 
    title="Sign in to InRobot" 
    icon="arrow-right" 
    href="https://inrobot.cogniteapp.com/"
  >
    Access the InRobot application to plan missions, control robots, and monitor autonomous operations in real time.
  </Card>
</CardGroup>


<!-- SOURCE_END: docs.cognite.com/docs\cdf\inrobot\index.mdx -->

================================================================================
