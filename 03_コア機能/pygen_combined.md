# pygen Documentation
Generated from: pygen



<!-- SOURCE_START: pygen/docs\api\api.md -->
## File: pygen/docs\api\api.md


::: cognite.pygen


<!-- SOURCE_END: pygen/docs\api\api.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\api\demo.md -->
## File: pygen/docs\api\demo.md

::: cognite.pygen.demo


<!-- SOURCE_END: pygen/docs\api\demo.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\api\exceptions.md -->
## File: pygen/docs\api\exceptions.md

::: cognite.pygen.exceptions


<!-- SOURCE_END: pygen/docs\api\exceptions.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\api\utils.md -->
## File: pygen/docs\api\utils.md

::: cognite.pygen.utils


<!-- SOURCE_END: pygen/docs\api\utils.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\api\utils_cdf.md -->
## File: pygen/docs\api\utils_cdf.md

::: cognite.pygen.utils.cdf


<!-- SOURCE_END: pygen/docs\api\utils_cdf.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\api\utils_external_id_factory.md -->
## File: pygen/docs\api\utils_external_id_factory.md

::: cognite.pygen.utils.external_id_factories


<!-- SOURCE_END: pygen/docs\api\utils_external_id_factory.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\api\utils_mock_generator.md -->
## File: pygen/docs\api\utils_mock_generator.md

::: cognite.pygen.utils.mock_generator


<!-- SOURCE_END: pygen/docs\api\utils_mock_generator.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\api\utils_text.md -->
## File: pygen/docs\api\utils_text.md

::: cognite.pygen.utils.text


<!-- SOURCE_END: pygen/docs\api\utils_text.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\cli.md -->
## File: pygen/docs\cli.md

## Create Python SDK using CLI

Given a Data Model with external id `Movie` in the space `movies` in CDF, the following command will generate a Python SDK


=== "Microsoft Entra ID (Azure AD)"

    ```bash
    pygen generate --space movies \
        --external-id Movie \
        --version 1 \
        --tenant-id <tenant-id> \
        --client-id <client-id> \
        --client-secret <client-secret> \
        --cdf-cluster <cdf-cluster> \
        --cdf-project <cdf-project>
        --instance-space <my_instance_space>
    ```

=== "Generic OIDC authentication"

    ```bash
    pygen generate --space movies \
        --external-id Movie \
        --version 1 \
        --token-url <token-url> \
        --scopes <scopes> \
        --audience <audience> \
        --client-id <client-id> \
        --client-secret <client-secret> \
        --cdf-cluster <cdf-cluster> \
        --cdf-project <cdf-project>
        --instance-space <my_instance_space>
    ```

In addition, the following options are available and recommended to be used:

* `--output-dir` This is the directory where the generated SDK will be placed.
* `--top-level-package` The top level package for where to place the SDK, for example `movie_sdk.client`.
* `--client-name` Client name for the generated client expected to be given in `PascalCase`, for example `MovieClient`.
* `--instance-space` The instance space to use for the generated SDK.

For more information about the available options, see `pygen --help`.

!!! warning "Multiple Data Models"
    The CLI does NOT support generating an SDK for multiple data models. Then, you have to use a `pyproject.toml` file,
    see below.


### <code>pyproject.toml</code>
When running the command `pygen generate`, the program looks for a `pyproject.toml` and `.secret.toml` file in the current
working directory. If the `pyproject.toml` file is found and has a `[tool.pygen]` section, the values from this section will be used as
default values for the command line arguments. The `.secret.toml` is expected to have a `[cognite]` section with a `client_secret` value.

Below is an example of the values that can be set in the `pyproject.toml` and `.secret.toml` files.

=== "pyproject.toml"

    ```toml
    [tool.pygen]
    data_models = [
        ["IntegrationTestsImmutable", "Movie", "2"],
    ]
    tenant_id = "<cdf-project>"
    client_id = "<client-id>"
    cdf_cluster = "<cdf-cluster>"
    cdf_project = "<cdf-project>"
    top_level_package = "movie_domain.client"
    client_name = "MovieClient"
    output_dir = "."
    instance_space = "my_instance_space"
    ```

=== ".secret.toml"

    ``` bash
    [cognite]
      client_secret = "<client-id>"
    ```


<!-- SOURCE_END: pygen/docs\cli.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\cognite_functions.md -->
## File: pygen/docs\cognite_functions.md

## Introduction

After you have built your logic with the newly generated data model SDK, you might want to deploy it to
Cognite Functions.

Cognite Functions enables Python code to be hosted and executed in the cloud, on demand or by using a schedule.

A few useful links for Cognite Functions:

- [Cognite Functions documentation](https://docs.cognite.com/cdf/functions/)
- Deploy and manage your Cognite Functions with [Cognite Toolkit](https://docs.cognite.com/cdf/deploy/cdf_toolkit/)
- Expected folder structure for Cognite Toolkit [here](https://docs.cognite.com/cdf/deploy/cdf_toolkit/references/configs#functions)
- [What is a wheel?](https://realpython.com/python-wheels/)

## Deploying your generated SDK to Cognite Functions

Cognite Functions supports private packages in your function deployment, and `pygen` can generate a `wheel` package of
your data model SDK. To generate a `wheel` package, use the [build_wheel](api/api.html#cognite.pygen.build_wheel) function.

For example, given the data model (`power-models`, `Windmill`, `1`) we generated in the
[Generation](usage/generation.html) guide,
we can build a `wheel` package with the following code:

```python
from cognite.client import CogniteClient
from cognite.pygen import build_wheel

client = CogniteClient()

build_wheel(("power-models", "Windmill", 1), client)
```

This will output a `wheel` package, `windmill-1.0.0-py3-none-any.whl`, in the `dist` folder of the current working directory.
**Note** The version of the package will always be `1.0.0` as the version of a data model can be an arbitrary string,
while the version of a package must be a valid semantic version.

To deploy generated SDK to Cognite Functions, you can use the `cognite-toolkit` CLI with the following structure,
```
📦functions/
┣ 📂my_function - The function folder
┃ ┣ 📦windmill-1.0.0-py3-none-any.whl - The generated wheel package
┃ ┣ 📜__init__.py - Empty file (required to make the function into a package)
┃ ┣ 📜handler.py -  Module with script inside a handle function
┃ ┗ 📜requirements.txt - Explicitly states the dependencies needed to run the handler.py script.
┣ 📜my_function.Function.yaml - The configuration file for the function
┣ 📜my_schedule.Schedule.yaml - The configuration file for the function schedule(s)
```

=== "handler.py"

    ```python
    from cognite.client import CogniteClient
    from windmill WindmillClient


    def handle(client: CogniteClient, data: dict):
        windmill_client = WindmillClient(client)
        windmills = windmill_client.windmill.list(limit=10)
        print(windmills)
        return "Success"

    ```

=== "requirements.txt"

    ```txt
    # The Cognite SDK should be specified and match the version used to generate the SDK.
    cognite-sdk==7.54.4
    # This is the syntax for adding a private wheel in a Cognite Function.
    # Note the 'function' prefix is not a placeholder, it is a required prefix for private packages.
    function/windmill-1.0.0-py3-none-any.whl
    ```

=== "my_function.Function.yaml"

    ```yaml
    name: my_function
    externalId: fn_my_function
    owner: Anonymous
    description: 'Demo of a function with a pygen generated SDK'
    runtime: 'py311'
    ```

=== "my_schedule.Schedule.yaml"

    ```yaml
    - name: "daily-8am-utc"
      functionExternalId: fn_my_function
      description: "Run every day at 8am UTC"
      cronExpression: "0 8 * * *"
      data:
        some: "data"
    ```

After you have the folder structure set up, you can deploy the function using the `cognite-toolkit` CLI,
go to [Deploy](https://docs.cognite.com/cdf/deploy/cdf_toolkit/guides/configure_deploy_modules)
for more information on how to use the CLI.


<!-- SOURCE_END: pygen/docs\cognite_functions.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\config\filtering_methods.md -->
## File: pygen/docs\config\filtering_methods.md

::: cognite.pygen.config.Filtering


<!-- SOURCE_END: pygen/docs\config\filtering_methods.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\config\index.md -->
## File: pygen/docs\config\index.md

::: cognite.pygen.config.PygenConfig


<!-- SOURCE_END: pygen/docs\config\index.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\config\naming.md -->
## File: pygen/docs\config\naming.md


::: cognite.pygen.config.NamingConfig

::: cognite.pygen.config.FieldNaming

::: cognite.pygen.config.DataClassNaming

::: cognite.pygen.config.APIClassNaming

::: cognite.pygen.config.MultiAPIClassNaming

::: cognite.pygen.config.Naming

::: cognite.pygen.config.Case

::: cognite.pygen.config.Number


<!-- SOURCE_END: pygen/docs\config\naming.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\developer_docs\core\generators.md -->
## File: pygen/docs\developer_docs\core\generators.md

::: cognite.pygen._core.generators


<!-- SOURCE_END: pygen/docs\developer_docs\core\generators.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\developer_docs\core\models\api_classes.md -->
## File: pygen/docs\developer_docs\core\models\api_classes.md

::: cognite.pygen._core.models.api_classes


<!-- SOURCE_END: pygen/docs\developer_docs\core\models\api_classes.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\developer_docs\core\models\data_classes.md -->
## File: pygen/docs\developer_docs\core\models\data_classes.md

::: cognite.pygen._core.models.data_classes


<!-- SOURCE_END: pygen/docs\developer_docs\core\models\data_classes.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\developer_docs\core\models\fields\base.md -->
## File: pygen/docs\developer_docs\core\models\fields\base.md

::: cognite.pygen._core.models.fields.base


<!-- SOURCE_END: pygen/docs\developer_docs\core\models\fields\base.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\developer_docs\core\models\fields\cdf_reference.md -->
## File: pygen/docs\developer_docs\core\models\fields\cdf_reference.md

::: cognite.pygen._core.models.fields.cdf_reference


<!-- SOURCE_END: pygen/docs\developer_docs\core\models\fields\cdf_reference.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\developer_docs\core\models\fields\connections.md -->
## File: pygen/docs\developer_docs\core\models\fields\connections.md

::: cognite.pygen._core.models.fields.connections


<!-- SOURCE_END: pygen/docs\developer_docs\core\models\fields\connections.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\developer_docs\core\models\fields\primitive.md -->
## File: pygen/docs\developer_docs\core\models\fields\primitive.md

::: cognite.pygen._core.models.fields.primitive


<!-- SOURCE_END: pygen/docs\developer_docs\core\models\fields\primitive.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\developer_docs\core\models\filter_methods.md -->
## File: pygen/docs\developer_docs\core\models\filter_methods.md

::: cognite.pygen._core.models.filter_methods


<!-- SOURCE_END: pygen/docs\developer_docs\core\models\filter_methods.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\developer_docs\core\templates.md -->
## File: pygen/docs\developer_docs\core\templates.md

::: cognite.pygen._core.templates


<!-- SOURCE_END: pygen/docs\developer_docs\core\templates.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\developer_docs\core\validation.md -->
## File: pygen/docs\developer_docs\core\validation.md

::: cognite.pygen._core.validation


<!-- SOURCE_END: pygen/docs\developer_docs\core\validation.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\developer_docs\index.md -->
## File: pygen/docs\developer_docs\index.md

# Developer Documentation

This section contains the developer documentation for the project. It is intended for developers who want to
contribute to the project or understand how it works.

!!! warning "Internal Documentation"
    This documentation contains the internal structure of the project. Internal modules, classes, and functions
    are subject to change without notice. If you are a user of the project, please
    refer to the [User Documentation](../api/api.md) for the public classes and functions which are stable.

The basic idea of Pygen is as follows:

1. Input Views in Read Format (Pygen does know about containers).
2. Converts these views into Pygen internal representation, located in the `pygen._core.models` module.
3. Use the internal representation as input to the Jinja templates, located in the `pygen.templates` module.
4. The logic for combining the internal representation with the Jinja templates is located in the `pygen.generator` module.


<!-- SOURCE_END: pygen/docs\developer_docs\index.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\example_docs\core_apis.md -->
## File: pygen/docs\example_docs\core_apis.md

::: examples.cognite_core._api


<!-- SOURCE_END: pygen/docs\example_docs\core_apis.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\example_docs\core_client.md -->
## File: pygen/docs\example_docs\core_client.md

::: examples.cognite_core._api_client.CogniteCoreClient


<!-- SOURCE_END: pygen/docs\example_docs\core_client.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\example_docs\core_data_classes.md -->
## File: pygen/docs\example_docs\core_data_classes.md

::: examples.cognite_core.data_classes


<!-- SOURCE_END: pygen/docs\example_docs\core_data_classes.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\example_docs\index.md -->
## File: pygen/docs\example_docs\index.md

# Example Documentation

This Section contains example documentation for SDKs generated with `pygen`.

* `Omni`: This is a data model designed to cover all functionality of the CDF Data Models.
* `Windmill`: This is the example SDK used in the usage section.
* `CogniteCore`: This is the SDK generated for the Cognite Core Model.

The intention of this documentation is to show what methods, fields, and classes that are
available from a generated SDK.


<!-- SOURCE_END: pygen/docs\example_docs\index.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\example_docs\omni_apis.md -->
## File: pygen/docs\example_docs\omni_apis.md

::: examples.omni._api


<!-- SOURCE_END: pygen/docs\example_docs\omni_apis.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\example_docs\omni_client.md -->
## File: pygen/docs\example_docs\omni_client.md

::: examples.omni._api_client.OmniClient


<!-- SOURCE_END: pygen/docs\example_docs\omni_client.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\example_docs\omni_data_classes.md -->
## File: pygen/docs\example_docs\omni_data_classes.md

::: examples.omni.data_classes


<!-- SOURCE_END: pygen/docs\example_docs\omni_data_classes.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\example_docs\wind_apis.md -->
## File: pygen/docs\example_docs\wind_apis.md

::: examples.wind_turbine._api


<!-- SOURCE_END: pygen/docs\example_docs\wind_apis.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\example_docs\wind_client.md -->
## File: pygen/docs\example_docs\wind_client.md

::: examples.wind_turbine._api_client.WindTurbineClient


<!-- SOURCE_END: pygen/docs\example_docs\wind_client.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\example_docs\wind_data_classes.md -->
## File: pygen/docs\example_docs\wind_data_classes.md

::: examples.wind_turbine.data_classes


<!-- SOURCE_END: pygen/docs\example_docs\wind_data_classes.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\index.md -->
## File: pygen/docs\index.md

# Welcome to Pygen Docs

[![release](https://img.shields.io/github/actions/workflow/status/cognitedata/pygen/release.yaml?style=for-the-badge)](https://github.com/cognitedata/pygen/actions/workflows/release.yaml)
[![Documentation Status](https://readthedocs.com/projects/cognite-pygen/badge/?version=latest&style=for-the-badge)](https://cognite-pygen.readthedocs-hosted.com/en/latest/?badge=latest)
[![Github](https://shields.io/badge/github-cognite/pygen-green?logo=github&style=for-the-badge)](https://github.com/cognitedata/pygen)
[![PyPI](https://img.shields.io/pypi/v/cognite-pygen?style=for-the-badge)](https://pypi.org/project/cognite-pygen/)
[![Downloads](https://img.shields.io/pypi/dm/cognite-pygen?style=for-the-badge)](https://pypistats.org/packages/cognite-pygen)
[![GitHub](https://img.shields.io/github/license/cognitedata/pygen?style=for-the-badge)](https://github.com/cognitedata/pygen/blob/master/LICENSE)
[![Code style: black](https://img.shields.io/badge/code%20style-black-000000.svg?style=for-the-badge)](https://github.com/ambv/black)
[![Ruff](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/astral-sh/ruff/main/assets/badge/v2.json&style=for-the-badge)](https://github.com/astral-sh/ruff)
[![mypy](https://img.shields.io/badge/mypy-checked-000000.svg?style=for-the-badge&color=blue)](http://mypy-lang.org)

This is the Cognite Python SDK Generator, `pygen`. The purpose of this package is to help developers to
work with Cognite Data Fusion's (CDF) Data Models (DM) in Python.

The core functionality is to provide a Python client that matches a data model. This enables the developer for the following
benefits

* Client-side validation of the data before writing it to CDF.
* Autocompletion is matching the data model in the integrated developer environment (IDE). This is important as it enables:
  * Discoverability of a data model through Python.
  * Reduced typing errors in development.
* Keeping the language domain specific for the developer. Instead of working with generic concepts such as instances,
  nodes and edges, the developer can work with the concepts in the data model.

## Documentation

See the [documentation](https://cognite-pygen.readthedocs-hosted.com/en/latest/) for more information.

## Installation

### Without any optional dependencies

To install this package without CLI support:
```bash
pip install cognite-pygen
```

### With optional dependencies

* `cli` This includes CLI support such that you can run the package from the command line.

```bash
pip install cognite-pygen[cli]
```
If using zsh:
```bash
pip install 'cognite-pygen[cli]'
```

## Usage

The goal of the package is to have representations of all the types in a given data model with API calls to *.list()*,
*.apply()*, *.delete()*, and *.retrieve()* individuals for each type.

![image](https://github.com/cognitedata/pygen/assets/60234212/b9942595-424c-4c5e-8a9c-37a43e0a5a7c)

![image](https://github.com/cognitedata/pygen/assets/60234212/70a5f6b0-cec0-4178-93e1-4f9902658638)


## Creating a Python SDK from a Data Model

Given a Data Model with external id `Movie` in the space `movies` in CDF, the following command will generate a Python SDK
```bash
pygen generate --space movies \
    --external-id Movie \
    --version 1 \
    --tenant-id <tenant-id> \
    --client-id <client-id> \
    --client-secret <client-secret> \
    --cdf-cluster <cdf-cluster> \
    --cdf-project <cdf-project>
```

If you are not using Microsoft Entra ID (Azure AD) you need to specify the parameter --token-url, --scopes and --audience instead of --tenant-id.

## Dependencies

### Dependencies for the generated SDK

* [cognite-sdk](https://cognite-sdk-python.readthedocs-hosted.com/en/latest/) This is the basis for all requests to the Cognite Data Fusion API.
* [pydantic](https://docs.pydantic.dev/latest/) This is used for all data classes in the generated SDK.
* [pandas](https://pandas.pydata.org/docs/) This is used for `.to_pandas()` methods in the generated SDK.

### Dependencies for the `pygen`

* [jinja2](https://jinja.palletsprojects.com/en/3.1.x/) This is used for the templating of the generated SDK.
* [inflect](https://pypi.org/project/inflect/) This is used for the singularization/pluralization of words in the generated SDK.
* [typer](https://typer.tiangolo.com/) (Optional) This is used for the CLI of the `pygen` package.
* [black](https://pypi.org/project/black/) (Optional) This is used to format the code generated by the `pygen` package.


## Changelog
Wondering about previous changes to the SDK? Take a look at the [CHANGELOG](https://github.com/cognitedata/pygen/blob/master/docs/CHANGELOG.md).

## Contributing
Want to contribute? Check out [CONTRIBUTING](https://github.com/cognitedata/pygen/blob/master/CONTRIBUTING.md).


<!-- SOURCE_END: pygen/docs\index.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\installation.md -->
## File: pygen/docs\installation.md

## Installation Local
**Prerequisites**: Installed Python 3.9 or newer, see [python.org](https://www.python.org/downloads/)

1. Create a virtual environment.
2. Activate your virtual environment.
3. Install `cognite-pygen` with the all option.

=== "Windows"

    ```
    python -m venv venv
    venv\Scripts\activate.bat
    pip install cognite-pygen[all]
    ```

=== "Mac/Linux"

    ```bash
    python -m venv venv
    source venv/bin/activate
    pip install "cognite-pygen[all]"
    ```

## Installation Options

=== "No extras"

    ```
    pip install cognite-pygen
    ```

    This only installs the core dependencies for `cognite-pygen` and is useful if you only want to generate the SDK
    from a Python script, for example, in a notebook.

=== "cli"

    ```
    pip install cognite-pygen[cli]
    ```

    This installs the core dependencies for `cognite-pygen` and the dependencies for the CLI. This is useful if you
    want to generate the SDK from the command line.

=== "format"

    ```
    pip install cognite-pygen[format]
    ```

    This installs the core dependencies for `cognite-pygen` and the dependencies for formatting the generated SDK.
    This is useful if you want to format the generated SDK code with black.

=== "all"

    ```
    pip install cognite-pygen[all]
    ```

    This installs the core dependencies for `cognite-pygen`, as well as the CLI and code formatting dependencies.


## Pyodide Installation

In a `CDF Notebook` you can install `cognite-pygen` by running the following code in a cell:

```python
%pip install cognite-pygen
```

## Pyodide Troubleshooting

### Can't find a pure Python 3 wheel: 'cognite-pygen'

**Context Seen**: CDF Streamlit environment

??? Error

    Traceback (most recent call last):
    File "/lib/python3.11/site-packages/micropip/_commands/install.py", line 146, in install
    raise ValueError(
          ValueError: Can't find a pure Python 3 wheel for: 'cognite-pygen==0.99.17'
          See: https://pyodide.org/en/stable/usage/faq.html#why-can-t-micropip-find-a-pure-python-wheel-for-a-package
    )

This error can occur if one of the dependencies of `cognite-pygen` is violated. For example, if you have
```requirements
cognite-sdk==7.5.0
cognite-pygen==0.99.17
```
You will get the error above because `cognite-pygen` requires the `cognite-sdk` version to be `>=7.13.6`. You
can fix this by updating the `cognite-sdk` version to `>=7.13.6` in the `requirements.txt` file.

```requirements
cognite-sdk==7.13.6
cognite-pygen==0.99.17
```

### Requested 'typing-extensions>=4.10.0; python_version < "3.13"', but typing-extensions==4.7.1 is already installed

**Context Seen**: CDF Notebook Environment


??? Error

    ValueError                                Traceback (most recent call last)
    Cell In[2], line 1
    ----> 1 await __import__("piplite").install(**{'requirements': ['cognite-pygen==0.99.16']})
          2 from cognite import pygen
          3 print(pygen.__version__)

    File /lib/python3.11/site-packages/piplite/piplite.py:117, in _install(requirements, keep_going, deps, credentials, pre, index_urls, verbose)
        115 """Invoke micropip.install with a patch to get data from local indexes"""
        116 with patch("micropip.package_index.query_package", _query_package):
    --> 117     return await micropip.install(
        118         requirements=requirements,
        119         keep_going=keep_going,
        120         deps=deps,
        121         credentials=credentials,
        122         pre=pre,
        123         index_urls=index_urls,
        124         verbose=verbose,
        125     )

    File /lib/python3.11/site-packages/micropip/_commands/install.py:142, in install(requirements, keep_going, deps, credentials, pre, index_urls, verbose)
        130     index_urls = package_index.INDEX_URLS[:]
        132 transaction = Transaction(
        133     ctx=ctx,
        134     ctx_extras=[],
       (...)
        140     index_urls=index_urls,
        141 )
    --> 142 await transaction.gather_requirements(requirements)
        144 if transaction.failed:
        145     failed_requirements = ", ".join([f"'{req}'" for req in transaction.failed])

    File /lib/python3.11/site-packages/micropip/transaction.py:204, in Transaction.gather_requirements(self, requirements)
        201 for requirement in requirements:
        202     requirement_promises.append(self.add_requirement(requirement))
    --> 204 await asyncio.gather(*requirement_promises)

    File /lib/python3.11/site-packages/micropip/transaction.py:211, in Transaction.add_requirement(self, req)
        208     return await self.add_requirement_inner(req)
        210 if not urlparse(req).path.endswith(".whl"):
    --> 211     return await self.add_requirement_inner(Requirement(req))
        213 # custom download location
        214 wheel = WheelInfo.from_url(req)

    File /lib/python3.11/site-packages/micropip/transaction.py:300, in Transaction.add_requirement_inner(self, req)
        297     if self._add_requirement_from_pyodide_lock(req):
        298         return
    --> 300     await self._add_requirement_from_package_index(req)
        301 else:
        302     try:

    File /lib/python3.11/site-packages/micropip/transaction.py:347, in Transaction._add_requirement_from_package_index(self, req)
        344 if satisfied:
        345     logger.info(f"Requirement already satisfied: {req} ({ver})")
    --> 347 await self.add_wheel(wheel, req.extras, specifier=str(req.specifier))

    File /lib/python3.11/site-packages/micropip/transaction.py:385, in Transaction.add_wheel(self, wheel, extras, specifier)
        383 await wheel.download(self.fetch_kwargs)
        384 if self.deps:
    --> 385     await self.gather_requirements(wheel.requires(extras))
        387 self.wheels.append(wheel)

    File /lib/python3.11/site-packages/micropip/transaction.py:204, in Transaction.gather_requirements(self, requirements)
        201 for requirement in requirements:
        202     requirement_promises.append(self.add_requirement(requirement))
    --> 204 await asyncio.gather(*requirement_promises)

    File /lib/python3.11/site-packages/micropip/transaction.py:208, in Transaction.add_requirement(self, req)
        206 async def add_requirement(self, req: str | Requirement) -> None:
        207     if isinstance(req, Requirement):
    --> 208         return await self.add_requirement_inner(req)
        210     if not urlparse(req).path.endswith(".whl"):
        211         return await self.add_requirement_inner(Requirement(req))

    File /lib/python3.11/site-packages/micropip/transaction.py:300, in Transaction.add_requirement_inner(self, req)
        297     if self._add_requirement_from_pyodide_lock(req):
        298         return
    --> 300     await self._add_requirement_from_package_index(req)
        301 else:
        302     try:

    File /lib/python3.11/site-packages/micropip/transaction.py:347, in Transaction._add_requirement_from_package_index(self, req)
        344 if satisfied:
        345     logger.info(f"Requirement already satisfied: {req} ({ver})")
    --> 347 await self.add_wheel(wheel, req.extras, specifier=str(req.specifier))

    File /lib/python3.11/site-packages/micropip/transaction.py:385, in Transaction.add_wheel(self, wheel, extras, specifier)
        383 await wheel.download(self.fetch_kwargs)
        384 if self.deps:
    --> 385     await self.gather_requirements(wheel.requires(extras))
        387 self.wheels.append(wheel)

    File /lib/python3.11/site-packages/micropip/transaction.py:204, in Transaction.gather_requirements(self, requirements)
        201 for requirement in requirements:
        202     requirement_promises.append(self.add_requirement(requirement))
    --> 204 await asyncio.gather(*requirement_promises)

    File /lib/python3.11/site-packages/micropip/transaction.py:208, in Transaction.add_requirement(self, req)
        206 async def add_requirement(self, req: str | Requirement) -> None:
        207     if isinstance(req, Requirement):
    --> 208         return await self.add_requirement_inner(req)
        210     if not urlparse(req).path.endswith(".whl"):
        211         return await self.add_requirement_inner(Requirement(req))

    File /lib/python3.11/site-packages/micropip/transaction.py:300, in Transaction.add_requirement_inner(self, req)
        297     if self._add_requirement_from_pyodide_lock(req):
        298         return
    --> 300     await self._add_requirement_from_package_index(req)
        301 else:
        302     try:

    File /lib/python3.11/site-packages/micropip/transaction.py:347, in Transaction._add_requirement_from_package_index(self, req)
        344 if satisfied:
        345     logger.info(f"Requirement already satisfied: {req} ({ver})")
    --> 347 await self.add_wheel(wheel, req.extras, specifier=str(req.specifier))

    File /lib/python3.11/site-packages/micropip/transaction.py:385, in Transaction.add_wheel(self, wheel, extras, specifier)
        383 await wheel.download(self.fetch_kwargs)
        384 if self.deps:
    --> 385     await self.gather_requirements(wheel.requires(extras))
        387 self.wheels.append(wheel)

    File /lib/python3.11/site-packages/micropip/transaction.py:204, in Transaction.gather_requirements(self, requirements)
        201 for requirement in requirements:
        202     requirement_promises.append(self.add_requirement(requirement))
    --> 204 await asyncio.gather(*requirement_promises)

    File /lib/python3.11/site-packages/micropip/transaction.py:208, in Transaction.add_requirement(self, req)
        206 async def add_requirement(self, req: str | Requirement) -> None:
        207     if isinstance(req, Requirement):
    --> 208         return await self.add_requirement_inner(req)
        210     if not urlparse(req).path.endswith(".whl"):
        211         return await self.add_requirement_inner(Requirement(req))

    File /lib/python3.11/site-packages/micropip/transaction.py:290, in Transaction.add_requirement_inner(self, req)
        287 # Is some version of this package is already installed?
        288 req.name = canonicalize_name(req.name)
    --> 290 satisfied, ver = self.check_version_satisfied(req)
        291 if satisfied:
        292     logger.info(f"Requirement already satisfied: {req} ({ver})")

    File /lib/python3.11/site-packages/micropip/transaction.py:235, in Transaction.check_version_satisfied(self, req)
        231 if req.specifier.contains(ver, prereleases=True):
        232     # installed version matches, nothing to do
        233     return True, ver
    --> 235 raise ValueError(
        236     f"Requested '{req}', " f"but {req.name}=={ver} is already installed"
        237 )

    ValueError: Requested 'typing-extensions>=4.10.0; python_version < "3.13"', but typing-extensions==4.7.1 is already installed

This is a known bug in `pyodide`, GitHub issue [pyodide#4234](https://github.com/pyodide/pyodide/issues/4234).

The workaround is to manually uninstall the `typing-extensions` package and then install the `cognite-pygen` package.

In a cell, run the following code:

```python
import micropip
micropip.uninstall('typing_extensions')
```

Then, install `cognite-pygen`:

```python
%pip install cognite-pygen
```

In a `CDF Streamlit` environment, you can install the `typing-extensions` package before installing `cognite-pygen`:

```text
pyodide-http==0.2.1
pydantic==1.10.7
typing-extensions>=4.10.0
cognite-sdk==7.54.4
cognite-pygen==0.99.27
```


<!-- SOURCE_END: pygen/docs\installation.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\quickstart\cdf_streamlit.md -->
## File: pygen/docs\quickstart\cdf_streamlit.md

# Building a Streamlit App in CDF

**Prerequisites**
- Access to a CDF Project.
- Some familiarity with Streamlit.

## Settings in Streamlit App

Streamlit is a beta feature in CDF. You can use `pygen` in it by
adding `cognite-pygen` to the installed packages under `settings`.

```text
pyodide-http==0.2.1
cognite-sdk==7.73.3
pydantic==2.7.0
cognite-pygen==0.99.60
```

Note that we also set `pydantic` to a specific version. This is because `pygen` supports both `pydantic` `v1` and `v2`, but
when we want to use `pygen` in the CDF streamlit environment, we need to use `pydantic` `v1`.

In case you have issues with the installation, check out the [troubleshooting](../installation.html#pyodide-troubleshooting) in the
installation guide.

## Minimal Example

The following is a minimal example of a Streamlit app that uses `pygen` to generate a Python SDK client with the
`ScenarioInstanceModel` from the [Working with TimeSeries](../examples/timeseries.html) example.

```python
import streamlit as st
from cognite.client import CogniteClient
from cognite.pygen import generate_sdk_notebook
st.title("An example app in CDF with Pygen")

client = CogniteClient()

@st.cache_data
def get_client():
  return generate_sdk_notebook(
    ("IntegrationTestsImmutable", "ScenarioInstance", "1"), client
  )

@st.cache_data
def get_scenario_instances():
  client = get_client()
  return client.scenario_instance.list().to_pandas()


st.dataframe(get_scenario_instances())
```

### Explanation

The first thing we do is to import the `CogniteClient` and `generate_sdk_notebook` from `cognite.pygen`.

```python
from cognite.client import CogniteClient
from cognite.pygen import generate_sdk_notebook
```

Then, we create a Streamlit app and a `CogniteClient` instance.

```python
st.title("An example app in CDF with Pygen")

client = CogniteClient()
```

Next, we define a function that generates a SDK for the `ScenarioInstance` model. We use the `@st.cache_data` decorator
to cache the result of the function. This means that the function will only be called once, and the result will be
cached for subsequent calls.

```python
@st.cache_data
def get_client():
  return generate_sdk_notebook(
    ("IntegrationTestsImmutable", "ScenarioInstance", "1"), client
  )
```

We then define a function that uses the generated SDK to list all `ScenarioInstance` objects in the project. We also
cache the result of this function.

```python
@st.cache_data
def get_scenario_instances():
  client = get_client()
  return client.scenario_instance.list().to_pandas()
```

Finally, we call the `get_scenario_instances` function and display the result in a dataframe.

```python
st.dataframe(get_scenario_instances())
```


<!-- SOURCE_END: pygen/docs\quickstart\cdf_streamlit.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\quickstart\project.md -->
## File: pygen/docs\quickstart\project.md

**Prerequisites**

- Access to a CDF Project.
- Know how to install and setup `Python` using `uv` and `pyproject.toml`.
- Know how to use `git` for version control.
- Know how to use a terminal, so you can run `pygen` from the command line to
  generate the SDK.


When you are ready to develop a solution based on a data model, you can use `pygen` to genrate a SDK that you check
into version history. This SDK can be used by the developer team to interact with CDF.

This pages shows the recommended project structure for a CDF SDK project.

## Project Structure

The following is the recommended project structure for a CDF SDK project:
```
📦my_python_application
 ┣ 📂client - CDF SDK generated by pygen
 ┣ ┣ 📂_api
 ┣ ┣ 📂data_classes
 ┃ ┗ 📜__init__.py
 ┃ ┗ 📜_api_client.py
 ...
 ┣ 📜.gitignore - Git ignore file
 ┣ 📜.secret.toml -Secret configuration.
 ┣ 📜pyproject.toml - Project configuration file
 ...
```

=== ".gitignore"

    ```
    # The config contains secrets, thus we do not want
    # to commit it to version history
    .secret.toml
    ```


=== "pyproject.toml"

    We recommend keeping the configuration for `pygen` in the `pyproject.toml` file. This way, the configuration is
    versioned together with the generated SDK. Making it easy for any team member to regenerate the SDK.

    ```toml
    ...

    [tool.pygen]
    data_models = [
        ["IntegrationTestsImmutable", "Movie", "2"],
    ]
    tenant_id = "<cdf-project>"
    client_id = "<client-id>"
    cdf_cluster = "<cdf-cluster>"
    cdf_project = "<cdf-project>"
    top_level_package = "movie_domain.client"
    client_name = "MovieClient"
    output_dir = "."

    ...

    [dependency-groups]
    dev = [
        "cognite-pygen[all]",
        ```

=== ".secret.toml"

    **IMPORTANT:** The `.secret.toml` file contains secrets and must **NOT** be committed to version history.
    ``` bash
    [cognite]
      client_secret = "<client-secret>"
    ```

## Generating the SDK and Checking it into Version History

The instructions below assume you use `git` for version control.

**Prerequisites:** Instantiated a git repository and added the `pyproject.toml`, `.gitignore` and `.secret.toml`
in the root of the repository. (Create the files and run `git init && git add . &&  git commit -m "initial commit"`)

### Generating the SDK
1. Check out a new branch for the SDK generation. (e.g. `git checkout -b sdk-generation`)
2. Ensure that `pygen` is installed and configured. (See [Installation](#installation))
3. Run `pygen generate` to generate the SDK. (e.g. `pygen`). `pygen` will pick ut the configuration from the
   `pyproject.toml` and `.secret.toml` files.
4. Commit the generated SDK to version history. (e.g. `git add . && git commit -m "generated sdk"`)
5. Push the branch to the remote repository. (e.g. `git push origin sdk-generation`)
6. Create a pull request for the branch.
7. Merge the pull request.


<!-- SOURCE_END: pygen/docs\quickstart\project.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\quickstart\where_to_start.md -->
## File: pygen/docs\quickstart\where_to_start.md

`pygen` has multiple use cases spanning different user skill levels. This section will help you find the right place to start.

The minimum requirements for using `pygen` is to have a Cognite Data Fusion (CDF) project available. Note that `pygen` is
made for people with a very basic understanding of Python, but instead have domain knowledge of the data they are
exploring and analyzing.

For the more advanced users, `pygen` is a great tool for building solutions quickly by tailoring the generated code to
your data model. Thus making it easy to get data in a format that is close to work with for your specific use case. It
is an alternative to `graphql` that is more pythonic.

## (Beginner) Exploration
If you are just curious about `pygen` and all you have is a CDF project, and you don't even have a data model or data
a great place to start is in the CDF notebook with a demo model packaged into `pygen`,
see [Generating SDK using Demo Data Model](cdf_notebook.html#generating-sdk-using-demo-data-model). The advantage of using
a CDF notebook is that you do not have to know how to install and setup `Python`.

Do you have your own data model you want to explore using the CDF notebook, then see [Explore in Notebook](cdf_notebook.html)

If you know how to install and setup `Python` and you have a CDF project, and prefer to work in your own environment,
then you can start with the [Local Notebook](notebook.html) guide.

Exploration is also useful in the data modeling process to quickly explore a data model and, for example, try
how it can be queried, and whether it will support a specific use case.

## (Intermediate) Building a Solution
If you have a CDF project and you want to build a solution, for example, a dashboard using `plotly` or `dash`, or
a machine learning model workflow using `scikit-learn` or `tensorflow`, then you should generate a `pygen` SDK and
check it into git history. See the [Project](project.html) for more information.

## (Intermediate) Ingestion Data into CDF
`pygen` can also be used to create an extractor that is used to ingest data into CDF. The typical use case
is when you have a data source that is creating nested data and you want to perform client side validation of the
data before ingesting it into CDF. See the [Data Population](ingestion.html) for more information.

## (Intermediate) Migration Data between Data Models
`pygen` can also be used to move data from one model to another. In short, you create an SDK using `pygen` for each
data model, and then write the code to move data between the models. See the [Data migration](migration.html) for more information.


<!-- SOURCE_END: pygen/docs\quickstart\where_to_start.md -->

================================================================================


<!-- SOURCE_START: pygen/docs\what_is_pygen.md -->
## File: pygen/docs\what_is_pygen.md

## What is `Pygen`?

You have created your data model and have it ready in Cognite Data Fusion (CDF). Now you want to start writing Python
code to interact with it. Then, you have multiple options

1. You can use `GraphQL`, and call the endpoint `https://{cluster}.cognitedata.com/api/v1/projects/{project}/userapis/spaces/{space}/datamodels/{externalId}/versions/{version}/graphql`.
   This gives you a flexible way to query your data model, but
    - writing the queries can be cumbersome.
    - the response is a dictionary that you need to parse.
    - you need to know the data model structure.
2. You can use the Data Modeling Storage, `DMS` endpoint `https://{cluster}.cognitedata.com/api/v1/projects/{project}/models/instances/`.
   This lacks the context of your data model
    - you are using a set of generic endpoints designed to work with any data model.
    - the response is a dictionary that you need to parse.
    - you need to know the data model structure.

`Pygen` is offering a third option. It generates Python code that wraps the `DMS` endpoint with your
data model. This way, you can interact with your data model using Python objects, which gives you the following benefits:

1. You can interact with your data model using Python objects.
2. Your IDE can provide you with code completion and type hints for your data model.
3. Client-side validation of data when creating or updating objects.
4. Enable you to work in the language of your data model.

## What does `Pygen` actually do?

The input to `Pygen` are the views of the data model (views are represented as types in GraphQL), the output for each
view is data classes and an API class tailored to the view. This is illustrated in the following diagram
(Note the code shown below is a simplified version of the actual code generated by `Pygen`):

<img src="figs/pygen_illustration.png" height="500">

In addition, `Pygen` generates a few shared methods that can be used by all the data classes generated
for the views. These are illustrated in the following diagram:

<img src="figs/pygen_client_illustration.png" height="500">


## Data Class(es)

`Pygen` generates three data classes for each view. For example, the view `WindTurbine` will
generate the following data classes:

1. `WindTurbine` - This is used when retrieving wind turbines from CDF. It has properties that
    correspond to the fields in the view that matches whether the field is required or not. In
    addition, it has `data_record` that contains server-set properties
    such as `createdTime`, `lastUpdatedTime`, and `version`.
2. `WindTurbineWrite` - This is used when writing wind turbines to CDF. It has properties that
    correspond to the fields in the view that matches whether the field is required or not. It
    also has a `data_record` field with one property `existing_version` that is used to decide
    how to handle conflicts when updating the wind turbine.
3. `WindTurbineGraphQL` - This is used when parsing a GraphQL response. It has properties that
    correspond to the fields in the view, but all fields are optional. This is because
    `GraphQL` responses can be partial.

In addition, to these three data classes for each view, `Pygen` generates a `WindTurbineList` and
`WindTurbineWriteList` that are UserList of the corresponding data classes. These behave like a
regular Python list with a few extra helper methods such as:

 * `.to_pandas()` - Converts the list of WindTurbine to a pandas DataFrame with each column corresponding
    to a property in the WindTurbine data class.
 * `.dump()` - Converts the list of WindTurbine to a list of dictionaries.
 * `.as_external_ids()` - Returns the external ids of the WindTurbine objects in the list.
 * `.as_node_ids()` - Returns the node ids of the WindTurbine objects in the list. A node ID is consist
   of the space + external ID of a WindTurbine object and is used to uniquely identify a WindTurbine object.
 * It also have built in methods for nice display of the list in a Jupyter notebook.

You can convert between the different data classes using the following methods:

 * `wind_turbine_read.as_write()` - Converts a `WindTurbine` object to a `WindTurbineWrite` object.
 * `wind_turbine_graphql.as_write()` - Converts a `WindTurbineGraphQL` object to a `WindTurbineWrite` object.
 * `wind_turbine_graphql.as_read()` - Converts a `WindTurbineGraphQL` object to a `WindTurbine` object.

**Note** When converting from a `WindTurbineGraphQL` to `WindTurbine` or `WindTurbineWrite` all required fields
must be present in the `WindTurbineGraphQL` object, otherwise an exception will be raised.


## API Class

`Pygen` generates an API class for each view. The API class contains methods for retrieving objects from CDF.
For example, the `WindTurbine` API class will have the following methods:

 * `.retrieve(...)` - Retrieves one or more wind turbines from CDF. The method takes a list of external ids
    and returns a `WindTurbineList` object.
 * `.list(...)` - This method returns all wind turbines from CDF matching the filter criteria passed in
   through the method arguments.
 * `.search(...)` - This method searches for wind turbines in CDF matching the search criteria passed in
   through the method arguments.
 * `.aggregate(...)` - This method aggregates wind turbines in CDF matching the aggregation criteria passed in
   through the method arguments.
 * `.histogram(...)` - This is a special aggregation method that returns a histogram of the wind turbines in CDF
   matching the aggregation criteria passed in through the method arguments.
 * `(...)` - Doing a call directly on the API class will enable you to write a Python query for retrieving wind turbines
   with nested objects from CDF.

For each of these methods, `pygen` generates filter parameters that correspond to the field types in the view. For example,
a field of type `string` will have `Equals`, `In`, and `Prefix` filter parameters in `list()`, `search()`,
`aggregate()`, `.histogram()` and `(...)` methods. For `WindTurbine` with a field `name` of type `string`, the first
parameters of the `list()` methods will be:

```python
def list(self, name: str | list[str] | None = None, name_prefix: str | None = None, ...):
    ...
```

The generated API class will also have properties linking to the other API classes for each edge and timeseries
field in the view. For example, the `WindTurbine` has edges to `Blade`and `Metmast`, so the `WindTurbine` API
class will have the following properties:

* `.blade` - This is a property that returns the `Blade` API class.
* `.metmast` - This is a property that returns the `Metmast` API class.

If the `WindTurbine` had a field `activePower` of type timeseries, then the `WindTurbine` API class will have the following
property:

* `.active_power` - This is a property that returns an API class for the `activePower` timeseries. This
   class will have methods for retrieving the timeseries data from CDF, as well as methods for retrieving the
   datapoints for the timeseries.


## API Client

`Pygen` generates a shared API client that contains all the API classes for each view. In addition, it has
three methods which are shared.

* `.upsert(...)` This method can take any of the write data classes generated for the views as input,
  or a list of write data classes. This has multiple benefits over using the `DMS` endpoint directly:

     - It supports nested objects. These will be unpacked into nodes and edges before being sent to CDF.
     - It automatically creates edges based on the relationships between the objects.
     - It will automatically create `TimeSeries` objects for timeseries fields.

* `.delete(...)` This method can is a thin wrapper around the `DMS` endpoint for deleting objects.

* `.graphql_query(...)` This method takes a `GraphQL` query as input and returns the response from CDF.
  In difference from using the regular `GraphQL` endpoint, this method will automatically parse the response
  to the corresponding data classes for the views in the query.


<!-- SOURCE_END: pygen/docs\what_is_pygen.md -->

================================================================================
