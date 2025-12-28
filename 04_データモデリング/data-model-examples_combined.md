# data-model-examples Documentation
Generated from: data-model-examples



<!-- SOURCE_START: data-model-examples/CHANGELOG.md -->
## File: data-model-examples/CHANGELOG.md

# Changelog

**Please add new changelog items at the top!**

## Oct 13, 2023

- Fixed a bug in the restore_datamodel.py script making it pretty much useful for loading a data model dump.

## Oct 5, 2023

- Add support for dumping and loading of all data models, views, containers, and spaces, also
  including global system models, views, and containers. `dump_datamodel all|global <model>|all <folder>`
  will dump from all (including global spaces) and either the model or all models. Also views and containers
  without a data model will be dumped. The dump will be structured with a directory per space.
- `load_data.py --load datamodeldump <example>` will load from examples/<example>/data_model/, including one level
  of subdirectories.
- Add support for cleaning out all data in a CDF project, CAREFUL!! `delete_data.py --delete datamodelall --instances --dry-run all` will delete all configuration in your project and clean out all instances. Use `--dry-run`
  to see what will happen. Drop the `--instances` to only delete the configuration and fail on deletion of spaces
  if there are remaining instances.

## Oct 2, 2023 - Adapt the repo to continuous testing

- Support use of env var CDF_TOKEN to supply OAuth2 token and thus not require
  OAuth2 client config. The token can also be supplied when instantiating CDFToolConfig.
- Add installable package data_model_examples.utils for dev and test purposed (not public).
- Add support for supplying OAuth2 token when instantiating CDFToolConfig.
- Add optional directory parameter to load and delete functions for direct
    loading and deletion of data from a directory.
- Upgrade to Cognite Python SDK 6.28.0
- Add new .environ(attr) method to CDFToolConfig to allow for easy loading of
    environment variables.
- Add new load_readwrite_group() function to load.py to support loading of
  groups with a set of capabilities (should not be used to manipulate admin group due to
  risk of loosing access).
- Add support for dumping and loading of transformations to/from JSON and SQL files without
  auth configuration (new dump_transformations.py scropt and dump_transformations() and
  load_transformations_dump() functions in utils).

## Sep 7, 2023

- Fix inconsistency in default naming of imports
- Fix bug in delete_datamodel() - not including ToolGlobals

## Aug 30, 2023

- Update timeseries datapoints to today

## Aug 24, 2023

- Factor out all functions into the utils/ module to make them easy to load from other scripts.
- Support and validate use of the utils/ module from JupyterLite notebooks in Fusion UI.

## Jul 7, 2023

- Add "audience" to the OAuth2 client config to support Auth0 without the default audience set.
- [BREAKING] Upgrade to Python 3.11 as 3.10 no longer has binary distributions.

## June 30, 2023

- Add direct loading of data model from load_data.py
- Add describe_datamodel.py <space> <data_mdodel> as a way to understand a data model
- Upgrade CDF Python SDK to 3.4.8
- Upgrade python-dotenv to 1.0.0
- Refactor delete_datamodel to use more specific queries
- Add --dry-run as option to delete_data.py

## June 12, 2023

- Made publicly available


<!-- SOURCE_END: data-model-examples/CHANGELOG.md -->

================================================================================


<!-- SOURCE_START: data-model-examples/CODE_OF_CONDUCT.md -->
## File: data-model-examples/CODE_OF_CONDUCT.md


# Contributor Covenant Code of Conduct

## Our Pledge

We as members, contributors, and leaders pledge to make participation in our
community a harassment-free experience for everyone, regardless of age, body
size, visible or invisible disability, ethnicity, sex characteristics, gender
identity and expression, level of experience, education, socio-economic status,
nationality, personal appearance, race, caste, color, religion, or sexual
identity and orientation.

We pledge to act and interact in ways that contribute to an open, welcoming,
diverse, inclusive, and healthy community.

## Our Standards

Examples of behavior that contributes to a positive environment for our
community include:

* Demonstrating empathy and kindness toward other people
* Being respectful of differing opinions, viewpoints, and experiences
* Giving and gracefully accepting constructive feedback
* Accepting responsibility and apologizing to those affected by our mistakes,
  and learning from the experience
* Focusing on what is best not just for us as individuals, but for the overall
  community

Examples of unacceptable behavior include:

* The use of sexualized language or imagery, and sexual attention or advances of
  any kind
* Trolling, insulting or derogatory comments, and personal or political attacks
* Public or private harassment
* Publishing others' private information, such as a physical or email address,
  without their explicit permission
* Other conduct which could reasonably be considered inappropriate in a
  professional setting

## Enforcement Responsibilities

Community leaders are responsible for clarifying and enforcing our standards of
acceptable behavior and will take appropriate and fair corrective action in
response to any behavior that they deem inappropriate, threatening, offensive,
or harmful.

Community leaders have the right and responsibility to remove, edit, or reject
comments, commits, code, wiki edits, issues, and other contributions that are
not aligned to this Code of Conduct, and will communicate reasons for moderation
decisions when appropriate.

## Scope

This Code of Conduct applies within all community spaces, and also applies when
an individual is officially representing the community in public spaces.
Examples of representing our community include using an official e-mail address,
posting via an official social media account, or acting as an appointed
representative at an online or offline event.

## Enforcement

Instances of abusive, harassing, or otherwise unacceptable behavior may be
reported to the community leaders responsible for enforcement at
<support@cognite.com>.
All complaints will be reviewed and investigated promptly and fairly.

All community leaders are obligated to respect the privacy and security of the
reporter of any incident.

## Enforcement Guidelines

Community leaders will follow these Community Impact Guidelines in determining
the consequences for any action they deem in violation of this Code of Conduct:

### 1. Correction

**Community Impact**: Use of inappropriate language or other behavior deemed
unprofessional or unwelcome in the community.

**Consequence**: A private, written warning from community leaders, providing
clarity around the nature of the violation and an explanation of why the
behavior was inappropriate. A public apology may be requested.

### 2. Warning

**Community Impact**: A violation through a single incident or series of
actions.

**Consequence**: A warning with consequences for continued behavior. No
interaction with the people involved, including unsolicited interaction with
those enforcing the Code of Conduct, for a specified period of time. This
includes avoiding interactions in community spaces as well as external channels
like social media. Violating these terms may lead to a temporary or permanent
ban.

### 3. Temporary Ban

**Community Impact**: A serious violation of community standards, including
sustained inappropriate behavior.

**Consequence**: A temporary ban from any sort of interaction or public
communication with the community for a specified period of time. No public or
private interaction with the people involved, including unsolicited interaction
with those enforcing the Code of Conduct, is allowed during this period.
Violating these terms may lead to a permanent ban.

### 4. Permanent Ban

**Community Impact**: Demonstrating a pattern of violation of community
standards, including sustained inappropriate behavior, harassment of an
individual, or aggression toward or disparagement of classes of individuals.

**Consequence**: A permanent ban from any sort of public interaction within the
community.

## Attribution

This Code of Conduct is adapted from the [Contributor Covenant][homepage],
version 2.1, available at
[https://www.contributor-covenant.org/version/2/1/code_of_conduct.html][v2.1].

Community Impact Guidelines were inspired by
[Mozilla's code of conduct enforcement ladder][Mozilla CoC].

For answers to common questions about this code of conduct, see the FAQ at
[https://www.contributor-covenant.org/faq][FAQ]. Translations are available at
[https://www.contributor-covenant.org/translations][translations].

[homepage]: https://www.contributor-covenant.org
[v2.1]: https://www.contributor-covenant.org/version/2/1/code_of_conduct.html
[Mozilla CoC]: https://github.com/mozilla/diversity
[FAQ]: https://www.contributor-covenant.org/faq
[translations]: https://www.contributor-covenant.org/translations


<!-- SOURCE_END: data-model-examples/CODE_OF_CONDUCT.md -->

================================================================================


<!-- SOURCE_START: data-model-examples/CONTRIBUTING.md -->
## File: data-model-examples/CONTRIBUTING.md

# CONTRIBUTING

This repo uses [cookiecutter](https://cookiecutter.readthedocs.io/en/stable/index.html) to manage examples
as templates. If checking out this repo, you can build the examples with your settings in an interactive way
by installing cookiecutter and running `cookiecutter .`.
Cookiecutter will store config files for you in `~/.cookiecutter`.

## The structure of an example

Each example should have a README.md file that introduces the data set and how
to use it. You should also include a LICENSE.dataset.md file to clearly articulate the terms
the data set is licensed under.

You can use `./examples/apm_simple/` directory as a reference for how to set up your own example.
The structure should be the following under the `examples/` directory:

* make a folder with the name of the example. This name is used throughout as a reference for
    the example.
* put all the data in the `data` directory with each data type in a sub-directory.
* YAML config files for transformations should be in the `transformations` directory. These are exported easily
    from each transformation in the `Integrate - Transform Data` meny in the Fusion UI (`CLI Manifest``).
* a `datamodel.graphql` file with the CDF data model to load should be in the root
    of the example directory. This is exported from your data model in the Fusion UI visual data modeling editor.

## To add a new example

1. Follow the structure of the existing ones and add a new one in a separate directory.
1. Edit the cookiecutter.json file to add the defaults for your example:

    ```json
    "apm_simple_raw_db": "tutorial_apm",
    "apm_simple_datamodel": "tutorial_apm_simple",
    "apm_simple_space": "tutorial_apm_simple",
    "apm_simple_data_set": "Valhall_System_23",
    "apm_simple_data_set_desc": "Valhall_System_23"
    ```

1. Then edit inventory.json to add your example to the list so that the scripts can pick up
    the example. Note that the cookiecutter variables allow the user to substitute with their own values when using the template.

1. Edit the README.md in this folder (at the end) with a short description of the example.

1. You should be good to go. Run `cookiecutter .` to generate the build directory, now with your example.

1. Update the [Changelog](./CHANGELOG.md)

1. Once it's working, open a Pull Request on this repo.

1. Thanks a lot!!!

### Format of inventory.json

You can add any config attribute/value pair into the inventory.json file. The CDFToolConfig() class
(instantiated in ToolsGlobal) has a .config("attr") method that will return the value of the attribute
from inventory.json. You need to set .example = "<dataset>". Your dataset does not have to have a folder
in the examples/ directory. You can use inventory.json to set up the configuration for the use of the
Cognite CDF client. However, the various support functions for loading and deleting data will expect
a set of attributes to be present in inventory.json. These are:

```json
{
    "<dataset/example>": {
        "raw_db": "{{cookiecutter.movie_actors_raw_db}}/<name of raw db for the dataset>",
        "data_set": "{{cookiecutter.movie_actors_data_set}}/<name of data set used when loading>",
        "data_set_desc": "{{cookiecutter.movie_actors_data_set_desc}}/<longer description of data set>",
        "model_space": "{{cookiecutter.movie_actors_space}}/<the space to use for the data model>",
        "data_model": "{{cookiecutter.movie_actors_datamodel}}/<the name of the data model>"
    }
}
```

As you can see above, you can use a cookiecutter template variable in the inventory.json file. This
will allow the user to substitute their own values when running `cookiecutter`. Or, you can define
a static value and inventory.json has to be edited.

### A note on the data_model json files

> The scripts will default load the `datamodel.graphql`. The json files in the `data_model` directory
> are not used unless you use the `load_datamodel_dump()` function in `load_data.py` in utils/.
> This function is used by `restore_datamodel.py`.

The json files found in the data_model folder are basically a dump of the data model in CDF.
You can use the `dump_datamodel.py` script (see below) to dump the data model from a CDF tenant in this format.

## Debugging python scripts

The cookiecutter template variables can make it harder to debug. To simplify debugging of
Python scripts and allow you to debug the files (like load_data.py) directly in an IDE,
do the following:

1. Copy your own .env file into the **root folder** of this repo (where this file lives).
2. Bypass the inventory.json file by using a debug hardcoded configuration for your example in
    `utils/__init__.py` (see below how)
3. If you use Visual Studio Code, debug configurations can be found in `.vscode/launch.json`
4. Start debugging the regular way. The debug status will be picked up and the config and .env file will be overridden.

You can see how the examples_config dict is overridden and loads the root .env instead of the .env
in the `{{cookiecutter.buildfolder}}/utils/__init__.py` file:

```python
if _envfile and not _jupyter:
    from dotenv import load_dotenv

    if _debug_status:
        # If you debug within the {{cookiecutter.buildfolder}}, you will already have a .env file as a template there (git controlled).
        # Rather use .env from the repo root (git ignored)
        # Override...
        load_dotenv("../.env")
    else:
        load_dotenv()
if _debug_status or _jupyter:
    if _debug_status:
        logger.warning("WARNING!!!! Debugging is active. Using .env from repo root.")
    ToolGlobals = CDFToolConfig(
        client_name=_client_name,
        config={
            "movie_actors": {
                "raw_db": "tutorial_movies",
                "data_set": "tutorial_movies",
                "data_set_desc": "Data set for the movies-actor tutorial",
                "model_space": "tutorial_movies",
                "data_model": "tutorial_MovieDM",
            },
            "apm_simple": {
                "raw_db": "tutorial_apm",
                "data_set": "Valhall_System_23",
                "data_set_desc": "Valhall_System_23",
                "model_space": "tutorial_apm_simple",
                "data_model": "tutorial_apm_simple",
            },
        },
    )
else:
    # ToolGlobals is a singleton that is loaded once as this is a python module
    ToolGlobals = CDFToolConfig(client_name=_client_name)
```

## Alternative way to run if you clone the repo

1. Edit temporary `cookiecutter.json` with your real values (not template values)
2. Run `cookiecutter --no-input .`
3. You will find the built examples in either `./build` or the folder you specified
4. **Don't check in the modified cookiecutter.json!!**

## When developing examples

You can also run `cookiecutter --replay .` This is very useful if you are editing the templates in `{{cookiecutter.buildfolder}}`.

## dump_datamodel.py

The `dump_datamodel.py` script is used to dump the data model from a Cognite Data Fusion tenant. It takes
four arguments: <space> <model_name> <version> <target_dir>. You should dump it to a directory called
`data_model` under examples/<your_model>/.

If you then use the script `restore_datamodel.py <your_model>` to load the data model, you have
basically done a backup and restore of the data model. Please note that the data model dump (json files) is
not used default by the CLI tool to load the data model.

## Deploying the repo

This repo does not have a CI/CD pipeline, so merging to main will make the changes immediately available
to users who use cookiecutter.

When changing the examples and utils directories, a Cognite engineer should update these two directories
for the JupyterLite notebooks in Fusion UI (the dshublite private repo).
It is a direct copy of the files in this repo with
a CI/CD pipeline to deploy a new version of JupyterLite.


<!-- SOURCE_END: data-model-examples/CONTRIBUTING.md -->

================================================================================


<!-- SOURCE_START: data-model-examples/README.md -->
## File: data-model-examples/README.md

# Cognite Data Fusion Data Model Examples

This repository contains examples of how to work with data models in Cognite Data Fusion.

See [Changelog](./CHANGELOG.md) for recent changes.

The code is licensed under the [Apache License 2.0](LICENSE.code.md), while the documentation and methods are licensed
under [Creative Commons](LICENSE.docs.md). Each of the example data sets have their own license,

see the LICENSE.dataset.md in each examples directory.

## Set up of the CDF project and credentials

> To sign up for a trial project, go to <https://developer.cognite.com/signup>.

The default configuration here has been adapted to these trial projects. The default identity provider (IDP)
URLs and IDP_CLIENT_ID are all set up to use the Cognite trial project identity provider.
The only settings you need
to change when running `cookiecutter` (see below) are `CDF_PROJECT`, `IDP_CLIENT_ID`, and `IDP_CLIENT_SECRET`.
Your trial project starts with `trial-` and then a sequence of numbers and
letters. You can find the project name in the email you received when you signed up for the trial project
or in the URL in your browser when you sign in <https://cog-trials.fusion.cognite.com>.
You will also have the client credentials in the trial project welcome email.

If you use a standard CDF project and not a trial project, you should change the IDP_* settings to
match your project's identity provider settings. E.g. if you use Microsoft Active Directory, the
IDP_TOKEN_URL setting should be in the format:
`"https://login.microsoftonline.com/{{cookiecutter.IDP_TENANT_ID}}/oauth2/v2.0/token"`.

**If you don't have a project and you are a customer or partner, you can get the CDF project from <support@cognite.com>.**

See [README.me](./{{cookiecutter.buildfolder}}/README.md) for the details on permissions required. If you are using
a trial project, all the permissions have already been set up for you in the basic_access group in your project.

## Get started using cookiecutter

The easiest way to use this repo is not to check it out but rather use cookiecutter to make a local copy:

```bash
pip install cookiecutter
cookiecutter https://github.com/cognitedata/data-model-examples.git
```

You will be prompted to supply your credentials for a CDF project (you need client credentials, so a client
id and a client secret).

The minimum you need to configure is the following (IDP = Identity Provider, typically Azure Active Directory):

* CDF_CLUSTER (the prefix before cognitedata.com)
* CDF_PROJECT (your CDF project name)
* IDP_TENANT_ID (the tenant id for the CDF project in your identity provider, for Azure this can be .something.onmicrosft.com)
* IDP_CLIENT_ID (the client id for the service principal/service account you have set up in your IDP)
* IDP_CLIENT_SECRET (the client secret for the service principal/service account you have set up in your IDP)

The remaining configurations are optional, but you will be prompted for them, so just press enter if you do not
need to change them.

**The prefix tells you where you get the information from: CDF_* is for your CDF project, while IDP_* is for your
identity provider.**

You will get a `./build` folder (unless you change the default) that contains the examples configured and ready
for your CDF project!

Cookiecutter will store your configurations in `~/.cookiecutter_replay/`, so you can update the build folder
by running `cookiecutter --replay https://github.com/cognitedata/data-model-examples.git`

## How to use the examples after using cookiecutter

See the README.md in the build folder for how to use the examples.
Before using cookiecutter, this file is [README.me](./{{cookiecutter.buildfolder}}/README.md).

## Use this template tool for your own data

When you do `import utils`, the code in `__init__.py` will execute and give you a pre-initiated
ToolGlobals object. This object has a .client pre-configured CDF API client that you can
use to call all the functions in the Cognite Python SDK. It will try to load the .env file
with the credentials you have set up (either using cookiecutter or manually set the values in the .env
file). It also detects if you are debugging, and if so, it will use the .env file in the root of the repo,
not the one in the `{{cookicutter.buildfolder}}` (as that one is a template).

So, ToolGlobals gives you a pre-configured CDF API client, a set of configuration attributes you can use
in your app (and used by the utils/ functions), as well as a simple way of loading and deleting data sets
using the utils/ functions. You simply set the example directory where your data is stored by setting
`ToolGlobals.example="folder_name"` and there should be an entry in inventory.json configuring
the data set, raw database, etc used when loading and deleting the data set.

If you want to use the code from this repo for your own project (it loads .env and/or environment variables
correclty), you can copy the utils/ directory
and the inventory.json file to your own project, edit inventory.json with an entry for your project
and do `import utils`. If you don't need config variables or load/delete data sets, you can create your
own ToolGlobals for calling the CDF API client by either explicitly setting the config to an empty dict
or deleting inventory.json:

```python
import utils
ToolGlobals = CDFToolConfig(client_name="name_of_your_app", config={})
ToolGlobals.client.raw.tables.list("raw_db_name")
```

If inventory.json is not present and you don't supply an empty config, all the variables will be set to `default`.
The configuration attributes are only used by the functions found in utils/, not the CDF SDK client.

CDFToolConfig() also gives you .environ(attr, default, fail) which allows you to get environment variables,
set a default, and optionally NOT fail if the variable is not found in the enviroment.
You can easily see what is configured from the environment using `print(CDFToolConfig())`.

If you have a generated OAuth2 Bearer token, you can set the environment variable CDF_TOKEN to the token
or you can supply the token when instantiating CDFToolConfig():

```python
import utils
ToolGlobals = CDFToolConfig(client_name="name_of_your_app", token="your_token")
ToolGlobals.client.raw.tables.list("raw_db_name")
```

[CONTRIBUTING.md](./CONTRIBUTING.md) explains more about how the data sets are set up.

And finally, if you just want to get inspired and copy some code to configure your own CDF SDK client, `utils/utils.py`
in `{{cookiecutter.buildfolder}}` is a good place to start.

## Contributing to this template repository

See [CONTRIBUTING.md](./CONTRIBUTING.md) for how to contribute to the templates or new examples.


<!-- SOURCE_END: data-model-examples/README.md -->

================================================================================


<!-- SOURCE_START: data-model-examples/{{cookiecutter.buildfolder}}\examples\apm_simple\LICENSE.dataset.md -->
## File: data-model-examples/{{cookiecutter.buildfolder}}\examples\apm_simple\LICENSE.dataset.md

# License for the Asset Performance Management (APM) data set

Please see [Terms of Use](./Open%20Industrial%20Data%20-%20Terms%20of%20Use.pdf) for the APM data set.

The Valhall compressor operational states Jupyter Notebook is licensed under Apache-2.


<!-- SOURCE_END: data-model-examples/{{cookiecutter.buildfolder}}\examples\apm_simple\LICENSE.dataset.md -->

================================================================================


<!-- SOURCE_START: data-model-examples/{{cookiecutter.buildfolder}}\examples\apm_simple\README.md -->
## File: data-model-examples/{{cookiecutter.buildfolder}}\examples\apm_simple\README.md

# Asset Performance Management Simple Example

## About the data

This data is from [Open Industrial Data](https://hub.cognite.com/open-industrial-data-211/what-is-open-industrial-data-994).

Below is a snippet from the Cognite Hub website describing the data:

> The data originates from a single compressor on Aker BP’s Valhall oil platform in the North Sea. Aker BP selected the first stage compressor on the Valhall because it is a subsystem with
> clearly defined boundaries, rich in time series and maintenance data.
>
> AkerBP uses Cognite Data Fusion to organize the data stream from this compressor. The data set available in Cognite Data Fusion includes time series data, maintenance history, and
> Process & Instrumentation Diagrams (P&IDs) for Valhall’s first stage compressor and associated process equipment: first stage suction cooler, first stage suction scrubber, first stage
> compressor and first stage discharge coolers. The data is continuously available and free of charge.
>
>By sharing this live stream of industrial data freely, Aker BP and Cognite hope to accelerate innovation within data-heavy fields. This includes predictive maintenance, condition
> monitoring, and advanced visualization techniques, as well as other new, unexpected applications. Advancement in these areas will directly benefit Aker BP’s operations and will also
>improve the health and outlook of the industrial ecosystem on the Norwegian Continental Shelf.

## Investigation into operational states of a compressor

The Jupyter notebook will show you how to query the data model using the Cognite Python SDK. The time series loaded in the data set are used to explore
various modes of operation for a compressor on Valhall.

See [Valhall Operational States 2.ipynb](./Valhall%20Operational%20States%202.ipynb)

## Use Charts

You can also use the Charts application to explore the data. Search for time series and add them to your chart.


<!-- SOURCE_END: data-model-examples/{{cookiecutter.buildfolder}}\examples\apm_simple\README.md -->

================================================================================


<!-- SOURCE_START: data-model-examples/{{cookiecutter.buildfolder}}\examples\movie_actors\LICENSE.dataset.md -->
## File: data-model-examples/{{cookiecutter.buildfolder}}\examples\movie_actors\LICENSE.dataset.md

# License for the movies-actors data set

The data set is from <https://www.kaggle.com/datasets/harshitshankhdhar/imdb-dataset-of-top-1000-movies-and-tv-shows>

The data set is licensed under CC0. To view a copy of this license, visit <https://creativecommons.org/publicdomain/zero/1.0/>


<!-- SOURCE_END: data-model-examples/{{cookiecutter.buildfolder}}\examples\movie_actors\LICENSE.dataset.md -->

================================================================================


<!-- SOURCE_START: data-model-examples/{{cookiecutter.buildfolder}}\examples\movie_actors\README.md -->
## File: data-model-examples/{{cookiecutter.buildfolder}}\examples\movie_actors\README.md

# Introduction to data modeling

Please see the [license for this data set](LICENSE.dataset.md).


<!-- SOURCE_END: data-model-examples/{{cookiecutter.buildfolder}}\examples\movie_actors\README.md -->

================================================================================


<!-- SOURCE_START: data-model-examples/{{cookiecutter.buildfolder}}\LICENSE.code.md -->
## File: data-model-examples/{{cookiecutter.buildfolder}}\LICENSE.code.md

# Copyright 2023 Cognite AS

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at
    
      http://www.apache.org/licenses/LICENSE-2.0
    
    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.


<!-- SOURCE_END: data-model-examples/{{cookiecutter.buildfolder}}\LICENSE.code.md -->

================================================================================


<!-- SOURCE_START: data-model-examples/{{cookiecutter.buildfolder}}\LICENSE.docs.md -->
## File: data-model-examples/{{cookiecutter.buildfolder}}\LICENSE.docs.md

# Creative Commons License

The documentation and methods (not the code) used in this repository are licensed under CC0.
To view a copy of this license, visit <https://creativecommons.org/publicdomain/zero/1.0/>


<!-- SOURCE_END: data-model-examples/{{cookiecutter.buildfolder}}\LICENSE.docs.md -->

================================================================================


<!-- SOURCE_START: data-model-examples/{{cookiecutter.buildfolder}}\README.md -->
## File: data-model-examples/{{cookiecutter.buildfolder}}\README.md

# Cognite Data Fusion Data Model Examples

These examples are examples of how to work with data models and data sets in Cognite Data Fusion.

The code is licensed under the [Apache License 2.0](LICENSE.code.md), while the documentation and methods are licensed
under [Creative Commons](LICENSE.docs.md). Each of the example data sets has its own license, see the LICENSE.dataset.md in each
directory.

## KNOWN ISSUES

* N/A

## Set up the CDF project and credentials

> To sign up for a trial project, go to <https://docs.cognite.com/trial>.

The default configuration here has been adapted to these trial projects. The default identity provider (IDP)
URLs and IDP_CLIENT_ID are all set up to use the Cognite trial project identity provider.
The only settings you must change when running `cookiecutter` (see below) are the `CDF_PROJECT`, `IDP_CLIENT_ID`, and the `IDP_CLIENT_SECRET`.
Your trial project starts with `trial-` and then a sequence of numbers and
letters. You can find the project name in the email you received when you signed up for the trial project
or in the URL in your browser when you sign in <https://cog-trials.fusion.cognite.com>.
You will also have the client credentials in the trial project welcome email.

If you are use a standard CDF project and not a trial project, you should change the IDP_* settings to
match your project's identity provider settings. E.g. if you use Microsoft Active Directory, the
IDP_TOKEN_URL setting should be in the format:
`"https://login.microsoftonline.com/{{cookiecutter.IDP_TENANT_ID}}/oauth2/v2.0/token"`

**If you don't have a project and you are a customer or partner, you get the CDF project
from <support@cognite.com>. See the [Python SDK Introduction Documentation](https://developer.cognite.com/dev/guides/sdk/python/python_auth_oidc/) to get the credentials.**

**NOTE: If you are using a trial project, the below access scopes have already been set up for you.**

The minimum CDF access scopes needed are (If you scope to data sets, you need to make sure you include all data sets for all
the examples you want to run or use the same data set for all examples):

* RAW: LIST, READ, WRITE either on all or scoped to the databases you configure for all examples
* dataSetsAcl: OWNER, READ, WRITE either on all or scoped to the data sets you configure for all examples
* dataModelsAcl: READ, WRITE either on all or scoped to the scopes you configure for all examples
* dataModelInstancesAcl: READ, WRITE either on all or scoped to the scopes you configure for all examples
* transformationsAcl: READ, WRITE either on all or scoped to the data sets you configure for all examples
* timeSeriesAcl: READ, WRITE either on all or scoped to the data sets you configure for all examples
* filesAcl: READ, WRITE either on all or scoped to the data sets you configure for all examples

## Prerequisites to run the examples

Each of the folders with examples has a README.md with more details, but you will need some prerequisites installed for all
examples.

1. Install Python and [pip](https://packaging.python.org/en/latest/tutorials/installing-packages/)
    if you do not already have them. We recommend Python v3.11. (The examples are tested using Python 3.10 and 3.11).

1. Change directory into the `./build` folder

1. Install the Python requirements (or use poetry if you prefer that package manager):

    ```bash
    pip install -r requirements.txt
    ```

    or

    ```bash
    poetry install
    ```

## Load the data

**Once you have installed pre-requisites, you can do the super-fast bootstrap of the dataset by running:**

```bash
./clean_and_load.sh <example>
```

This script will drop all data (if present), re-load everything, and then run the transformations to
get data into your data model.

Or, you can do the steps one by one (you can also look at the `./clean_and_load.sh` script):

1. Load the environment variables from the .env file in the build folder:

    ```bash
    cd build
    set -a; source .env; set +a
    ```

    This is necessary for the Python scripts (also transformations-cli tool if you use it) to find the correct credentials.

1. To load the data set, use `load_data.py` script (if you use poetry, remember to run `poetry shell` first)

    Run `./load_data.py --help` to see the options.

    If you want to load all the data in the apm_simple data set, run:

    ```bash
    ./load_data.py --drop apm_simple
    ```

    Adding `--drop` will delete all the data that can be deleted before
    loading the data fresh. **Please note that CDF datasets cannot be deleted, they will be empty after a delete!!**

    *This command will also default load the transformations in the example directory. You can also use the transformations-cli tool to load the transformations (and more). The environment variables needed for transformations are already set.*

1. Run the transformations

    To run the transformations for a specific example, run:

    ```bash
    ./run_transformations.py <example_dir>
    ```

    This will run all the transformations in the example folder. If you want to run a specific
    transformation, you can specify `--file name-of-transformation.yaml` as an option.

1. Look at the README.md in each `./example/*` folder. It will tell you if there are more example-specific options.

## About the examples

This library of examples will be continuously maintained and expanded.

### movie_actors

The movie_actors example contains CSV raw data that are loaded into CDF RAW, a simple data model,
and transformations that will ingest data from the RAW database into the data model.

The example is simple to understand and the documentation can be found at: <https://docs.cognite.com/cdf/data_modeling/guides/upload_demo_dm>.

### apm_simple

The apm_simple example is a more full-fledged data set with a data model that exemplifies how to
store data within the Asset Performance Management space. The model itself is simplified and
should not be used for production purposes, but illustrates some key principles.

In addition to raw CSV data, transformations, and a data model, this data set also contains
a select few time series from a compressor at Valhall from the North Sea. Also, a set of
Process and Instrumentation Diagrams (P&IDs) are included.

This makes this example more suitable for testing out the wider set of CDF functionalities, like
Charts for timeseries investigations and plotting.

See [./examples/apm_simple/README.md](./examples/apm_simple/README.md) for more information.

### Data Modeling CLI tool

You can install the cdf-cli tool for data models from npm:

```bash
sudo npm install -g @cognite/cdf-cli
```

Alternatively, run without `sudo` and add `~/.npm/bin` to your `PATH` environment variable.

You can then log into the tool:

```bash
set -a 
source ./.env
set +a
echo "Logging into CDF using environment variables from .env..."
cdf login --client-id=$IDP_CLIENT_ID --client-secret=$IDP_CLIENT_SECRET --tenant=$IDP_TENANT_ID --cluster=$CDF_CLUSTER $CDF_PROJECT
cdf status
```

And then load the data model:

```bash
cdf dm create --interactive false --external-id data_model_name --space space_name data_model_name
cdf dm publish --interactive false --external-id data_model_name --space space_name --version 1 --file ./examples/<example>/datamodel.graphql
```

## Structure of each example

In the `./examples` directory, you will find the following:

* `LICENSE.dataset.md`: The license for the data set.
* `README.md`: More details about the data set.
* `requirements.txt/pyproject.toml`: if there are any additional requirements for using the data set (not
    for loading the data set, that is handled by the `requirements.txt` in the build folder).
* `data/` directory with files, raw, and timeseries that are loaded by load_data.py.
* `transformations/` directory with transformations definitions (YAML files) that are loaded by
    load_data.py.
* `data_model/` directory with data model definitions (json files) that are loaded by load_data.py
    using the Python SDK (and the /model/ REST APIs).
* `datamodel.graphql`: the graphql schema for the data model that you can load info CDF from the
    CDF UI. This schema file can also be used by the CDF CLI tool for data models (see below).

## describe_datamodel.py

The `describe_datamodel.py <space> <data_model>` script will describe any data model (no writing happens) and
can be used on your own data models as well (not only the examples).

An example for the apm_simple model can be seen below.

```txt
Describing data model ({model_name}) in space ({space_name})...
Verifying access rights...
Found the space tutorial_apm_simple with name (None) and description (None).
  - created_time: 2023-06-29 10:41:41.282000
  - last_updated_time: 2023-06-29 10:41:46.139000
Found 3 containers in the space tutorial_apm_simple:
  Asset
  WorkItem
  WorkOrder
Found data model tutorial_apm_simple in space tutorial_apm_simple:
  version: 1
  global: False
  description: None
  created_time: 2023-06-29 10:41:41.963000
  last_updated_time: 2023-06-29 10:41:47.444000
  tutorial_apm_simple has 3 views:
    Asset, version: aea363cc6d37ba
       - properties: 11
       - used for nodes
       - implements: None
       - direct relation 1:1 parent --> (tutorial_apm_simple, Asset, aea363cc6d37ba)
       - edge relation 1:MANY children -- outwards --> (tutorial_apm_simple, Asset, aea363cc6d37ba)
    WorkOrder, version: 5f8749a07c2940
       - properties: 21
       - used for nodes
       - implements: None
       - edge relation 1:MANY workItems -- outwards --> (tutorial_apm_simple, WorkItem, f9c8dbdc9ddb2d)
       - edge relation 1:MANY linkedAssets -- outwards --> (tutorial_apm_simple, Asset, aea363cc6d37ba)
    WorkItem, version: f9c8dbdc9ddb2d
       - properties: 10
       - used for nodes
       - implements: None
       - direct relation 1:1 workOrder --> (tutorial_apm_simple, WorkOrder, 5f8749a07c2940)
       - edge relation 1:MANY linkedAssets -- outwards --> (tutorial_apm_simple, Asset, aea363cc6d37ba)
Total direct relations: 2
Total edge relations: 4
------------------------------------------
Found in total 15628 edges in space tutorial_apm_simple spread over 4 types:
  WorkOrder.workItems: 1536
  WorkItem.linkedAssets: 6494
  WorkOrder.linkedAssets: 6493
  Asset.children: 1105
------------------------------------------
Found in total 3961 nodes in space tutorial_apm_simple across all views and containers.
  1105 nodes of view Asset.
  1314 nodes of view WorkOrder.
  1536 nodes of view WorkItem.
```


<!-- SOURCE_END: data-model-examples/{{cookiecutter.buildfolder}}\README.md -->

================================================================================


<!-- SOURCE_START: data-model-examples/{{cookiecutter.buildfolder}}\utils\README.md -->
## File: data-model-examples/{{cookiecutter.buildfolder}}\utils\README.md

# The utils directory

The transformation*.py files and load_yaml.py file are all from the transformations_cli tool.
They are loaded here to avoid having to install the transformations_cli tool and to avoid
dependency mismatch between different versions of the CDF SDK.

The utils.py and __init__.py files defines ToolGlobals, a single object instance (singleton)
that gives you simple access to the CDF API client and an ACL verification tool.

See docs of CDFToolConfig (defined in utils.py) for more information.


<!-- SOURCE_END: data-model-examples/{{cookiecutter.buildfolder}}\utils\README.md -->

================================================================================
