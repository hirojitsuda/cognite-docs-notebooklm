# toolkit Documentation
Generated from: toolkit



<!-- SOURCE_START: toolkit/.gemini\styleguide.md -->
## File: toolkit/.gemini\styleguide.md

# Cognite Python Style Guide

## Key Principles

- **Strong Typing**: Use type hints extensively with pyright. Avoid `Any` when possible
- **Type Safety**: Use dataclasses and Pydantic models for complex data
  structures instead of untyped dictionaries
- **IO Safety**: Always use typed data structures for file operations and data parsing
- **Readability**: Code should be immediately understandable
- **Maintainability**: Write code that is easy to modify and extend
- **Consistency**: Follow established patterns across the codebase

## Principles on doing pull request reviews

- **Main point first.** Start with the key feedback or required action.
- **Be concise.** Use short, direct comments. Avoid unnecessary explanations.
- **Actionable suggestions.** If something needs fixing, state exactly what and how.
- **One issue per comment.** Separate unrelated feedback for clarity.
- **Code, not prose.** Prefer code snippets or examples over long text.
- **Background only if needed.** Add context only if the main point isn't obvious.

## How to do pull request summaries

- **Short recap.** Summarize the main point of the PR in one or two sentences.
- **Don't repeat the PR description.** Only add new or clarifying information.
- **Be brief unless needed.** Only write a longer summary if the PR description
  is missing crucial details.
- **Extend, don't duplicate.** If more detail is needed, clearly state what is
  missing from the PR description and add only the necessary context.

## Line Length and Formatting

- **Maximum line length**: 120 characters (configured in ruff)
- **Target Python version**: 3.10+
- **Indentation**: 4 spaces per level

## Type Hints

- **Required**: All functions, methods, and class attributes must have type hints
- **Avoid `Any`**: Use specific types whenever possible
- **Complex data**: Use dataclasses or Pydantic models instead of `dict[str, Any]`
- **File operations**: Always parse file content into typed structures

```python
# Good - typed data structure
@dataclass
class Config:
    model_name: str
    temperature: float
    max_tokens: int

def load_config(path: Path) -> Config:
    data = json.loads(path.read_text())
    return Config(**data)

# Bad - untyped dictionary
def load_config(path: Path) -> dict[str, Any]:
    return json.loads(path.read_text())
```

## Imports

- **Group imports**: Standard library, third-party, local application
- **Absolute imports**: Always use absolute imports for clarity
- **Sort alphabetically** within groups
- **Type checking imports**: Use `TYPE_CHECKING` for type-only imports
- **`_cdf_tk` imports**: Always use the full path `cognite_toolkit._cdf_tk`
when importing from `_cdf_tk`, never use `from _cdf_tk import ...`

```python
import json
import logging
from pathlib import Path
from typing import TYPE_CHECKING

from cognite.client import CogniteClient
from pydantic import BaseModel

from cog_ai.common.types import ModelConfig

# Good - full path for _cdf_tk imports
from cognite_toolkit._cdf_tk.feature_flags import Flags
from cognite_toolkit._cdf_tk.commands.repo import RepoCommand

# Bad - don't use relative _cdf_tk imports
# from _cdf_tk.feature_flags import Flags  # ❌

if TYPE_CHECKING:
    from cog_ai.tools.query.types import QueryCompletion
```

## Naming Conventions

- **Variables/functions**: `snake_case`
- **Constants**: `UPPER_SNAKE_CASE`
- **Classes**: `PascalCase`
- **Modules**: `snake_case`
- **Private members**: Single leading underscore `_private`

## Docstrings

Use concise docstrings with Args/Returns format. Based on repository patterns:

```python
def render_header(header: str) -> str:
    """
    Renders a (markdown) heading.

    Args:
        header (str): header

    Returns:
        str: The rendered header
    """
    return f"{header}\n{'=' * len(header)}\n"

def walk_sdk_documentation(content: Tag, parser: Parser[T]) -> Iterable[T]:
    """Parse the content of a file and yields documents. The parser controls how
    the sections are transformed into documents."""
    # Implementation here
```

**Docstring patterns**:

- Start with a concise description
- Use `Args:` and `Returns:` for complex functions
- Omit obvious parameter descriptions
- Keep descriptions brief and factual
- Ok for `__init__` methods to omit docstring

## Error Handling

- **Specific exceptions**: Avoid broad `Exception` catches
- **Graceful handling**: Provide meaningful error messages
- **Type safety**: Return `None` or use Union types for fallible operations

```python
def load_llm_response(response: str) -> dict[str, Any] | None:
    try:
        return json.loads(response)
    except (TypeError, json.JSONDecodeError) as e:
        log.warning(f"Failed json load response from LLM: {e}")
        return None
```

## Tooling

- **Formatter**: `uv run ruff format --force-exclude --quiet`
- **Linter**: `uv run ruff check --force-exclude --fix --exit-non-zero-on-fix`
- **Type checker**: `dmypy run -- cognite_toolkit/ --config-file pyproject.toml` (uses mypy)
- **Pre-commit**: `pre-commit run --all-files` for comprehensive checks

## Ruff Configuration Deviations

Current project configuration deviates from PEP 8:

- **Line length**: 120 characters (vs PEP 8's 79)
- **Ignored rules**: E501 (line too long), UP017 (datetime.timezone.utc)
- **Selected rules**: Includes bugbear (B0), pyupgrade (UP), and isort (I)

## Data Structures

**Prefer typed structures**:

```python
# Good
@dataclass
class FunctionError:
    function_name: str
    message: str

# Good
class QueryCompletion(BaseModel):
    query: str
    variables: dict[str, Any]

# Avoid
error_data = {"function_name": "foo", "message": "bar"}
```

## Logging

- Use the `logging` module with appropriate levels
- Include contextual information for debugging
- Format: `log.warning(f"Description: {variable}")`


<!-- SOURCE_END: toolkit/.gemini\styleguide.md -->

================================================================================


<!-- SOURCE_START: toolkit/.github\pull_request_template.md -->
## File: toolkit/.github\pull_request_template.md

# Description

Please describe the change you have made.

## Bump

- [ ] Patch
- [ ] Skip

## Changelog

### Added/Changed/Improved/Removed/Fixed

- My change


<!-- SOURCE_END: toolkit/.github\pull_request_template.md -->

================================================================================


<!-- SOURCE_START: toolkit/cognite_toolkit\_repo_files\AzureDevOps\.devops\README.md -->
## File: toolkit/cognite_toolkit\_repo_files\AzureDevOps\.devops\README.md

# Verify and deploy modules in Azure DevOps Pipelines

Setting up Pipelines requires some additional steps in Azure DevOps.

Please follow the CI/CD guide in the [deployment documentation](https://docs.cognite.com/cdf/deploy/cdf_toolkit/).


<!-- SOURCE_END: toolkit/cognite_toolkit\_repo_files\AzureDevOps\.devops\README.md -->

================================================================================


<!-- SOURCE_START: toolkit/CONTRIBUTING.md -->
## File: toolkit/CONTRIBUTING.md

# Contributing

## How to contribute

We are always looking for ways to improve the Cognite Toolkit CLI. You can
report bugs and ask questions in [our Cognite Hub group](https://hub.cognite.com/groups/cognite-data-fusion-toolkit-277).

We are also looking for contributions to new modules (content) and the Toolkit codebase that make the configuration of
Cognite Data Fusion easier, faster and more reliable.

## Improving the codebase

If you want to contribute to the codebase, you can do so by creating a new branch and
[opening a pull request](https://github.com/cognitedata/toolkit/compare). Prefix the PR title with the Jira issue
number on the form `[CDF-12345]`. A good PR should include a good description of the change to help the reviewer
understand the nature and context of the change.

### Linting and testing

The Cognite Toolkit CLI and modules have an extensive test and linting battery to ensure quality and speed of development.

See [pyproject.toml](pyproject.toml) for the linting and testing configuration.

See [tests](tests/README.md) for more information on how to run and maintain tests.

The `cdf_` prefixed modules are tested as part of the product development.

### Setting up the local environment

Your local environment needs a working Python installation and We use `uv` to manage the environment and its dependencies.

To install dependencies run `uv sync` to install the default dependencies.
Run `uv sync --group dev` to include the `dev` dependency group.
Run `uv sync --all-extras` to install all optional extras defined under `project.optional-dependencies`.
Run `uv sync --group dev --all-extras` to include both the `dev` group and all extras.

By default, `uv` creates and uses a `.venv` in the project directory. If you already have an active virtual environment,
pass `--active` to install into it instead.

When developing in vscode, the `cdf-tk-dev.py` file is useful to run the toolkit. This script will set the
environment and paths correctly (to avoid conflicts with the installed cdf package) and also sets the
`SENTRY_ENABLED` environment variable to `false` to avoid sending errors to Sentry.
In .vscode/launch.json you will see a number of examples of debugging configurations that you can use to debug.

### Essential code

- Main app entry point: [cognite_toolkit/_cdf.py](cognite_toolkit/_cdf.py)
- App subcommands: [cognite_toolkit/_cdf_tk/commands](cognite_toolkit/_cdf_tk/commands)
- Resource loaders: [cognite_toolkit/_cdf_tk/loaders](cognite_toolkit/_cdf_tk/loaders)
- Tests: [tests](tests)
- CI/CD: [.github/workflows](.github/workflows)

### Sentry

When you develop the Cognite Toolkit you should avoid sending errors to  `sentry`. You can control `sentry` by setting
the  `environment` variable `SENTRY_ENABLED=false`. This is set automatically when you use the `cdf-tk-dev.py`.

## Contributing in modules

### Module ownership

The official cdf_* modules are owned by the respective teams in Cognite. Any changes to these
will be reviewed by the teams to ensure that nothing breaks. If you open a PR on these modules,
the PR will be reviewed by the team owning the module.

cdf_infield_location is an example of a team-owned module.

### Adding a new module

Adding a new module consists of the following steps:

1. Determine where to put it (core, common, modules, examples, or experimental).
2. Create a new directory for the module with sub-directories per configuration type the module needs. See the
   [YAML reference documentation](https://developer.cognite.com/sdks/toolkit/references/configs).
3. Add a `default.config.yaml` file to the module root directory if you have variables in the templates.
4. Add a `README.md` file to the module root directory with a description of the module and variables.
5. Update `default.packages.yaml` in cognite_toolkit root with the new module if it is part of a package
6. If this is an official module, add a description of the module in the
   [module and package documentation](https://developer.cognite.com/sdks/toolkit/references/module_reference).

> If you are not a Cognite employee and would like to contribute a module, please open an issue, so we can
> get in touch with you.

Each module should be as standalone as possible, but they can be dependent on other modules.
If you need to deploy a data model as a foundational
element for both transformations and applications to work, you may add a module with the data model.
However, a better module would be one that includes all the elements needed to get data from the
source system, through RAW (if necessary), into a source data model, and then transformed by one or
more transformations into a domain data model. The solution data models can then be a separate module
that relies on the ingestion module.

Please take care to think about the best grouping of modules to make it easy to deploy and maintain.
We are aiming at standardizing as much as possible, so we do not optimize for customer-specific
changes and naming conventions except where we design to support it.

> NOTE! Customer-specific projects should be able to use these templates directly, and also adopt
> new changes from this repository as they are released.
> Configurations that contain defaults that are meant to be changed by the customer, e.g. mapping
> of properties from source systems to CDF, should be contained in separate modules.

## Data formats

All the configurations should be kept in camelCase YAML and in a format that is compatible with the CDF API.
The configuration files are loaded directly into the Python SDK's support data classes for
use towards the CDF API. Client side schema validation should be done in the Python SDK and not in `cdf-tk`
to ensure that you can immediately
add a yaml configuration property without upcoming anything else than the version of the Python SDK.

> NOTE!! As of now, any non-recognised properties will just be ignored by the Python SDK. If you don't
> get the desired configuration deployed, check your spelling.

The scripts currently support many resources like raw, data models, time series, groups, and transformations.
It also has some support for loading of data that may be used as example data for CDF projects. However,
as a general rule, templates should contain governed configurations necessary to set up ingest, data pipelines,
and contextualisations, but not the actual data itself.

Of course, where data population of e.g. data model is part of the configuration, that is fine.
The scripts are continuously under development to simplify management of configurations, and
we are pushing the functionality into the Python SDK when that makes sense.

## Releasing

The templates are bundled with the `cdf` tool, so they are released together.
To release a new version of the `cdf` tool and the templates, you need to do the following:

1. Make sure that the CHANGELOG.cdf-tk.md is up to date. The top entry should be `## TBD` only.
2. Go to <https://github.com/cognitedata/toolkit/actions/workflows/prepare-release.yaml> and run the workflow.

   > Use `patch` to bump z in the x.y.z version number, `minor` to bump y, and `major` to bump x.

   This will create a new preparation branch from `main` with final changes and version bumping,
   e.g. `prepare_for_0_1_0b3`. Use `aX` for alpha, `bX` for beta, and `rcX` for
   release candidate:
   - Updates `CHANGELOG.cdf-tk.md` file with a header e.g. `## [0.1.0b3] - 2024-01-12` and review the
      change comments since the previous release. Ensure that the changes are correctly reflected in the
      comments and that the changes can be easily understood. Also verify that any breaking changes
      are clearly marked as such (`**BREAKING**`).
   - Does the same update to `CHANGELOG.templates.md` file.
   - Updates the files with the new version number, this is done with
      the `cdf bump --patch` (or `--minor`, `--major`, `--alpha`, `--beta`) command.
      - `cognite_toolkit/_version.py`
      - `pyproject.toml`
      - `_system.yaml` (multiple)

   - Runs `poetry lock` to update the `poetry.lock` file.
   - Runs `pytest tests` locally to ensure that tests pass.
   - Runs `python module_upgrade/run_check.py` to ensure that the `cdf-tk modules upgrade` command works as expected
      against previous versions. See [Module Upgrade](module_upgrade/README.md) for more information.

3. Get approval to **squash merge** the branch into `main`:
   1. Verify that all Github actions pass.
4. Create a release branch: `release-x.y.z` from `main`:
   1. Create a new tag on the branch with the version number, e.g. `v0.1.0b3`.
   2. Open a PR with the existing `release` branch as base comparing to your new `release-x.y.z` branch.
   3. Get approval and merge (**do not squash**).
   4. Verify that the Github action `release` passes and pushes to PyPi.
5. Create a new release on github.com with the tag and release notes:
   1. Find the tag you created and create the new release.
   2. Copy the release notes from the `CHANGELOG.cdf-tk.md` file, add a `# cdf-tk` header.
   3. Copy then further below the release notes from the `CHANGELOG.templates.md` file, add
      a `# Templates` header.
   4. Remember to mark as pre-release if this is not a final release.
6. Evaluate necessary announcements:
   1. On the Cognite Hub group, create a new post.
   2. As part of product releases, evaluate what to include.
   3. Cognite internal announcements.


<!-- SOURCE_END: toolkit/CONTRIBUTING.md -->

================================================================================


<!-- SOURCE_START: toolkit/demo\README.md -->
## File: toolkit/demo\README.md

# demo directory

This directory contains configuration files and a preproc.py script used
as part of deploying the default demo to a Cognite-internal demo project.

You can delete this directory if you are not using the demo functionality for your own purposes.


<!-- SOURCE_END: toolkit/demo\README.md -->

================================================================================


<!-- SOURCE_START: toolkit/LIBRARIES.md -->
## File: toolkit/LIBRARIES.md

# Module libraries [ALPHA]

The Toolkit can import modules from external libraries. This is an ALPHA feature.

## Usage

A user can add packages to cdf.toml. At the moment, the Toolkit only loads one library.

```toml
[alpha_flags]
external-libraries = true


[library.package_1]
url = "https://raw.githubusercontent.com/cognitedata/toolkit-data/librarian/builtins.zip"
checksum = "sha256:1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef"

```

When running `cdf modules init` or `cdf modules add`, the packages will be imported and selectable for the user.

## Publishing a library

The library must be available over https. Authentication is not currently supported.

To publish a library, create a repository that contains one or more downloadable zip files.
The zip file must have the structure and content described below.

The checksum is mandatory. It is used to verify the integrity of the downloaded zip file.
It must be a SHA-256 checksum of the zip file.

### <package_1>.zip

Zip file content must be structured like this:

```shell

<package_1>.zip
├── packages.toml
└── module_1
    ├── <module content>
    ├── module.toml
    └── default.config.yaml
└── module_2
    ├── <module content>
    ├── module.toml
    └── default.config.yaml
```

#### packages.toml

The packages.toml in the root folder is required to make the library discoverable by the Toolkit. It allows you to describe
the content of the library and bundle modules into packages. A module can belong to several packages.

The **packages.toml** file should contain the following information:

```toml

[library]
description = "<Description for the end user>"
toolkit-version = ">=0.6.0" # Recommended version of the Toolkit required to use this library
canCherryPick = true # Set to false if the user should not be able to pick individual modules in this package

[packages.quickstart]
id = "tk:quickstart"
title = "Quickstart"
description = "Get started with Cognite Data Fusion in minutes."
canCherryPick = false
modules = [
    "cdf_ingestion",
    "sourcesystem/cdf_pi",
    "sourcesystem/cdf_sap_assets",
    "sourcesystem/cdf_sap_events",
    "sourcesystem/cdf_sharepoint",
    "models/cdf_process_industry_extension",
    "contextualization/cdf_connection_sql",
    "contextualization/cdf_p_and_id_parser",
    "industrial_tools/cdf_search"
]

[packages.infield]
title = "InField"
id = "infield"
description = "Put real-time data into the hands of every field worker."
modules = [
    "infield/cdf_infield_location",
    "infield/cdf_infield_second_location",
    "infield/cdf_infield_common",
    "common/cdf_apm_base"
]
```

#### module.toml

Each module must have a **module.toml** file in its root folder. This file describes the module and its content.

The **module.toml** file should contain the following information:

```toml
[module]
title = "OSIsoft/AVEVA PI Data Source" # Title displayed to the user
is_selected_by_default = false # If true, the checkbox is selected by default
id = "aveva"
package_id = "quickstart"

[dependencies]
modules = [] # List of modules that this module depends on
```

#### default.config.yaml

A **default.config.yaml** file must be present in each module folder *if the module contains any value placeholders.*

In other words, if your module has any curly bracket placeholders in the resource file `my.<Kind>.yaml` file,
you must provide a **default.config.yaml** file with those values:

```yaml
#<root folder>/module_1/data_models/my.Space.yaml
space: sp_{{ location }}_instances
name: {{ location_name }}_instances
description: 'Contains instances for {{ location_name }}.'

#<root folder>/module_1/data_models/my.DataSet.yaml
externalId: ds_{{ location }}_functions
name: '{{location_name}} Functions'
description: This dataset contains Functions for the {{location_name}}.

#root folder>/module_1/default.config.yaml
location: <not set>
location_name: <not set>
```

When cdf modules init or cdf modules add is run, the default values from **default.config.yaml** will be automatically
added to **config.dev.yaml** and **config.prod.yaml** so that the user can maintain the correct values.

```yaml

environment:
  project: <cdf project>
  type: dev
  selected:
  - modules/

variables:
  modules:
    module_1:
      location: <not set>
      location_name: <not set>
```

## Verification

To verify that the library is correctly structured:

1. Add your library path ending with the root folder to cdf.toml:

```toml
[libraries.my_library]
url = https://example.com/my-library/my-root-folder
```

1. Run `cdf modules init` or `cdf modules add` to verify that packages and modules are shown correctly
1. Verify that the module content is copied into the local modules/ folder

## Rate limiting

Certain cloud services, like GitHub, may rate limit requests to the library URL. Consider using a CDN or
similar if concurrent requests are expected.


<!-- SOURCE_END: toolkit/LIBRARIES.md -->

================================================================================


<!-- SOURCE_START: toolkit/module_upgrade\README.md -->
## File: toolkit/module_upgrade\README.md

# Module Upgrade

This directory contains the script `run_check.py` that is used to check for breaking changes in the package,
and that the `cdf-tk module upgrade` command works as expected.

## Motivation

This could have been part of the test suite, but it is not for two reasons:

* It needs to have project_inits for each version of the package, each at the size of `~25MB` which is too large to
  include in the test suite.
* Running the check is time consuming, and only needs to be run before a new release.

## Workflow

1. The constant `cognite_toolkit/_cdf_tk/constants.py:SUPPORT_MODULE_UPGRADE_FROM_VERSION` controls the
   earliest version that the `cdf-tk module upgrade` command should support.
2. Run `python module_upgrade/run_check.py` to check that the `cdf-tk module upgrade` command works as expected.
   If any exceptions are raised, you need to update the `_changes.py` file in the `modules` commands, so that the
   `cdf-tk module upgrade` command works as expected.


<!-- SOURCE_END: toolkit/module_upgrade\README.md -->

================================================================================


<!-- SOURCE_START: toolkit/README.md -->
## File: toolkit/README.md

# Cognite Data Fusion Toolkit

[![release](https://img.shields.io/github/actions/workflow/status/cognitedata/toolkit/release.yaml?style=for-the-badge)](https://github.com/cognitedata/toolkit/actions/workflows/release.yaml)
[![Github](https://shields.io/badge/github-cognite/toolkit-green?logo=github&style=for-the-badge)](https://github.com/cognitedata/toolkit)
[![PyPI](https://img.shields.io/pypi/v/cognite-toolkit?style=for-the-badge)](https://pypi.org/project/cognite-toolkit/)
[![Downloads](https://img.shields.io/pypi/dm/cognite-toolkit?style=for-the-badge)](https://pypistats.org/packages/cognite-toolkit)
[![GitHub](https://img.shields.io/github/license/cognitedata/toolkit?style=for-the-badge)](https://github.com/cognitedata/toolkit/blob/master/LICENSE)
[![Docker Pulls](https://img.shields.io/docker/pulls/cognite/toolkit?style=for-the-badge)](https://hub.docker.com/r/cognite/toolkit)
[![Ruff](https://img.shields.io/endpoint?url=https://raw.githubusercontent.com/astral-sh/ruff/main/assets/badge/v2.json&style=for-the-badge)](https://github.com/astral-sh/ruff)
[![mypy](https://img.shields.io/badge/mypy-checked-000000.svg?style=for-the-badge&color=blue)](http://mypy-lang.org)

The CDF Toolkit is a command-line interface (`cdf`) used for configuring and administrating
Cognite Data Fusion (CDF) projects. It ships with modularised `templates` that helps you
configure Cognite Data Fusion according to best practices.

It supports three different modes of operation:

1. As an **interactive command-line tool** used alongside the Cognite Data Fusion web application to retrieve and
   push configuration of the different Cognite Data Fusion services like data sets, data models, transformations,
   and more. This mode also supports configuration of new Cognite Data Fusion projects to quickly get started.
2. As tool to support the **project life-cycle by scripting and automating** configuration and management of Cognite Data
   Fusion projects where CDF configurations are kept as yaml-files that can be checked into version
   control. This mode also supports DevOps workflows with development, staging, and production projects.
3. As a **tool to deploy official Cognite project templates** to your Cognite Data Fusion project. The tool comes
   bundled with templates useful for getting started with Cognite Data Fusion, as well as for specific use cases
   delivered by Cognite or its partners. You can also create your own templates and share them.

## Usage

Install the Toolkit by running:

```bash
pip install cognite-toolkit
```

Then run `cdf --help` to get started with the interactive command-line tool.

## For more information

More details about the tool can be found at
[docs.cognite.com](https://docs.cognite.com/cdf/deploy/cdf_toolkit/).

You can find an overview of the modules and packages in the
[module and package documentation](https://docs.cognite.com/cdf/deploy/cdf_toolkit/references/resource_library).

See [./CONTRIBUTING.md](./CONTRIBUTING.md) for information about how to contribute to the `cdf-tk` tool or
templates.


<!-- SOURCE_END: toolkit/README.md -->

================================================================================


<!-- SOURCE_START: toolkit/tests\data\complete_org\modules\my_example_module\functions\README.md -->
## File: toolkit/tests\data\complete_org\modules\my_example_module\functions\README.md

# cdf_functions_dummy

This module is an example for how to define a simple function as a template in a tookit module.
The functions can be found below the 'functions' folder, with one folder per function.


<!-- SOURCE_END: toolkit/tests\data\complete_org\modules\my_example_module\functions\README.md -->

================================================================================


<!-- SOURCE_START: toolkit/tests\data\complete_org\modules\my_example_module\README.md -->
## File: toolkit/tests\data\complete_org\modules\my_example_module\README.md

# README.md

This is an example of a custom module. It contains two TimeSeries with a dataset.


<!-- SOURCE_END: toolkit/tests\data\complete_org\modules\my_example_module\README.md -->

================================================================================


<!-- SOURCE_START: toolkit/tests\data\complete_org\README.md -->
## File: toolkit/tests\data\complete_org\README.md

# about

This folder contains examples of all supported resource types


<!-- SOURCE_END: toolkit/tests\data\complete_org\README.md -->

================================================================================


<!-- SOURCE_START: toolkit/tests\data\naughty_project\README.md -->
## File: toolkit/tests\data\naughty_project\README.md

# Naughty Project

Test project with tricky test data.


<!-- SOURCE_END: toolkit/tests\data\naughty_project\README.md -->

================================================================================


<!-- SOURCE_START: toolkit/tests\README.md -->
## File: toolkit/tests\README.md

# Testing

Tests are organized into two main categories, integration and unit tests.
The integration tests are dependent on the CDF, while the unit tests are not:

```bash
📦tests
 ┣ 📂tests_integration - Tests depending on CDF.
 ┣ 📂tests_unit - Tests without any external dependencies.
 ┣ 📜constants.py - Constants used in the tests.
 ┗ 📜README.md - This file
```

## Integration Testing

To support the integration testing you need to have a `.env` file in the root of the project following the
[`.env.templ`](../cognite_toolkit/.env.templ) file structure.

## Unit Testing

### Snapshot Testing (Regression Testing)

In `cdf-tk`, we use snapshot testing to check that a sequence of CLI commands produces consistent output. This type
of testing is also called [Regression Testing](https://en.wikipedia.org/wiki/Regression_testing). Note the
idea of snapshot testing can be used in integration tests as well, but in `cdf-tk` we only use it
in the unit tests.

We have three main types of snapshot tests in `cdf-tk`:

1. `tests/tests_unit/test_approval_modules.py:test_deploy_module_approval` Doing the deployment for each module:
   1. `cdf-tk init`
   2. `cdf-tk build`
   3. `cdf-tk deploy`
2. `tests/tests_unit/test_approval_modules.py:test_deploy_dry_run_module_approval` Doing the deployment for each
   module with `--dry-run`
   1. `cdf-tk init`
   2. `cdf-tk build`
   3. `cdf-tk deploy --dry-run`
3. `tests/tests_unit/test_approval_modules.py:test_clean_module_approval` Doing the clean for each module:
   1. `cdf-tk init`
   2. `cdf-tk build`
   3. `cdf-tk clean`

These tests run the above commands, intercepts the calls to CDF, dumps the calls to a file, and compares the
output to the previous output of running the same commands for the same modules. See,
for example, [cdf_apm_simple](tests_unit/test_approval_modules_snapshots/cdf_apm_simple.yaml) to see an
output of a snapshot test.

The typical errors these tests' catch are:

* Any unexpected exceptions thrown by running the commands.
* Any unexpected changes to what is written to CDF.

**Note** that when you add a new module or do a change to an existing module that expects to change what is
written to CDF, these tests will fail.

* If you have added a new module, the first time you run the tests, the tests will fail as there is no previous
  output to compare to. This will automatically create a new snapshot file `.yaml` and the next time you run
  the tests, the tests will pass if the output produced is the same as the previous output.
* If you have changed an existing module, the tests will fail, and you need to manually verify that the changes
  are expected and then update the snapshot file. You do this by running the tests with the `pytest --force-regen`

### <code>ApprovalCogniteClient</code>

To intercept CDF calls, we have created an `ApprovalCogniteClient` that is used in the snapshot tests. To understand
this client, you need to understand mocking. The first Section below goes through the basics of mocking in the CDF client
context. The second section goes through how the `ApprovalCogniteClient` is built, and the last how to extend it.

**Note** that the `ApprovalCogniteClient` is not only used in the snapshot tests, but also in other tests where we want
to simulate CDF.

#### Mocking CDF Client

From the `cognite-sdk` there is a built-in mock client available. This can be used as shown below:

```python
from cognite.client.testing import CogniteClientMock
from cognite.client import CogniteClient
from cognite.client.data_classes import Asset, AssetList

def my_function() -> list[str]:
    client = CogniteClient()
    assets = client.assets.list()
    return [asset.name for asset in assets]


def tests_my_function():
    expected_asset_names = ["MyAsset"]
    
    with CogniteClientMock() as client_mock:
        client_mock.assets.list.return_value = AssetList([Asset(id=1, name="MyAsset")])
        actual_asset_names = my_function()
    
    assert actual_asset_names == expected_asset_names
```

In the example above, we have a function `my_function` that uses the `CogniteClient` to get the names of all assets.
In the test `tests_my_function` we use the `CogniteClientMock` to mock the `CogniteClient` and set the return value
of the `assets.list` method to a list of assets with the name `MyAsset`. We then call `my_function` and verify that
the return value is as expected.

#### ApprovalClient

A challenge with the example above is that there are typically 3–5 methods that need to be mocked for each resource
type. As of, 16. February 2024, the `cognite-toolkit` supports 18 different resource types, which means that the
number of methods that need to be mocked is ~72. In addition, the mocking of these calls we want to be able
to support the following:

* The ability to check which calls have been made. For example, when we run `cdf-tk deploy --dry-run` no `create`
  or `delete` calls should be made to CDF.
* We need to be able to get all the resources that have been created, updated, or deleted. This is used in the
  snapshot tests to verify that the output is as expected.
* We need to be able to get the resource to return a resource we have in a test, so we can check that the rest
  of the function works as expected.
* By default, all `list` and `retrieve` calls should return empty lists and `None`, respectively. Instead of
  a mock object which is the default behavior of the `CogniteClientMock`.

The approval client is put into a package called `approval_client` and is organized as follows:

```bash
```bash
📦approval_client
 ┣ 📜client.py - Module withe the ApprovalCogniteClient class
 ┣ 📜config.py - Configuration of which API methods are mocked by which mock functions.
 ┗ 📜data_classes.py - Helper classes for the ApprovalCogniteClient
```

Each of the public methods in the `ApprovalCogniteClient` should be documented with the most up-to-date information.

The most important methods/properties in the `ApprovalCogniteClient` are:

* Property `client` - Returns a mocked `CogniteClient`
* Method `append` - Allows you to append a resource to the client. This is used to simulate
  the resources that are returned from CDF.
* Method `dump` - Dumps all resources that have been created, updated, or deleted to a dictionary.

#### Extending the ApprovalClient

To extend the `ApprovalCogniteClient`, you either reuse existing mock functions or you have to create new ones.

The most important private methods in the `ApprovalCogniteClient` are:

* `_create_delete_method` - Creates a mock function for the `delete` methods in the API call
* `_create_create_method` - Creates a mock function for the `create` methods in the API call
* `_create_retrieve_method` - Creates a mock function for the `retrieve` methods in the API call

Inside each of these methods, you will find multiple functions that are used to create the mock functions. Which
function is used to mock which methods are controlled by the constant `API_RESOURCES` in `config.py`. This constant
is a list of APIClasses with the resource classes and which methods are mocked by which mock functions. If you
can reuse an existing mock function, you can add the resource class to the list and set the mock function to the
existing mock function. If you need to create a new mock function, you need to create a new function and add it
inside the relevant method in the `ApprovalCogniteClient`.

The reason why we have multiple different mock functions for the same type of methods is that the `cognite-sdk`
does not have a uniform way of handling the different resource types. For example, the `create` method
for most resource is create and takes a single or a list of the resource type `client.time_series.create` is
an example of this. However, when creating a `datapoints` the method is `client.time_series.data.insert` and takes a
`pandas.DataFrame` as input, same for `sequences`. Another exception if `client.files.upload_bytes` which is used
to upload files to CDF.

## Task Guides

This section contains guides on how to do different tasks related to the ApprovalClient

### Debugging Function Calls to CDF

Sometimes you want to debug the function calls to CDF. This can be to check what is actually written, or try to make
sense of an error message. It is not easy to use a debugger to step through the call to CDF, as you will be taken
through a maze of code that is related to the mocked client. In the example below, if you set a breakpoint at
`print("break here")` and run the code, if the `client` is mocked, you will have trouble stepping into the
`insert_dataframe` call.

```python
import pandas as pd

data = pd.DataFrame(
    {
        "timestamp": [1, 2, 3],
        "value": [1, 2, 3],
    }
)
print("break here")
client.raw.rows.insert_dataframe(
    db_name="my_db_name", table_name="my_table", dataframe = data, ensure_parent = False
)
```

Instead, you can check in the `tests_unit/approval_client/config.py` to figure out which method is used to mock
the `.insert_dataframe` call, looking through the file you will find this section:

```python
    APIResource(
        api_name="raw.rows",
        resource_cls=Row,
        _write_cls=RowWrite,
        list_cls=RowList,
        _write_list_cls=RowWriteList,
        methods={
            "create": [Method(api_class_method="insert_dataframe", mock_class_method="insert_dataframe")],
            "delete": [Method(api_class_method="delete", mock_class_method="delete_raw")],
            "retrieve": [
                Method(api_class_method="list", mock_class_method="return_values"),
                Method(api_class_method="retrieve", mock_class_method="return_values"),
            ],
        },
    ),
```

Some extra explanation of the code above:

* The `_write_cls` and `_write_list_cls` are the private, as the `write_cls` and `write_list_cls` are properties
  which uses the `resoruce_cls` and `list_cls` as fallbacks if the `_write_cls` and `_write_list_cls` are not set.
* In the methods dictionary, the key is the Loader classification. This is used to classify the type of method
  you are mocking. This is, for example, used to check `cdf-tk deploy --dry-run` do not make any `create` or `delete`
  calls. Note as of writing this `update` is not used in any tests, and have thus not been implemented.
* In the methods dictionary, the value is a linking between the method in the `cognite-sdk` and the mock function
  that is used to mock the method. The reason for this is to easily reuse mock methods for different `cognite-sdk`
  methods (very many of the `cognite-sdk` methods can be mocked in exactly the same way). You can see what mock methods
  are available by checking the functions inside each of the `_create_create_method`, `_create_delete_method`,
  and `_create_retrieve_method` methods in the `ApprovalCogniteClient`.

We see that the `create` method in the cognite-sdk for RAW rows is mocked by the `insert_dataframe` method in the mock
class. We can then go to the `tests_unit/approval_client/client.py` and find the `insert_dataframe` method inside the
private `_create_create_method` and set the breakpoint there. This will then stop the code execution when the
`insert_dataframe` method is called.

![image](https://github.com/cognitedata/toolkit/assets/60234212/aa8f72c9-0ecd-4166-bb41-f438fba25b4b)

### Simulate Existing Resource in CDF

You can simulate an existing resource in CDF by using the `append` method in the `ApprovalCogniteClient`. Below
is an example of a test that simulates an existing `Transformation` in CDF. Note that the `ApprovalCogniteClient`
does not do any logic when the `.retrieve` or `.list` methods are called, it just returns the resource that has
been appended to the client.

```python
def test_pull_transformation(
    monkeypatch: MonkeyPatch,
    cognite_client_approval: ApprovalCogniteClient,
    cdf_tool_config: CDFToolConfig,
    typer_context: typer.Context,
    init_project: Path,
) -> None:
    loader = TransformationLoader.create_loader(cdf_tool_config.toolkit_client)

    loaded = load_transformation()

    # Simulate a change in the transformation in CDF.
    loaded.name = "New transformation name"
    read_transformation = Transformation.load(loaded.dump())
    
    # Here we append the transformation to the ApprovalCogniteClient which 
    # simulates that the transformation exists in CDF.
    cognite_client_approval.append(Transformation, read_transformation)

    pull_transformation_cmd(
        typer_context,
        source_dir=str(init_project),
        external_id=read_transformation.external_id,
        env="dev",
        dry_run=False,
    )

    after_loaded = load_transformation()

    assert after_loaded.name == "New transformation name"
```

### Adding Support for a new Resource Type

When you add support for a new resource type, you need to first add the an entry to the
`approval_client/config.py` file which defines which methods are mocked by which mock functions.

If none of the existing mock functions can be used, you need to create a new mock function in the
`approval_client/client.py` file. The mock function should be added to the relevant method in the
`ApprovalCogniteClient` class.

You can check the following example PR when support was added for `Workflows` in the `ApprovalCogniteClient`:

[PR #453](https://github.com/cognitedata/toolkit/pull/453/files#diff-57825118cb6e6afb003f556137ba38af9f770c69f7223dfcaa2779413228e2f1R393)


<!-- SOURCE_END: toolkit/tests\README.md -->

================================================================================


<!-- SOURCE_START: toolkit/tests_smoke\README.md -->
## File: toolkit/tests_smoke\README.md

# Smoke Tests

This directory contains smoke tests for the Cognite Toolkit. These tests are designed to run periodically
(e.g., via a scheduled CI job) to verify that:

1. The CDF API documentation URLs are accessible
2. Critical integrations are working correctly

## Structure

```text
tests_smoke/
├── conftest.py              # Pytest fixtures (ToolkitClient setup)
├── check_smoke_results.py   # Script to parse test results and notify Slack
├── tests_cruds/
│   └── tests_resource_curd.py  # CRUD resource validation tests
└── README.md
```

## Tests

### `tests_cruds/tests_resource_curd.py`

- **`test_doc_url_is_valid`**: Validates that all CRUD resource documentation URLs are accessible.
  Uses async HTTP requests in parallel for fast execution. If any URLs fail, they are reported together
  in a single assertion error.

- **`test_workflow_working`**: A canary test that intentionally fails to verify that Slack notifications
  are working correctly.

## Running the Tests

### Prerequisites

1. Create a `.env` file in the repository root with the following variables:

   ```env
   CDF_CLUSTER=<your-cluster>
   CDF_PROJECT=<your-project>
   IDP_TOKEN_URL=<token-url>
   IDP_CLIENT_ID=<client-id>
   IDP_CLIENT_SECRET=<client-secret>
   ```

2. Install dependencies:

   ```bash
   uv sync --group dev
   ```

### Running Tests

```bash
# Run all smoke tests
pytest tests_smoke/

# Run with JSON report (for Slack notification script)
pytest tests_smoke/ --json-report --json-report-file=smoke_results.json
```

## Slack Notifications

The `check_smoke_results.py` script parses pytest JSON reports and sends notifications to Slack
when tests fail.

### Usage

```bash
python tests_smoke/check_smoke_results.py <results_file> [--dry-run]
```

### Required Environment Variables

- `SLACK_WEBHOOK_URL`: Slack webhook URL for sending notifications
- `GITHUB_REPO_URL`: URL to the GitHub repository (included in error messages)

### Notification Behavior

- **On failure**: Sends a message to Slack with the failure details
- **On Monday mornings (UTC)**: Sends an "alive" message confirming tests are running
- **On success (other days)**: No notification sent

## CI Integration

These tests are intended to run on a schedule (e.g., daily) in CI. The typical workflow is:

1. Run pytest with JSON report output
2. Run `check_smoke_results.py` to parse results and notify Slack
3. The script handles both test failures and execution failures (missing report file, invalid JSON, etc.)

## Creating Tests

To add new smoke tests, create new test files in the `tests_smoke/` directory and create a regular `pytest`.
The difference is that you should NOT use `assert` statements directly. Instead, raise `AssertionError`
with a descriptive message, something that is readable, also for a non-technical audience, since the
messages will be sent to Slack.

The reason for this is that `assert` statements will always include the line number and code snippet
in the error message, which can be confusing when the message is sent to Slack. By raising
`AssertionError` directly, you have full control over the error message content.


<!-- SOURCE_END: toolkit/tests_smoke\README.md -->

================================================================================
