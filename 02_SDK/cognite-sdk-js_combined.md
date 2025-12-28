# cognite-sdk-js Documentation
Generated from: cognite-sdk-js



<!-- SOURCE_START: cognite-sdk-js/.github\ISSUE_TEMPLATE\bug_report.md -->
## File: cognite-sdk-js/.github\ISSUE_TEMPLATE\bug_report.md

---
name: Bug report
about: Create a report to help us improve
title: ''
labels: ''
assignees: 'cognitedata/development-experiences'

---

**Describe the bug**
A clear and concise description of what the bug is.

**To Reproduce**
Steps to reproduce the behavior:
1. Go to '...'
2. Click on '....'
3. Scroll down to '....'
4. See error

**Expected behavior**
A clear and concise description of what you expected to happen.

**Screenshots**
If applicable, add screenshots to help explain your problem.

**Desktop (please complete the following information):**
 - OS: [e.g. iOS]
 - Browser [e.g. chrome, safari]
 - Version [e.g. 22]

**Smartphone (please complete the following information):**
 - Device: [e.g. iPhone6]
 - OS: [e.g. iOS8.1]
 - Browser [e.g. stock browser, safari]
 - Version [e.g. 22]

**Additional context**
Add any other context about the problem here.


<!-- SOURCE_END: cognite-sdk-js/.github\ISSUE_TEMPLATE\bug_report.md -->

================================================================================


<!-- SOURCE_START: cognite-sdk-js/.github\ISSUE_TEMPLATE\feature_request.md -->
## File: cognite-sdk-js/.github\ISSUE_TEMPLATE\feature_request.md

---
name: Feature request
about: Suggest an idea for this project
title: ''
labels: ''
assignees: 'cognitedata/development-experiences'

---

**Is your feature request related to a problem? Please describe.**
A clear and concise description of what the problem is. Ex. I'm always frustrated when [...]

**Describe the solution you'd like**
A clear and concise description of what you want to happen.

**Describe alternatives you've considered**
A clear and concise description of any alternative solutions or features you've considered.

**Additional context**
Add any other context or screenshots about the feature request here.


<!-- SOURCE_END: cognite-sdk-js/.github\ISSUE_TEMPLATE\feature_request.md -->

================================================================================


<!-- SOURCE_START: cognite-sdk-js/CODE_OF_CONDUCT.md -->
## File: cognite-sdk-js/CODE_OF_CONDUCT.md

This project is governed by the [Contributor Covenant version 1.4](https://www.contributor-covenant.org/version/1/4/code-of-conduct.html).

All contributors and participants agree to abide by its terms.

To report violations, send an email to fredrik.anfinsen@cognite.com.


<!-- SOURCE_END: cognite-sdk-js/CODE_OF_CONDUCT.md -->

================================================================================


<!-- SOURCE_START: cognite-sdk-js/CONTRIBUTING.md -->
## File: cognite-sdk-js/CONTRIBUTING.md

# Contributing

Contributions are welcome, and this document details how changes can be made and submitted,
and eventually included in a release. We use monorepo tooling, and a git setup for automatically releasing
new versions based on commit messages.

Please note we have a [code of conduct](./CODE_OF_CONDUCT.md).

## Making changes

Make the changes to the package(s) you want to change, and commit them to a fork or branch. Commits
need to follow [proper commit
messages](https://github.com/conventional-changelog/conventional-changelog/tree/master/packages/conventional-changelog-angular).
The commit messages are used to automatically bump the versions of changed packages, and write
automatic changelogs. See the [Releases & Versioning](#releases--versioning) section below about
releasing changes.

We use semantic versioning, with versions `MAJOR.MINOR.PATCH`.

- For fixes, start the commit with `fix: msg` or `fix(topic): msg`. This will bump PATCH.
- For features, start the commit with `feat: msg` or `feat(topic): msg`. This will bump MINOR.
- For changes that break backwards compatibility, add `BREAKING CHANGE: description` to the footer.
  This will bump MAJOR version.

Note that using `<type>!: msg` **is not supported**, it will actually break the semantics of some
types. `feat!: msg` will result in a _patch_ release, not major. `<type>!: msg` [is a
thing](https://www.conventionalcommits.org/en/v1.0.0/) but that is _not_ supported by the
implementation used in this repo.

Also note that a major version upgrades are _not_ propagated up the dependency tree. If you do a
breaking change in `@cognite/sdk-core` it will get a major version bump, if you use the `BREAKING CHANGE: desc` footer. However, packages that depends on `@cognite/sdk-core` will only get a patch
upgrade. So if you do a breaking change to a non-top level package and also want to get major
version bump of the upstream packages you have to ensure that manually. This can be done in two
ways, run `lerna version major` or do a inconsequential change to the top level packages. If you do
_any_ change to the top packages (including the smallest white space cleanup), it will all be
covered by the `BREAKING CHANGE: msg` commit, leading them to all get a major version bump.

- For extra details in the changelog, you can specify a scope like so: `feat(assets): `.
- For other changes there are types without version bumping semantics:
  - `docs: ` changes to documentation
  - `build: ` changes to build scripts and config
  - `ci: ` changes to ci scripts and pipeline
  - `refactor: ` code moving and renaming
  - `style: ` fixes to code style
  - `test: ` changes to tests
  - `perf: ` changes to improve performance
  - `revert: ` changing things back
  - `chore: ` miscelanious changes

#### Example

```
docs(contributing-readme): add example of commit with subject line
```

A commit hook makes sure the syntax is followed. Automated commit messages such as `Merge pull request` are handled.

## Code generation

This SDK support generating TypeScript types from the Cognite OpenAPI document.
The idea is to use the OpenAPI document as a source of truth and to automate
part of the process. Any incorrect or missing types should be fixed/added
in the OpenAPI document instead of manually adjusting the generated types.
This also helps to keep documentation up to date.

Use the command `yarn codegen` for available commands.

More details are documented in the [codegen README](packages/codegen/README.md).

## Pull request

Make a pull request from your branch to the master branch. When merging the pull request,
only use squashing if the resulting squash commit can accurately describe the change as a single conventional commit.
Once the change is pushed to the master branch, it is time for a release.

## Releases & Versioning

**We use a simple manual release process for enhanced security and control.**

### How to Release

Releases are initiated manually but executed automatically through pull requests:

```bash
# 1. Pull latest master and run release command
git checkout master
git pull origin master
yarn release

# 2. Review the generated PR in GitHub UI
# 3. Merge PR → packages automatically publish to NPM
```

### What Happens

1. **`yarn release`** - Calculates version bumps using conventional commits and creates a PR with:

   - Updated `package.json` versions for changed packages
   - Generated changelog entries
   - Automatic branch creation and push

2. **PR Review** - Review the version changes and release notes in the generated PR

3. **Auto-publish** - When the PR is merged to master:
   - Packages are built and published to NPM
   - Git tags are created automatically
   - GitHub releases are generated

### Security Features

- ✅ **No direct master pushes** - All changes go through PR review
- ✅ **Manual approval required** - Human oversight for every release
- ✅ **Clear audit trail** - Every release documented in PR history
- ✅ **Branch protection compatible** - Works with strict branch protection rules

### Prerequisites

- Repository must have `production` environment configured for manual approval
- `NPM_PUBLISH_TOKEN` secret must be available

### Emergency Releases

Use the same process - no exceptions. The security model ensures all releases are reviewable:

```bash
yarn release  # Create emergency release PR
# Review → Merge → Auto-publish
```

## Patching older major versions

If you need to backport a fix to a previous MAJOR version of a package,
you have to do it manually.

Let's say you want to make a fix to `@cognite/sdk-core@2`,
after `@cognite/sdk-core@3.0.0` is already published.

First check if there already is a backporting branch called `@cognite/sdk-core@2.x`.

```bash
git fetch
git checkout @cognite/sdk-core@2.x
```

If there isn't, make it based on the latest release of MAJOR version 2.
You can look at git tags to find the last release.

```bash
git fetch --all --tags
git tag | grep @cognite/sdk-core@2.
```

Let's say `2.38.1` was the last release before `3.0.0`.
We make a branch based on it for all backporting needs.

```bash
git checkout tags/@cognite/sdk-core@2.38.1 -b @cognite/sdk-core@2.x
```

Now we have a branch for our changes, in case we later need to backport more things.
In this branch, make your changes, and push them to GitHub.
We are not using CI/CD for publishing, but by pushing changes we can at least
see automated tests run on the branch. Make sure they pass!

Once you are ready to make the new version, and have pushed everything to git,
open a terminal in the root of the project and run:

```bash
yarn
yarn build
```

Then go to `packages/core` (or whatever package you are backporting to), and run:

```bash
npm version patch -m "backport fix to %s for reasons"
npm publish
git push && git push --tags
```

This will make a commit with the updated `package.json`, create a new git tag, and publish to npm.
Make sure you are logged in to npm, talk to a maintainer.

## Code overview

### HTTP Client

The core of the SDK is the HTTP client. The HTTP client is divided into multiple layers:

1. [BasicHttpClient](./packages/core/src/httpClient/basicHttpClient.ts)
2. [RetryableHttpClient](./packages/core/src/httpClient/retryableHttpClient.ts)
3. [CDFHttpClient](./packages/core/src/httpClient/cdfHttpClient.ts)

See each file for a description of what they do.

### Pagination

We have multiple utilities to easy pagination handling. The first entrypoint is [cursorBasedEndpoint](./packages/core/src/baseResourceApi.ts) which adds a `next()` function on the response to fetch the next page of result. Then we use [makeAutoPaginationMethods](./packages/core/src/autoPagination.ts) to add the following methods:

- autoPagingToArray
  ```ts
  const assets = await client.assets.list().autoPagingToArray({ limit: 100 });
  ```
- autoPagingEach
  ```ts
  for await (const asset of client.assets
    .list()
    .autoPagingEach({ limit: Infinity })) {
    // ...
  }
  ```

### Date parser

Some API responses includes DateTime responses represented as UNIX timestamps. We have utility class to automatically translate the number response into a Javascript [Date instance](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date).

See the [DateParser class](./packages/core/src/dateParser.ts).

### Metadata

We offer users to get hold of the HTTP response status code and headers through the [MetadataMap class](./packages/core/src/metadata.ts).

### Core utilities

- [promiseAllWithData](./packages/core/src/utils.ts)
- [promiseCache](./packages/core/src/utils.ts)
- [topologicalSort](./packages/core/src/graphUtils.ts)
- [RevertableArraySorter](./packages/core/src/revertableArraySorter.ts)

### Cognite Clients

There is a Cognite Client per SDK package:

- [Core](./packages/core/src/baseCogniteClient.ts)
- [Stable](./packages/stable/src/cogniteClient.ts)
- [Beta](./packages/beta/src/cogniteClient.ts)
- [Alpha](./packages/alpha/src/cogniteClient.ts)
- [Template](./packages/template/src/cogniteClient.ts)

The Core one is the base, meaning the others extends from it.

#### Authentication

The authentication logic lives in the [core BaseCogniteClient](./packages/core/src/baseCogniteClient.ts).
The client constructor offer the field `oidcTokenProvider` (formely called `getToken`) where the SDK user will provide a valid access token.

The SDK will call this method when:

- The user calls `authenticate` on the client.
- The SDK receives a 401 from the API.
  When multiple requests receives a 401, then only a single call to `oidcTokenProvider` will be invoked. All requests will wait for `oidcTokenProvider` to resolve/reject. If it's resolved, then all the requests will retry before returning the response to the SDK caller. However, if the resolved access token matches the original access token, then no retry will be performed.


<!-- SOURCE_END: cognite-sdk-js/CONTRIBUTING.md -->

================================================================================


<!-- SOURCE_START: cognite-sdk-js/guides\assets.md -->
## File: cognite-sdk-js/guides\assets.md

# Assets

<!--What are Assets?  Generic overview information-->

In Cognite Data Fusion, the [asset](https://docs.cognite.com/dev/concepts/resource_types/assets) **resource type** stores the **digital representations** of objects or groups of **objects from the physical world**. Water pumps, heart rate monitors, machine rooms, and production lines are examples for those assets.

:::info NOTE
**Note** A note to make is, all steps below you will need to be authenticated with [OIDC](./authentication.md#openid-connect-oidc).
:::

**In this article:**

- [Assets](#assets)
  - [Aggregate assets](#aggregate-assets)
  - [Create a asset](#create-a-asset)
  - [Delete assets](#delete-assets)
  - [List assets](#list-assets)
  - [Retrieve assets by id](#retrieve-assets-by-id)
  - [Search for assets](#search-for-assets)
  - [Update assets](#update-assets)

## Aggregate assets

Aggregate assets.

**_Parameters_**

| Properties                                                                                                          | Definition                                 |
| ------------------------------------------------------------------------------------------------------------------- | ------------------------------------------ |
| **query** ([AssetAggregateQuery](https://cognitedata.github.io/cognite-sdk-js/interfaces/assetaggregatequery.html)) | Query schema for asset aggregate endpoint. |

**_Returns_**

| Return                    | Type                                                                                                  |
| ------------------------- | ----------------------------------------------------------------------------------------------------- |
| List of aggregated assets | Promise<[AssetAggregate](https://cognitedata.github.io/cognite-sdk-js/globals.html#assetaggregate)[]> |

**_Examples_**

Aggregating asset:

```ts
const filters: AssetAggregateQuery = { filter: { root: true } };

const aggregates: Promise<AggregateResponse[]> = await client.assets.aggregate(
  filters
);
```

## Create a asset

Creating assets.

**_Parameters_**

| Properties                                                                                                        | Definition                   |
| ----------------------------------------------------------------------------------------------------------------- | ---------------------------- |
| **items** ([ExternalAssetItem](https://cognitedata.github.io/cognite-sdk-js/interfaces/externalassetitem.html)[]) | Array with asset properties. |

**_Returns_**

| Return                   | Type                                                                                   |
| ------------------------ | -------------------------------------------------------------------------------------- |
| List of requested assets | Promise<[Asset](https://cognitedata.github.io/cognite-sdk-js/interfaces/asset.html)[]> |

**_Examples_**

Creating a single asset:

```ts
const assets: ExternalAssetItem[] = [
  {
    name: 'First asset',
    description: 'My first asset',
    externalId: 'firstAsset',
  },
];

const createdAssets: Promise<Asset[]> = await client.assets.create(assets);
```

Creating multiple assets:

```ts
const assets: ExternalAssetItem[] = [
  { name: 'First asset' },
  {
    name: 'Second asset',
    description: 'Another asset',
    externalId: 'anotherAsset',
  },
  { name: 'Child asset', parentExternalId: 'anotherAsset' },
];

const createdAssets: Promise<Asset[]> = await client.assets.create(assets);
```

## Delete assets

Deleting assets.

**_Parameters_**

| Properties                                                                                                      | Definition                     |
| --------------------------------------------------------------------------------------------------------------- | ------------------------------ |
| **ids** ([AssetIdEither](https://cognitedata.github.io/cognite-sdk-js/globals.html#assetideither)[])            | Asset internal or externalIds. |
| **params** ([AssetDeleteParams](https://cognitedata.github.io/cognite-sdk-js/interfaces/assetdeleteparams.html) | (_Optional_). Delete params.   |

**_Returns_**

| Return        | Type            |
| ------------- | --------------- |
| Empty Promise | Promise<object> |

**_Examples_**

Deleting single asset with id:

```ts
const ids: IdEither[] = [{ id: 123 }];

await client.assets.delete(ids);
```

Deleting multiple assets with id:

```ts
const ids: IdEither[] = [{ id: 123 }, { id: 456 }];

await client.assets.delete(ids);
```

Deleting single asset with externalId:

```ts
const ids: IdEither[] = [{ externalId: 'abc' }];

await client.assets.delete(ids);
```

Deleting multiple assets with externalId:

```ts
const ids: IdEither[] = [{ externalId: 'abc' }, { externalId: 'def' }];

await client.assets.delete(ids);
```

## List assets

Listing assets.

**_Parameters_**

| Properties                                                                                                | Definition                                          |
| --------------------------------------------------------------------------------------------------------- | --------------------------------------------------- |
| **scope** ([AssetListScope](https://cognitedata.github.io/cognite-sdk-js/interfaces/assetlistscope.html)) | (_Optional_). Query cursor, limit and some filters. |

**_Returns_**

| Return        | Type                                                                                                                                                                                    |
| ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Empty Promise | [CursorAndAsyncIterator](https://cognitedata.github.io/cognite-sdk-js/globals.html#cursorandasynciterator)<[Asset](https://cognitedata.github.io/cognite-sdk-js/interfaces/asset.html)> |

**_Examples_**

Listing assets:

```ts
const filters: AssetListScope = { filter: { name: '21PT1019' } };

const assets: CursorAndAsyncIterator<Asset> = await client.assets.list(filters);
```

## Retrieve assets by id

Retrieving assets by id.

**_Parameters_**

| Properties                                                                                                           | Definition                                                    |
| -------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------- |
| **ids** ([IdEither[]](https://cognitedata.github.io/cognite-sdk-js/globals.html#ideither))                           | InternalId or ExternalId array.                               |
| **params** ([AssetRetrieveParams](https://cognitedata.github.io/cognite-sdk-js/interfaces/assetretrieveparams.html)) | _(Optional)_. Ignore IDs and external IDs that are not found. |

**_Returns_**

| Return                | Type                                                                                   |
| --------------------- | -------------------------------------------------------------------------------------- |
| List requested assets | Promise<[Asset](https://cognitedata.github.io/cognite-sdk-js/interfaces/asset.html)[]> |

**_Examples_**

Retrieving a single asset by id:

```ts
const ids: IdEither[] = [{ id: 123 }];

const assets: Promise<Asset[]> = await client.assets.retrieve(ids);
```

Retrieving multiple assets by id:

```ts
const ids: IdEither[] = [{ id: 123 }, { id: 456 }];

const assets: Promise<Asset[]> = await client.assets.retrieve(ids);
```

Retrieving a single asset by externalId:

```ts
const ids: IdEither[] = [{ externalId: 'abc' }];

const assets: Promise<Asset[]> = await client.assets.retrieve(ids);
```

Retrieving multiple assets by externalId:

```ts
const ids: IdEither[] = [{ externalId: 'abc' }, { externalId: 'def' }];

const assets: Promise<Asset[]> = await client.assets.retrieve(ids);
```

## Search for assets

Searching for assets.

**_Parameters_**

| Properties                                                                                                      | Definition                            |
| --------------------------------------------------------------------------------------------------------------- | ------------------------------------- |
| **query** ([AssetSearchFilter](https://cognitedata.github.io/cognite-sdk-js/interfaces/assetsearchfilter.html)) | Query cursor, limit and some filters. |

**_Returns_**

| Return               | Type                                                                                   |
| -------------------- | -------------------------------------------------------------------------------------- |
| List searched assets | Promise<[Asset](https://cognitedata.github.io/cognite-sdk-js/interfaces/asset.html)[]> |

**_Examples_**

Searching assets:

```ts
const filters: AssetSearchFilter = {
  filter: {
    parentIds: [1, 2],
  },
  search: {
    query: '21PT1019',
  },
};

const assets: Promise<Asset[]> = await client.assets.search(filters);
```

## Update assets

Updating assets.

**_Parameters_**

| Properties                                                                                          | Definition              |
| --------------------------------------------------------------------------------------------------- | ----------------------- |
| **change** ([AssetChange](https://cognitedata.github.io/cognite-sdk-js/globals.html#assetchange)[]) | Assets data for update. |

**_Returns_**

| Return              | Type                                                                                   |
| ------------------- | -------------------------------------------------------------------------------------- |
| List updated assets | Promise<[Asset](https://cognitedata.github.io/cognite-sdk-js/interfaces/asset.html)[]> |

**_Examples_**

Updating a single asset by id:

```ts
const assets: AssetChange[] = [
  { id: 123, update: { name: { set: 'New name' } } },
];

const updatedAssets: Asset[] = await client.assets.update(assets);
```

Updating multiple assets by id:

```ts
const assets: AssetChange[] = [
  { id: 123, update: { name: { set: 'New name 123' } } },
  { id: 456, update: { name: { set: 'New name 456' } } },
];

const updatedAssets: Asset[] = await client.assets.update(assets);
```

Updating a single asset by externalId:

```ts
const assets: AssetChange[] = [
  { externalId: 'abc', update: { name: { set: 'New name' } } },
];

const updatedAssets: Asset[] = await client.assets.update(assets);
```

Updating multiple assets by externalId:

```ts
const assets: AssetChange[] = [
  { externalId: 'abc', update: { name: { set: 'New name abc' } } },
  { externalId: 'def', update: { name: { set: 'New name def' } } },
];

const updatedAssets: Asset[] = await client.assets.update(assets);
```


<!-- SOURCE_END: cognite-sdk-js/guides\assets.md -->

================================================================================


<!-- SOURCE_START: cognite-sdk-js/guides\authentication.md -->
## File: cognite-sdk-js/guides\authentication.md

# Authentication in browsers

- [Authentication in browsers](#authentication-in-browsers)
  - [Use of access tokens](#use-of-access-tokens)
  - [Accessing different clusters](#accessing-different-clusters)
  - [How to authenticate with the SDK?](#how-to-authenticate-with-the-sdk)
    - [Example using Entra ID through MSAL](#example-using-entra-id-through-msal)
    - [OIDC authentication using client credentials](#oidc-authentication-using-client-credentials)
      - [Example](#example)
  - [Manually trigger authentication](#manually-trigger-authentication)
  - [Cache access tokens](#cache-access-tokens)
  - [More](#more)

## Use of access tokens

The Cognite Data Fusion API only supports [access tokens](https://datatracker.ietf.org/doc/html/rfc6749#section-1.4), which are short-lived tokens (typically a [JSON Web Token](https://datatracker.ietf.org/doc/html/rfc7519)). Therefore, the SDK must attach such a token to each request. The SDK doesn't itself generate an access token but provides a mechanism to ask for a valid access token when the SDK needs one. This is done through the `oidcTokenProvider` field in the CogniteClient constructor:

```ts
const client = new CogniteClient({
  appId: 'sample-app',
  baseUrl: 'https://api.cognitedata.com',
  project: 'demo-project',
  oidcTokenProvider: async () => { ... },
});
```

`oidcTokenProvider` is a function that must be provided and has the return type `Promise<string>`. The return value should be a promise resolving into a valid access token. It's the developer's responsibility to get hold of such a token. How to get the token depends on the Identity Provider (IdP) that is configured to be used for the Cognite Data Fusion project/organization. The most common is to use Entra ID (former Azure AD), and there is an example below on how to authenticate with Entra ID.

The first invocation of `oidcTokenProvider` will happen after one of these actions:
1. `client.authenticate()` is called.
2. The SDK gets a [HTTP 401 status code](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/401) response from the Cognite Data Fusion API.

`oidcTokenProvider` may be invoked several times as the access token is short-lived. Whenever the SDK receives a 401, the SDK will call `oidcTokenProvider` to get an updated token. If the new token differs from the token used in the 401 request, then the request will be retried with the new token.

## Accessing different clusters

Cognite operates multiple clusters. The default cluster `api.cognitedata.com` will be
used unless you override it. To access projects on a different cluster, use the [baseUrl](https://cognitedata.github.io/cognite-sdk-js/interfaces/clientoptions.html#baseurl) parameter in the SDK constructor.

```js
// Specify the cluster `bluefield`
const client = new CogniteClient({
  appId: 'sample-app',
  baseUrl: 'https://bluefield.cognitedata.com',
  project: 'demo-project',
  oidcTokenProvider: ...
});
```

## How to authenticate with the SDK?

Quickly summarized, the application passes a `oidcTokenProvider` callback to the SDK and uses it to get and renew tokens as needed. If you need to integrate your application and the SDK with, for example, Entra ID (Microsoft), use the appropriate library
from Microsoft ([msal](https://www.npmjs.com/package/@azure/msal-browser)). If you need to integrate with Auth0, use their libraries and integrate with the SDK via `oidcTokenProvider`.

### Example using Entra ID through MSAL

This example shows how to use the Microsoft [msal](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) library to get a token from Microsoft on behalf of a user using the [authorization code flow](https://oauth.net/2/grant-types/authorization-code/) with [PKCE](https://oauth.net/2/pkce/):

```js
import { Configuration, PublicClientApplication } from "@azure/msal-browser";
import { CogniteClient } from "@cognite/sdk";

const cluster = process.env.REACT_APP_CLUSTER || 'api'
const baseUrl = `https://${cluster}.cognitedata.com`;

const scopes = [
  `${baseUrl}/DATA.VIEW`,
  `${baseUrl}/IDENTITY`
];

// MSAL configuration
const configuration: Configuration = {
  auth: {
    clientId: "$AZURE_APP_ID",
    authority: "https://login.microsoftonline.com/$AZURE_TENANT_ID",
  },
};

const pca = new PublicClientApplication(configuration);
const oidcTokenProvider = async () => {
  const accountId = sessionStorage.getItem("account");
  const account = pca.getAccountByLocalId(accountId)!;
  const token = await pca.acquireTokenSilent({
    account,
    scopes,
  }).catch(e => {
    return pca.acquireTokenPopup({
    account,
    scopes,
    });
  });
  return token.accessToken;
};


const client = new CogniteClient({
  project: "my-project",
  appId: "demo-sample",
  oidcTokenProvider
});

```

You can find a full sample application [here](https://github.com/cognitedata/cognite-sdk-js/tree/master/samples/react/msal-browser-react/) and [here](https://github.com/cognitedata/cognite-sdk-js/tree/master/samples/react/msal-advanced-browser-react).

### OIDC authentication using client credentials

This flow gets a token on behalf of a [client credential](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow). This is typically a non-human entity with some access to a system and is appropriate for
background operations like extractors.

#### Example

```ts
import { ConfidentialClientApplication } from "@azure/msal-node";

async function quickstart() {
  const pca = new ConfidentialClientApplication({
    auth: {
      clientId,
      clientSecret,
      authority: `https://login.microsoftonline.com/${azureTenant}`,
    },
  });

  const client = new CogniteClient({
    appId: 'Cognite SDK samples',
    project,
    baseUrl: "https://api.cognitedata.com",
    oidcTokenProvider: () =>
      pca
        .acquireTokenByClientCredential({
          scopes: ['https://api.cognitedata.com/.default'],
          skipCache: true,
        })
        .then((response) => response?.accessToken! as string),
  });

  await client.authenticate();

  const info = (await client.get('/api/v1/token/inspect')).data;

  console.log('tokenInfo', JSON.stringify(info, null, 2));

  try {
    const assets = await client.assets.list();
    console.log(assets);
  } catch (e) {
    console.log('asset error');
    console.log(e);
  } //
}

quickstart()
```

[Demo project](https://github.com/cognitedata/cognite-sdk-js/tree/master/samples/nodejs/oidc-typescript)

## Manually trigger authentication

Instead of waiting for the first `401` response, you can trigger the authentication flow manually like this:

```ts

const client = new CogniteClient({ ... });
await client.authenticate(); // this also returns the token received
```

## Cache access tokens

If you already have an access token, you can use it to skip the authentication flow.

<!-- (See this [section](#tokens) on how to get hold of the token). -->

If the token is invalid or timed out, the SDK triggers a standard auth-flow on the first 401-response from CDF.

```js
const client = new CogniteClient({
  project: 'YOUR PROJECT NAME HERE',
  oidcTokenProvider: () => Promise.resolve('ACCESS TOKEN FOR THE PROJECT HERE'),
});
```

## More

Read more about the [authentication process](https://developer.cognite.com/dev/guides/iam/external-application#tokens).


<!-- SOURCE_END: cognite-sdk-js/guides\authentication.md -->

================================================================================


<!-- SOURCE_START: cognite-sdk-js/guides\derived_SDKs.md -->
## File: cognite-sdk-js/guides\derived_SDKs.md

# Derived SDKs HOW-TO

This document serves as a guide for working with SDK packages.
First there is an overview, describing the most important packages
in the repository and how they interact. Then there are guides for
creating your own SDK packages, how to add APIs to the SDK, how to
make a derived SDK, and how to override APIs from the derived SDK.

## Overview of packages

### @cognite/sdk-core
exports a `BaseCogniteClient` ([docs](https://cognitedata.github.io/cognite-sdk-js/core/classes/basecogniteclient.html)) which supports logging in, upon which it creates
a `CDFHttpClient` containing the API keys needed to interact with the API.
Helper functions for logging in with browser popups and redirects also live here, and a generic `BaseResourceApi<T>` class provides protected templated methods for working with resource APIs with
pagination and chunking.

### @cognite/sdk (stable)
exports a `CogniteClient` ([docs](https://cognitedata.github.io/cognite-sdk-js/classes/cogniteclient.html)) which inherits from the `BaseCogniteClient` to add accessors to all stable API endpoints. Endpoint access is grouped by resource type. Example: `TimeSeriesAPI` ([docs](https://cognitedata.github.io/cognite-sdk-js/classes/timeseriesapi.html)) lets you create, filter, count, delete time series.

Most types used in requests and responses are defined as interfaces and type aliases in `src/types.ts`,
and closely mimic the structure of the REST API.

### @cognite/sdk-beta
depends on both stable and core, and uses inheritance to augment the stable sdk.
The resulting class is exported as `CogniteClient` ([docs](https://cognitedata.github.io/cognite-sdk-js/beta/classes/_beta_src_cogniteclient_.cogniteclient.html)), and the rest of stable is forward-exported as well,
which makes the beta package behave as stable by default. To understand how the beta class can override
parts of stable, see the sections below.

### Any other potential packages
This is not an exhaustive list, because you can create your own packages.
See the sections below.

# Creating an SDK derived from stable

To create a new package, copy the `packages/template` folder in place and give it a new name. Go into the folder and change `package.json` to give the npm package a name. Tooling expects the package name to match the folder name like so:
```json
    "private": true,
    "name": "@cognite/sdk-<folder name>",
    "version": "0.0.0",
```
Keep the package private until it's ready to be published, since CI will version and publish all public packages
when releases are triggered.

Finally, if you want docs from this SDK to appear as a subfolder on the reference docs,
add the following script, which will run when docs are built for deployment:
```json
        "docs:bundle": "yarn docs && mkdir -p ../../docs/<subfolder> && cp -r docs/* ../../docs/<subfolder>/"
```

The `README.md` file will appear on npm if published, so write something about the package.

## The structure of a package
In your newly created folder you will find some config files and a `src/` directory.
```
 src/
 |--> __test__/
 |--> cogniteClient.ts
 \--> index.ts
```

The client is defined in `cogniteClient.ts` and is exported from `index.ts`.
The simplest possible client (derived from stable) looks like this:
```ts
import { CogniteClient as CogniteClientStable } from '@cognite/sdk';

export default class CogniteClient extends CogniteClientStable {

}
```
With the accompanying `index.ts`:
```ts
export * from '@cognite/sdk';
export { default as CogniteClient } from './cogniteClient';
```

# Adding endpoints to an SDK

Endpoints are all implemented as functions on subclasses of `BaseResourceAPI<T>`, but that is not a strict
requirement. The class simply helps with common actions on resources (when the REST API is laid out like in CDF), and the generic parameter lets you specify
the type. For an example, see `TimeSeriesAPI` [here](../packages/stable/src/api/timeSeries/timeSeriesApi.ts).
The class has several endpoints, most of which already have generic implementations in the superclass.

Let's define a new API class in `src/api/coolThing/coolThingApi.ts`:
```ts
import {
    BaseResourceAPI,
    InternalId,
    CursorAndAsyncIterator,
} from '@cognite/sdk-core';

export interface ExternalCoolThing {
    name: string;
    coolness: number;
}

export type CoolThing = ExternalCoolThing & InternalId;

export interface CoolThingQuery {
    /** Only get cool things at least this cool */
    min?: number;

    /** Only get cool things no cooler than */
    max?: number;
}

export class CoolThingAPI extends BaseResource<CoolThing> {

    /**
    * [Create cool things](https://doc.cognitedata.com/api/v1/#operation/postCoolThing)
    *
    * ```js
    * const coolthings = [
    *   { name: 'Shrub', coolness: 40 },
    *   { name: 'Cactus', coolness: 80 },
    * ];
    * const created = await client.coolThing.create(coolthings);
    * ```
    */
    public create = (items: ExternalCoolThing[]): Promise<CoolThing[]> => {
        return super.createEndpoint(items);
    };

    /** <docstring here> */
    public list = (query?: CoolThingQuery): CursorAndAsyncIterator<CoolThing> => {
        return super.listEndpoints(this.callListEndpointWithPost, query);
    }
}
```

The `CogniteClient` class makes instances of these classes in `initAPIs()`.

Let's add our `CoolThingAPI` to our derived `CogniteClient`.
```ts
import { CogniteClient as CogniteClientStable } from '@cognite/sdk';
import { CoolThingAPI } from './api/coolThing/coolThingApi';
import { accessApi } from '@cognite/sdk-core';

export default class CogniteClient extends CogniteClientStable {

    private coolThingApi?: CoolThingAPI;

    protected initAPIs() {
        super.initAPIs();

        // Turns into $BASE_URL/api/$API_VERSION/projects/$PROJECT/coolthing
        this.coolThingApi = this.apiFactory(CoolThingAPI, 'coolthing');
    }

    get coolThing(): CoolThingAPI {
        return accessApi(this.coolThingApi);
    }
}
```

The resulting CogniteClient now contains all API classes from stable, as well as our new API class.
When you create API classes, they should be exported in `index.ts`, along with any types used by
the API class.
```ts
export * from '@cognite/sdk';
export { default as CogniteClient } from './cogniteClient';
export {
    CoolThingAPI,
    ExternalCoolThing,
    CoolThing,
    CoolThingQuery,
} from './api/coolThing/coolThingApi';
```

In the stable API all type definitions are placed in `types.ts`, and then `*` is exported from `types.ts`. If `CoolThingAPI` was in stable, the types would be there instead, and we wouldn't have to explicitly export them in `index.ts`.

In derived SDKs, it is recommended to put the types with the API file that uses it.
Then it is easier to see what has been changed when moving features to stable.

# Adding an endpoint in a derived API class

Let's say we are writing an SDK derived from stable and want to add a new endpoint in `TimeSeriesAPI`.
To do this, we can create our own `TimeSeriesAPI` class inheriting from the stable class,
and put our new endpoint there.

We make our own `src/api/timeseries/timeSeriesApi.ts`:
```ts
import { TimeSeriesAPI as TimeSeriesAPIStable } from '@cognite/sdk';
import { InternalId } from '@cognite/sdk-core';

export class TimeSeriesAPI extends TimeSeriesAPIStable {
    /** <docstring> */
    public flip = async (id: InternalId) => {
        const path = this.url('flip');
        const response = await this.post(path, { data: { id }});
        return this.addToMapAndReturn({}, response);
    }
}
```

Then we need to use this new `TimeSeriesAPI` in the client. It's not enough to
give it the same name. We must re-define the `timeseries` field to return an instance
of our new class. The problem is that stable's `CogniteClient.timeseries` already has a defined type.
Subclasses normally can't broaden the type of fields. We do however have a trick:
```ts
import { CogniteClient as CogniteClientStable } from '@cognite/sdk';
import { TimeSeriesAPI } from './api/timeSeries/timeSeriesApi';

class CogniteClientCleaned extends CogniteClientStable {
    // Remove type restriction on timeseries
    timeseries: any;
}

export default class CogniteClient extends CogniteClientCleaned {

    private timeSeriesApi?: TimeSeriesAPI;
    protected initAPIs() {
        super.initAPIs();
        this.timeSeriesApiBeta = this.apiFactory(TimeSeriesAPI, 'timeseries');
    }

    get timeseries(): TimeSeriesAPI {
        return accessApi(this.timeSeriesApiBeta);
    }
}
```

Typescript always allows the `any` type to be used, which weakens the type of `timeseries` in `CogniteClientCleaned`.
We can then override that class again, and specify the correct strict type.
IDEs now understand that the `timeseries` field has the new type, and gives correct suggestions.

It is now very important to export this new `TimeSeriesAPI` in `index.ts`:
```ts
export * from '@cognite/sdk'; // This /won't/ export TimeSeriesAPI
export { default as CogniteClient } from './cogniteClient';
export { TimeSeriesAPI } from './api/timeSeries/timeSeriesApi'; // Because we export it here
```

Because we specify by name, it will take precedence over `export * from @cognite/sdk`,
which normally exports its own `TimeSeriesAPI`.

# Changing existing endpoints and types

If you want to change an existing endpoint, such as adding a field to time series,
the easiest way is to completely copy the `timeSeriesApi.ts` file. In here you define your
own `Timeseries` interface, instead of importing it from `../../types`.
You must then export this new API class and new type, just like in previous sections.

The resulting `index.ts` should look something like this:
```ts
export * from '@cognite/sdk'; // This /won't/ export Timeseries
export { default as CogniteClient } from './cogniteClient';
export { TimeSeriesAPI, Timeseries } from './api/timeSeries/timeSeriesApi';
```

# Adding tests

Each SDK has its own `src/__tests__/` folder where all `*.test.ts` and `*.spec.ts` get run.
There are some example tests in the template, but you should add your own, in separate files
for separate API classes.

# Accessing endpoints from outside of CDF

If you want to access a different domain, it is recommended to make your own `BasicHttpClient`
 ([docs](https://cognitedata.github.io/cognite-sdk-js/beta/classes/_core_src_httpclient_basichttpclient_.basichttpclient.html)).
 You specify a `baseUrl` in the constructor, and relative paths in calls to `get`, `post` etc.

Example of a custom `CogniteClient` class with an external API:
```ts
import { BasicHttpClient } from '@cognite/sdk-core';

export default class CogniteClient extends CogniteClientStable {

    private openDoor?: (name: string) => Promise<boolean>;

    protected initAPIs() {
        super.initAPIs();

        const doorClient = new BasicHttpClient("https://example.com");

        type DoorState = {
            open: boolean;
        };

        this.openDoor = async (name: string) => {
            // calls https://example.com/doors/open?name=<name>
            const response = await doorClient.post<DoorState>('doors/open', { params: { name } });
            return response.data.open;
        }
    }

    get doorControl() {
        return {
            openDoor: accessApi(this.openDoor)
        };
    }
}
```
You may also provide full paths (starting in `http://` or `https://`) to `get`, `post`, etc.,
which will cause the `baseUrl` to be ignored.


# Compiling and testing your SDK

The monorepo tooling will compile and test all packages when commits are pushed to GitHub,
but you can also (and should) do it locally.

You should first make sure all packages are built, since your dependencies live in the repository.
```sh
yarn
yarn build
```

After this is done, you can go into your package folder and run
```
yarn test
```

Whenever you make changes, remember to `yarn build`, and if other packages in the repo change,
go back to the root and run the command there.

# Creating an SDK not derived from stable
If you want to make an SDK derived from a beta or alpha, replace the dependency on `@cognite/sdk` with the correct package,
and yarn workspace will find it. All imports must be changed to the new package.
In theory all SDK versions are quite similar, except for additions and small changes, so find & replace on imports should mostly work.
```ts
import { CogniteClient as CogniteClientStable } from '@cognite/sdk-beta';
```

If however, you want to create an SDK not based on any existing SDK, you only need to depend on core.
The stable SDK is such an SDK, see [stable/src/cogniteClient.ts](../packages/stable/src/cogniteClient.ts).

# Publishing your SDK

There are two ways of publishing your derived SDK.

Any package with `"private": false` in the master branch will be published
to npm when a `[release]` commit happens. The version number will also be automatically
bumped based on commit messages. See [CONTRIBUTING.md](../CONTRIBUTING.md).

**NOTE:** If you depend on e.g. `stable`, and `stable` changes, your PATCH version will be bumped
to use the new version of `stable`.

It's also possible to keep your package private and only release it manually.
In this case you need to update the version and versions of dependencies yourself.

# Using a derived SDK as a drop-in replacement

If your derived SDK is backwards compatible with its parent, you can use your derived SDK under an alias to make it a drop-in replacement.
Let's say you have a feature in `@cognite/sdk-beta@1.3.0` that
you would like to use in an app where `@cognite/sdk` is a dependency.

Assuming the beta package is published to npm, use the following line in the app's `package.json`:
```json
"dependencies": {
    "@cognite/sdk": "npm:@cognite/sdk-beta@^1.3.0",
    ...
}
```
Run `yarn` or `npm install` to install the beta in place of stable, with the same name.
All your imports will stay the same, but now reference the beta package.
```ts
import { CogniteClient } from '@cognite/sdk'; // Now gives the beta client
```

# Moving features upstream

One of the main use cases for derived SDKs is implementing experimental features before they
reach stable. When the features do reach stable, the SDK implementation should move as well.
The guides above try making this move as painless as possible, and in general the following should be enough:
 - Move the new API class file to replace the old one
 - Move types into `types.ts` if applicable (e.g. when moving to stable)
 - Move any tests concerning new features
 - Remove overridden API class accessors
 - build & test
 - Look very hard at the diff to see that you didn't lose anything


<!-- SOURCE_END: cognite-sdk-js/guides\derived_SDKs.md -->

================================================================================


<!-- SOURCE_START: cognite-sdk-js/guides\files.md -->
## File: cognite-sdk-js/guides\files.md

# Files

<!--What are Files?  Generic overview information-->

In Cognite Data Fusion, the [file](https://docs.cognite.com/dev/concepts/resource_types/files) **resource type** stores **files** and **documents** that are related to one or more assets. For example, a file can contain a piping and instrumentation diagram (P&ID) that shows how multiple assets are connected.

:::info NOTE
**Note** A note to make is, all steps below you will need to be authenticated with [OIDC](./authentication.md#openid-connect-oidc).
:::

**In this article:**

- [Aggregate files](#aggregate-files)
- [Delete files](#delete-files)
- [List files](#list-files)
- [Retrieve files by id](#retrieve-files-by-id)
- [Search for file](#search-for-files)
- [Update files](#update-files)
- [Upload files](#upload-files)

## Aggregate files

Aggregating files.

**_Parameters_**

| Properties                                                                                                        | Definition                                 |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------ |
| **query** ([FileAggregateQuery](https://cognitedata.github.io/cognite-sdk-js/interfaces/fileaggregatequery.html)) | Query schema for files aggregate endpoint. |

**_Returns_**

| Return                   | Type                                                                                                |
| ------------------------ | --------------------------------------------------------------------------------------------------- |
| List of aggregated files | Promise<[FileAggregate](https://cognitedata.github.io/cognite-sdk-js/globals.html#fileaggregate)[]> |

**_Examples_**

Aggregating files:

```ts
const filters: FileAggregateQuery = { filter: { uploaded: true } };

const aggregates: Promise<FileAggregate[]> = await client.files.aggregate(
  filters
);
```

## Delete files

Deleting files.

**_Parameters_**

| Properties                                                                                 | Definition                     |
| ------------------------------------------------------------------------------------------ | ------------------------------ |
| **ids** ([IdEither](https://cognitedata.github.io/cognite-sdk-js/globals.html#ideither)[]) | InternalId or ExternalId array |

**_Returns_**

| Return        | Type            |
| ------------- | --------------- |
| Empty Promise | Promise<object> |

**_Examples_**

Deleting a single file by id:

```ts
const ids: IdEither[] = [{ id: 123 }];

await client.files.delete(ids);
```

Deleting multiple files by id:

```ts
const ids: IdEither[] = [{ id: 123 }, { id: 456 }];

await client.files.delete(ids);
```

Deleting a single file by externalId:

```ts
const ids: IdEither[] = [{ externalId: 'abc' }];

await client.files.delete(ids);
```

Deleting multiple files by externalId:

```ts
const ids: IdEither[] = [{ externalId: 'abc' }, { externalId: 'def' }];

await client.files.delete(ids);
```

## List files

Listing files.

**_Parameters_**

| Properties                                                                                                      | Definition                                          |
| --------------------------------------------------------------------------------------------------------------- | --------------------------------------------------- |
| **scope** ([FileRequestFilter](https://cognitedata.github.io/cognite-sdk-js/interfaces/filerequestfilter.html)) | (_Optional_). Query cursor, limit and some filters. |

**_Returns_**

| Return                  | Type                                                                                                                                                                                          |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| List of requested files | [CursorAndAsyncIterator](https://cognitedata.github.io/cognite-sdk-js/globals.html#cursorandasynciterator)<[FileInfo](https://cognitedata.github.io/cognite-sdk-js/interfaces/fileinfo.html)> |

**_Examples_**

List files with filters:

```ts
const filters: FileRequestFilter = { filter: { mimeType: 'image/png' } };

const sequences: CursorAndAsyncIterator<FileInfo> = await client.files.list(
  filters
);
```

## Retrieve files by id

Retrieve a single or multiple sequences by external id.

**_Parameters_**

| Properties                                                                                                      | Definition                                                    |
| --------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------- |
| **ids** ([IdEither[]](https://cognitedata.github.io/cognite-sdk-js/globals.html#ideither))                      | InternalId or ExternalId array.                               |
| **params** ([FileRetrieveParams](https://cognitedata.github.io/cognite-sdk-js/globals.html#fileretrieveparams)) | _(Optional)_. Ignore IDs and external IDs that are not found. |

**_Returns_**

| Return                  | Type                                                                                         |
| ----------------------- | -------------------------------------------------------------------------------------------- |
| List of requested files | Promise<[FileInfo](https://cognitedata.github.io/cognite-sdk-js/interfaces/fileinfo.html)[]> |

**_Examples_**

Get a single file by external id:

```ts
const externalId: IdEither[] = [{ externalId: 'abc' }];

const retrievedFiles: Promise<FileInfo[]> = await client.files.retrieve(
  externalId
);
```

Get a single file by id:

```ts
const id: IdEither[] = [{ id: 123 }];

const retrievedFiles: Promise<FileInfo[]> = await client.files.retrieve(id);
```

Get multiple files by external id:

```ts
const externalIds: IdEither[] = [
  { externalId: 'abc' },
  { externalId: 'def' },
  { externalId: 'ghi' },
];

const retrievedFiles: Promise<FileInfo[]> = await client.files.retrieve(
  externalIds
);
```

Get multiple files by id:

```ts
const ids: IdEither[] = [{ id: 123 }, { id: 456 }, { id: 789 }];

const retrievedFiles: Promise<FileInfo[]> = await client.files.retrieve(ids);
```

## Search for files

Searching for files.

**_Parameters_**

| Properties                                                                                                      | Definition |
| --------------------------------------------------------------------------------------------------------------- | ---------- |
| **query** ([FilesSearchFilter](https://cognitedata.github.io/cognite-sdk-js/interfaces/filessearchfilter.html)) | Filters.   |

**_Returns_**

| Return                 | Type                                                                                         |
| ---------------------- | -------------------------------------------------------------------------------------------- |
| List of searched files | Promise<[FileInfo](https://cognitedata.github.io/cognite-sdk-js/interfaces/fileinfo.html)[]> |

**_Examples_**

Search for files:

```ts
const filters: FilesSearchFilter = [
  {
    filter: { mimeType: 'image/jpeg' },
    search: { name: 'Pump' },
  },
];

const sequences: Promise<FileInfo[]> = await client.files.search(filters);
```

## Update files

Updating files.

**_Parameters_**

| Properties                                                                                                     | Definition  |
| -------------------------------------------------------------------------------------------------------------- | ----------- |
| **changes** ([FileChangeUpdate](https://cognitedata.github.io/cognite-sdk-js/globals.html#filechangeupdate)[]) | Files data. |

**_Returns_**

| Return                | Type                                                                                         |
| --------------------- | -------------------------------------------------------------------------------------------- |
| List of updated files | Promise<[FileInfo](https://cognitedata.github.io/cognite-sdk-js/interfaces/fileinfo.html)[]> |

**_Examples_**

Updating file by id:

```ts
const files: FileChangeUpdate[] = [
  { id: 123, update: { source: { set: 'New source' } } },
];

const [updatedFile]: Promise<FileInfo[]> = await client.files.update(sequence);
```

Updating multiple files by id:

```ts
const files: FileChangeUpdate[] = [
  { id: 123, update: { source: { set: 'New source' } } },
  { id: 456, update: { source: { set: 'New source two' } } },
];

const [updatedFile]: Promise<FileInfo[]> = await client.files.update(sequence);
```

Updating files by externalId:

```ts
const files: FileChangeUpdate[] = [
  { externalId: 'abc', update: { source: { set: 'New source' } } },
];

const [updatedFile]: Promise<FileInfo[]> = await client.files.update(sequence);
```

Updating multiple files by externalId:

```ts
const files: FileChangeUpdate[] = [
  { externalId: 'abc', update: { source: { set: 'New source' } } },
  { externalId: 'def', update: { source: { set: 'New source two' } } },
];

const [updatedFile]: Promise<FileInfo[]> = await client.files.update(sequence);
```

## Upload files

Uploading files.

**_Parameters_**

| Properties                                                                                                       | Definition                  |
| ---------------------------------------------------------------------------------------------------------------- | --------------------------- |
| **fileInfo** ([ExternalFileInfo](https://cognitedata.github.io/cognite-sdk-js/interfaces/externalfileinfo.html)) | File information.           |
| **fileContent** ([FileContent](https://cognitedata.github.io/cognite-sdk-js/globals.html#filecontent))           | (_Optional_). File content. |
| **overwrite** (boolean = false)                                                                                  | (_Optional_).               |
| **waitUntilAcknowledged** (boolean = false)                                                                      | (_Optional_).               |

**_Returns_**

| Return                 | Type                                                                                                                                                                                               |
| ---------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Uploaded file response | Promise<[FileUploadResponse](https://cognitedata.github.io/cognite-sdk-js/interfaces/fileuploadresponse.html) / [FileInfo](https://cognitedata.github.io/cognite-sdk-js/interfaces/fileinfo.html)> |

**_Examples_**

Uploading files with fileContent:

```ts
const fileContent: FileContent = 'file data here'; // can also be of type ArrayBuffer, Buffer, Blob, File or any

const file: Promise<FileUploadResponse | FileInfo> = await client.files.upload(
  { name: 'examplefile.jpg', mimeType: 'image/jpeg' },
  fileContent
);
```

Uploading files with manually:

```ts
const file: Promise<FileUploadResponse | FileInfo> = await client.files.upload({
  name: 'examplefile.jpg',
  mimeType: 'image/jpeg',
});
// then upload using the file.uploadUrl
```


<!-- SOURCE_END: cognite-sdk-js/guides\files.md -->

================================================================================


<!-- SOURCE_START: cognite-sdk-js/guides\MIGRATION_GUIDE_1xx_2xx.md -->
## File: cognite-sdk-js/guides\MIGRATION_GUIDE_1xx_2xx.md

# Migration guide from 1.x.x to 2.x.x

Short migration guide on how to upgrade from SDK version 1.x.x to 2.x.x.

## Constructor

In 2.x.x you need to create a new SDK instance before you can use the SDK:

```js
const client = new CogniteClient({ appId: 'YOUR APPLICATION NAME' });
```

After that you need to authenticate your client with calling one of these methods on the client:

With api-key:

```js
client.loginWithApiKey({ project, apiKey });
```

With OAuth:

```js
client.loginWithOAuth({
  project: 'publicdata',
});
```

For more info about OAuth-authentication look [here](./authentication.md).

For quickstarts and samples go [here](../samples/).

## Changes

| 1.x.x                                                            | 2.x.x                                                                                             |
| ---------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- |
| **AUTHENTICATION**                                               |
| `sdk.Login.authorize(...)`                                       | See [constructor-section](#constructor)                                                           |
| `sdk.Login.loginWithApiKey(...)`                                 | See [constructor-section](#constructor)                                                           |
| `sdk.Logout.logout()`                                            | Removed                                                                                           |
| `sdk.Logout.retrieveLogoutUrl(...)`                              | Removed                                                                                           |
| **ASSETS**                                                       |
| `sdk.Assets.create([...])`                                       | `client.assets.create([...])`                                                                     |
| `sdk.Assets.retrieve(123)`                                       | `client.assets.retrieve([{id: 123}])`                                                             |
| `sdk.Assets.retrieveMultiple([123, 456])`                        | `client.assets.retrieve([{id: 123}, {id: 456}])`                                                  |
| `sdk.Assets.update(id, changes)`                                 | `client.assets.update([{id, update: changes}])`                                                   |
| `sdk.Assets.updateMultiple([{id, ...changes}])`                  | `client.assets.update([{id, update: changes}])`                                                   |
| `sdk.Assets.overwriteMultiple(...)`                              | Removed                                                                                           |
| `sdk.Assets.delete([id])`                                        | `client.assets.delete([{id: 123}])`                                                               |
| `sdk.Assets.list({name: 'abc'})`                                 | `client.assets.list({filter: {name: 'abc'}}).autoPagingToArray({limit: 1000})`                    |
| `sdk.Assets.listDescendants(id, params)`                         | Not implemented yet. Follow issue [here](https://github.com/cognitedata/cognite-sdk-js/issues/129) |
| `sdk.Assets.search({name: 'abc'})`                               | `client.assets.search({search: {name: 'abc'})`                                                    |
| **TIME SERIES**                                                  |
| `sdk.TimeSeries.create([...])`                                   | `client.timeseries.create([...])`                                                                 |
| `sdk.TimeSeries.retrieve(123)`                                   | `client.timeseries.retrieve([{id: 123}])`                                                         |
| `sdk.TimeSeries.retrieveMultiple([123, 456])`                    | `client.timeseries.retrieve([{id: 123}, {id: 456}])`                                              |
| `sdk.TimeSeries.update(id, changes)`                             | `client.timeseries.update([{id, update: changes}])`                                               |
| `sdk.TimeSeries.updateMultiple([{id, ...changes}])`              | `client.timeseries.update([{id, update: changes}])`                                               |
| `sdk.TimeSeries.list({limit: 20})`                               | `client.timeseries.list().autoPagingToArray({limit: 20})`                                         |
| `sdk.TimeSeries.search({name: 'abc'})`                           | `client.timeseries.search({search: {name: 'abc'}})`                                              |
| **DATA POINTS**                                                  |
| `sdk.Datapoints.insert(id, datapoints)`                          | `client.datapoints.insert([{id, datapoints}])`                                                    |
| `sdk.Datapoints.insertByName(...)`                               | Removed                                                                                           |
| `sdk.Datapoints.insertMultiple([{name, datapoints}])`            | Not supported using name. Use IDs instead: `client.datapoints.insert([{id, datapoints}])`         |
| `sdk.Datapoints.retrieve(id, params)`                            | `client.datapoints.retrieve({items: [{id}], ...params})`                                          |
| `sdk.Datapoints.retrieveByName(...)`                             | Removed (use ID instead)                                                                          |
| `sdk.Datapoints.retrieveMultiple(...)`                           | Not directly supported. Use `client.datapoints.retrieve`                                          |
| `sdk.Datapoints.retrieveLatest(name, before)`                    | Use IDs: `client.datapoints.retrieveLatest([{id, before}])`                                       |
| `client.datapoints.retrieveCSV(...)`                             | Removed                                                                                           |
| `sdk.Datapoints.delete(name, timestamp)`                         | Use IDs: `client.datapoints.delete([{id, inclusiveBegin: timestamp, exclusiveEnd: timestamp}])`   |
| `sdk.Datapoints.deleteRange(name, inclusiveBegin, exclusiveEnd)` | Use IDs: `client.datapoints.delete([{id, inclusiveBegin, exclusiveEnd}])`                         |
| **EVENTS**                                                       |
| `sdk.Events.create([...])`                                       | `client.events.create([...])`                                                                     |
| `sdk.Events.retrieve(id)`                                        | `client.events.retrieve([{id}])`                                                                  |
| `sdk.Events.retrieveMultiple([123, 456])`                        | `client.events.retrieve([{id: 123}, {id: 456}])`                                                  |
| `sdk.Events.update([{id, ...changes}])`                          | `client.events.update([{id, update: changes}])`                                                   |
| `sdk.Events.delete([id])`                                        | `client.events.delete([{id}])`                                                                    |
| `sdk.Events.list({limit: 20})`                                   | `client.events.list().autoPagingToArray({limit: 20})`                                             |
| `sdk.Events.search({name: 'abc'})`                               | `client.events.search({search: {name: 'abc'}})`                                                 |
| **FILES**                                                        |
| `sdk.Files.upload(fileMetadata, params)`                         | `client.files.upload(fileMetadata, fileContent?, overwrite?, waitUntilAcknowledged?)`             |
| `sdk.Files.download(id)`                                         | `client.files.getDownloadUrls([{id}])`                                                            |
| `sdk.Files.retrieveMetadata(id)`                                 | `client.files.retrieve([{id}])`                                                                   |
| `sdk.Files.retrieveMultipleMetadata([123, 456])`                 | `client.files.retrieve([{id: 123}, {id: 456}])`                                                   |
| `sdk.Files.updateMetadata(id, changes)`                          | `client.files.update([{id, update: changes}])`                                                    |
| `sdk.Files.updateMultipleMetadata([id, ...changes])`             | `client.files.update([{id, update: changes}])`                                                    |
| `sdk.Files.delete([id])`                                         | `client.files.delete([{id}])`                                                                     |
| `sdk.Files.list({limit: 20})`                                    | `client.files.list().autoPagingToArray({limit: 20})`                                              |
| `sdk.Files.search({name: 'abc'})`                                | `client.files.search({search: {name: 'abc'}})`                                                  |
| `sdk.Files.replaceMetadata(...)`                                 | Removed                                                                                           |
| **3D**                                                           |
| `sdk.ThreeD.createAssetMappings(modelId, revisionId, mappings)`  | `client.assetMappings3D.create(modelId, revisionId, mappings)`                                    |
| `sdk.ThreeD.createModels([name])`                                | `client.models3D.create([{name}])`                                                                |
| `sdk.ThreeD.createRevisions(modelId, revisions)`                 | `client.revisions3D.create(modelId, revisions)`                                                   |
| `sdk.ThreeD.retrieveFile(fileId, responseType)`                  | `client.files3D.retrieve(fileId)`                                                                 |
| `sdk.ThreeD.retrieveModel(modelId)`                              | `client.models3D.retrieve(modelId)`                                                               |
| `sdk.ThreeD.retrieveRevision(modelId, revisionId)`               | `client.models3D.retrieve(modelId, revisionId)`                                                   |
| `sdk.ThreeD.updateModels({id, name})`                            | `client.models3D.update([{id, update: {name}])`                                                   |
| `sdk.ThreeD.updateRevisions(modelId, {id, ...changes})`          | `client.revisions3D.update(modelId, [{id, update: changes])`                                      |
| `sdk.ThreeD.updateRevisionThumbnail(...)`                        | `client.revisions3D.updateThumbnail(modelId, revisionId, fileId)` |
| `sdk.ThreeD.deleteModels([id])`                                  | `client.models3D.delete([{id}])`                                                                  |
| `sdk.ThreeD.deleteRevisions(modelId, [id])`                      | `client.revisions3D.delete(modelId, [{id}])`                                                      |
| `sdk.ThreeD.deleteAssetMappings(modelId, revisionId, mappings)`  | `client.assetMappings3D.delete(modelId, revisionId, mappings)`                                    |
| `sdk.ThreeD.listAssetMappings(modelId, revisionId, params)`      | `client.assetMappings3D.list(modelId, revisionId, params).autoPagingToArray()`                    |
| `sdk.ThreeD.listModels(params)`                                  | `client.models3D.list(params).autoPagingToArray()`                                                |
| `sdk.ThreeD.listNodes(modelId, revisionId, params)`              | `client.revisions3D.list3DNodes(modelId, revisionId, params).autoPagingToArray()`                 |
| `sdk.ThreeD.listNodeAncestors(modelId, revisionId, params)`      | `client.revisions3D.list3DNodeAncestors(modelId, revisionId, nodeId, params).autoPagingToArray()` |
| `sdk.ThreeD.listRevisions(modelId, params)`                      | `client.revisions3D.list(modelId, params).autoPagingToArray()`                                    |
| `sdk.ThreeD.listSectors(modelId, revisionId, params)`            | `client.viewer3D.listRevealSectors3D(modelId, revisionId, params).autoPagingToArray()`            |


<!-- SOURCE_END: cognite-sdk-js/guides\MIGRATION_GUIDE_1xx_2xx.md -->

================================================================================


<!-- SOURCE_START: cognite-sdk-js/guides\MIGRATION_GUIDE_2xx_3xx.md -->
## File: cognite-sdk-js/guides\MIGRATION_GUIDE_2xx_3xx.md

# Migration guide from 2.x.x to 3.x.x

Short migration guide on how to upgrade from SDK version 2.x.x to 3.x.x.

## Renamed interfaces

Some typescript interfaces has been renamed to comply the consistency between different resource types.
Among the most used ones:
- GetTimeSeriesMetadataDTO -> Timeseries
- PostTimeSeriesMetadataDTO -> ExternalTimeseries
- DatapointsGetAggregateDatapoint -> DatapointsAggregates
- FilesMetadata -> FileInfo
More comprehensive list can be found in [changelog](../packages/stable/CHANGELOG.md)

## Events aggregate endpoints moved to a sub-api 

- `client.events.aggregate(...)` -> `client.events.aggregate.count()`
- `client.events.uniqueValuesAggregate(...)` -> `client.events.aggregate.uniqueValues(...)`

## Timestamp-to-Date auto-conversion

The JavaScript SDK automatically converts response timestamp values to date objects. This caused a [bug](https://github.com/cognitedata/cognite-sdk-js/issues/333) in the Raw API. The fix can introduce a breaking change if you use the Raw API, and possibly in other places.

## Helper classes removed

AssetClass, TimeSeriesClass, TimeSeriesList and AssetList containing some helper methods has been removed.

Most of these helpers can be easily re-implemented using other SDK calls.

Some examples:

**Asset.delete()**
```ts
public async delete(options: DeleteOptions = {}) {
  return this.client.assets.delete(
    [{ id: this.id }],
    options
  );
}
```

**Asset.subtree()**
```ts
public async subtree() {
  const { items } = await client.assets.list({
    filter: {
      assetSubtreeIds: [{ id: this.id }],
    }
  });
}
```

**TimeSeriesList.getAllDatapoints(...)**
```ts
public getAllDatapoints = async (options: DatapointsMultiQueryBase = {}) => {
  const tsIds = this.map(({ id }) => ({ id }));
  return this.client.datapoints.retrieve({
    items: tsIds,
    ...options,
  });
};
```

## Removed client.assets.retrieveSubtree(...) 

Copies the functionality of `Asset.subtree()` (read above)

## Change filter passing to client.timeseries.list

The function previously took a `TimeseriesFilter` as input, but now takes a `TimeseriesFilterQuery` which
corresponds closely to the [API spec](https://docs.cognite.com/api/v1/#operation/listTimeSeries).
All filter related fields should now be in the `filter` object.

### Example:
```ts
// Old way
const timeseries = await client.timeseries.list({ assetIds: [1, 2], unit: "liter", limit: 100});

//New way
const timeseries = await client.timeseries.list({ filter: { assetIds: [1, 2], unit: "liter" }, limit: 100});
```


<!-- SOURCE_END: cognite-sdk-js/guides\MIGRATION_GUIDE_2xx_3xx.md -->

================================================================================


<!-- SOURCE_START: cognite-sdk-js/guides\MIGRATION_GUIDE_9xx_10xx.md -->
## File: cognite-sdk-js/guides\MIGRATION_GUIDE_9xx_10xx.md

# Migration guide: Upgrading from 9.x.x to 10.x.x

This guide provides instructions on migrating from SDK version 9.x.x to 10.x.x. While this major version upgrade includes breaking changes, most users should experience minimal disruption.

## CogniteClient Constructor Changes

The `getToken` property in the CogniteClient constructor has been renamed to `oidcTokenProvider`. Update your code accordingly:
```ts
// Before:
const client = new CogniteClient({ getToken: () => {}, ...})

// After:
const client = new CogniteClient({ oidcTokenProvider: () => {}, ...})
```
Using `getToken` is still supported but will trigger a console warning. [PR #1153](https://github.com/cognitedata/cognite-sdk-js/pull/1153)


## Breaking Changes

- **Deprecated API-key feature removed:** API-key authentication has been removed since the backend no longer supports it. [PR #1148](https://github.com/cognitedata/cognite-sdk-js/pull/1148)
- **Legacy authentication removed:** Removed support for deprecated authentication mechanisms. [PR #1150](https://github.com/cognitedata/cognite-sdk-js/pull/1150)
- **ADFS 2016 authentication removed:** No longer supported. [PR #1150](https://github.com/cognitedata/cognite-sdk-js/pull/1150)
- **Deprecated service account feature removed:** Note that this does not affect the upcoming new service account feature. [PR #1151](https://github.com/cognitedata/cognite-sdk-js/pull/1151)
- **Upgraded to TypeScript 5:** Ensure your code is compatible with TypeScript 5. [PR #1135](https://github.com/cognitedata/cognite-sdk-js/pull/1135)
- **Removed `update` and `updateProject` APIs from the Projects API:** These deprecated methods have been removed. [PR #1152](https://github.com/cognitedata/cognite-sdk-js/pull/1152)
- **Made `treeIndex` and `subtreeSize` optional in AssetMapping3D:** These fields are now optional. [PR #1029](https://github.com/cognitedata/cognite-sdk-js/pull/1029)
- **Removed deprecated Playground package:** The API is no longer supported. [PR #1218](https://github.com/cognitedata/cognite-sdk-js/pull/1218)
- **Removed noAuthMode option**: The same behavior can be achieved with a dummy `oidcTokenProvider` function. [PR #1150](https://github.com/cognitedata/cognite-sdk-js/pull/1150)
- **Cleaned up Annotation types**: Removed long prefix from annotation types. [PR #1248](https://github.com/cognitedata/cognite-sdk-js/pull/1248)
  - AnnotationsCogniteAnnotationTypesDiagramsAssetLink -> AnnotationsTypesDiagramsAssetLink
  - AnnotationsCogniteAnnotationTypesDiagramsInstanceLink -> AnnotationsTypesDiagramsInstanceLink
  - AnnotationsCogniteAnnotationTypesImagesInstanceLink -> AnnotationsTypesImagesInstanceLink
  - AnnotationsCogniteAnnotationTypesPrimitivesGeometry3DGeometry -> AnnotationsTypesPrimitivesGeometry3DGeometry
  - AnnotationsCogniteAnnotationTypesPrimitivesGeometry2DGeometry -> AnnotationsTypesPrimitivesGeometry2DGeometry
  - AnnotationsCogniteAnnotationTypesImagesAssetLink -> AnnotationsTypesImagesAssetLink


## New features:

- **Instance ID support added to Timeseries API** [PR #1165](https://github.com/cognitedata/cognite-sdk-js/pull/1165)
- **Instance ID support added to Files API** [PR #1166](https://github.com/cognitedata/cognite-sdk-js/pull/1166)
- **Asset instance (Core Data Modeling) support for 3D asset mappings:** Enhances 3D asset integration. [PR #1189](https://github.com/cognitedata/cognite-sdk-js/pull/1189)
- **CommonJS support added:** The SDK now ships both CommonJS and ES modules. [PR #1187](https://github.com/cognitedata/cognite-sdk-js/pull/1187), [PR #1135](https://github.com/cognitedata/cognite-sdk-js/pull/1135)
- **Exporting Data Modeling-related types:** Improves type availability for developers. [PR #1208](https://github.com/cognitedata/cognite-sdk-js/pull/1208)
- **Updated types for Vision and Annotations APIs:** Enhancements to API typings. [PR #1164](https://github.com/cognitedata/cognite-sdk-js/pull/1164)


## Documentation Updates

- **Cleaned up outdated samples:** Removed obsolete examples. [PR #1154](https://github.com/cognitedata/cognite-sdk-js/pull/1154)
- **Added Typedoc to core classes and improved contribution documentation:** Enhancements to developer resources. [PR #1156](https://github.com/cognitedata/cognite-sdk-js/pull/1156)

By following these updates, you can smoothly transition to SDK version 10.x.x while taking advantage of the latest features and improvements.


<!-- SOURCE_END: cognite-sdk-js/guides\MIGRATION_GUIDE_9xx_10xx.md -->

================================================================================


<!-- SOURCE_START: cognite-sdk-js/guides\MIGRATION_GUIDE_v6.md -->
## File: cognite-sdk-js/guides\MIGRATION_GUIDE_v6.md

# Version 6 breaking changes

## New authentication API

* New mandatory `getToken` argument to `CogniteClient` constructor

## General breaking changes

* `project` is changed from optional to mandatory constructor argument for `CogniteClient` and
  `setProject` is removed. Create a new SDK instance if you need to change the project.
* `setBaseUrl` removed, specify base url through constructor and make a new instance if you need a
  different base url.
* `CogniteClient#setOneTimeSdkHeader` removed


<!-- SOURCE_END: cognite-sdk-js/guides\MIGRATION_GUIDE_v6.md -->

================================================================================


<!-- SOURCE_START: cognite-sdk-js/guides\monorepo.md -->
## File: cognite-sdk-js/guides\monorepo.md

# Monorepo
The sdk is implemented with several npm packages, all contained in the `packages/` folder.
We use `lerna` to run commands in all repos, and also to configure versions.
This document goes into details and hacks needed to develop the repo, and is not needed by end users.

## Structure
 - `packages/stable/` - holds `@cognite/sdk`, the stable SDK
 - `packages/core/` - holds `@cognite/sdk-core`, core functionality used by the SDK
 - `packages/beta/` - holds `@cognite/sdk-beta`, the beta SDK
 - `samples/` - holds several folders with sample use of the SDK

## Development
All dependencies can be installed from the repository root:
```
yarn
```
To `build`, `clean`, `test`, `lint` or `lint:fix`, run the command like so:
```
$ yarn <command>
```
If in the root folder, the operation is run in all packages (excluding samples), in topological order.
Remember to build all packages before testing, and the environment variables needed to run certain tests.

## Cross references
`@cognite/sdk` is dependent on a specific version of `@cognite/sdk-core`,
which matches the version number in `core/package.json`.
When building, you should always build all packages at once using `yarn build` in the root folder.
When changes are merged, `lerna` updates the versions and makes sure the packages still reference
each other exactly.

## Typescript
Each package has both `tsconfig.json` and `tsconfig.build.json`.
The difference is that `tsconfig.json` uses path aliases to reference the source folders of other packages.
This allows IDEs to visit source code across packages when going to definition.
Otherwise, Ctrl-clicking types from another package would open `dist/**/*.d.ts` files.

The `tsconfig.build.json` files don't do any path aliasing, so when building or linting
we use `dist/index.js` and `dist/src/**/*.d.ts` from the other packages.
To make sure all `dist/` folders are up to date, build all packages
by running `yarn build` in the root of the project.

To avoid duplicating configuration, there is a root `tsconfig.common.json` with most configuration,
a root `tsconfig.build.json` for build-specific configuration,
and a root `tsconfig.json` with the path aliasing.
The latter two extend the first.

Package specific configuration is mostly specifying output folder, and extending the corresponding
file in the project root. You only need to specify output folder in build configuration,
since your IDE will reference by source through path aliases.

Tests are written in typescript, but are explicitly excluded in `tsconfig.build.json`.
This means test are not compiled with the build, but by `vitest` when tests are run.
To make the IDE experience better, tests are not excluded in `tsconfig.json`.
This means you can still use Go To Definition from test into source, and even into other packages.
When running tests, however, packages' `dist/index.js` files are used, so run `yarn build` first.

## Versioning and releasing
See [Contributing](../CONTRIBUTING.md).

### About Lerna version
Our setup has lerna individually update versions of packages based on the messages of commits affecting each package.
It will also document the changes to each package's `CHANGELOG.md`.
You can make the changelog clearer by specifying scope in commit messages, like `feat(assets):`

If no changes are made in a package, or the commits only affect files ignored by `lerna version` (see lerna.json),
the package will not be updated.

 - All commits, even `docs:` or `test:` will cause a PATCH bump if they change files not ignored by lerna.
 - Commits that start with `fix:` will bump PATCH, and will be displayed in the changelog under `Bug Fixes`.
 - Commits that start with `feat:` will cause a bump to the MINOR version, and show as `Features`
 - Commits with `feat!:`, `fix!:` etc. or with a `BREAKING CHANGE:` footer will cause a MAJOR version bump.

**Note:** The `BREAKING CHANGE:` footer is parsed very precisely, you are encouraged to use `!` in the header. 

If multiple commits are made, the most significant bump is made.
References between packages are also updated, so even if a package has no commits,
its PATCH can still be bumped to reference the latest versions of bumped dependecies.

After bumping versions, a commit is made with updates to `package.json`-files and `CHANGELOG.md` files.

## Adding new packages
To add a new package, copy whatever is needed from another package, and give it a unique name in `package.json`.
If it's a sample in the sample folder, make sure its package name ends in `-sample` and that the package is private.
Lerna will create any public packages on npm, so make sure the names are correct before committing.

**Note:** Scoped packages (e.g. `@cognite/sdk`) need special confirmation that they are public the first time they are published.
This is specified in the individual `package.json` files: `"publishConfig": { "access": "public" }`.

## Samples
The `samples/` folder contains several samples, each top level folder being a private package in the workspace.
Though tecnically in the workspace for ease of testing, they would also work as-is as stand alone dependants of the sdk.

To test the samples, first build the sdk.
```
$ yarn build
```

Then you can run the samples' tests.
```
$ yarn test-samples
```

## Documentation
Since each package gets its own npm page, they all have `README.md` files.
These are also used as front pages of typedoc pages.
This means all links must be absolute.


<!-- SOURCE_END: cognite-sdk-js/guides\monorepo.md -->

================================================================================


<!-- SOURCE_START: cognite-sdk-js/guides\relationships.md -->
## File: cognite-sdk-js/guides\relationships.md

# Relationships

<!--What are Relationships?  Generic overview information-->

The [**Relationships**](https://docs.cognite.com/dev/concepts/resource_types/relationships) resource type represents connections between resource objects in Cognite Data Fusion (CDF). Each relationship is between a source and a target object and is defined by a **relationship type** and the **external IDs** and **resource types** of the source and target objects. Optionally, a relationship can be time-constrained with a start and end time.

:::info NOTE
**Note** A note to make is, all steps below you will need to be authenticated with [OIDC](./authentication.md#openid-connect-oidc).
:::

**In this article:**

- [Retrieve a relationship by id](#retrieve-a-relationship-by-id)
- [Retrieve multiple relationships by id](#retrieve-multiple-relationships-by-id)
- [List relationships](#list-relationships)
- [Create relationship](#create-relationship)
- [Delete relationships](#delete-relationships)

## Retrieve a relationship by id

Retrieve a single relationship by external id.

**_Parameters_**

| Properties                                                                                                                        | Definition                                                   |
| --------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| **ids** ([ExternalId[]](https://cognitedata.github.io/cognite-sdk-js/interfaces/externalid.html))                                 | External ID's array.                                         |
| **params** ([RelationshipsRetrieveParams](https://cognitedata.github.io/cognite-sdk-js/globals.html#relationshipsretrieveparams)) | _(Optional)_. Ignore IDs and external IDs that are not found |

**_Returns_**

| Return                         | Type                                                                                                 |
| ------------------------------ | ---------------------------------------------------------------------------------------------------- |
| List of requested relationship | Promise<[Relationship[]](https://cognitedata.github.io/cognite-sdk-js/interfaces/relationship.html)> |

**_Examples_**

Get relationship by external id:

```ts
const externalIds = [{ externalId: 'relationship_1' }];

const createdRelationships = await client.relationships.retrieve(externalIds);
```

## Retrieve multiple relationships by id

Retrieve multiple relationships by external id.

**_Parameters_**

| Properties                                                                                                                        | Definition                                                   |
| --------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| **ids** ([ExternalId[]](https://cognitedata.github.io/cognite-sdk-js/interfaces/externalid.html))                                 | External ID's array.                                         |
| **params** ([RelationshipsRetrieveParams](https://cognitedata.github.io/cognite-sdk-js/globals.html#relationshipsretrieveparams)) | _(Optional)_. Ignore IDs and external IDs that are not found |

**_Returns_**

| Return                          | Type                                                                                                 |
| ------------------------------- | ---------------------------------------------------------------------------------------------------- |
| List of requested relationships | Promise<[Relationship[]](https://cognitedata.github.io/cognite-sdk-js/interfaces/relationship.html)> |

**_Examples_**

Get relationships by external id:

```ts
const externalIds = [
  { externalId: 'relationship_1' },
  { externalId: 'relationship_2' },
  { externalId: 'relationship_3' },
  { externalId: 'relationship_4' },
];

const createdRelationships = await client.relationships.retrieve(externalIds);
```

## List relationships

Lists relationships stored in the project based on a query filter given in the payload of this request. Up to 1000 relationships can be retrieved in one operation.

**_Parameters_**

| Properties                                                                                                                                    | Definition                                          |
| --------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------- |
| **query** ([RelationshipsFilterRequest](https://cognitedata.github.io/cognite-sdk-js/interfaces/relationshipsfilterrequest.html) / undefined) | _(Optional)_. Query cursor, limit and some filters. |

**_Returns_**

| Return                          | Type                                                                                                                                                                                                  |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| List of requested relationships | [CursorAndAsyncIterator](https://cognitedata.github.io/cognite-sdk-js/globals.html#cursorandasynciterator)<[Relationship](https://cognitedata.github.io/cognite-sdk-js/interfaces/relationship.html)> |

**_Examples_**

List relationships with filters:

```ts
const filters = {
  filter: {
    createdTime: {
      min: new Date('1 jan 2018'),
      max: new Date('1 jan 2019'),
    },
  },
};

const createdRelationships = await client.relationships.list(filters);
```

List relationships without filters:

```ts
const createdRelationships = await client.relationships.list();
```

## Create relationship

Create one or more relationships.

**_Parameters_**

| Properties                                                                                                            | Definition                                       |
| --------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------ |
| **items** ([ExternalRelationship](https://cognitedata.github.io/cognite-sdk-js/interfaces/externalrelationship.html)) | Relationship or list of relationships to create. |

**_Returns_**

| Return                        | Type                                                                                               |
| ----------------------------- | -------------------------------------------------------------------------------------------------- |
| List of created relationships | Promise<[Relationship](https://cognitedata.github.io/cognite-sdk-js/interfaces/relationship.html)> |

**_Examples_**

Create a relationship between two assets:

```ts
const relationships = [
  {
    externalId: 'relationship_1',
    sourceExternalId: 'asset_1',
    sourceType: 'asset' as const,
    targetExternalId: 'asset_2',
    targetType: 'asset' as const,
  },
];

const createRelationships = await client.relationships.create(relationships);
```

Create a relationship between a file and an asset:

```ts
const relationships = [
  {
    externalId: 'relationship_1',
    sourceExternalId: 'file_1',
    sourceType: 'file' as const,
    targetExternalId: 'asset_1',
    targetType: 'asset' as const,
  },
];

const createRelationships = await client.relationships.create(relationships);
```

## Delete relationships

Delete one or more relationships.

**_Parameters_**

| Properties                                                                                                                    | Definition                                                   |
| ----------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| **ids** ([ExternalId[]](https://cognitedata.github.io/cognite-sdk-js/interfaces/externalid.html))                             | External ID's array.                                         |
| **params** ([RelationshipsDeleteParams](https://cognitedata.github.io/cognite-sdk-js/globals.html#relationshipsdeleteparams)) | _(Optional)_. Ignore IDs and external IDs that are not found |

**_Returns_**

| Return        | Type            |
| ------------- | --------------- |
| Empty promise | Promise<object> |

**_Examples_**

Delete one relationship:

```ts
await client.relationships.delete([{ externalId: 'abc' }]);
```

Delete more than one relationship:

```ts
await client.relationships.delete([
  { externalId: 'abc' },
  { externalId: 'def' },
]);
```


<!-- SOURCE_END: cognite-sdk-js/guides\relationships.md -->

================================================================================


<!-- SOURCE_START: cognite-sdk-js/guides\sequences.md -->
## File: cognite-sdk-js/guides\sequences.md

# Sequences

<!--What are Sequences?  Generic overview information-->

In Cognite Data Fusion, a [sequence](https://docs.cognite.com/dev/concepts/resource_types/sequences) is a generic **resource type** for indexing a series of **rows** by **row number**. Each **row** contains one or more **columns** with either string or numeric data. Examples of sequences are performance curves and various types of logs.

:::info NOTE
**Note** A note to make is, all steps below you will need to be authenticated with one of our methods, [legacy](./authentication.md#cdf-auth-flow) or [OIDC](./authentication.md#openid-connect-oidc)(*preferable*).
:::

**In this article:**

  - [Retrieve sequences by id](#retrieve-sequences-by-id)
  - [List sequences](#list-sequences)
  - [Aggregate sequences](#aggregate-sequences)
  - [Search for sequences](#search-for-sequences)
  - [Create a sequence](#create-a-sequence)
  - [Delete sequences](#delete-sequences)
  - [Update sequences](#update-sequences)
  - [Insert rows into a sequence](#insert-rows-into-a-sequence)
  - [Delete rows from a sequence](#delete-rows-from-a-sequence)
  - [Retrieve rows from a sequence](#retrieve-rows-from-a-sequence)

## Retrieve sequences by id

Retrieve a single or multiple sequences by external id.

***Parameters***

| Properties                                                                                                              | Definition                                                    |
| ----------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------- |
| **ids** ([IdEither[]](https://cognitedata.github.io/cognite-sdk-js/globals.html#ideither))                              | InternalId or ExternalId array.                               |
| **params** ([SequenceRetrieveParams](https://cognitedata.github.io/cognite-sdk-js/globals.html#sequenceretrieveparams)) | *(Optional)*. Ignore IDs and external IDs that are not found. |

***Returns***

| Return                      | Type                                                                                         |
| --------------------------- | -------------------------------------------------------------------------------------------- |
| List of requested sequences | Promise<[Sequence[]](https://cognitedata.github.io/cognite-sdk-js/interfaces/sequence.html)> |

***Examples***

Get a single sequence by external id:

```ts
const externalId: IdEither[] = [{ externalId: 'abc' }];

const retrievedSequence: Promise<Sequence[]> = await client.sequences.retrieve(externalId);
```

Get a single sequence by id:

```ts
const id: IdEither[] = [{ id: 123 }];

const retrievedSequence: Promise<Sequence[]> = await client.sequences.retrieve(id);
```

Get multiple sequences by external id:

```ts
const externalIds: IdEither[] = [
    { externalId: 'abc' },
    { externalId: 'def' },
    { externalId: 'ghi' },
];

const retrievedSequence: Promise<Sequence[]> = await client.sequences.retrieve(externalIds);
```

Get multiple sequences by id:

```ts
const ids: IdEither[] = [
    { id: 123 },
    { id: 456 },
    { id: 789 }
];

const retrievedSequence: Promise<Sequence[]> = await client.sequences.retrieve(ids);
```

## List sequences

List sequences.

***Parameters***

| Properties                                                                                                      | Definition                                          |
| --------------------------------------------------------------------------------------------------------------- | --------------------------------------------------- |
| **scope** ([SequenceListScope](https://cognitedata.github.io/cognite-sdk-js/interfaces/sequencelistscope.html)) | (*Optional*). Query cursor, limit and some filters. |

***Returns***

| Return                      | Type                                                                                                                                                                                            |
| --------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| List of requested sequences | [CursorAndAsyncIterator](https://cognitedata.github.io/cognite-sdk-js/globals.html#cursorandasynciterator)<[Sequence](https://cognitedata.github.io/cognite-sdk-js/interfaces/sequence.html)[]> |

***Examples***

List sequences with filters:

```ts
const filters: SequenceListScope = [{ filter: { name: 'sequence_name' } }];

const sequences: CursorAndAsyncIterator<Sequence[]> = await client.sequences.list(filters);
```

## Aggregate sequences

Aggregate sequences.

***Parameters***

| Properties                                                                                                | Definition |
| --------------------------------------------------------------------------------------------------------- | ---------- |
| **query** ([SequenceFilter](https://cognitedata.github.io/cognite-sdk-js/interfaces/sequencefilter.html)) | Filters.   |

***Returns***

| Return                      | Type                                                                                                        |
| --------------------------- | ----------------------------------------------------------------------------------------------------------- |
| List of sequence aggregates | Promise<[SequenceAggregate](https://cognitedata.github.io/cognite-sdk-js/globals.html#sequenceaggregate)[]> |

***Examples***

Aggregate sequences:

```ts
const filters: SequenceFilter = [{ filter: { name: "Well" } }];

const aggregates: Promise<SequenceAggregate[]> = await client.sequences.aggregate(filters);

console.log('Number of sequences named Well: ', aggregates[0].count)
```

## Search for sequences

Search sequences.

***Parameters***

| Properties                                                                                                            | Definition |
| --------------------------------------------------------------------------------------------------------------------- | ---------- |
| **query** ([SequenceSearchFilter](https://cognitedata.github.io/cognite-sdk-js/interfaces/sequencesearchfilter.html)) | Filters.   |

***Returns***

| Return                     | Type                                                                                         |
| -------------------------- | -------------------------------------------------------------------------------------------- |
| List of searched sequences | Promise<[Sequence](https://cognitedata.github.io/cognite-sdk-js/interfaces/sequence.html)[]> |

***Examples***

Search for a sequence:

```ts
const filters: SequenceSearchFilter = [
    {
        filter: { assetIds: [1, 2] },
        search: { query: 'n*m* desc*ion' }
    }
];

const sequences: Promise<Sequence[]>  = await client.sequences.search(filters);
```

## Create a sequence

Some description.

***Parameters***

| Properties                                                                                                      | Definition           |
| --------------------------------------------------------------------------------------------------------------- | -------------------- |
| **items** ([ExternalSequence](https://cognitedata.github.io/cognite-sdk-js/interfaces/externalsequence.html)[]) | Sequence properties. |

***Returns***

| Return                      | Type                                                                                         |
| --------------------------- | -------------------------------------------------------------------------------------------- |
| List of requested sequences | Promise<[Sequence](https://cognitedata.github.io/cognite-sdk-js/interfaces/sequence.html)[]> |

***Examples***

Creating a sequence:

```ts
const sequences: ExternalSequence[] = [
    {
        externalId: 'sequence_name',
        columns: [
            { externalId: 'one', valueType: SequenceValueType.LONG },
            { externalId: 'two' },
            { externalId: 'three', valueType: SequenceValueType.STRING }
        ]
    }
];

const [sequence]: Promise<Sequence[]> = await client.sequences.create(sequences);
```

Creating multiple sequences:

```ts
const sequences: ExternalSequence[] = [
    {
        externalId: 'sequence_name',
        columns: [
            { externalId: 'one', valueType: SequenceValueType.LONG },
            { externalId: 'two' },
            { externalId: 'three', valueType: SequenceValueType.STRING }
        ]
    },
    {
        externalId: 'sequence_name_two',
        columns: [
            { externalId: 'one_one', valueType: SequenceValueType.LONG },
            { externalId: 'two_two' },
            { externalId: 'three_three', valueType: SequenceValueType.STRING }
        ]
    }
];

const [sequence]: Promise<Sequence[]> = await client.sequences.create(sequences);
```

## Delete sequences

Delete sequences.

***Parameters***

| Properties                                                                                 | Definition                     |
| ------------------------------------------------------------------------------------------ | ------------------------------ |
| **ids** ([IdEither](https://cognitedata.github.io/cognite-sdk-js/globals.html#ideither)[]) | InternalId or ExternalId array |

***Returns***

| Return        | Type            |
| ------------- | --------------- |
| Empty Promise | Promise<object> |

***Examples***

Deleting a sequence by externalId:

```ts
const externalId: IdEither[] = [{ externalId: 'abc' }];

await client.sequences.delete(externalId);
```

Deleting multiple sequences by externalId:

```ts
const externalId: IdEither[] = [{ externalId: 'abc' }, { externalId: 'def' }];

await client.sequences.delete(externalId);
```

Deleting a sequence by id:

```ts
const ids: IdEither[] = [{ id: 123 }];

await client.sequences.delete(ids);
```

Deleting multiple sequences by id:

```ts
const ids: IdEither[] = [{ id: 123 }, { id: 456 }];

await client.sequences.delete(ids);
```

## Update sequences

Update sequences.

***Parameters***

| Properties                                                                                                 | Definition      |
| ---------------------------------------------------------------------------------------------------------- | --------------- |
| **changes** ([SequenceChange](https://cognitedata.github.io/cognite-sdk-js/globals.html#sequencechange)[]) | Sequences data. |

***Returns***

| Return                    | Type                                                                                         |
| ------------------------- | -------------------------------------------------------------------------------------------- |
| List of updated sequences | Promise<[Sequence](https://cognitedata.github.io/cognite-sdk-js/interfaces/sequence.html)[]> |

***Examples***

Updating sequence by id:

```ts
const sequence: SequenceChange[] = [{id: 123, update: {name: {set: 'New name'}}}];

const [updatedSequence]: Promise<Sequence[]> = await client.sequences.update(sequence);
```

Updating multiple sequences by id:

```ts
const sequence: SequenceChange[] = [
    {id: 123, update: {name: {set: 'New name'}}},
    {id: 456, update: {name: {set: 'New name two'}}}
];

const [updatedSequence]: Promise<Sequence[]> = await client.sequences.update(sequence);
```

Updating sequence by externalId:

```ts
const sequence: SequenceChange[] = [{externalId: 'abc', update: {name: {set: 'New name'}}}];

const [updatedSequence]: Promise<Sequence[]> = await client.sequences.update(sequence);
```

Updating multiple sequences by externalId:

```ts
const sequence: SequenceChange[] = [
    {externalId: 'abc', update: {name: {set: 'New name'}}},
    {externalId: 'def', update: {name: {set: 'New name two'}}}
];

const [updatedSequence]: Promise<Sequence[]> = await client.sequences.update(sequence);
```

## Insert rows into a sequence

Insert rows.

***Parameters***

| Properties                                                                                                       | Definition                       |
| ---------------------------------------------------------------------------------------------------------------- | -------------------------------- |
| **items** ([SequenceRowsInsert](https://cognitedata.github.io/cognite-sdk-js/globals.html#sequencerowsinsert)[]) | A request for datapoints stored. |

***Returns***

| Return        | Type            |
| ------------- | --------------- |
| Empty Promise | Promise<object> |

***Examples***

Inserting row with id:

```ts
const rows: SequenceRowsInsert[] = [
    {
        id: 123,
        rows: [
            { rowNumber: 0, values: [1, 2.2, 'three'] },
            { rowNumber: 1, values: [4, 5, 'six'] }
        ],
        columns: ['one', 'two', 'three'],
    }
];

await client.sequences.insertRows(rows);
```

Inserting multiple rows with id:

```ts
const rows: SequenceRowsInsert[] = [
    {
        id: 123,
        rows: [
            { rowNumber: 0, values: [1, 2.2, 'three'] },
            { rowNumber: 1, values: [4, 5, 'six'] }
        ],
        columns: ['one', 'two', 'three'],
    },
    {
        id: 456,
        rows: [
            { rowNumber: 0, values: [1, 2.2, 'three'] },
            { rowNumber: 1, values: [4, 5, 'six'] }
        ],
        columns: ['one', 'two', 'three'],
    }
];

await client.sequences.insertRows(rows);
```

Inserting row with externalId:

```ts
const rows: SequenceRowsInsert[] = [
    {
        externalId: 'abc',
        rows: [
            { rowNumber: 0, values: [1, 2.2, 'three'] },
            { rowNumber: 1, values: [4, 5, 'six'] }
        ],
        columns: ['one', 'two', 'three'],
    }
];

await client.sequences.insertRows(rows);
```

Inserting multiple rows with externalId:

```ts
const rows: SequenceRowsInsert[] = [
    {
        externalId: 'abc',
        rows: [
            { rowNumber: 0, values: [1, 2.2, 'three'] },
            { rowNumber: 1, values: [4, 5, 'six'] }
        ],
        columns: ['one', 'two', 'three'],
    },
    {
        externalId: 'def',
        rows: [
            { rowNumber: 0, values: [1, 2.2, 'three'] },
            { rowNumber: 1, values: [4, 5, 'six'] }
        ],
        columns: ['one', 'two', 'three'],
    }
];

await client.sequences.insertRows(rows);
```

## Delete rows from a sequence

Delete rows.

***Parameters***

| Properties                                                                                                       | Definition                      |
| ---------------------------------------------------------------------------------------------------------------- | ------------------------------- |
| **query** ([SequenceRowsDelete](https://cognitedata.github.io/cognite-sdk-js/globals.html#sequencerowsdelete)[]) | Rows to delete from a sequence. |

***Returns***

| Return        | Type            |
| ------------- | --------------- |
| Empty Promise | Promise<object> |

***Examples***

Delete a single row sequence by id.

```ts
const rows: SequenceRowsDelete[] = [{ id: 32423849, rows: [1, 2, 3] }];

await client.sequences.deleteRows(rows);
```

Delete a single row sequence by externalId.

```ts
const rows: SequenceRowsDelete[] = [{ externalId: 'abcdef', rows: [1, 2, 3] }];

await client.sequences.deleteRows(rows);
```

Delete multiple row sequences by id.

```ts
const rows: SequenceRowsDelete[] = [
    { id: 32423849, rows: [1, 2, 3] },
    { id: 37481923, rows: [1, 2, 3] }
];

await client.sequences.deleteRows(rows);
```

Delete multiples row sequences by externalId.

```ts
const rows: SequenceRowsDelete[] = [
    { externalId: 'abcdef', rows: [1, 2, 3] },
    { externalId: 'ghijkl', rows: [1, 2, 3] }
];

await client.sequences.deleteRows(rows);
```

## Retrieve rows from a sequence

Retrieve rows.

***Parameters***

| Properties                                                                                                         | Definition                       |
| ------------------------------------------------------------------------------------------------------------------ | -------------------------------- |
| **query** ([SequenceRowsRetrieve](https://cognitedata.github.io/cognite-sdk-js/globals.html#sequencerowsretrieve)) | A request for datapoints stored. |

***Returns***

| Return                          | Type                                                                                                                                                                                                |
| ------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| List of requested sequence rows | [CursorAndAsyncIterator](https://cognitedata.github.io/cognite-sdk-js/globals.html#cursorandasynciterator)<[SequenceRow](https://cognitedata.github.io/cognite-sdk-js/interfaces/sequencerow.html)> |

***Examples***

Retrieving a single row by id:

```ts
const rows: SequenceRowsRetrieve = { id: 123 };

const retrievedRows: SequenceRow[] = await client.sequences
    .retrieveRows(rows)
    .autoPagingToArray({ limit: 100 });
```

Retrieving a single row by externalId:

```ts
const rows: SequenceRowsRetrieve = { externalId: 'sequence1' };

const retrievedRows: SequenceRow[] = await client.sequences
    .retrieveRows(rows)
    .autoPagingToArray({ limit: 100 });
```


<!-- SOURCE_END: cognite-sdk-js/guides\sequences.md -->

================================================================================


<!-- SOURCE_START: cognite-sdk-js/packages\alpha\CHANGELOG.md -->
## File: cognite-sdk-js/packages\alpha\CHANGELOG.md

# Change Log

All notable changes to this project will be documented in this file.
See [Conventional Commits](https://conventionalcommits.org) for commit guidelines.

## [0.38.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.38.0...@cognite/sdk-alpha@0.38.1) (2025-11-27)

**Note:** Version bump only for package @cognite/sdk-alpha





# [0.38.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.37.0...@cognite/sdk-alpha@0.38.0) (2025-11-04)


### Features

* **simint:** add aggregation support for routines ([#1335](https://github.com/cognitedata/cognite-sdk-js/issues/1335)) ([5daabb8](https://github.com/cognitedata/cognite-sdk-js/commit/5daabb8a8684053eddf1db0f475e2983c89bc201))





# [0.37.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.36.5...@cognite/sdk-alpha@0.37.0) (2025-09-29)


### Features

* **simint:** add support for filtering models by prefix ([#1326](https://github.com/cognitedata/cognite-sdk-js/issues/1326)) ([83d282d](https://github.com/cognitedata/cognite-sdk-js/commit/83d282dfa210b5c329a8e7c18ad719973cf8e1e3))





## [0.36.5](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.36.4...@cognite/sdk-alpha@0.36.5) (2025-09-25)

**Note:** Version bump only for package @cognite/sdk-alpha





## [0.36.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.36.3...@cognite/sdk-alpha@0.36.4) (2025-09-22)

**Note:** Version bump only for package @cognite/sdk-alpha





## [0.36.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.36.2...@cognite/sdk-alpha@0.36.3) (2025-09-19)

**Note:** Version bump only for package @cognite/sdk-alpha





## [0.36.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.36.1...@cognite/sdk-alpha@0.36.2) (2025-09-17)

**Note:** Version bump only for package @cognite/sdk-alpha





## [0.36.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.36.0...@cognite/sdk-alpha@0.36.1) (2025-09-11)

**Note:** Version bump only for package @cognite/sdk-alpha





# [0.36.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.35.2...@cognite/sdk-alpha@0.36.0) (2025-02-03)


### Features

* release v10 [release] ([#1243](https://github.com/cognitedata/cognite-sdk-js/issues/1243)) ([b3d5595](https://github.com/cognitedata/cognite-sdk-js/commit/b3d55951bb2a302981fa67ce694d21749461386a)), closes [#1135](https://github.com/cognitedata/cognite-sdk-js/issues/1135) [#1144](https://github.com/cognitedata/cognite-sdk-js/issues/1144) [#1148](https://github.com/cognitedata/cognite-sdk-js/issues/1148) [#1150](https://github.com/cognitedata/cognite-sdk-js/issues/1150) [#1151](https://github.com/cognitedata/cognite-sdk-js/issues/1151) [#1152](https://github.com/cognitedata/cognite-sdk-js/issues/1152) [#1153](https://github.com/cognitedata/cognite-sdk-js/issues/1153) [#1029](https://github.com/cognitedata/cognite-sdk-js/issues/1029)


### BREAKING CHANGES

* es6 module (vs es5) and typescript 3 -> 5

* chore: release pre-release from the release-v10 branch

* test: skip flaky alerts test

* chore: trigger [release]

* chore(release): publish new package versions [skip ci]

 - @cognite/sdk-alpha@1.0.0-rc.0
 - @cognite/sdk-beta@6.0.0-rc.0
 - @cognite/sdk-core@5.0.0-rc.0
 - @cognite/sdk-playground@8.0.0-rc.0
 - @cognite/sdk@10.0.0-rc.0





## [0.35.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.35.1...@cognite/sdk-alpha@0.35.2) (2025-01-17)


### Bug Fixes

* **simint:** add simulation run example ([#1188](https://github.com/cognitedata/cognite-sdk-js/issues/1188)) ([a912037](https://github.com/cognitedata/cognite-sdk-js/commit/a912037c1c4afaddffef6d644decc4ec561b6def))





## [0.35.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.35.0...@cognite/sdk-alpha@0.35.1) (2024-12-18)


### Bug Fixes

* use correct type for aggregate query ([#1180](https://github.com/cognitedata/cognite-sdk-js/issues/1180)) ([f2e30c6](https://github.com/cognitedata/cognite-sdk-js/commit/f2e30c653324d9c991afa3c981494f33e4a42533))





# [0.35.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.34.1...@cognite/sdk-alpha@0.35.0) (2024-12-18)


### Features

* add aggregate to modelsApi ([#1179](https://github.com/cognitedata/cognite-sdk-js/issues/1179)) ([7173057](https://github.com/cognitedata/cognite-sdk-js/commit/7173057b76e4ec6a7dc66a226eea864ba08e89f8))





## [0.34.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.34.0...@cognite/sdk-alpha@0.34.1) (2024-12-12)

**Note:** Version bump only for package @cognite/sdk-alpha





# [0.34.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.33.0...@cognite/sdk-alpha@0.34.0) (2024-12-05)


### Features

* use enum for connector status ([#1175](https://github.com/cognitedata/cognite-sdk-js/issues/1175)) ([ad0d6d9](https://github.com/cognitedata/cognite-sdk-js/commit/ad0d6d9c3243cd1121d1c02f462e552107319fd4))





# [0.33.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.32.2...@cognite/sdk-alpha@0.33.0) (2024-11-21)


### Features

* **alpha:** multiple updates for simint APIs ([#1174](https://github.com/cognitedata/cognite-sdk-js/issues/1174)) ([96bddf1](https://github.com/cognitedata/cognite-sdk-js/commit/96bddf183eb46c3edb97be793edb8e8e8e5e857f))





## [0.32.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.32.1...@cognite/sdk-alpha@0.32.2) (2024-11-07)

**Note:** Version bump only for package @cognite/sdk-alpha





## [0.32.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.32.0...@cognite/sdk-alpha@0.32.1) (2024-10-07)

**Note:** Version bump only for package @cognite/sdk-alpha





# [0.32.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.31.3...@cognite/sdk-alpha@0.32.0) (2024-09-17)


### Features

* **simint:** add and remove properties from simulators API ([#1147](https://github.com/cognitedata/cognite-sdk-js/issues/1147)) ([4cd6467](https://github.com/cognitedata/cognite-sdk-js/commit/4cd6467658fde491c20f274fbd8fa6af55610c60))





## [0.31.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.31.1...@cognite/sdk-alpha@0.31.3) (2024-08-26)


### Bug Fixes

* dummy release to override a bad one ([#1139](https://github.com/cognitedata/cognite-sdk-js/issues/1139)) ([5b036da](https://github.com/cognitedata/cognite-sdk-js/commit/5b036dabd4630b45d51558ee7f95d951c7227137))





## [0.31.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.31.0...@cognite/sdk-alpha@0.31.1) (2024-08-26)

**Note:** Version bump only for package @cognite/sdk-alpha





# [0.31.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.30.1...@cognite/sdk-alpha@0.31.0) (2024-08-22)


### Features

* **simint:** add sort property to filter queries ([#1134](https://github.com/cognitedata/cognite-sdk-js/issues/1134)) ([b293eee](https://github.com/cognitedata/cognite-sdk-js/commit/b293eee3fdf1a931c9ed5ef555db488c3e7fc10c))





## [0.30.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.30.0...@cognite/sdk-alpha@0.30.1) (2024-08-20)

**Note:** Version bump only for package @cognite/sdk-alpha

# [0.30.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.29.0...@cognite/sdk-alpha@0.30.0) (2024-08-19)

### Features

- add log ID to simulator integration ([#1130](https://github.com/cognitedata/cognite-sdk-js/issues/1130)) ([defee1f](https://github.com/cognitedata/cognite-sdk-js/commit/defee1fd43d78906270bea84130267ad3f5f3862))

# [0.29.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.28.3...@cognite/sdk-alpha@0.29.0) (2024-08-06)

### Features

- remove valueType from input timeseries ([#1123](https://github.com/cognitedata/cognite-sdk-js/issues/1123)) ([cf1e5d1](https://github.com/cognitedata/cognite-sdk-js/commit/cf1e5d114278801cc7718c820fae98bb336ed197))

## [0.28.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.28.2...@cognite/sdk-alpha@0.28.3) (2024-06-20)

### Bug Fixes

- type for simulator routine input values ([#1115](https://github.com/cognitedata/cognite-sdk-js/issues/1115)) ([dc5c64d](https://github.com/cognitedata/cognite-sdk-js/commit/dc5c64d945d61da806cf1d235325c03507d8772f))

## [0.28.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.28.1...@cognite/sdk-alpha@0.28.2) (2024-06-18)

### Bug Fixes

- **simint:** fix tests and update filter for routines and models ([#1114](https://github.com/cognitedata/cognite-sdk-js/issues/1114)) ([4c49ce2](https://github.com/cognitedata/cognite-sdk-js/commit/4c49ce2118f150558a9699e23f159a6a382ece14))

## [0.28.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.28.0...@cognite/sdk-alpha@0.28.1) (2024-06-10)

**Note:** Version bump only for package @cognite/sdk-alpha

# [0.28.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.27.1...@cognite/sdk-alpha@0.28.0) (2024-06-03)

### Features

- export const for two enum types ([#1107](https://github.com/cognitedata/cognite-sdk-js/issues/1107)) ([e346d92](https://github.com/cognitedata/cognite-sdk-js/commit/e346d92f9ea6ad31f472863092148ddee3dc3b71))

## [0.27.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.27.0...@cognite/sdk-alpha@0.27.1) (2024-05-31)

### Bug Fixes

- allow data sampling to be disabled ([#1106](https://github.com/cognitedata/cognite-sdk-js/issues/1106)) ([a1fb47a](https://github.com/cognitedata/cognite-sdk-js/commit/a1fb47a3a5399ff5945243e89d5a03880b97f114))

# [0.27.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.26.0...@cognite/sdk-alpha@0.27.0) (2024-05-24)

### Features

- **simint-alpha:** adopt breaking changes from the API ([#1105](https://github.com/cognitedata/cognite-sdk-js/issues/1105)) ([8f8dba6](https://github.com/cognitedata/cognite-sdk-js/commit/8f8dba6eababc86a16985f232980e7462cd79cec))

# [0.26.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.25.3...@cognite/sdk-alpha@0.26.0) (2024-05-15)

### Features

- **simint-alpha:** remove unit systems, rename unitQuantities ([#1104](https://github.com/cognitedata/cognite-sdk-js/issues/1104)) ([d7817c9](https://github.com/cognitedata/cognite-sdk-js/commit/d7817c933f2806b2e7da110aa2e8ef228313055a))

## [0.25.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.25.2...@cognite/sdk-alpha@0.25.3) (2024-05-15)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.25.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.25.1...@cognite/sdk-alpha@0.25.2) (2024-05-13)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.25.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.25.0...@cognite/sdk-alpha@0.25.1) (2024-05-02)

### Bug Fixes

- simulation run data api path ([#1102](https://github.com/cognitedata/cognite-sdk-js/issues/1102)) ([24ced6f](https://github.com/cognitedata/cognite-sdk-js/commit/24ced6f529452d5e7946edb1512d1b20cb965a3d))

# [0.25.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.24.4...@cognite/sdk-alpha@0.25.0) (2024-04-30)

### Features

- add simulation run data API ([#1098](https://github.com/cognitedata/cognite-sdk-js/issues/1098)) ([8808338](https://github.com/cognitedata/cognite-sdk-js/commit/8808338a5f11c1dcbcc15a46d612ddf8dcd153a9))

## [0.24.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.24.3...@cognite/sdk-alpha@0.24.4) (2024-04-29)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.24.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.24.2...@cognite/sdk-alpha@0.24.3) (2024-04-11)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.24.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.24.1...@cognite/sdk-alpha@0.24.2) (2024-04-11)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.24.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.24.0...@cognite/sdk-alpha@0.24.1) (2024-04-10)

**Note:** Version bump only for package @cognite/sdk-alpha

# [0.24.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.23.2...@cognite/sdk-alpha@0.24.0) (2024-04-10)

### Features

- add enabled property to data sampling ([#1081](https://github.com/cognitedata/cognite-sdk-js/issues/1081)) ([cf2c6c3](https://github.com/cognitedata/cognite-sdk-js/commit/cf2c6c3aac190ae67746750104bf752ce393a58f))

## [0.23.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.23.1...@cognite/sdk-alpha@0.23.2) (2024-04-09)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.23.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.23.0...@cognite/sdk-alpha@0.23.1) (2024-04-05)

### Bug Fixes

- fix simulator cron type ([#1078](https://github.com/cognitedata/cognite-sdk-js/issues/1078)) ([09c03e0](https://github.com/cognitedata/cognite-sdk-js/commit/09c03e0f0a907db9495ff9e8998732331842a301))

# [0.23.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.22.0...@cognite/sdk-alpha@0.23.0) (2024-04-04)

### Features

- add logId to SimulatorModelRevision ([#1075](https://github.com/cognitedata/cognite-sdk-js/issues/1075)) ([33b79ed](https://github.com/cognitedata/cognite-sdk-js/commit/33b79ed5df42f83265b99acb3b698d7151392d49))

# [0.22.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.21.0...@cognite/sdk-alpha@0.22.0) (2024-03-26)

### Features

- add simulator logs API ([#1073](https://github.com/cognitedata/cognite-sdk-js/issues/1073)) ([10bf22a](https://github.com/cognitedata/cognite-sdk-js/commit/10bf22ab81987e0a2f57facff53cbd90356aeceb))

# [0.21.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.20.2...@cognite/sdk-alpha@0.21.0) (2024-03-21)

### Features

- **alpha:** add sort option for filtering simulation routine revision ([#1072](https://github.com/cognitedata/cognite-sdk-js/issues/1072)) ([6bbb200](https://github.com/cognitedata/cognite-sdk-js/commit/6bbb2001abe637919d3591635485c00c35056e24))

## [0.20.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.20.1...@cognite/sdk-alpha@0.20.2) (2024-03-15)

### Bug Fixes

- add prefix to simint types ([#1069](https://github.com/cognitedata/cognite-sdk-js/issues/1069)) ([cbaa5b7](https://github.com/cognitedata/cognite-sdk-js/commit/cbaa5b75c8431f4b2ea843410aef3c7dabd289e1))

## [0.20.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.20.0...@cognite/sdk-alpha@0.20.1) (2024-03-14)

### Bug Fixes

- export types for simint ([#1068](https://github.com/cognitedata/cognite-sdk-js/issues/1068)) ([c5feec3](https://github.com/cognitedata/cognite-sdk-js/commit/c5feec3b483f26e34980a6d37e26102214f02581))

# [0.20.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.19.0...@cognite/sdk-alpha@0.20.0) (2024-03-11)

### Features

- improve filtering for routine and routine revisions ([#1067](https://github.com/cognitedata/cognite-sdk-js/issues/1067)) ([9de2470](https://github.com/cognitedata/cognite-sdk-js/commit/9de2470f08a1977d1545d108969f494715094836))

# [0.19.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.18.1...@cognite/sdk-alpha@0.19.0) (2024-03-07)

### Features

- update simulation run and filter types ([#1066](https://github.com/cognitedata/cognite-sdk-js/issues/1066)) ([b8a0004](https://github.com/cognitedata/cognite-sdk-js/commit/b8a0004d07719dc825948104f2b5fef9953cb616))

## [0.18.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.18.0...@cognite/sdk-alpha@0.18.1) (2024-02-23)

### Bug Fixes

- **alpha:** simint model status type ([#1065](https://github.com/cognitedata/cognite-sdk-js/issues/1065)) ([c315b40](https://github.com/cognitedata/cognite-sdk-js/commit/c315b40123c30836d266e9f791b8b9e0926483b3))

# [0.18.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.17.0...@cognite/sdk-alpha@0.18.0) (2024-02-23)

### Features

- use enum for revision statuses in types ([#1064](https://github.com/cognitedata/cognite-sdk-js/issues/1064)) ([fe8eccd](https://github.com/cognitedata/cognite-sdk-js/commit/fe8eccd006eeb9c30bdf015c06df0f925798327e))

# [0.17.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.16.0...@cognite/sdk-alpha@0.17.0) (2024-02-23)

### Features

- add simulator model revisions by IDs ([#1063](https://github.com/cognitedata/cognite-sdk-js/issues/1063)) ([2ffee72](https://github.com/cognitedata/cognite-sdk-js/commit/2ffee725b1c4c854d2db855d799e8b800f61c653))

# [0.16.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.15.2...@cognite/sdk-alpha@0.16.0) (2024-02-20)

### Features

- add query byids for simulator models ([#1062](https://github.com/cognitedata/cognite-sdk-js/issues/1062)) ([1c174be](https://github.com/cognitedata/cognite-sdk-js/commit/1c174beebfc32ef54eb754ebd0f3bab62f7c69de))

## [0.15.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.15.1...@cognite/sdk-alpha@0.15.2) (2024-02-20)

### Bug Fixes

- **alpha:** simint nullable types ([#1061](https://github.com/cognitedata/cognite-sdk-js/issues/1061)) ([f633820](https://github.com/cognitedata/cognite-sdk-js/commit/f6338205f6657c67cb43733ab3ebdd277047eb80))

## [0.15.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.15.0...@cognite/sdk-alpha@0.15.1) (2024-02-16)

### Bug Fixes

- **alpha:** simulator integration optional props ([#1060](https://github.com/cognitedata/cognite-sdk-js/issues/1060)) ([26bd5c3](https://github.com/cognitedata/cognite-sdk-js/commit/26bd5c30beb0e04c483698e61fc2671be9e4f28e))

# [0.15.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.14.0...@cognite/sdk-alpha@0.15.0) (2024-02-16)

### Features

- **alpha-simint:** update to the latest simint APIs ([#1059](https://github.com/cognitedata/cognite-sdk-js/issues/1059)) ([262f0de](https://github.com/cognitedata/cognite-sdk-js/commit/262f0de7b761aae41492aff98ea089ae09407c63))

# [0.14.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.13.7...@cognite/sdk-alpha@0.14.0) (2024-02-16)

### Features

- add support for SimInt models and routines ([#1057](https://github.com/cognitedata/cognite-sdk-js/issues/1057)) ([c7a0e2d](https://github.com/cognitedata/cognite-sdk-js/commit/c7a0e2da6c3dd7edbe094cd85e87e4d07cd8905b))

## [0.13.7](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.13.6...@cognite/sdk-alpha@0.13.7) (2024-02-08)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.13.6](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.13.5...@cognite/sdk-alpha@0.13.6) (2024-02-06)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.13.5](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.13.4...@cognite/sdk-alpha@0.13.5) (2024-02-05)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.13.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.13.3...@cognite/sdk-alpha@0.13.4) (2024-01-22)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.13.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.13.2...@cognite/sdk-alpha@0.13.3) (2024-01-18)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.13.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.13.1...@cognite/sdk-alpha@0.13.2) (2023-12-22)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.13.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.13.0...@cognite/sdk-alpha@0.13.1) (2023-12-19)

**Note:** Version bump only for package @cognite/sdk-alpha

# [0.13.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.12.4...@cognite/sdk-alpha@0.13.0) (2023-12-14)

### Features

- **alpha:** add simulation runs ([#1043](https://github.com/cognitedata/cognite-sdk-js/issues/1043)) ([6a2d518](https://github.com/cognitedata/cognite-sdk-js/commit/6a2d518209f782439583e0eba2c54fea05016614))

## [0.12.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.12.3...@cognite/sdk-alpha@0.12.4) (2023-12-06)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.12.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.12.2...@cognite/sdk-alpha@0.12.3) (2023-12-01)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.12.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.12.1...@cognite/sdk-alpha@0.12.2) (2023-11-28)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.12.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.12.0...@cognite/sdk-alpha@0.12.1) (2023-11-28)

**Note:** Version bump only for package @cognite/sdk-alpha

# [0.12.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.11.4...@cognite/sdk-alpha@0.12.0) (2023-11-27)

### Features

- add options field to simulator steps and name to simulator DM ([#1041](https://github.com/cognitedata/cognite-sdk-js/issues/1041)) ([8d4f872](https://github.com/cognitedata/cognite-sdk-js/commit/8d4f872edd0a63ecfee2afbd5c7e644c222739e1))

## [0.11.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.11.3...@cognite/sdk-alpha@0.11.4) (2023-11-24)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.11.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.11.2...@cognite/sdk-alpha@0.11.3) (2023-11-23)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.11.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.11.1...@cognite/sdk-alpha@0.11.2) (2023-11-21)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.11.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.11.0...@cognite/sdk-alpha@0.11.1) (2023-11-09)

### Bug Fixes

- **alpha:** export types for simint API ([#1036](https://github.com/cognitedata/cognite-sdk-js/issues/1036)) ([2677fe6](https://github.com/cognitedata/cognite-sdk-js/commit/2677fe61e2c89d3284f6187c4d56d384cfabdc6f))

# [0.11.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.10.0...@cognite/sdk-alpha@0.11.0) (2023-11-09)

### Features

- **alpha:** simulator integration API ([#1035](https://github.com/cognitedata/cognite-sdk-js/issues/1035)) ([010e5f1](https://github.com/cognitedata/cognite-sdk-js/commit/010e5f14259e6dd3caede8cfbe87adde1b0d80cf))

# [0.10.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.9.1...@cognite/sdk-alpha@0.10.0) (2023-10-17)

### Features

- **AH-1934:** add monitoring tasks and alerts to beta ([#1028](https://github.com/cognitedata/cognite-sdk-js/issues/1028)) ([17bde9c](https://github.com/cognitedata/cognite-sdk-js/commit/17bde9ccda49721d4e718c17e37e870792d038bf))

## [0.9.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.9.0...@cognite/sdk-alpha@0.9.1) (2023-10-10)

**Note:** Version bump only for package @cognite/sdk-alpha

# [0.9.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.8.0...@cognite/sdk-alpha@0.9.0) (2023-09-27)

### Features

- **alpha:** add optional timeseriesExternalId type to monitoring task ([#1025](https://github.com/cognitedata/cognite-sdk-js/issues/1025)) ([45fb525](https://github.com/cognitedata/cognite-sdk-js/commit/45fb5255c53116a668a62760097b3a8272c51e03))

# [0.8.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.7.5...@cognite/sdk-alpha@0.8.0) (2023-09-20)

### Features

- **alpha:** monitoring task add source and sourceId props - [AH-1863] ([#1020](https://github.com/cognitedata/cognite-sdk-js/issues/1020)) ([506ad9b](https://github.com/cognitedata/cognite-sdk-js/commit/506ad9be6368322f08ac606642414a37f37cec67))

## [0.7.5](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.7.4...@cognite/sdk-alpha@0.7.5) (2023-06-20)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.7.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.7.3...@cognite/sdk-alpha@0.7.4) (2023-04-28)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.7.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.7.2...@cognite/sdk-alpha@0.7.3) (2023-04-27)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.7.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.7.1...@cognite/sdk-alpha@0.7.2) (2023-04-20)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.7.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.7.0...@cognite/sdk-alpha@0.7.1) (2023-03-21)

**Note:** Version bump only for package @cognite/sdk-alpha

# [0.7.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.6.0...@cognite/sdk-alpha@0.7.0) (2023-03-07)

### Features

- **alpha:** monitoring task double threshold model ([#996](https://github.com/cognitedata/cognite-sdk-js/issues/996)) ([7ce4c61](https://github.com/cognitedata/cognite-sdk-js/commit/7ce4c610d7bdb39761c1a1d9ea73cf874b1dade1))

# [0.6.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.5.4...@cognite/sdk-alpha@0.6.0) (2023-03-01)

### Features

- add name to mt-class ([#994](https://github.com/cognitedata/cognite-sdk-js/issues/994)) ([6b086d7](https://github.com/cognitedata/cognite-sdk-js/commit/6b086d70f3d4749fa1327e753e32f6950773b9d4)), closes [#993](https://github.com/cognitedata/cognite-sdk-js/issues/993)

## [0.5.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.5.3...@cognite/sdk-alpha@0.5.4) (2023-02-08)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.5.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.5.2...@cognite/sdk-alpha@0.5.3) (2023-01-24)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.5.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.5.1...@cognite/sdk-alpha@0.5.2) (2023-01-23)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.5.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.5.0...@cognite/sdk-alpha@0.5.1) (2023-01-23)

**Note:** Version bump only for package @cognite/sdk-alpha

# [0.5.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.4.2...@cognite/sdk-alpha@0.5.0) (2023-01-11)

### Features

- **alpha:** monitoring task listing and model change ([#932](https://github.com/cognitedata/cognite-sdk-js/issues/932)) ([c415feb](https://github.com/cognitedata/cognite-sdk-js/commit/c415feb360b2848fe38139eb909ef773a460b2e3))

## [0.4.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.4.1...@cognite/sdk-alpha@0.4.2) (2023-01-09)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.4.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.4.0...@cognite/sdk-alpha@0.4.1) (2023-01-06)

**Note:** Version bump only for package @cognite/sdk-alpha

# [0.4.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.3.3...@cognite/sdk-alpha@0.4.0) (2022-12-15)

### Features

- **alpha:** add alert subscribers delete ([#923](https://github.com/cognitedata/cognite-sdk-js/issues/923)) ([0f378ca](https://github.com/cognitedata/cognite-sdk-js/commit/0f378caf1cf68acd9dcd2bfc289dd23aa40076fb))

## [0.3.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.3.2...@cognite/sdk-alpha@0.3.3) (2022-12-13)

### Bug Fixes

- **alpha:** export cognite client properly ([#931](https://github.com/cognitedata/cognite-sdk-js/issues/931)) ([c4399eb](https://github.com/cognitedata/cognite-sdk-js/commit/c4399ebdfacbcc41ba14e35f90b3de8be1918462))

## [0.3.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.3.1...@cognite/sdk-alpha@0.3.2) (2022-12-09)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.3.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.3.0...@cognite/sdk-alpha@0.3.1) (2022-12-02)

### Bug Fixes

- **alpha:** rename monitoringtask to monitoringtasks ([#924](https://github.com/cognitedata/cognite-sdk-js/issues/924)) ([d5b9390](https://github.com/cognitedata/cognite-sdk-js/commit/d5b9390dcbb288e060c5a0b4ff00df9a54a851a9))

# [0.3.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.2.0...@cognite/sdk-alpha@0.3.0) (2022-12-01)

### Features

- **alpha:** monitoring tasks api ([#921](https://github.com/cognitedata/cognite-sdk-js/issues/921)) ([9161a69](https://github.com/cognitedata/cognite-sdk-js/commit/9161a69e2b0f4093099e0f36a79c0abefce6723c))

# [0.2.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.1.17...@cognite/sdk-alpha@0.2.0) (2022-11-23)

### Features

- **alpha:** alerts list subscribers and subscriptions ([#920](https://github.com/cognitedata/cognite-sdk-js/issues/920)) ([ae6b04b](https://github.com/cognitedata/cognite-sdk-js/commit/ae6b04b115bf8d5c92cdb0108f1427de36a17c31))

## [0.1.17](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.1.16...@cognite/sdk-alpha@0.1.17) (2022-11-21)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.1.16](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.1.15...@cognite/sdk-alpha@0.1.16) (2022-11-18)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.1.15](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.1.14...@cognite/sdk-alpha@0.1.15) (2022-11-17)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.1.14](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.1.13...@cognite/sdk-alpha@0.1.14) (2022-11-02)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.1.13](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.1.12...@cognite/sdk-alpha@0.1.13) (2022-10-28)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.1.12](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.1.11...@cognite/sdk-alpha@0.1.12) (2022-10-04)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.1.11](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.1.10...@cognite/sdk-alpha@0.1.11) (2022-09-26)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.1.10](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.1.9...@cognite/sdk-alpha@0.1.10) (2022-09-23)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.1.9](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.1.8...@cognite/sdk-alpha@0.1.9) (2022-09-23)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.1.8](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.1.7...@cognite/sdk-alpha@0.1.8) (2022-09-23)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.1.7](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.1.6...@cognite/sdk-alpha@0.1.7) (2022-09-12)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.1.6](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.1.5...@cognite/sdk-alpha@0.1.6) (2022-09-12)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.1.5](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.1.4...@cognite/sdk-alpha@0.1.5) (2022-09-07)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.1.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.1.3...@cognite/sdk-alpha@0.1.4) (2022-09-07)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.1.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.1.2...@cognite/sdk-alpha@0.1.3) (2022-09-06)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.1.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.1.1...@cognite/sdk-alpha@0.1.2) (2022-08-31)

**Note:** Version bump only for package @cognite/sdk-alpha

## [0.1.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-alpha@0.1.0...@cognite/sdk-alpha@0.1.1) (2022-08-21)

**Note:** Version bump only for package @cognite/sdk-alpha

# 0.1.0 (2022-08-15)

### Features

- alerts api (version alpha) ([#851](https://github.com/cognitedata/cognite-sdk-js/issues/851)) ([109cfca](https://github.com/cognitedata/cognite-sdk-js/commit/109cfca9d70902b4aae36d27a1b7a2528df5d4bc))


<!-- SOURCE_END: cognite-sdk-js/packages\alpha\CHANGELOG.md -->

================================================================================


<!-- SOURCE_START: cognite-sdk-js/packages\alpha\README.md -->
## File: cognite-sdk-js/packages\alpha\README.md

Cognite Javascript SDK (alpha)
================================
The package `@cognite/sdk-alpha` provides a subset of API bindings to the CDF alpha APIs. Note that this package is considered unstable and may periodically be out of date for certain resources. The alpha SDK is not recommended for production and should only be used if you know what you are doing.
When a resource is removed from the alpha it _may_ be found in the beta or stable packages, unless they are discontinued.

install instructions:
```
yarn add @cognite/sdk-alpha
```
or with npm
```
npm install @cognite/sdk-alpha --save
```

This will download `@cognite/sdk-alpha`. Import the `CogniteClientAlpha`:
```js
import CogniteClientAlpha from '@cognite/sdk-alpha';
```

The CogniteClientAlpha can be initialized/configured in the same manner as the other packages (eg. stable, beta, etc.).

## Documentation

[cognitedata.github.io/cognite-sdk-js/alpha](https://cognitedata.github.io/cognite-sdk-js/alpha/classes/_alpha_src_cogniteclient_.cogniteclientalpha.html)


<!-- SOURCE_END: cognite-sdk-js/packages\alpha\README.md -->

================================================================================


<!-- SOURCE_START: cognite-sdk-js/packages\beta\CHANGELOG.md -->
## File: cognite-sdk-js/packages\beta\CHANGELOG.md

# Change Log

All notable changes to this project will be documented in this file.
See [Conventional Commits](https://conventionalcommits.org) for commit guidelines.

## [6.0.8](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@6.0.7...@cognite/sdk-beta@6.0.8) (2025-11-27)

**Note:** Version bump only for package @cognite/sdk-beta





## [6.0.7](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@6.0.6...@cognite/sdk-beta@6.0.7) (2025-11-04)

**Note:** Version bump only for package @cognite/sdk-beta





## [6.0.6](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@6.0.5...@cognite/sdk-beta@6.0.6) (2025-09-29)

**Note:** Version bump only for package @cognite/sdk-beta





## [6.0.5](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@6.0.4...@cognite/sdk-beta@6.0.5) (2025-09-25)

**Note:** Version bump only for package @cognite/sdk-beta





## [6.0.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@6.0.3...@cognite/sdk-beta@6.0.4) (2025-09-22)

**Note:** Version bump only for package @cognite/sdk-beta





## [6.0.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@6.0.2...@cognite/sdk-beta@6.0.3) (2025-09-19)

**Note:** Version bump only for package @cognite/sdk-beta





## [6.0.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@6.0.0...@cognite/sdk-beta@6.0.2) (2025-09-17)

**Note:** Version bump only for package @cognite/sdk-beta





## [6.0.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@6.0.0...@cognite/sdk-beta@6.0.1) (2025-09-11)

**Note:** Version bump only for package @cognite/sdk-beta





# [6.0.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.12.3...@cognite/sdk-beta@6.0.0) (2025-02-03)


### Features

* release v10 [release] ([#1243](https://github.com/cognitedata/cognite-sdk-js/issues/1243)) ([b3d5595](https://github.com/cognitedata/cognite-sdk-js/commit/b3d55951bb2a302981fa67ce694d21749461386a)), closes [#1135](https://github.com/cognitedata/cognite-sdk-js/issues/1135) [#1144](https://github.com/cognitedata/cognite-sdk-js/issues/1144) [#1148](https://github.com/cognitedata/cognite-sdk-js/issues/1148) [#1150](https://github.com/cognitedata/cognite-sdk-js/issues/1150) [#1151](https://github.com/cognitedata/cognite-sdk-js/issues/1151) [#1152](https://github.com/cognitedata/cognite-sdk-js/issues/1152) [#1153](https://github.com/cognitedata/cognite-sdk-js/issues/1153) [#1029](https://github.com/cognitedata/cognite-sdk-js/issues/1029)


### BREAKING CHANGES

* es6 module (vs es5) and typescript 3 -> 5

* chore: release pre-release from the release-v10 branch

* test: skip flaky alerts test

* chore: trigger [release]

* chore(release): publish new package versions [skip ci]

 - @cognite/sdk-alpha@1.0.0-rc.0
 - @cognite/sdk-beta@6.0.0-rc.0
 - @cognite/sdk-core@5.0.0-rc.0
 - @cognite/sdk-playground@8.0.0-rc.0
 - @cognite/sdk@10.0.0-rc.0





## [5.12.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.12.2...@cognite/sdk-beta@5.12.3) (2025-01-17)

**Note:** Version bump only for package @cognite/sdk-beta





## [5.12.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.12.1...@cognite/sdk-beta@5.12.2) (2024-12-12)

**Note:** Version bump only for package @cognite/sdk-beta





## [5.12.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.12.0...@cognite/sdk-beta@5.12.1) (2024-11-07)

**Note:** Version bump only for package @cognite/sdk-beta





# [5.12.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.11.6...@cognite/sdk-beta@5.12.0) (2024-10-07)


### Features

* **files:** move multipart functionality to stable [BND3D-4545] ([#1132](https://github.com/cognitedata/cognite-sdk-js/issues/1132)) ([1e33003](https://github.com/cognitedata/cognite-sdk-js/commit/1e33003f364ad0932cca24ea2a9d457ac382f9ec))





## [5.11.6](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.11.4...@cognite/sdk-beta@5.11.6) (2024-08-26)


### Bug Fixes

* dummy release to override a bad one ([#1139](https://github.com/cognitedata/cognite-sdk-js/issues/1139)) ([5b036da](https://github.com/cognitedata/cognite-sdk-js/commit/5b036dabd4630b45d51558ee7f95d951c7227137))





## [5.11.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.11.3...@cognite/sdk-beta@5.11.4) (2024-08-26)

**Note:** Version bump only for package @cognite/sdk-beta





## [5.11.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.11.2...@cognite/sdk-beta@5.11.3) (2024-08-22)

**Note:** Version bump only for package @cognite/sdk-beta





## [5.11.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.11.1...@cognite/sdk-beta@5.11.2) (2024-08-20)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.11.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.11.0...@cognite/sdk-beta@5.11.1) (2024-08-19)

**Note:** Version bump only for package @cognite/sdk-beta

# [5.11.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.10.2...@cognite/sdk-beta@5.11.0) (2024-06-26)

### Features

- add test for limit ([#1117](https://github.com/cognitedata/cognite-sdk-js/issues/1117)) ([582891e](https://github.com/cognitedata/cognite-sdk-js/commit/582891ef7ce034425c500036af08d91964200a0f))
- add test for pagination cursor ([#1116](https://github.com/cognitedata/cognite-sdk-js/issues/1116)) ([65fe563](https://github.com/cognitedata/cognite-sdk-js/commit/65fe56349905569d5a9961ce3ff4f4f8dc49ee19))

## [5.10.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.10.1...@cognite/sdk-beta@5.10.2) (2024-06-10)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.10.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.10.0...@cognite/sdk-beta@5.10.1) (2024-05-15)

**Note:** Version bump only for package @cognite/sdk-beta

# [5.10.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.9.0...@cognite/sdk-beta@5.10.0) (2024-05-13)

### Features

- **files-beta:** add multi part upload sdk and tests (BND3D-4012) ([#1088](https://github.com/cognitedata/cognite-sdk-js/issues/1088)) ([a2fbbe6](https://github.com/cognitedata/cognite-sdk-js/commit/a2fbbe67db54518f3de08ec03dd4c6898c43465a))

# [5.9.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.8.0...@cognite/sdk-beta@5.9.0) (2024-05-02)

### Features

- add sorting for alerts ([#1097](https://github.com/cognitedata/cognite-sdk-js/issues/1097)) ([1a08cca](https://github.com/cognitedata/cognite-sdk-js/commit/1a08ccaf7a2d0d16e057bcd8819774c3b5a513dd))

# [5.8.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.7.4...@cognite/sdk-beta@5.8.0) (2024-04-29)

### Features

- **datapoints:** add support for data quality (beta) ([#1071](https://github.com/cognitedata/cognite-sdk-js/issues/1071)) ([d414ae4](https://github.com/cognitedata/cognite-sdk-js/commit/d414ae43f62e45f975bbcffe178dfbc29d1f913e)), closes [#1070](https://github.com/cognitedata/cognite-sdk-js/issues/1070) [#1072](https://github.com/cognitedata/cognite-sdk-js/issues/1072) [#1073](https://github.com/cognitedata/cognite-sdk-js/issues/1073) [#1075](https://github.com/cognitedata/cognite-sdk-js/issues/1075) [#1076](https://github.com/cognitedata/cognite-sdk-js/issues/1076) [#1078](https://github.com/cognitedata/cognite-sdk-js/issues/1078) [#1080](https://github.com/cognitedata/cognite-sdk-js/issues/1080) [#1079](https://github.com/cognitedata/cognite-sdk-js/issues/1079) [#1081](https://github.com/cognitedata/cognite-sdk-js/issues/1081)

## [5.7.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.7.3...@cognite/sdk-beta@5.7.4) (2024-04-11)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.7.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.7.2...@cognite/sdk-beta@5.7.3) (2024-04-11)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.7.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.7.1...@cognite/sdk-beta@5.7.2) (2024-04-10)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.7.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.7.0...@cognite/sdk-beta@5.7.1) (2024-04-09)

**Note:** Version bump only for package @cognite/sdk-beta

# [5.7.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.6.3...@cognite/sdk-beta@5.7.0) (2024-03-20)

### Features

- update alerts filter ([#1070](https://github.com/cognitedata/cognite-sdk-js/issues/1070)) ([078f396](https://github.com/cognitedata/cognite-sdk-js/commit/078f3960cb0077f17fd2d20eaf14395ab10b1038))

## [5.6.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.6.2...@cognite/sdk-beta@5.6.3) (2024-02-08)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.6.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.6.1...@cognite/sdk-beta@5.6.2) (2024-02-06)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.6.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.6.0...@cognite/sdk-beta@5.6.1) (2024-02-05)

**Note:** Version bump only for package @cognite/sdk-beta

# [5.6.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.5.7...@cognite/sdk-beta@5.6.0) (2024-01-24)

### Features

- update monitoringTaskCreate type ([#1052](https://github.com/cognitedata/cognite-sdk-js/issues/1052)) ([3df9f81](https://github.com/cognitedata/cognite-sdk-js/commit/3df9f81fa5ce68d488eeb364894f6eb3b5e14e28))

## [5.5.7](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.5.6...@cognite/sdk-beta@5.5.7) (2024-01-22)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.5.6](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.5.5...@cognite/sdk-beta@5.5.6) (2024-01-18)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.5.5](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.5.4...@cognite/sdk-beta@5.5.5) (2023-12-22)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.5.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.5.3...@cognite/sdk-beta@5.5.4) (2023-12-19)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.5.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.5.2...@cognite/sdk-beta@5.5.3) (2023-12-06)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.5.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.5.1...@cognite/sdk-beta@5.5.2) (2023-12-01)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.5.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.5.0...@cognite/sdk-beta@5.5.1) (2023-11-28)

**Note:** Version bump only for package @cognite/sdk-beta

# [5.5.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.4.3...@cognite/sdk-beta@5.5.0) (2023-11-28)

### Features

- monitoring tasks upsert ([#1042](https://github.com/cognitedata/cognite-sdk-js/issues/1042)) ([7e0e24c](https://github.com/cognitedata/cognite-sdk-js/commit/7e0e24cfb5c4d91302e54a270457b8d9b914ffd4))

## [5.4.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.4.2...@cognite/sdk-beta@5.4.3) (2023-11-24)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.4.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.4.1...@cognite/sdk-beta@5.4.2) (2023-11-23)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.4.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.4.0...@cognite/sdk-beta@5.4.1) (2023-11-21)

**Note:** Version bump only for package @cognite/sdk-beta

# [5.4.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.3.0...@cognite/sdk-beta@5.4.0) (2023-11-01)

### Features

- **beta:** add alertContext to monitoring create type ([#1032](https://github.com/cognitedata/cognite-sdk-js/issues/1032)) ([ab21181](https://github.com/cognitedata/cognite-sdk-js/commit/ab211812f93a45fa2485a5d5464c30392d49f110))

# [5.3.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.34...@cognite/sdk-beta@5.3.0) (2023-10-17)

### Features

- **AH-1934:** add monitoring tasks and alerts to beta ([#1028](https://github.com/cognitedata/cognite-sdk-js/issues/1028)) ([17bde9c](https://github.com/cognitedata/cognite-sdk-js/commit/17bde9ccda49721d4e718c17e37e870792d038bf))

## [5.2.34](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.33...@cognite/sdk-beta@5.2.34) (2023-10-10)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.2.33](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.32...@cognite/sdk-beta@5.2.33) (2023-09-27)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.2.32](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.31...@cognite/sdk-beta@5.2.32) (2023-06-20)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.2.31](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.30...@cognite/sdk-beta@5.2.31) (2023-04-28)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.2.30](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.29...@cognite/sdk-beta@5.2.30) (2023-04-27)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.2.29](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.28...@cognite/sdk-beta@5.2.29) (2023-04-20)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.2.28](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.27...@cognite/sdk-beta@5.2.28) (2023-03-21)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.2.27](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.26...@cognite/sdk-beta@5.2.27) (2023-03-01)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.2.26](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.25...@cognite/sdk-beta@5.2.26) (2023-02-08)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.2.25](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.24...@cognite/sdk-beta@5.2.25) (2023-01-24)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.2.24](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.23...@cognite/sdk-beta@5.2.24) (2023-01-23)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.2.23](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.22...@cognite/sdk-beta@5.2.23) (2023-01-23)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.2.22](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.21...@cognite/sdk-beta@5.2.22) (2023-01-09)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.2.21](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.20...@cognite/sdk-beta@5.2.21) (2023-01-06)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.2.20](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.19...@cognite/sdk-beta@5.2.20) (2022-12-09)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.2.19](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.18...@cognite/sdk-beta@5.2.19) (2022-11-21)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.2.18](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.17...@cognite/sdk-beta@5.2.18) (2022-11-18)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.2.17](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.16...@cognite/sdk-beta@5.2.17) (2022-11-17)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.2.16](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.15...@cognite/sdk-beta@5.2.16) (2022-11-02)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.2.15](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.14...@cognite/sdk-beta@5.2.15) (2022-10-28)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.2.14](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.13...@cognite/sdk-beta@5.2.14) (2022-10-04)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.2.13](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.12...@cognite/sdk-beta@5.2.13) (2022-09-26)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.2.12](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.11...@cognite/sdk-beta@5.2.12) (2022-09-23)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.2.11](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.10...@cognite/sdk-beta@5.2.11) (2022-09-23)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.2.10](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.9...@cognite/sdk-beta@5.2.10) (2022-09-23)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.2.9](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.8...@cognite/sdk-beta@5.2.9) (2022-09-12)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.2.8](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.7...@cognite/sdk-beta@5.2.8) (2022-09-12)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.2.7](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.6...@cognite/sdk-beta@5.2.7) (2022-09-07)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.2.6](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.5...@cognite/sdk-beta@5.2.6) (2022-09-07)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.2.5](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.4...@cognite/sdk-beta@5.2.5) (2022-09-06)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.2.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.3...@cognite/sdk-beta@5.2.4) (2022-08-31)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.2.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.2...@cognite/sdk-beta@5.2.3) (2022-08-21)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.2.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.1...@cognite/sdk-beta@5.2.2) (2022-08-15)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.2.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.2.0...@cognite/sdk-beta@5.2.1) (2022-08-05)

**Note:** Version bump only for package @cognite/sdk-beta

# [5.2.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.1.4...@cognite/sdk-beta@5.2.0) (2022-07-25)

### Features

- code generation for types based on openapi doc ([#801](https://github.com/cognitedata/cognite-sdk-js/issues/801)) ([07eb308](https://github.com/cognitedata/cognite-sdk-js/commit/07eb3087705c550758fcc9e1b12ea43428ecf79d)), closes [#820](https://github.com/cognitedata/cognite-sdk-js/issues/820)

## [5.1.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.1.3...@cognite/sdk-beta@5.1.4) (2022-07-07)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.1.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.1.2...@cognite/sdk-beta@5.1.3) (2022-06-20)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.1.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.1.1...@cognite/sdk-beta@5.1.2) (2022-06-17)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.1.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.1.0...@cognite/sdk-beta@5.1.1) (2022-06-09)

**Note:** Version bump only for package @cognite/sdk-beta

# [5.1.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.0.25...@cognite/sdk-beta@5.1.0) (2022-06-02)

### Features

- automatic token management with @cognite/auth-wrapper ([#796](https://github.com/cognitedata/cognite-sdk-js/issues/796)) ([d13c53f](https://github.com/cognitedata/cognite-sdk-js/commit/d13c53f9d5a5e65d6e2fc7d1a0e2efe2d36e678c))

### Reverts

- Revert "feat: automatic token management with @cognite/auth-wrapper (#796)" (#813) ([0a8a61c](https://github.com/cognitedata/cognite-sdk-js/commit/0a8a61c2b2c3d992f80b40f6282b20595537edac)), closes [#796](https://github.com/cognitedata/cognite-sdk-js/issues/796) [#813](https://github.com/cognitedata/cognite-sdk-js/issues/813)

## [5.0.25](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.0.24...@cognite/sdk-beta@5.0.25) (2022-05-27)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.0.24](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.0.23...@cognite/sdk-beta@5.0.24) (2022-05-27)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.0.23](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.0.22...@cognite/sdk-beta@5.0.23) (2022-05-25)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.0.22](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.0.21...@cognite/sdk-beta@5.0.22) (2022-05-23)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.0.21](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.0.20...@cognite/sdk-beta@5.0.21) (2022-05-20)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.0.20](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.0.19...@cognite/sdk-beta@5.0.20) (2022-05-20)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.0.19](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.0.18...@cognite/sdk-beta@5.0.19) (2022-05-19)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.0.18](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.0.17...@cognite/sdk-beta@5.0.18) (2022-05-18)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.0.17](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.0.16...@cognite/sdk-beta@5.0.17) (2022-05-16)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.0.16](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.0.15...@cognite/sdk-beta@5.0.16) (2022-05-16)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.0.15](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.0.14...@cognite/sdk-beta@5.0.15) (2022-05-13)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.0.14](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.0.13...@cognite/sdk-beta@5.0.14) (2022-04-12)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.0.13](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.0.12...@cognite/sdk-beta@5.0.13) (2022-04-08)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.0.12](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.0.11...@cognite/sdk-beta@5.0.12) (2022-03-01)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.0.11](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.0.10...@cognite/sdk-beta@5.0.11) (2022-02-09)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.0.10](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.0.9...@cognite/sdk-beta@5.0.10) (2022-01-10)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.0.9](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.0.8...@cognite/sdk-beta@5.0.9) (2022-01-10)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.0.8](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.0.7...@cognite/sdk-beta@5.0.8) (2021-12-21)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.0.7](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.0.6...@cognite/sdk-beta@5.0.7) (2021-12-07)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.0.6](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.0.5...@cognite/sdk-beta@5.0.6) (2021-11-19)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.0.5](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.0.4...@cognite/sdk-beta@5.0.5) (2021-11-17)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.0.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.0.3...@cognite/sdk-beta@5.0.4) (2021-11-04)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.0.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.0.2...@cognite/sdk-beta@5.0.3) (2021-10-29)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.0.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.0.1...@cognite/sdk-beta@5.0.2) (2021-10-19)

**Note:** Version bump only for package @cognite/sdk-beta

## [5.0.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@5.0.0...@cognite/sdk-beta@5.0.1) (2021-10-12)

**Note:** Version bump only for package @cognite/sdk-beta

# [5.0.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@4.4.1...@cognite/sdk-beta@5.0.0) (2021-10-12)

### Features

- **auth:** re-release auth patch ([#700](https://github.com/cognitedata/cognite-sdk-js/issues/700)) ([a53c40d](https://github.com/cognitedata/cognite-sdk-js/commit/a53c40ddd7eca5d2dee9149f5df0b2e533d19575))

### BREAKING CHANGES

- **auth:** release v6

re-release (revert reversion) of "feat(core): move authentication out of CogniteClient"
https://github.com/cognitedata/cognite-sdk-js/pull/687

This reverts commit 72e1ecb61603e0ac3926124c26f4e009df88f020.

Co-authored-by: Vegard Økland <vegard.okland@cognite.com>

## [4.4.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@4.4.0...@cognite/sdk-beta@4.4.1) (2021-10-12)

### Bug Fixes

- **release:** undo major version release without major version bump ([#697](https://github.com/cognitedata/cognite-sdk-js/issues/697)) ([72e1ecb](https://github.com/cognitedata/cognite-sdk-js/commit/72e1ecb61603e0ac3926124c26f4e009df88f020)), closes [#687](https://github.com/cognitedata/cognite-sdk-js/issues/687)

# [4.4.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@4.3.5...@cognite/sdk-beta@4.4.0) (2021-10-12)

### Features

- **core:** move authentication out of CogniteClient ([#687](https://github.com/cognitedata/cognite-sdk-js/issues/687)) ([879ed31](https://github.com/cognitedata/cognite-sdk-js/commit/879ed31d05dd6d6f4b691b99eaca5fa7363e96e6))

## [4.3.5](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@4.3.4...@cognite/sdk-beta@4.3.5) (2021-10-07)

**Note:** Version bump only for package @cognite/sdk-beta

## [4.3.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@4.3.3...@cognite/sdk-beta@4.3.4) (2021-09-22)

**Note:** Version bump only for package @cognite/sdk-beta

## [4.3.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@4.3.2...@cognite/sdk-beta@4.3.3) (2021-09-20)

**Note:** Version bump only for package @cognite/sdk-beta

## [4.3.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@4.3.1...@cognite/sdk-beta@4.3.2) (2021-09-03)

### Bug Fixes

- remove test files in published packages ([#673](https://github.com/cognitedata/cognite-sdk-js/issues/673)) ([cf6deae](https://github.com/cognitedata/cognite-sdk-js/commit/cf6deae6d80d0bfb3b2b3e8a8db6c30a1bb1ec0a))

## [4.3.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@4.3.0...@cognite/sdk-beta@4.3.1) (2021-08-30)

**Note:** Version bump only for package @cognite/sdk-beta

# [4.3.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@4.2.1...@cognite/sdk-beta@4.3.0) (2021-08-30)

### Features

- promote templates to stable ([#661](https://github.com/cognitedata/cognite-sdk-js/issues/661)) ([def00b6](https://github.com/cognitedata/cognite-sdk-js/commit/def00b654ad0696ef2be8dcd47a94fcd099f7277))

## [4.2.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@4.2.0...@cognite/sdk-beta@4.2.1) (2021-08-20)

**Note:** Version bump only for package @cognite/sdk-beta

# [4.2.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@4.1.0...@cognite/sdk-beta@4.2.0) (2021-08-18)

### Features

- add update support template instances ([#634](https://github.com/cognitedata/cognite-sdk-js/issues/634)) ([15ca576](https://github.com/cognitedata/cognite-sdk-js/commit/15ca5762a8163fba8dd87d6a69124c4cf2c5dc38))

# [4.1.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@4.0.5...@cognite/sdk-beta@4.1.0) (2021-08-11)

### Features

- **templates:** add lastCreatedTime and lastUpdatedTime ([#581](https://github.com/cognitedata/cognite-sdk-js/issues/581)) ([829af6c](https://github.com/cognitedata/cognite-sdk-js/commit/829af6c2f617052fffbe0bbf4e9b6d70c9cdf6f1))
- add delete support to template views ([#580](https://github.com/cognitedata/cognite-sdk-js/issues/580)) ([2e16a17](https://github.com/cognitedata/cognite-sdk-js/commit/2e16a1746e4959a0a660a4a4f8d496706fe766d4))

## [4.0.5](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@4.0.4...@cognite/sdk-beta@4.0.5) (2021-08-10)

**Note:** Version bump only for package @cognite/sdk-beta

## [4.0.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@4.0.3...@cognite/sdk-beta@4.0.4) (2021-07-21)

**Note:** Version bump only for package @cognite/sdk-beta

## [4.0.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@4.0.2...@cognite/sdk-beta@4.0.3) (2021-07-21)

**Note:** Version bump only for package @cognite/sdk-beta

## [4.0.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@4.0.1...@cognite/sdk-beta@4.0.2) (2021-07-21)

**Note:** Version bump only for package @cognite/sdk-beta

## [4.0.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@4.0.0...@cognite/sdk-beta@4.0.1) (2021-07-20)

**Note:** Version bump only for package @cognite/sdk-beta

# [4.0.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@3.0.4...@cognite/sdk-beta@4.0.0) (2021-07-15)

### Features

- **core:** add oidc auth code flow [release] ([#587](https://github.com/cognitedata/cognite-sdk-js/issues/587)) ([0cc44aa](https://github.com/cognitedata/cognite-sdk-js/commit/0cc44aa82b7d7461e8629fe2e712f743bf6c7138))

### BREAKING CHANGES

- **core:** stop silencing errors from aad

- **core:** change loginWithOAuth API signature

## [3.0.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@3.0.3...@cognite/sdk-beta@3.0.4) (2021-07-14)

**Note:** Version bump only for package @cognite/sdk-beta

## [3.0.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@3.0.2...@cognite/sdk-beta@3.0.3) (2021-07-08)

**Note:** Version bump only for package @cognite/sdk-beta

## [3.0.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@3.0.1...@cognite/sdk-beta@3.0.2) (2021-06-30)

**Note:** Version bump only for package @cognite/sdk-beta

## [3.0.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@3.0.0...@cognite/sdk-beta@3.0.1) (2021-06-30)

### Bug Fixes

- remove documents-api [release] ([#599](https://github.com/cognitedata/cognite-sdk-js/issues/599)) ([473dfea](https://github.com/cognitedata/cognite-sdk-js/commit/473dfea3768bd9533d0b935aafe5111ed2558f40))

# [3.0.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@2.1.0...@cognite/sdk-beta@3.0.0) (2021-06-30)

### Features

- [SBS-3722] search by size range ([#567](https://github.com/cognitedata/cognite-sdk-js/issues/567)) ([1b35d6c](https://github.com/cognitedata/cognite-sdk-js/commit/1b35d6ce73ded7f36be725e2b8c802fa7f8dac88))
- add template views ([#577](https://github.com/cognitedata/cognite-sdk-js/issues/577)) ([0195c40](https://github.com/cognitedata/cognite-sdk-js/commit/0195c4022b58bfda1641cd10ab439e2e25095598))
- added support for variables and operationName for templates [release] ([#591](https://github.com/cognitedata/cognite-sdk-js/issues/591)) ([19514b0](https://github.com/cognitedata/cognite-sdk-js/commit/19514b08945cc3f1d16b34bcbb1f4d63bb25f8da))
- document preview endpoints [release] ([#579](https://github.com/cognitedata/cognite-sdk-js/issues/579)) ([1e8283b](https://github.com/cognitedata/cognite-sdk-js/commit/1e8283b5d5286536faa059bc46c42f24a1980d73))

### BREAKING CHANGES

- The templates runQuery function in beta has changed signature, now accepts an object with query, variables and operationName

# [2.1.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@2.0.6...@cognite/sdk-beta@2.1.0) (2021-06-08)

### Bug Fixes

- template tests ([#557](https://github.com/cognitedata/cognite-sdk-js/issues/557)) ([3099c37](https://github.com/cognitedata/cognite-sdk-js/commit/3099c37d01356de94efee3c9ddda7850af5d2225))

### Features

- **documents:** [SBS-3442, SBS-3648] Endpoints for documents ([#541](https://github.com/cognitedata/cognite-sdk-js/issues/541)) ([0e69be5](https://github.com/cognitedata/cognite-sdk-js/commit/0e69be5d8a221494e47bbd5522b9f020e496ceae))

## [2.0.6](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@2.0.5...@cognite/sdk-beta@2.0.6) (2021-05-19)

**Note:** Version bump only for package @cognite/sdk-beta

## [2.0.5](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@2.0.4...@cognite/sdk-beta@2.0.5) (2021-04-30)

**Note:** Version bump only for package @cognite/sdk-beta

## [2.0.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@2.0.3...@cognite/sdk-beta@2.0.4) (2021-04-29)

### Bug Fixes

- setting a field resolver to undefined [release] ([#486](https://github.com/cognitedata/cognite-sdk-js/issues/486)) ([75c43eb](https://github.com/cognitedata/cognite-sdk-js/commit/75c43eb2163991485efb6afcade5919d66bbe80f))

## [2.0.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@2.0.2...@cognite/sdk-beta@2.0.3) (2021-04-26)

**Note:** Version bump only for package @cognite/sdk-beta

## [2.0.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@2.0.1...@cognite/sdk-beta@2.0.2) (2021-04-13)

**Note:** Version bump only for package @cognite/sdk-beta

## [2.0.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@2.0.0...@cognite/sdk-beta@2.0.1) (2021-04-08)

**Note:** Version bump only for package @cognite/sdk-beta

# [2.0.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@1.5.3...@cognite/sdk-beta@2.0.0) (2021-03-25)

**Note:** Version bump only for package @cognite/sdk-beta

## [1.5.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@1.5.2...@cognite/sdk-beta@1.5.3) (2021-03-25)

**Note:** Version bump only for package @cognite/sdk-beta

## [1.5.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@1.5.1...@cognite/sdk-beta@1.5.2) (2021-03-25)

**Note:** Version bump only for package @cognite/sdk-beta

## [1.5.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@1.5.0...@cognite/sdk-beta@1.5.1) (2021-03-24)

**Note:** Version bump only for package @cognite/sdk-beta

# [1.5.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@1.4.4...@cognite/sdk-beta@1.5.0) (2021-03-16)

### Features

- **entitymatching:** new stable api ([#491](https://github.com/cognitedata/cognite-sdk-js/issues/491)) ([e91e707](https://github.com/cognitedata/cognite-sdk-js/commit/e91e707d5a348537c24e3e27510580072e2acf71))

## [1.4.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@1.4.3...@cognite/sdk-beta@1.4.4) (2021-03-05)

**Note:** Version bump only for package @cognite/sdk-beta

## [1.4.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@1.4.2...@cognite/sdk-beta@1.4.3) (2021-03-02)

**Note:** Version bump only for package @cognite/sdk-beta

## [1.4.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@1.4.0...@cognite/sdk-beta@1.4.2) (2021-03-01)

**Note:** Version bump only for package @cognite/sdk-beta

## [1.4.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@1.4.0...@cognite/sdk-beta@1.4.1) (2021-02-26)

**Note:** Version bump only for package @cognite/sdk-beta

# [1.4.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@1.3.2...@cognite/sdk-beta@1.4.0) (2021-02-12)

### Features

- **beta:** add Template Groups API ([#477](https://github.com/cognitedata/cognite-sdk-js/issues/477)) ([1b1398b](https://github.com/cognitedata/cognite-sdk-js/commit/1b1398b57e83a4313b2ae4c215eb24540ea48683))

## [1.3.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@1.3.1...@cognite/sdk-beta@1.3.2) (2020-12-17)

**Note:** Version bump only for package @cognite/sdk-beta

## [1.3.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@1.3.0...@cognite/sdk-beta@1.3.1) (2020-12-14)

**Note:** Version bump only for package @cognite/sdk-beta

# [1.3.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@1.2.0...@cognite/sdk-beta@1.3.0) (2020-11-25)

### Features

- **relationships:** promote new api to stable ([526becc](https://github.com/cognitedata/cognite-sdk-js/commit/526beccaeb95371bc6504da2b434b6095415a878))

# [1.2.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@1.1.3...@cognite/sdk-beta@1.2.0) (2020-11-25)

### Features

- **contextualization:** entity matching API ([9609226](https://github.com/cognitedata/cognite-sdk-js/commit/960922642188898c1eee160b304d0fcf57c9661e))

## [1.1.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@1.1.2...@cognite/sdk-beta@1.1.3) (2020-11-18)

**Note:** Version bump only for package @cognite/sdk-beta

## [1.1.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@1.1.1...@cognite/sdk-beta@1.1.2) (2020-11-18)

**Note:** Version bump only for package @cognite/sdk-beta

## [1.1.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@1.1.0...@cognite/sdk-beta@1.1.1) (2020-10-07)

### Bug Fixes

- beta re-export bug ([#432](https://github.com/cognitedata/cognite-sdk-js/issues/432)) ([9dbec81](https://github.com/cognitedata/cognite-sdk-js/commit/9dbec813197e450c9655d2e6df36e3b4d0cf39b2))

# [1.1.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@1.0.2...@cognite/sdk-beta@1.1.0) (2020-09-28)

### Features

- add relationships resource api ([#423](https://github.com/cognitedata/cognite-sdk-js/issues/423)) ([f51e21c](https://github.com/cognitedata/cognite-sdk-js/commit/f51e21c18b943e408d284f9467a2296dcba8061a))

## [1.0.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@1.0.1...@cognite/sdk-beta@1.0.2) (2020-09-17)

**Note:** Version bump only for package @cognite/sdk-beta

## [1.0.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-beta@1.0.0...@cognite/sdk-beta@1.0.1) (2020-09-10)

### Bug Fixes

- exports from packages, add guide for making derived SDKs ([#421](https://github.com/cognitedata/cognite-sdk-js/issues/421)) ([a3a2eb0](https://github.com/cognitedata/cognite-sdk-js/commit/a3a2eb03645733c289591b187f19e55b5294fbc7))

# 1.0.0 (2020-08-03)

- creation of beta sdk


<!-- SOURCE_END: cognite-sdk-js/packages\beta\CHANGELOG.md -->

================================================================================


<!-- SOURCE_START: cognite-sdk-js/packages\beta\README.md -->
## File: cognite-sdk-js/packages\beta\README.md

Cognite Javascript SDK beta
===========================
The package `@cognite/sdk-beta` provides convenient access to the beta version of the Cognite API.
Setup is very similar to [stable](https://github.com/cognitedata/cognite-sdk-js/blob/master/packages/stable/README.md),
but use the following command to install:
```
yarn add @cognite/sdk@npm:@cognite/sdk-beta
```
or with npm
```
npm install @cognite/sdk@npm:@cognite/sdk-beta --save
```

This will install the package `@cognite/sdk-beta` as a dependency, but aliased as `@cognite/sdk`.
In `package.json`, it will look like this:
```
    "@cognite/sdk": "npm:@cognite/sdk-beta@^X.X.X"
```

This will download `@cognite/sdk-beta` and pretend it is `@cognite/sdk`.
With the beta package installed under an alias, you don't need to modify your code
to access beta features. Import the `CogniteClient` as you normally would:
```js
import { CogniteClient } from '@cognite/sdk';
```

## Documentation

See the reference doc of `CogniteClient` [here](https://cognitedata.github.io/cognite-sdk-js/beta/classes/_beta_src_cogniteclient_.cogniteclient.html).

The beta API is a superset of stable. See the [stable readme](https://github.com/cognitedata/cognite-sdk-js/blob/master/packages/stable/README.md).


<!-- SOURCE_END: cognite-sdk-js/packages\beta\README.md -->

================================================================================


<!-- SOURCE_START: cognite-sdk-js/packages\codegen\CHANGELOG.md -->
## File: cognite-sdk-js/packages\codegen\CHANGELOG.md

# Change Log

All notable changes to this project will be documented in this file.
See [Conventional Commits](https://conventionalcommits.org) for commit guidelines.

# 0.0.0-development.1 (2025-09-29)


### Bug Fixes

* add codegen support for CusrorAndAsyncIterator  ([#907](https://github.com/cognitedata/cognite-sdk-js/issues/907)) ([3cde0d3](https://github.com/cognitedata/cognite-sdk-js/commit/3cde0d3df8a4135f38849a2cd6408ded32246065)), closes [#908](https://github.com/cognitedata/cognite-sdk-js/issues/908)


### Features

* add vision api ([#855](https://github.com/cognitedata/cognite-sdk-js/issues/855)) ([1d9116e](https://github.com/cognitedata/cognite-sdk-js/commit/1d9116ed53405c34b6c47b348644d093c61dde33))
* **annotations:** add annotation payload types ([#905](https://github.com/cognitedata/cognite-sdk-js/issues/905)) ([990ea8f](https://github.com/cognitedata/cognite-sdk-js/commit/990ea8fda85bc26e9a50bd9160c6b8a76e26ed55))
* code generation for types based on openapi doc ([#801](https://github.com/cognitedata/cognite-sdk-js/issues/801)) ([07eb308](https://github.com/cognitedata/cognite-sdk-js/commit/07eb3087705c550758fcc9e1b12ea43428ecf79d)), closes [#820](https://github.com/cognitedata/cognite-sdk-js/issues/820)
* release v10 [release] ([#1243](https://github.com/cognitedata/cognite-sdk-js/issues/1243)) ([b3d5595](https://github.com/cognitedata/cognite-sdk-js/commit/b3d55951bb2a302981fa67ce694d21749461386a)), closes [#1135](https://github.com/cognitedata/cognite-sdk-js/issues/1135) [#1144](https://github.com/cognitedata/cognite-sdk-js/issues/1144) [#1148](https://github.com/cognitedata/cognite-sdk-js/issues/1148) [#1150](https://github.com/cognitedata/cognite-sdk-js/issues/1150) [#1151](https://github.com/cognitedata/cognite-sdk-js/issues/1151) [#1152](https://github.com/cognitedata/cognite-sdk-js/issues/1152) [#1153](https://github.com/cognitedata/cognite-sdk-js/issues/1153) [#1029](https://github.com/cognitedata/cognite-sdk-js/issues/1029)
* update openapi spec to bump types for docs, annotations and vision ([#1021](https://github.com/cognitedata/cognite-sdk-js/issues/1021)) ([#1024](https://github.com/cognitedata/cognite-sdk-js/issues/1024)) ([e337d73](https://github.com/cognitedata/cognite-sdk-js/commit/e337d73c9e6a1888e165d8b4a59bfe94b522bbb3))


### Reverts

* "feat: update openapi spec to bump types fo docs, annotations and vision" ([#1021](https://github.com/cognitedata/cognite-sdk-js/issues/1021)) ([b908def](https://github.com/cognitedata/cognite-sdk-js/commit/b908defed27eb610c8c7b97d2c24013da735422f))


### BREAKING CHANGES

* es6 module (vs es5) and typescript 3 -> 5

* chore: release pre-release from the release-v10 branch

* test: skip flaky alerts test

* chore: trigger [release]

* chore(release): publish new package versions [skip ci]

 - @cognite/sdk-alpha@1.0.0-rc.0
 - @cognite/sdk-beta@6.0.0-rc.0
 - @cognite/sdk-core@5.0.0-rc.0
 - @cognite/sdk-playground@8.0.0-rc.0
 - @cognite/sdk@10.0.0-rc.0
* documents, annotations and vision types have been updated

Thanks to @danlevings for his work in commit: https://github.com/cognitedata/cognite-sdk-js/commit/ac6efec6e884d0cb436be7c2e91f3aa78bfbe15c





# 0.0.0-development.0 (2025-09-25)


### Bug Fixes

* add codegen support for CusrorAndAsyncIterator  ([#907](https://github.com/cognitedata/cognite-sdk-js/issues/907)) ([3cde0d3](https://github.com/cognitedata/cognite-sdk-js/commit/3cde0d3df8a4135f38849a2cd6408ded32246065)), closes [#908](https://github.com/cognitedata/cognite-sdk-js/issues/908)


### Features

* add vision api ([#855](https://github.com/cognitedata/cognite-sdk-js/issues/855)) ([1d9116e](https://github.com/cognitedata/cognite-sdk-js/commit/1d9116ed53405c34b6c47b348644d093c61dde33))
* **annotations:** add annotation payload types ([#905](https://github.com/cognitedata/cognite-sdk-js/issues/905)) ([990ea8f](https://github.com/cognitedata/cognite-sdk-js/commit/990ea8fda85bc26e9a50bd9160c6b8a76e26ed55))
* code generation for types based on openapi doc ([#801](https://github.com/cognitedata/cognite-sdk-js/issues/801)) ([07eb308](https://github.com/cognitedata/cognite-sdk-js/commit/07eb3087705c550758fcc9e1b12ea43428ecf79d)), closes [#820](https://github.com/cognitedata/cognite-sdk-js/issues/820)
* release v10 [release] ([#1243](https://github.com/cognitedata/cognite-sdk-js/issues/1243)) ([b3d5595](https://github.com/cognitedata/cognite-sdk-js/commit/b3d55951bb2a302981fa67ce694d21749461386a)), closes [#1135](https://github.com/cognitedata/cognite-sdk-js/issues/1135) [#1144](https://github.com/cognitedata/cognite-sdk-js/issues/1144) [#1148](https://github.com/cognitedata/cognite-sdk-js/issues/1148) [#1150](https://github.com/cognitedata/cognite-sdk-js/issues/1150) [#1151](https://github.com/cognitedata/cognite-sdk-js/issues/1151) [#1152](https://github.com/cognitedata/cognite-sdk-js/issues/1152) [#1153](https://github.com/cognitedata/cognite-sdk-js/issues/1153) [#1029](https://github.com/cognitedata/cognite-sdk-js/issues/1029)
* update openapi spec to bump types for docs, annotations and vision ([#1021](https://github.com/cognitedata/cognite-sdk-js/issues/1021)) ([#1024](https://github.com/cognitedata/cognite-sdk-js/issues/1024)) ([e337d73](https://github.com/cognitedata/cognite-sdk-js/commit/e337d73c9e6a1888e165d8b4a59bfe94b522bbb3))


### Reverts

* "feat: update openapi spec to bump types fo docs, annotations and vision" ([#1021](https://github.com/cognitedata/cognite-sdk-js/issues/1021)) ([b908def](https://github.com/cognitedata/cognite-sdk-js/commit/b908defed27eb610c8c7b97d2c24013da735422f))


### BREAKING CHANGES

* es6 module (vs es5) and typescript 3 -> 5

* chore: release pre-release from the release-v10 branch

* test: skip flaky alerts test

* chore: trigger [release]

* chore(release): publish new package versions [skip ci]

 - @cognite/sdk-alpha@1.0.0-rc.0
 - @cognite/sdk-beta@6.0.0-rc.0
 - @cognite/sdk-core@5.0.0-rc.0
 - @cognite/sdk-playground@8.0.0-rc.0
 - @cognite/sdk@10.0.0-rc.0
* documents, annotations and vision types have been updated

Thanks to @danlevings for his work in commit: https://github.com/cognitedata/cognite-sdk-js/commit/ac6efec6e884d0cb436be7c2e91f3aa78bfbe15c


<!-- SOURCE_END: cognite-sdk-js/packages\codegen\CHANGELOG.md -->

================================================================================


<!-- SOURCE_START: cognite-sdk-js/packages\codegen\README.md -->
## File: cognite-sdk-js/packages\codegen\README.md

# Code generation

> Generate TypeScript types from the Cognite OpenAPI document.

The current version aims to focus only on types, and does not generate
any SDK code for endpoints or tests.

Types are written to `types.gen.ts` for each service (e.g. `documents`)
and automatically exported. Only services configured via a `codegen.json`
file is processed, and will generate types for its given configuration.

## Things to be aware of

1. **Date based fields will be a numeric type**. This can be solved through AST manipulation, and may be fixed in the future.
2. **Changes like renaming component definition in the service-contract/openapi spec, will cause a breaking change in the cognite-sdk-js**. Essentially names are copied from the openapi spec. Treat your openapi spec with a strict fashion to avoid unnecessary breaking changes in the cognite-sdk-js. e.g. renaming your MyBadlyNamedResponse to something more suitable, will result in a breaking change and is discouraged.

## OpenAPI document snapshot

In order to have build reproducability we keep a copy of the OpenAPI document
in this repository (named `.cognite-openapi-snapshot.json`). To generate new types
it will need to be updated to the latest available. E.g.:

```console
yarn codegen fetch-latest --package=stable
```

In the future this can be automated so changes to the OpenAPI document is
frequently made automatically available via SDK updates.

## Generate updated types

After updating the OpenAPI document snapshot types must be updated. E.g:

```console
yarn codegen generate-types --package=stable
```

## Overriding the OpenAPI document

It is possible to override the OpenAPI document used to generate
types by specifying a path to the document instead of the API version.

The `codegen.json` file must be changed to contain:

```json
{
  "snapshot": {
    "path": "path/to/openapi-doc.json"
  }
}
```

This can be used to test local changes to the OpenAPI document, or to 
lock a service to an older OpenAPI document should the latest version
cause issues.

## Enable a service for type generation

> Code generation must first be enable for a package

```console
yarn codegen enable --package stable --service my-service
```

This will generate a `codegen.json` file for the given service
so types will be created on next run.

The types extracted are based on the paths in the OpenAPI document
that matches the name of the service. It is currently not possible
to select individually paths other than this.

You may have to remove existing types to avoid conflicts.

## Disable a service for type generation

```console
yarn codegen disable --package stable --service my-service
```

This will will cleanup files within the service folder. Note that you
must re-generate types afterwards to cleanup export statements.


<!-- SOURCE_END: cognite-sdk-js/packages\codegen\README.md -->

================================================================================


<!-- SOURCE_START: cognite-sdk-js/packages\core\CHANGELOG.md -->
## File: cognite-sdk-js/packages\core\CHANGELOG.md

# Change Log

All notable changes to this project will be documented in this file.
See [Conventional Commits](https://conventionalcommits.org) for commit guidelines.

## [5.1.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@5.1.2...@cognite/sdk-core@5.1.3) (2025-11-27)

**Note:** Version bump only for package @cognite/sdk-core





## [5.1.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@5.1.1...@cognite/sdk-core@5.1.2) (2025-11-04)

**Note:** Version bump only for package @cognite/sdk-core





## [5.1.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@5.1.0...@cognite/sdk-core@5.1.1) (2025-09-29)

**Note:** Version bump only for package @cognite/sdk-core





# [5.1.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@5.0.1...@cognite/sdk-core@5.1.0) (2025-09-25)


### Features

* replace fixed HTTP request retry delay with "exponential jitter delay" ([#1287](https://github.com/cognitedata/cognite-sdk-js/issues/1287)) ([aab7ebb](https://github.com/cognitedata/cognite-sdk-js/commit/aab7ebbe1c7a69336dc52de6a53f8e4a2953e8a8))





## [5.0.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@5.0.0...@cognite/sdk-core@5.0.1) (2025-09-22)

**Note:** Version bump only for package @cognite/sdk-core





# [5.0.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.10.7...@cognite/sdk-core@5.0.0) (2025-02-03)


### Bug Fixes

* **core:** security patch ([#1219](https://github.com/cognitedata/cognite-sdk-js/issues/1219)) ([edab269](https://github.com/cognitedata/cognite-sdk-js/commit/edab269370b13a18203fbdfa13f62be64c2bb8d7))


### Features

* release v10 [release] ([#1243](https://github.com/cognitedata/cognite-sdk-js/issues/1243)) ([b3d5595](https://github.com/cognitedata/cognite-sdk-js/commit/b3d55951bb2a302981fa67ce694d21749461386a)), closes [#1135](https://github.com/cognitedata/cognite-sdk-js/issues/1135) [#1144](https://github.com/cognitedata/cognite-sdk-js/issues/1144) [#1148](https://github.com/cognitedata/cognite-sdk-js/issues/1148) [#1150](https://github.com/cognitedata/cognite-sdk-js/issues/1150) [#1151](https://github.com/cognitedata/cognite-sdk-js/issues/1151) [#1152](https://github.com/cognitedata/cognite-sdk-js/issues/1152) [#1153](https://github.com/cognitedata/cognite-sdk-js/issues/1153) [#1029](https://github.com/cognitedata/cognite-sdk-js/issues/1029)


### BREAKING CHANGES

* es6 module (vs es5) and typescript 3 -> 5

* chore: release pre-release from the release-v10 branch

* test: skip flaky alerts test

* chore: trigger [release]

* chore(release): publish new package versions [skip ci]

 - @cognite/sdk-alpha@1.0.0-rc.0
 - @cognite/sdk-beta@6.0.0-rc.0
 - @cognite/sdk-core@5.0.0-rc.0
 - @cognite/sdk-playground@8.0.0-rc.0
 - @cognite/sdk@10.0.0-rc.0





## [4.10.7](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.10.6...@cognite/sdk-core@4.10.7) (2025-01-17)

**Note:** Version bump only for package @cognite/sdk-core





## [4.10.6](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.10.4...@cognite/sdk-core@4.10.6) (2024-08-26)


### Bug Fixes

* dummy release to override a bad one ([#1139](https://github.com/cognitedata/cognite-sdk-js/issues/1139)) ([5b036da](https://github.com/cognitedata/cognite-sdk-js/commit/5b036dabd4630b45d51558ee7f95d951c7227137))





## [4.10.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.10.3...@cognite/sdk-core@4.10.4) (2024-08-22)

**Note:** Version bump only for package @cognite/sdk-core





## [4.10.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.10.2...@cognite/sdk-core@4.10.3) (2024-08-20)

**Note:** Version bump only for package @cognite/sdk-core

## [4.10.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.10.1...@cognite/sdk-core@4.10.2) (2024-08-19)

**Note:** Version bump only for package @cognite/sdk-core

## [4.10.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.10.0...@cognite/sdk-core@4.10.1) (2024-01-22)

### Bug Fixes

- use named parameters for postInParallelWithAutomaticChunking ([#1048](https://github.com/cognitedata/cognite-sdk-js/issues/1048)) ([db561d0](https://github.com/cognitedata/cognite-sdk-js/commit/db561d0d2891a0ed61d2116cba86bd256211547a))

# [4.10.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.9.0...@cognite/sdk-core@4.10.0) (2023-11-28)

### Features

- monitoring tasks upsert ([#1042](https://github.com/cognitedata/cognite-sdk-js/issues/1042)) ([7e0e24c](https://github.com/cognitedata/cognite-sdk-js/commit/7e0e24cfb5c4d91302e54a270457b8d9b914ffd4))

# [4.9.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.8.3...@cognite/sdk-core@4.9.0) (2023-04-28)

### Features

- **core:** add optional retryValidator to override the default one ([#1005](https://github.com/cognitedata/cognite-sdk-js/issues/1005)) ([a425990](https://github.com/cognitedata/cognite-sdk-js/commit/a425990ed8d3f92774fc03bd45b941d1fa0647c8))

## [4.8.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.8.2...@cognite/sdk-core@4.8.3) (2023-04-20)

### Bug Fixes

- **core:** remove package-lock.json to fix CVE ([#1008](https://github.com/cognitedata/cognite-sdk-js/issues/1008)) ([b0ba725](https://github.com/cognitedata/cognite-sdk-js/commit/b0ba7259b6cc25bfa9609fae9d93061a2365e3aa))

## [4.8.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.8.1...@cognite/sdk-core@4.8.2) (2022-11-17)

**Note:** Version bump only for package @cognite/sdk-core

## [4.8.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.8.0...@cognite/sdk-core@4.8.1) (2022-09-26)

### Bug Fixes

- override auth header while retrying ([#896](https://github.com/cognitedata/cognite-sdk-js/issues/896)) ([25592fe](https://github.com/cognitedata/cognite-sdk-js/commit/25592fe990e315bb2aa976f428b2cba88448a580))

# [4.8.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.7.0...@cognite/sdk-core@4.8.0) (2022-09-23)

### Features

- allow overriding retry validator at request level ([#876](https://github.com/cognitedata/cognite-sdk-js/issues/876)) ([39807e9](https://github.com/cognitedata/cognite-sdk-js/commit/39807e9e2b2380fcc8d842e8634f13d10f181e25))

# [4.7.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.6.7...@cognite/sdk-core@4.7.0) (2022-09-23)

### Features

- update 401 handler logic ([#878](https://github.com/cognitedata/cognite-sdk-js/issues/878)) ([aa40002](https://github.com/cognitedata/cognite-sdk-js/commit/aa40002f0384c6146f63d2a80a8fe2c0cbdef40f))

## [4.6.7](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.6.6...@cognite/sdk-core@4.6.7) (2022-09-23)

### Bug Fixes

- ensure response ordering matches request when chunking a request ([#893](https://github.com/cognitedata/cognite-sdk-js/issues/893)) ([77eb797](https://github.com/cognitedata/cognite-sdk-js/commit/77eb7976abb21029c22a16c0ceeb22d577d1ce68))

## [4.6.6](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.6.5...@cognite/sdk-core@4.6.6) (2022-09-12)

### Bug Fixes

- expired token check for adfs ([#874](https://github.com/cognitedata/cognite-sdk-js/issues/874)) ([af03c73](https://github.com/cognitedata/cognite-sdk-js/commit/af03c73fd9d69671dc7bd1c3169eee9ae17e61fe))

## [4.6.5](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.6.4...@cognite/sdk-core@4.6.5) (2022-09-07)

### Bug Fixes

- make sure sessionKey for adfs is consistent across class instances ([#865](https://github.com/cognitedata/cognite-sdk-js/issues/865)) ([31e0d1e](https://github.com/cognitedata/cognite-sdk-js/commit/31e0d1ec961b49a4f3cf06e07f6b2642dc1f81f6))

## [4.6.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.6.3...@cognite/sdk-core@4.6.4) (2022-09-07)

### Bug Fixes

- don't retry 401 requests ([#868](https://github.com/cognitedata/cognite-sdk-js/issues/868)) ([4d17461](https://github.com/cognitedata/cognite-sdk-js/commit/4d174616ccf8ddfafed8a45b64d99e5ceaa06ce7))

## [4.6.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.6.2...@cognite/sdk-core@4.6.3) (2022-09-06)

### Bug Fixes

- removing messages from authentication ([#866](https://github.com/cognitedata/cognite-sdk-js/issues/866)) ([cc61fce](https://github.com/cognitedata/cognite-sdk-js/commit/cc61fce8557039e8fb4e0cf3fe57deacdb3b0402))

## [4.6.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.6.1...@cognite/sdk-core@4.6.2) (2022-08-31)

### Bug Fixes

- adfs login when iframe doesn't work by storing tokens ([#864](https://github.com/cognitedata/cognite-sdk-js/issues/864)) ([c9e1860](https://github.com/cognitedata/cognite-sdk-js/commit/c9e1860a598dd8f4a8f91ef538306f03e9a3f709))

## [4.6.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.6.0...@cognite/sdk-core@4.6.1) (2022-08-21)

**Note:** Version bump only for package @cognite/sdk-core

# [4.6.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.5.1...@cognite/sdk-core@4.6.0) (2022-08-05)

### Features

- add react-auth-wrapper support ([#849](https://github.com/cognitedata/cognite-sdk-js/issues/849)) ([3166687](https://github.com/cognitedata/cognite-sdk-js/commit/3166687af62446cbb5dba4085ef47c1964a70e19))

## [4.5.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.5.0...@cognite/sdk-core@4.5.1) (2022-07-07)

### Bug Fixes

- return token instead of bearertoken ([#834](https://github.com/cognitedata/cognite-sdk-js/issues/834)) ([2829149](https://github.com/cognitedata/cognite-sdk-js/commit/2829149c906b1ecd2c1f28808738ddc28f8f798c))

# [4.5.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.4.0...@cognite/sdk-core@4.5.0) (2022-06-17)

### Features

- noAuthMode support ([#824](https://github.com/cognitedata/cognite-sdk-js/issues/824)) ([69a33ce](https://github.com/cognitedata/cognite-sdk-js/commit/69a33ce1f352706a9e36b119a99be9132e9e3c9c))

# [4.4.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.3.0...@cognite/sdk-core@4.4.0) (2022-06-09)

### Features

- auth wrapper as a provider ([#816](https://github.com/cognitedata/cognite-sdk-js/issues/816)) ([4c2fc03](https://github.com/cognitedata/cognite-sdk-js/commit/4c2fc0367e1aba347c6d8ab0a3b1e5f3a15a7a2d))

# [4.3.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.2.0...@cognite/sdk-core@4.3.0) (2022-06-02)

### Features

- automatic token management with @cognite/auth-wrapper ([#796](https://github.com/cognitedata/cognite-sdk-js/issues/796)) ([d13c53f](https://github.com/cognitedata/cognite-sdk-js/commit/d13c53f9d5a5e65d6e2fc7d1a0e2efe2d36e678c))
- automatic token management with @cognite/auth-wrapper ([#814](https://github.com/cognitedata/cognite-sdk-js/issues/814)) ([d2c3a30](https://github.com/cognitedata/cognite-sdk-js/commit/d2c3a3083746e234f5dfc3e290188414d9ea17a6))

### Reverts

- Revert "feat: automatic token management with @cognite/auth-wrapper (#796)" (#813) ([0a8a61c](https://github.com/cognitedata/cognite-sdk-js/commit/0a8a61c2b2c3d992f80b40f6282b20595537edac)), closes [#796](https://github.com/cognitedata/cognite-sdk-js/issues/796) [#813](https://github.com/cognitedata/cognite-sdk-js/issues/813)

# [4.2.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.1.7...@cognite/sdk-core@4.2.0) (2022-05-27)

### Features

- **documents:** aggregate api ([#793](https://github.com/cognitedata/cognite-sdk-js/issues/793)) ([445c39f](https://github.com/cognitedata/cognite-sdk-js/commit/445c39ff90822a357e20e624828bcd6e2ee36369))

## [4.1.7](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.1.6...@cognite/sdk-core@4.1.7) (2022-05-23)

### Bug Fixes

- wrong token returned ([#807](https://github.com/cognitedata/cognite-sdk-js/issues/807)) ([420b741](https://github.com/cognitedata/cognite-sdk-js/commit/420b74150cec75ab6ef03f0c119f9dc2a96280bf))

## [4.1.6](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.1.5...@cognite/sdk-core@4.1.6) (2022-05-20)

### Bug Fixes

- 401 not authorized ([#804](https://github.com/cognitedata/cognite-sdk-js/issues/804)) ([0b4d8be](https://github.com/cognitedata/cognite-sdk-js/commit/0b4d8beb666aff24dbb1309b552193cdd1d80f94))

## [4.1.5](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.1.4...@cognite/sdk-core@4.1.5) (2022-05-20)

### Reverts

- Revert "fix: fixing 401 problem (#800)" (#802) ([341b68d](https://github.com/cognitedata/cognite-sdk-js/commit/341b68dd271b7781489343637c861921d647b483)), closes [#800](https://github.com/cognitedata/cognite-sdk-js/issues/800) [#802](https://github.com/cognitedata/cognite-sdk-js/issues/802)

## [4.1.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.1.3...@cognite/sdk-core@4.1.4) (2022-05-19)

### Bug Fixes

- fixing 401 problem ([#800](https://github.com/cognitedata/cognite-sdk-js/issues/800)) ([b5789b1](https://github.com/cognitedata/cognite-sdk-js/commit/b5789b1a15ed9fb574c636c5515c85a2868b60a2))

## [4.1.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.1.2...@cognite/sdk-core@4.1.3) (2022-05-16)

### Bug Fixes

- fixing missing token when call sdk methods using Promises ([#789](https://github.com/cognitedata/cognite-sdk-js/issues/789)) ([6a6d62a](https://github.com/cognitedata/cognite-sdk-js/commit/6a6d62a6744f94bef46e8fe1b397d07e8f9de873))

## [4.1.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.1.1...@cognite/sdk-core@4.1.2) (2022-05-13)

### Bug Fixes

- adding @types/geojson to core package ([#780](https://github.com/cognitedata/cognite-sdk-js/issues/780)) ([b8b5715](https://github.com/cognitedata/cognite-sdk-js/commit/b8b57155f1e40df33743a61bad8fce63fe2d247f))

## [4.1.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.1.0...@cognite/sdk-core@4.1.1) (2022-03-01)

**Note:** Version bump only for package @cognite/sdk-core

# [4.1.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.0.4...@cognite/sdk-core@4.1.0) (2022-01-10)

### Features

- **core:** add generic for id, path to aggregate ([#726](https://github.com/cognitedata/cognite-sdk-js/issues/726)) ([b927e24](https://github.com/cognitedata/cognite-sdk-js/commit/b927e24c044eb934bbc0fd74975b64660f8b599f))

## [4.0.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.0.3...@cognite/sdk-core@4.0.4) (2021-12-21)

### Bug Fixes

- use case-insensitive lookup for http header ([#724](https://github.com/cognitedata/cognite-sdk-js/issues/724)) ([e0f803f](https://github.com/cognitedata/cognite-sdk-js/commit/e0f803f79ae3bae2e6282bd8bfacab40bbf38050))

## [4.0.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.0.2...@cognite/sdk-core@4.0.3) (2021-12-07)

### Bug Fixes

- retry 429 responses, and fallback to text() if responseHandler fails [BEST-902] ([#705](https://github.com/cognitedata/cognite-sdk-js/issues/705)) ([f02946f](https://github.com/cognitedata/cognite-sdk-js/commit/f02946ff51382bec2a291f5f746dbf87a3bcbec1))

## [4.0.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.0.1...@cognite/sdk-core@4.0.2) (2021-10-29)

**Note:** Version bump only for package @cognite/sdk-core

## [4.0.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@4.0.0...@cognite/sdk-core@4.0.1) (2021-10-19)

### Bug Fixes

- **core:** re-export project name from sdk ([#706](https://github.com/cognitedata/cognite-sdk-js/issues/706)) ([34341f0](https://github.com/cognitedata/cognite-sdk-js/commit/34341f09222c0bb35578fa02f76aecf4aefbf01f))

# [4.0.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@3.4.1...@cognite/sdk-core@4.0.0) (2021-10-12)

### Features

- **auth:** re-release auth patch ([#700](https://github.com/cognitedata/cognite-sdk-js/issues/700)) ([a53c40d](https://github.com/cognitedata/cognite-sdk-js/commit/a53c40ddd7eca5d2dee9149f5df0b2e533d19575))

### BREAKING CHANGES

- **auth:** release v6

re-release (revert reversion) of "feat(core): move authentication out of CogniteClient"
https://github.com/cognitedata/cognite-sdk-js/pull/687

This reverts commit 72e1ecb61603e0ac3926124c26f4e009df88f020.

Co-authored-by: Vegard Økland <vegard.okland@cognite.com>

## [3.4.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@3.4.0...@cognite/sdk-core@3.4.1) (2021-10-12)

### Bug Fixes

- **release:** undo major version release without major version bump ([#697](https://github.com/cognitedata/cognite-sdk-js/issues/697)) ([72e1ecb](https://github.com/cognitedata/cognite-sdk-js/commit/72e1ecb61603e0ac3926124c26f4e009df88f020)), closes [#687](https://github.com/cognitedata/cognite-sdk-js/issues/687)

# [3.4.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@3.3.0...@cognite/sdk-core@3.4.0) (2021-10-12)

### Features

- **core:** move authentication out of CogniteClient ([#687](https://github.com/cognitedata/cognite-sdk-js/issues/687)) ([879ed31](https://github.com/cognitedata/cognite-sdk-js/commit/879ed31d05dd6d6f4b691b99eaca5fa7363e96e6))

# [3.3.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@3.2.5...@cognite/sdk-core@3.3.0) (2021-10-07)

### Features

- **spatial:** adds spatial API to playground ([#680](https://github.com/cognitedata/cognite-sdk-js/issues/680)) ([e0b2d1d](https://github.com/cognitedata/cognite-sdk-js/commit/e0b2d1dd6ac85eb6fd8a6d6fce61e26cc909c5e7))

## [3.2.5](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@3.2.4...@cognite/sdk-core@3.2.5) (2021-09-20)

### Bug Fixes

- better error message for flow param ([#681](https://github.com/cognitedata/cognite-sdk-js/issues/681)) ([31bb98b](https://github.com/cognitedata/cognite-sdk-js/commit/31bb98b8078ce1f30799fcc499bed23b2891c2d1))

## [3.2.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@3.2.3...@cognite/sdk-core@3.2.4) (2021-09-03)

### Bug Fixes

- remove test files in published packages ([#673](https://github.com/cognitedata/cognite-sdk-js/issues/673)) ([cf6deae](https://github.com/cognitedata/cognite-sdk-js/commit/cf6deae6d80d0bfb3b2b3e8a8db6c30a1bb1ec0a))

## [3.2.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@3.2.2...@cognite/sdk-core@3.2.3) (2021-08-20)

**Note:** Version bump only for package @cognite/sdk-core

## [3.2.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@3.2.1...@cognite/sdk-core@3.2.2) (2021-08-11)

### Bug Fixes

- **core:** headers set by the sdk must respect case insensitive keys ([#627](https://github.com/cognitedata/cognite-sdk-js/issues/627)) ([02703c5](https://github.com/cognitedata/cognite-sdk-js/commit/02703c53fc7e8779a8f01cc2da403d32208d9acb))

## [3.2.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@3.2.0...@cognite/sdk-core@3.2.1) (2021-08-10)

### Bug Fixes

- **core:** remove slash suffix from uris [release] ([#628](https://github.com/cognitedata/cognite-sdk-js/issues/628)) ([fe65d57](https://github.com/cognitedata/cognite-sdk-js/commit/fe65d57a4ce9f6fc37ce37ad2d295ef8006c603c))

# [3.2.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@3.1.2...@cognite/sdk-core@3.2.0) (2021-07-21)

### Features

- add custom login flow method [release] ([#620](https://github.com/cognitedata/cognite-sdk-js/issues/620)) ([d99a2aa](https://github.com/cognitedata/cognite-sdk-js/commit/d99a2aa25aa6a271c09c4cfc944e825047dec72a))

## [3.1.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@3.1.1...@cognite/sdk-core@3.1.2) (2021-07-21)

### Bug Fixes

- silentlogin added for oidc ([#619](https://github.com/cognitedata/cognite-sdk-js/issues/619)) ([b7977eb](https://github.com/cognitedata/cognite-sdk-js/commit/b7977eba255cda3b11d792b2934933854556c67c))

## [3.1.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@3.1.0...@cognite/sdk-core@3.1.1) (2021-07-21)

### Bug Fixes

- revert client credentials [release] ([#618](https://github.com/cognitedata/cognite-sdk-js/issues/618)) ([08a0d8c](https://github.com/cognitedata/cognite-sdk-js/commit/08a0d8cf01105aa326e73d93c703c7fc0ee6f68d))

# [3.1.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@3.0.0...@cognite/sdk-core@3.1.0) (2021-07-20)

### Features

- client credentials flow [release] ([#607](https://github.com/cognitedata/cognite-sdk-js/issues/607)) ([28ed890](https://github.com/cognitedata/cognite-sdk-js/commit/28ed890ebf15da151e05cf0c487bca4b91b8ea96))
- expose id token of AAD/ADFS/OIDC in one method ([#616](https://github.com/cognitedata/cognite-sdk-js/issues/616)) ([5179796](https://github.com/cognitedata/cognite-sdk-js/commit/5179796f83c51d6cec4323bf7ed06e6ea3df2434))

# [3.0.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@2.2.0...@cognite/sdk-core@3.0.0) (2021-07-15)

### Features

- **core:** add oidc auth code flow [release] ([#587](https://github.com/cognitedata/cognite-sdk-js/issues/587)) ([0cc44aa](https://github.com/cognitedata/cognite-sdk-js/commit/0cc44aa82b7d7461e8629fe2e712f743bf6c7138))

### BREAKING CHANGES

- **core:** stop silencing errors from aad

- **core:** change loginWithOAuth API signature

# [2.2.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@2.1.4...@cognite/sdk-core@2.2.0) (2021-07-14)

### Features

- add api version parameter to BaseCogniteClient [release] ([#610](https://github.com/cognitedata/cognite-sdk-js/issues/610)) ([11aca94](https://github.com/cognitedata/cognite-sdk-js/commit/11aca9498a8aef23c7855d675dc9d4d6cf70f10c))

## [2.1.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@2.1.3...@cognite/sdk-core@2.1.4) (2021-06-30)

### Reverts

- Revert "feat!: add client credentials flow (#589)" (#604) ([12d65a4](https://github.com/cognitedata/cognite-sdk-js/commit/12d65a41e919409582d76a3a59798737808cefac)), closes [#589](https://github.com/cognitedata/cognite-sdk-js/issues/589) [#604](https://github.com/cognitedata/cognite-sdk-js/issues/604)

## [2.1.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@2.1.2...@cognite/sdk-core@2.1.3) (2021-06-30)

**Note:** Version bump only for package @cognite/sdk-core

## [2.1.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@2.1.1...@cognite/sdk-core@2.1.2) (2021-04-26)

### Bug Fixes

- **core:** don't check if its https if its not a browser [release] ([#524](https://github.com/cognitedata/cognite-sdk-js/issues/524)) ([4ed1528](https://github.com/cognitedata/cognite-sdk-js/commit/4ed15281a21047b8230d9022b3cb9ab23768a3cc))

## [2.1.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@2.1.0...@cognite/sdk-core@2.1.1) (2021-04-13)

**Note:** Version bump only for package @cognite/sdk-core

# [2.1.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@2.0.0...@cognite/sdk-core@2.1.0) (2021-04-08)

### Features

- **adfs:** add auth flow for adfs ([#508](https://github.com/cognitedata/cognite-sdk-js/issues/508)) ([1130f8d](https://github.com/cognitedata/cognite-sdk-js/commit/1130f8d0d463144dff67480fdb960c531e9816ee))

# [2.0.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@1.2.2...@cognite/sdk-core@2.0.0) (2021-03-25)

### Features

- breaking changes to login with OAuth [release] ([#504](https://github.com/cognitedata/cognite-sdk-js/issues/504)) ([383d7f6](https://github.com/cognitedata/cognite-sdk-js/commit/383d7f6d0888a8acb8121af6cf39d1adbf724882))

## [1.2.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@1.2.1...@cognite/sdk-core@1.2.2) (2021-03-25)

### Bug Fixes

- revert breaking changes being published as patch ([#503](https://github.com/cognitedata/cognite-sdk-js/issues/503)) ([3b7afd9](https://github.com/cognitedata/cognite-sdk-js/commit/3b7afd94030c75b2122a8e8323678455bcef0a29))

## [1.2.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@1.2.0...@cognite/sdk-core@1.2.1) (2021-03-25)

**Note:** Version bump only for package @cognite/sdk-core

# [1.2.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@1.1.0...@cognite/sdk-core@1.2.0) (2021-03-05)

### Features

- **aad:** use LS state as source of truth ([#493](https://github.com/cognitedata/cognite-sdk-js/issues/493)) ([a03404c](https://github.com/cognitedata/cognite-sdk-js/commit/a03404c084d73c55ab22aeb29cd6724772c079a5))

# [1.1.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@1.0.6...@cognite/sdk-core@1.1.0) (2021-03-02)

### Features

- **aad-auth:** add ability to login using AzureAD auth flow ([#487](https://github.com/cognitedata/cognite-sdk-js/issues/487)) ([4cbadf1](https://github.com/cognitedata/cognite-sdk-js/commit/4cbadf1164b8e2ed3ade8fd3e0875d412739e7a3))

## [1.0.6](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@1.0.4...@cognite/sdk-core@1.0.6) (2021-03-01)

### Bug Fixes

- allows CDF authentication from localhost via http ([#489](https://github.com/cognitedata/cognite-sdk-js/issues/489)) ([9257972](https://github.com/cognitedata/cognite-sdk-js/commit/9257972dc87dfd5590df8d6ff253326407a8880a))

## [1.0.5](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@1.0.4...@cognite/sdk-core@1.0.5) (2021-02-26)

### Bug Fixes

- allows CDF authentication from localhost via http ([#489](https://github.com/cognitedata/cognite-sdk-js/issues/489)) ([9257972](https://github.com/cognitedata/cognite-sdk-js/commit/9257972dc87dfd5590df8d6ff253326407a8880a))

## [1.0.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@1.0.3...@cognite/sdk-core@1.0.4) (2020-12-17)

### Bug Fixes

- use granular lodash import ([2144d6f](https://github.com/cognitedata/cognite-sdk-js/commit/2144d6f439ba91ec47ba86052953b0240db7de22))

## [1.0.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@1.0.2...@cognite/sdk-core@1.0.3) (2020-11-18)

### Bug Fixes

- **files:** file upload for browsers in fileApi ([#446](https://github.com/cognitedata/cognite-sdk-js/issues/446)) ([41d2466](https://github.com/cognitedata/cognite-sdk-js/commit/41d2466c57f3ffd8069238556775177f67ac0180))

## [1.0.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@1.0.1...@cognite/sdk-core@1.0.2) (2020-09-17)

### Bug Fixes

- fileAPI upload endpoint ([#427](https://github.com/cognitedata/cognite-sdk-js/issues/427)) ([9c12661](https://github.com/cognitedata/cognite-sdk-js/commit/9c12661c12adc5312d0e5046adf6dd97c11554c8))

## [1.0.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk-core@1.0.0...@cognite/sdk-core@1.0.1) (2020-09-10)

### Bug Fixes

- exports from packages, add guide for making derived SDKs ([#421](https://github.com/cognitedata/cognite-sdk-js/issues/421)) ([a3a2eb0](https://github.com/cognitedata/cognite-sdk-js/commit/a3a2eb03645733c289591b187f19e55b5294fbc7))

# 1.0.0 (2020-08-03)

### Bug Fixes

- **core:** auto-conversion timestamp to Date ([2f06b60](https://github.com/cognitedata/cognite-sdk-js/commit/2f06b604f8c6276466d3105e60892a266eb2a4f7))

### Features

- removed AssetClass, AssetList, TimeseriesClass, TimeSeriesList ([9315a95](https://github.com/cognitedata/cognite-sdk-js/commit/9315a95360561429af2e6f050a1e13f9ac9a2979))

### BREAKING CHANGES

- **core:** Raw API doesn't automatically convert timestamp columns to Date objects.

Full list of column names affected: createdTime, lastUpdatedTime, uploadedTime, deletedTime, timestamp, sourceCreatedTime, sourceModifiedTime.

- Helper classes has been removed:

* AssetClass
* AssetList
* TimeseriesClass
* TimeSeriesList

Learn more: ./guides/MIGRATION_GUIDE_2xx_3xx.md


<!-- SOURCE_END: cognite-sdk-js/packages\core\CHANGELOG.md -->

================================================================================


<!-- SOURCE_START: cognite-sdk-js/packages\core\README.md -->
## File: cognite-sdk-js/packages\core\README.md

A core library used by Cognite Javascript SDKs, published as `@cognite/sdk-core`.
It is used in the official stable and beta SDK and can also be used to create custom SDKs and alpha builds.

See the repository README [here](https://github.com/cognitedata/cognite-sdk-js#readme).

See the stable SDK (`@cognite/sdk`) [here](https://github.com/cognitedata/cognite-sdk-js/blob/master/packages/stable/README.md).

See the beta SDK (`@cognite/sdk-beta`) [here](https://github.com/cognitedata/cognite-sdk-js/blob/master/packages/beta/README.md).


<!-- SOURCE_END: cognite-sdk-js/packages\core\README.md -->

================================================================================


<!-- SOURCE_START: cognite-sdk-js/packages\stable\CHANGELOG.md -->
## File: cognite-sdk-js/packages\stable\CHANGELOG.md

# Change Log

All notable changes to this project will be documented in this file.
See [Conventional Commits](https://conventionalcommits.org) for commit guidelines.

## [10.4.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@10.3.0...@cognite/sdk@10.4.0) (2025-11-27)


### Features

* **streams:** add support for Streams API ([#1338](https://github.com/cognitedata/cognite-sdk-js/issues/1338)) ([683d581](https://github.com/cognitedata/cognite-sdk-js/commit/683d581f24))





# [10.3.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@10.2.0...@cognite/sdk@10.3.0) (2025-11-04)


### Features

* **auth-4121:** add support for sessionAcl proto type ([#1296](https://github.com/cognitedata/cognite-sdk-js/issues/1296)) ([54ef860](https://github.com/cognitedata/cognite-sdk-js/commit/54ef8603d9895f7014a4a9802a0362beb0f3b953))





# [10.2.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@10.1.4...@cognite/sdk@10.2.0) (2025-09-29)


### Features

* **stable:** implement inspect method in instances API ([#1295](https://github.com/cognitedata/cognite-sdk-js/issues/1295)) ([d27bf77](https://github.com/cognitedata/cognite-sdk-js/commit/d27bf77cf2cfdb9a9de22a9251b3892de337798f))
* **stable:** update container API types ([#1293](https://github.com/cognitedata/cognite-sdk-js/issues/1293)) ([bc68645](https://github.com/cognitedata/cognite-sdk-js/commit/bc68645ea6db7e6e490a29102fab1d4e5e5ede36))
* **stable:** update models API types ([#1292](https://github.com/cognitedata/cognite-sdk-js/issues/1292)) ([0a2ddfc](https://github.com/cognitedata/cognite-sdk-js/commit/0a2ddfc5201a9a47881a36482e6cbdeb637e5428))
* **stable:** update types for instances API ([#1291](https://github.com/cognitedata/cognite-sdk-js/issues/1291)) ([469ffae](https://github.com/cognitedata/cognite-sdk-js/commit/469ffae879ae8e703a0a1d15f4bb00572844c6c0))
* **stable:** update view API types ([#1294](https://github.com/cognitedata/cognite-sdk-js/issues/1294)) ([e74596e](https://github.com/cognitedata/cognite-sdk-js/commit/e74596eb73586d8c82a24cfb7a9eb95142aab320))





## [10.1.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@10.1.3...@cognite/sdk@10.1.4) (2025-09-25)

**Note:** Version bump only for package @cognite/sdk





## [10.1.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@10.1.2...@cognite/sdk@10.1.3) (2025-09-22)

**Note:** Version bump only for package @cognite/sdk





## [10.1.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@10.1.1...@cognite/sdk@10.1.2) (2025-09-19)

**Note:** Version bump only for package @cognite/sdk





## [10.1.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@10.1.0...@cognite/sdk@10.1.1) (2025-09-17)

**Note:** Version bump only for package @cognite/sdk





# [10.1.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@10.0.0...@cognite/sdk@10.1.0) (2025-09-11)


### Features

* **datapoints-latest:** add automatic chunking [release] ([#1283](https://github.com/cognitedata/cognite-sdk-js/issues/1283)) ([7f53521](https://github.com/cognitedata/cognite-sdk-js/commit/7f5352175e4659973947d8b73a71d1938fbde442))
* **stable:** added datamodelacl and dataModelInstancesacl to group type ([#1274](https://github.com/cognitedata/cognite-sdk-js/issues/1274)) ([ae36253](https://github.com/cognitedata/cognite-sdk-js/commit/ae36253c53c2a5fad086e047f805f2e9447c0bda))
* **stable:** generate latest types (04.03.25) ([#1279](https://github.com/cognitedata/cognite-sdk-js/issues/1279)) ([79abb79](https://github.com/cognitedata/cognite-sdk-js/commit/79abb793f40f44ec34fe79aafdfeaab19f57603d))





# [10.0.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@9.17.1...@cognite/sdk@10.0.0) (2025-02-03)


### Features

* release v10 [release] ([#1243](https://github.com/cognitedata/cognite-sdk-js/issues/1243)) ([b3d5595](https://github.com/cognitedata/cognite-sdk-js/commit/b3d55951bb2a302981fa67ce694d21749461386a)), closes [#1135](https://github.com/cognitedata/cognite-sdk-js/issues/1135) [#1144](https://github.com/cognitedata/cognite-sdk-js/issues/1144) [#1148](https://github.com/cognitedata/cognite-sdk-js/issues/1148) [#1150](https://github.com/cognitedata/cognite-sdk-js/issues/1150) [#1151](https://github.com/cognitedata/cognite-sdk-js/issues/1151) [#1152](https://github.com/cognitedata/cognite-sdk-js/issues/1152) [#1153](https://github.com/cognitedata/cognite-sdk-js/issues/1153) [#1029](https://github.com/cognitedata/cognite-sdk-js/issues/1029)


### BREAKING CHANGES

* es6 module (vs es5) and typescript 3 -> 5

* chore: release pre-release from the release-v10 branch

* test: skip flaky alerts test

* chore: trigger [release]

* chore(release): publish new package versions [skip ci]

 - @cognite/sdk-alpha@1.0.0-rc.0
 - @cognite/sdk-beta@6.0.0-rc.0
 - @cognite/sdk-core@5.0.0-rc.0
 - @cognite/sdk-playground@8.0.0-rc.0
 - @cognite/sdk@10.0.0-rc.0





## [9.17.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@9.17.0...@cognite/sdk@9.17.1) (2025-01-17)

**Note:** Version bump only for package @cognite/sdk





# [9.17.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@9.16.1...@cognite/sdk@9.17.0) (2024-12-12)


### Features

* **stable:** add support for sessions API ([#1177](https://github.com/cognitedata/cognite-sdk-js/issues/1177)) ([28b0fdd](https://github.com/cognitedata/cognite-sdk-js/commit/28b0fddf880091508238fea416b87d72cea32529))





## [9.16.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@9.16.0...@cognite/sdk@9.16.1) (2024-11-07)


### Bug Fixes

* **files:** arrange-imports for multipart session ([#1171](https://github.com/cognitedata/cognite-sdk-js/issues/1171)) ([0848b3f](https://github.com/cognitedata/cognite-sdk-js/commit/0848b3f57bf788cee8d991b76b19a647590a063d))





# [9.16.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@9.15.7...@cognite/sdk@9.16.0) (2024-10-07)


### Features

* **files:** move multipart functionality to stable [BND3D-4545] ([#1132](https://github.com/cognitedata/cognite-sdk-js/issues/1132)) ([1e33003](https://github.com/cognitedata/cognite-sdk-js/commit/1e33003f364ad0932cca24ea2a9d457ac382f9ec))





## [9.15.7](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@9.15.5...@cognite/sdk@9.15.7) (2024-08-26)


### Bug Fixes

* dummy release to override a bad one ([#1139](https://github.com/cognitedata/cognite-sdk-js/issues/1139)) ([5b036da](https://github.com/cognitedata/cognite-sdk-js/commit/5b036dabd4630b45d51558ee7f95d951c7227137))





## [9.15.5](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@9.15.4...@cognite/sdk@9.15.5) (2024-08-26)


### Bug Fixes

* **units:** correct external id type ([#1137](https://github.com/cognitedata/cognite-sdk-js/issues/1137)) ([80161a3](https://github.com/cognitedata/cognite-sdk-js/commit/80161a3e30595ecbd5a604522ffb3718829f984d))





## [9.15.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@9.15.3...@cognite/sdk@9.15.4) (2024-08-22)

**Note:** Version bump only for package @cognite/sdk





## [9.15.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@9.15.2...@cognite/sdk@9.15.3) (2024-08-20)

### Bug Fixes

- add missing properties to datapoints api ([#1124](https://github.com/cognitedata/cognite-sdk-js/issues/1124)) ([42c3160](https://github.com/cognitedata/cognite-sdk-js/commit/42c31609a7809ba866eeb62251fd765ab93eb7bc))

## [9.15.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@9.15.1...@cognite/sdk@9.15.2) (2024-08-19)

**Note:** Version bump only for package @cognite/sdk

## [9.15.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@9.15.0...@cognite/sdk@9.15.1) (2024-06-10)

### Bug Fixes

- **stable/models:** pass 'inlineViews' as params instead of request body for data models retrieve ([#1108](https://github.com/cognitedata/cognite-sdk-js/issues/1108)) ([2a9ca81](https://github.com/cognitedata/cognite-sdk-js/commit/2a9ca81016cdc2926b7d5a2996fe9ba289c640cd))

# [9.15.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@9.14.0...@cognite/sdk@9.15.0) (2024-05-15)

### Features

- **annotations:** generate annotations for isolation planning app ([#1103](https://github.com/cognitedata/cognite-sdk-js/issues/1103)) ([3acda38](https://github.com/cognitedata/cognite-sdk-js/commit/3acda3806b324750680460005aedee7b83e453ca))

# [9.14.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@9.13.0...@cognite/sdk@9.14.0) (2024-05-13)

### Features

- **files-beta:** add multi part upload sdk and tests (BND3D-4012) ([#1088](https://github.com/cognitedata/cognite-sdk-js/issues/1088)) ([a2fbbe6](https://github.com/cognitedata/cognite-sdk-js/commit/a2fbbe67db54518f3de08ec03dd4c6898c43465a))

# [9.13.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@9.12.0...@cognite/sdk@9.13.0) (2024-04-29)

### Features

- **datapoints:** add support for data quality (beta) ([#1071](https://github.com/cognitedata/cognite-sdk-js/issues/1071)) ([d414ae4](https://github.com/cognitedata/cognite-sdk-js/commit/d414ae43f62e45f975bbcffe178dfbc29d1f913e)), closes [#1070](https://github.com/cognitedata/cognite-sdk-js/issues/1070) [#1072](https://github.com/cognitedata/cognite-sdk-js/issues/1072) [#1073](https://github.com/cognitedata/cognite-sdk-js/issues/1073) [#1075](https://github.com/cognitedata/cognite-sdk-js/issues/1075) [#1076](https://github.com/cognitedata/cognite-sdk-js/issues/1076) [#1078](https://github.com/cognitedata/cognite-sdk-js/issues/1078) [#1080](https://github.com/cognitedata/cognite-sdk-js/issues/1080) [#1079](https://github.com/cognitedata/cognite-sdk-js/issues/1079) [#1081](https://github.com/cognitedata/cognite-sdk-js/issues/1081)

# [9.12.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@9.11.2...@cognite/sdk@9.12.0) (2024-04-11)

### Features

- introduce remaining instance endpoints ([#1085](https://github.com/cognitedata/cognite-sdk-js/issues/1085)) ([9969e47](https://github.com/cognitedata/cognite-sdk-js/commit/9969e47c23e03b0fd3ecfd93dda9dda3867fce32))

## [9.11.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@9.11.1...@cognite/sdk@9.11.2) (2024-04-11)

### Bug Fixes

- **Group:** explicitly define sourceId for all group variants ([#1084](https://github.com/cognitedata/cognite-sdk-js/issues/1084)) ([220b7c1](https://github.com/cognitedata/cognite-sdk-js/commit/220b7c1b481c28c4115d6e3e55ed093074404ec9))

## [9.11.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@9.11.0...@cognite/sdk@9.11.1) (2024-04-10)

### Bug Fixes

- **groups:** inconsistent types compared to the API spec ([#1082](https://github.com/cognitedata/cognite-sdk-js/issues/1082)) ([f07f136](https://github.com/cognitedata/cognite-sdk-js/commit/f07f13690dd5e10ba08bbec18b9d3f483eae60e0))

# [9.11.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@9.10.0...@cognite/sdk@9.11.0) (2024-04-09)

### Features

- group membership ([#1079](https://github.com/cognitedata/cognite-sdk-js/issues/1079)) ([4a7c24a](https://github.com/cognitedata/cognite-sdk-js/commit/4a7c24a260453495fdc1006660093b9f15f37ac5))

# [9.10.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@9.9.0...@cognite/sdk@9.10.0) (2024-02-08)

### Features

- introduce datamodels endpoints + export types correctly ([#1058](https://github.com/cognitedata/cognite-sdk-js/issues/1058)) ([0c3405f](https://github.com/cognitedata/cognite-sdk-js/commit/0c3405f3f842c203b79726d541be11379274d064))

# [9.9.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@9.8.0...@cognite/sdk@9.9.0) (2024-02-06)

### Features

- add containers and views to the SDK ([#1056](https://github.com/cognitedata/cognite-sdk-js/issues/1056)) ([c05cc57](https://github.com/cognitedata/cognite-sdk-js/commit/c05cc57851e4a2f4df0a566f6fe1926da9eeeb93))

# [9.8.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@9.7.1...@cognite/sdk@9.8.0) (2024-02-05)

### Features

- add spaces to js sdk ([#1055](https://github.com/cognitedata/cognite-sdk-js/issues/1055)) ([3840acf](https://github.com/cognitedata/cognite-sdk-js/commit/3840acfa19b17be13729a55efdee118e6f66bfe6))

## [9.7.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@9.7.0...@cognite/sdk@9.7.1) (2024-01-22)

**Note:** Version bump only for package @cognite/sdk

# [9.7.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@9.6.2...@cognite/sdk@9.7.0) (2024-01-18)

### Features

- instances/search and instances/list ([#1047](https://github.com/cognitedata/cognite-sdk-js/issues/1047)) ([84e5ea2](https://github.com/cognitedata/cognite-sdk-js/commit/84e5ea23f265a54aeafc0aa83e4607a7d1262d7c))

## [9.6.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@9.6.1...@cognite/sdk@9.6.2) (2023-12-22)

### Bug Fixes

- set empty array for no results ([#1046](https://github.com/cognitedata/cognite-sdk-js/issues/1046)) ([0ae93aa](https://github.com/cognitedata/cognite-sdk-js/commit/0ae93aa0e0ba55db9741138fc9b046a26e755883))

## [9.6.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@9.6.0...@cognite/sdk@9.6.1) (2023-12-19)

### Bug Fixes

- return all time series when asking for multiple time series of monthly aggregates ([#1045](https://github.com/cognitedata/cognite-sdk-js/issues/1045)) ([129de8b](https://github.com/cognitedata/cognite-sdk-js/commit/129de8be32f4cd8c6c2bb0910accd17df618f7d9))

# [9.6.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@9.5.0...@cognite/sdk@9.6.0) (2023-12-06)

### Features

- user profiles api ([#1019](https://github.com/cognitedata/cognite-sdk-js/issues/1019)) ([33eee73](https://github.com/cognitedata/cognite-sdk-js/commit/33eee73e40d8306ee145301641d7af1cdba2e3c5))

# [9.5.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@9.4.0...@cognite/sdk@9.5.0) (2023-12-01)

### Features

- **annotations:** generate annotations for isolation planning app ([#1033](https://github.com/cognitedata/cognite-sdk-js/issues/1033)) ([30418ac](https://github.com/cognitedata/cognite-sdk-js/commit/30418acb31d4a553a0c0d9174d5ae50c8b1c8d8e))

# [9.4.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@9.3.0...@cognite/sdk@9.4.0) (2023-11-28)

### Features

- add units catalog and timeseries unit conversion ([#1027](https://github.com/cognitedata/cognite-sdk-js/issues/1027)) ([89cffa8](https://github.com/cognitedata/cognite-sdk-js/commit/89cffa8d7a1401274f52008b7bd7745683a2d817))

# [9.3.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@9.2.1...@cognite/sdk@9.3.0) (2023-11-28)

### Features

- monitoring tasks upsert ([#1042](https://github.com/cognitedata/cognite-sdk-js/issues/1042)) ([7e0e24c](https://github.com/cognitedata/cognite-sdk-js/commit/7e0e24cfb5c4d91302e54a270457b8d9b914ffd4))

## [9.2.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@9.2.0...@cognite/sdk@9.2.1) (2023-11-24)

### Bug Fixes

- use UTC timezone regardless of client time zone ([#1040](https://github.com/cognitedata/cognite-sdk-js/issues/1040)) ([ad717bd](https://github.com/cognitedata/cognite-sdk-js/commit/ad717bd96ad4c3a5dbb6019b418417b23098c689))

# [9.2.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@9.1.0...@cognite/sdk@9.2.0) (2023-11-23)

### Features

- **stable:** generate new types ([#1039](https://github.com/cognitedata/cognite-sdk-js/issues/1039)) ([4dd4e5c](https://github.com/cognitedata/cognite-sdk-js/commit/4dd4e5c594d2db01d7d5051a4b96d01212122f84))

# [9.1.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@9.0.0...@cognite/sdk@9.1.0) (2023-11-21)

### Features

- **monthly aggregate:** retrieve datapoints where granularity set as month ([#1034](https://github.com/cognitedata/cognite-sdk-js/issues/1034)) ([4e4145d](https://github.com/cognitedata/cognite-sdk-js/commit/4e4145d8d4c560779b847d530311820723950a19))

# [9.0.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@8.3.0...@cognite/sdk@9.0.0) (2023-10-10)

### Features

- update openapi spec to bump types for docs, annotations and vision ([#1021](https://github.com/cognitedata/cognite-sdk-js/issues/1021)) ([#1024](https://github.com/cognitedata/cognite-sdk-js/issues/1024)) ([e337d73](https://github.com/cognitedata/cognite-sdk-js/commit/e337d73c9e6a1888e165d8b4a59bfe94b522bbb3))

### BREAKING CHANGES

- documents, annotations and vision types have been updated

Thanks to @danlevings for his work in commit: https://github.com/cognitedata/cognite-sdk-js/commit/ac6efec6e884d0cb436be7c2e91f3aa78bfbe15c

# [8.3.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@8.2.0...@cognite/sdk@8.3.0) (2023-09-27)

### Features

- **alpha:** monitoring task add source and sourceId props - [AH-1863] ([#1020](https://github.com/cognitedata/cognite-sdk-js/issues/1020)) ([506ad9b](https://github.com/cognitedata/cognite-sdk-js/commit/506ad9be6368322f08ac606642414a37f37cec67))

### Reverts

- "feat: update openapi spec to bump types fo docs, annotations and vision" ([#1021](https://github.com/cognitedata/cognite-sdk-js/issues/1021)) ([b908def](https://github.com/cognitedata/cognite-sdk-js/commit/b908defed27eb610c8c7b97d2c24013da735422f))

# [8.2.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@8.1.1...@cognite/sdk@8.2.0) (2023-06-20)

### Features

- annotation reverse-lookup [release] ([#1010](https://github.com/cognitedata/cognite-sdk-js/issues/1010)) ([8e8844d](https://github.com/cognitedata/cognite-sdk-js/commit/8e8844d46d16cbc7278ccf92b605248ed32604a2))

## [8.1.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@8.1.0...@cognite/sdk@8.1.1) (2023-04-28)

**Note:** Version bump only for package @cognite/sdk

# [8.1.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@8.0.1...@cognite/sdk@8.1.0) (2023-04-27)

### Features

- **project:** project update user profiles ([#1009](https://github.com/cognitedata/cognite-sdk-js/issues/1009)) ([ee3d952](https://github.com/cognitedata/cognite-sdk-js/commit/ee3d952635580c87624a48b480d1c2cc1a73817c))

## [8.0.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@8.0.0...@cognite/sdk@8.0.1) (2023-04-20)

**Note:** Version bump only for package @cognite/sdk

# [8.0.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.19.1...@cognite/sdk@8.0.0) (2023-03-21)

### Features

- **documents:** update uniqueProperties and allUniqueProperties ([#992](https://github.com/cognitedata/cognite-sdk-js/issues/992)) ([e9dfbc6](https://github.com/cognitedata/cognite-sdk-js/commit/e9dfbc611d870c1efcc81f6eedd57daa25fa3129))

### BREAKING CHANGES

- **documents:** uniqueProperties has a different value type and no longer supports CursorAndAsyncIterator. Please use allUniqueProperties for pagination instead.

## [7.19.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.19.0...@cognite/sdk@7.19.1) (2023-03-01)

**Note:** Version bump only for package @cognite/sdk

# [7.19.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.18.0...@cognite/sdk@7.19.0) (2023-02-08)

### Features

- **documents:** add `highlight` field for document search ([#977](https://github.com/cognitedata/cognite-sdk-js/issues/977)) ([8a02727](https://github.com/cognitedata/cognite-sdk-js/commit/8a02727aaa4344232c815d884e02b385b9e3915a))

# [7.18.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.17.0...@cognite/sdk@7.18.0) (2023-01-24)

### Features

- support names filter for 3D list nodes endpoint [release] ([#939](https://github.com/cognitedata/cognite-sdk-js/issues/939)) ([3a3e03f](https://github.com/cognitedata/cognite-sdk-js/commit/3a3e03f9c9a6e13b365362694c0720d8894bad2b))

# [7.17.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.16.0...@cognite/sdk@7.17.0) (2023-01-23)

### Features

- **documents:** add new document search functionality ([#937](https://github.com/cognitedata/cognite-sdk-js/issues/937)) ([9aee431](https://github.com/cognitedata/cognite-sdk-js/commit/9aee43199071b6101264a357302b673ffdde4fc6))

# [7.16.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.15.0...@cognite/sdk@7.16.0) (2023-01-23)

### Features

- translation and scale for 3D revisions [release] ([#938](https://github.com/cognitedata/cognite-sdk-js/issues/938)) ([30921ee](https://github.com/cognitedata/cognite-sdk-js/commit/30921ee6bdaa167a9f42e2c3bbc968084e549a5c))

# [7.15.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.14.1...@cognite/sdk@7.15.0) (2023-01-09)

### Features

- **documents:** add asset subtree filter ([#935](https://github.com/cognitedata/cognite-sdk-js/issues/935)) ([ba5e6a1](https://github.com/cognitedata/cognite-sdk-js/commit/ba5e6a1510fa7f14399c4400d37600b2042b9e45))

## [7.14.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.14.0...@cognite/sdk@7.14.1) (2023-01-06)

### Bug Fixes

- **stable:** document preview fields that were optional ([#933](https://github.com/cognitedata/cognite-sdk-js/issues/933)) ([f011dad](https://github.com/cognitedata/cognite-sdk-js/commit/f011dad9554d04619a4a0ce05242c49c8cf6d463))

# [7.14.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.13.0...@cognite/sdk@7.14.0) (2022-12-09)

### Bug Fixes

- add codegen support for CusrorAndAsyncIterator ([#907](https://github.com/cognitedata/cognite-sdk-js/issues/907)) ([3cde0d3](https://github.com/cognitedata/cognite-sdk-js/commit/3cde0d3df8a4135f38849a2cd6408ded32246065)), closes [#908](https://github.com/cognitedata/cognite-sdk-js/issues/908)

### Features

- **alpha:** monitoring tasks api ([#921](https://github.com/cognitedata/cognite-sdk-js/issues/921)) ([9161a69](https://github.com/cognitedata/cognite-sdk-js/commit/9161a69e2b0f4093099e0f36a79c0abefce6723c))

# [7.13.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.12.0...@cognite/sdk@7.13.0) (2022-11-21)

### Features

- geospatial compute endpoint ([#899](https://github.com/cognitedata/cognite-sdk-js/issues/899)) ([7496ad0](https://github.com/cognitedata/cognite-sdk-js/commit/7496ad023b970baac0857ea35dd7356d6fbebeee))

# [7.12.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.11.1...@cognite/sdk@7.12.0) (2022-11-18)

### Features

- **annotations:** add `description` ([#918](https://github.com/cognitedata/cognite-sdk-js/issues/918)) ([aefa215](https://github.com/cognitedata/cognite-sdk-js/commit/aefa215f8a78cc8ee370098757b2f65948648425))

## [7.11.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.11.0...@cognite/sdk@7.11.1) (2022-11-17)

**Note:** Version bump only for package @cognite/sdk

# [7.11.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.10.0...@cognite/sdk@7.11.0) (2022-11-02)

### Features

- **geospatial:** add feature list ([#900](https://github.com/cognitedata/cognite-sdk-js/issues/900)) ([fa71394](https://github.com/cognitedata/cognite-sdk-js/commit/fa71394748f661a79c62707f97ba671d9563e01a))

# [7.10.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.9.6...@cognite/sdk@7.10.0) (2022-10-28)

### Features

- **annotations:** add annotation payload types ([#905](https://github.com/cognitedata/cognite-sdk-js/issues/905)) ([990ea8f](https://github.com/cognitedata/cognite-sdk-js/commit/990ea8fda85bc26e9a50bd9160c6b8a76e26ed55))

## [7.9.6](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.9.5...@cognite/sdk@7.9.6) (2022-10-04)

### Bug Fixes

- move `@types/geojson` to depedencies ([#897](https://github.com/cognitedata/cognite-sdk-js/issues/897)) ([66b3bb0](https://github.com/cognitedata/cognite-sdk-js/commit/66b3bb0b8d37d814d5bb0dcc35b576fe924abce3))

## [7.9.5](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.9.4...@cognite/sdk@7.9.5) (2022-09-26)

**Note:** Version bump only for package @cognite/sdk

## [7.9.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.9.3...@cognite/sdk@7.9.4) (2022-09-23)

**Note:** Version bump only for package @cognite/sdk

## [7.9.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.9.2...@cognite/sdk@7.9.3) (2022-09-23)

**Note:** Version bump only for package @cognite/sdk

## [7.9.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.9.1...@cognite/sdk@7.9.2) (2022-09-23)

### Bug Fixes

- ensure response ordering matches request when chunking a request ([#893](https://github.com/cognitedata/cognite-sdk-js/issues/893)) ([77eb797](https://github.com/cognitedata/cognite-sdk-js/commit/77eb7976abb21029c22a16c0ceeb22d577d1ce68))

## [7.9.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.9.0...@cognite/sdk@7.9.1) (2022-09-12)

**Note:** Version bump only for package @cognite/sdk

# [7.9.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.8.7...@cognite/sdk@7.9.0) (2022-09-12)

### Features

- move vision extract api to v1 ([#872](https://github.com/cognitedata/cognite-sdk-js/issues/872)) ([23d0218](https://github.com/cognitedata/cognite-sdk-js/commit/23d021863020bc3e8caf2ad94caafefe1d1e405a)), closes [#873](https://github.com/cognitedata/cognite-sdk-js/issues/873)

## [7.8.7](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.8.6...@cognite/sdk@7.8.7) (2022-09-07)

**Note:** Version bump only for package @cognite/sdk

## [7.8.6](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.8.5...@cognite/sdk@7.8.6) (2022-09-07)

### Bug Fixes

- don't retry 401 requests ([#868](https://github.com/cognitedata/cognite-sdk-js/issues/868)) ([4d17461](https://github.com/cognitedata/cognite-sdk-js/commit/4d174616ccf8ddfafed8a45b64d99e5ceaa06ce7))

## [7.8.5](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.8.4...@cognite/sdk@7.8.5) (2022-09-06)

**Note:** Version bump only for package @cognite/sdk

## [7.8.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.8.3...@cognite/sdk@7.8.4) (2022-08-31)

**Note:** Version bump only for package @cognite/sdk

## [7.8.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.8.2...@cognite/sdk@7.8.3) (2022-08-21)

**Note:** Version bump only for package @cognite/sdk

## [7.8.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.8.1...@cognite/sdk@7.8.2) (2022-08-15)

**Note:** Version bump only for package @cognite/sdk

## [7.8.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.8.0...@cognite/sdk@7.8.1) (2022-08-05)

**Note:** Version bump only for package @cognite/sdk

# [7.8.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.7.1...@cognite/sdk@7.8.0) (2022-07-25)

### Features

- code generation for types based on openapi doc ([#801](https://github.com/cognitedata/cognite-sdk-js/issues/801)) ([07eb308](https://github.com/cognitedata/cognite-sdk-js/commit/07eb3087705c550758fcc9e1b12ea43428ecf79d)), closes [#820](https://github.com/cognitedata/cognite-sdk-js/issues/820)

## [7.7.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.7.0...@cognite/sdk@7.7.1) (2022-07-07)

**Note:** Version bump only for package @cognite/sdk

# [7.7.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.6.2...@cognite/sdk@7.7.0) (2022-06-20)

### Features

- **annotations:** move annotations to stable ([#823](https://github.com/cognitedata/cognite-sdk-js/issues/823)) ([b453685](https://github.com/cognitedata/cognite-sdk-js/commit/b4536855ad62e9888af11419b89af1621a50a7ff))

## [7.6.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.6.1...@cognite/sdk@7.6.2) (2022-06-17)

**Note:** Version bump only for package @cognite/sdk

## [7.6.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.6.0...@cognite/sdk@7.6.1) (2022-06-09)

**Note:** Version bump only for package @cognite/sdk

# [7.6.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.5.1...@cognite/sdk@7.6.0) (2022-06-02)

### Features

- automatic token management with @cognite/auth-wrapper ([#796](https://github.com/cognitedata/cognite-sdk-js/issues/796)) ([d13c53f](https://github.com/cognitedata/cognite-sdk-js/commit/d13c53f9d5a5e65d6e2fc7d1a0e2efe2d36e678c))
- automatic token management with @cognite/auth-wrapper ([#814](https://github.com/cognitedata/cognite-sdk-js/issues/814)) ([d2c3a30](https://github.com/cognitedata/cognite-sdk-js/commit/d2c3a3083746e234f5dfc3e290188414d9ea17a6))

### Reverts

- Revert "feat: automatic token management with @cognite/auth-wrapper (#796)" (#813) ([0a8a61c](https://github.com/cognitedata/cognite-sdk-js/commit/0a8a61c2b2c3d992f80b40f6282b20595537edac)), closes [#796](https://github.com/cognitedata/cognite-sdk-js/issues/796) [#813](https://github.com/cognitedata/cognite-sdk-js/issues/813)

## [7.5.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.5.0...@cognite/sdk@7.5.1) (2022-05-27)

### Bug Fixes

- **documents:** label type in documents ([#809](https://github.com/cognitedata/cognite-sdk-js/issues/809)) ([fd5dcf6](https://github.com/cognitedata/cognite-sdk-js/commit/fd5dcf6ba6401eade7ad4e9c1f970494223a8b4a))

# [7.5.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.4.0...@cognite/sdk@7.5.0) (2022-05-27)

### Features

- **documents:** aggregate api ([#793](https://github.com/cognitedata/cognite-sdk-js/issues/793)) ([445c39f](https://github.com/cognitedata/cognite-sdk-js/commit/445c39ff90822a357e20e624828bcd6e2ee36369))

# [7.4.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.3.4...@cognite/sdk@7.4.0) (2022-05-25)

### Features

- add fetchResources to relationships api ([#808](https://github.com/cognitedata/cognite-sdk-js/issues/808)) ([b37fdd2](https://github.com/cognitedata/cognite-sdk-js/commit/b37fdd230043c8c24af1e1fe6df339064d722636))

## [7.3.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.3.3...@cognite/sdk@7.3.4) (2022-05-23)

**Note:** Version bump only for package @cognite/sdk

## [7.3.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.3.2...@cognite/sdk@7.3.3) (2022-05-20)

### Bug Fixes

- 401 not authorized ([#804](https://github.com/cognitedata/cognite-sdk-js/issues/804)) ([0b4d8be](https://github.com/cognitedata/cognite-sdk-js/commit/0b4d8beb666aff24dbb1309b552193cdd1d80f94))

## [7.3.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.3.1...@cognite/sdk@7.3.2) (2022-05-20)

### Bug Fixes

- **geospatial:** change feature search stream response ([#805](https://github.com/cognitedata/cognite-sdk-js/issues/805)) ([4e207aa](https://github.com/cognitedata/cognite-sdk-js/commit/4e207aa41eab84a40968b9aed826ac29da7700d1))

### Reverts

- Revert "fix: fixing 401 problem (#800)" (#802) ([341b68d](https://github.com/cognitedata/cognite-sdk-js/commit/341b68dd271b7781489343637c861921d647b483)), closes [#800](https://github.com/cognitedata/cognite-sdk-js/issues/800) [#802](https://github.com/cognitedata/cognite-sdk-js/issues/802)

## [7.3.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.3.0...@cognite/sdk@7.3.1) (2022-05-19)

### Bug Fixes

- fixing 401 problem ([#800](https://github.com/cognitedata/cognite-sdk-js/issues/800)) ([b5789b1](https://github.com/cognitedata/cognite-sdk-js/commit/b5789b1a15ed9fb574c636c5515c85a2868b60a2))

# [7.3.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.2.5...@cognite/sdk@7.3.0) (2022-05-18)

### Features

- **documents:** content and list endpoints + stricter types ([#792](https://github.com/cognitedata/cognite-sdk-js/issues/792)) ([e906571](https://github.com/cognitedata/cognite-sdk-js/commit/e906571f0501312315b4a20909fb302ebc8a8827))

## [7.2.5](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.2.4...@cognite/sdk@7.2.5) (2022-05-16)

**Note:** Version bump only for package @cognite/sdk

## [7.2.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.2.3...@cognite/sdk@7.2.4) (2022-05-16)

### Bug Fixes

- use `type` field instead of instanceOf ([#761](https://github.com/cognitedata/cognite-sdk-js/issues/761)) ([45c5ca8](https://github.com/cognitedata/cognite-sdk-js/commit/45c5ca8a26f8dc6cbbd0efd9172d0fb1d7a842de))

## [7.2.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.2.2...@cognite/sdk@7.2.3) (2022-05-13)

### Bug Fixes

- adding @types/geojson to core package ([#780](https://github.com/cognitedata/cognite-sdk-js/issues/780)) ([b8b5715](https://github.com/cognitedata/cognite-sdk-js/commit/b8b57155f1e40df33743a61bad8fce63fe2d247f))

## [7.2.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.2.0...@cognite/sdk@7.2.2) (2022-04-12)

### Bug Fixes

- adding geojson to fix import problems ([#779](https://github.com/cognitedata/cognite-sdk-js/issues/779)) ([14d437a](https://github.com/cognitedata/cognite-sdk-js/commit/14d437af5e027ec7ef69b87442f1c0552fec7ee6))

# [7.2.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.1.1...@cognite/sdk@7.2.0) (2022-04-08)

### Features

- **documents:** add documents preview to stable ([#776](https://github.com/cognitedata/cognite-sdk-js/issues/776)) ([49f45f1](https://github.com/cognitedata/cognite-sdk-js/commit/49f45f1164f9af932eba56989172890ae2a55831))

## [7.1.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.1.0...@cognite/sdk@7.1.1) (2022-03-01)

**Note:** Version bump only for package @cognite/sdk

# [7.1.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@7.0.0...@cognite/sdk@7.1.0) (2022-02-09)

### Features

- **documents:** add document search API to stable ([#749](https://github.com/cognitedata/cognite-sdk-js/issues/749)) ([28c0a08](https://github.com/cognitedata/cognite-sdk-js/commit/28c0a0860e5d8ff1c8c1e6f48936eb4dec14341a)), closes [#753](https://github.com/cognitedata/cognite-sdk-js/issues/753) [#754](https://github.com/cognitedata/cognite-sdk-js/issues/754)

# [7.0.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@6.3.3...@cognite/sdk@7.0.0) (2022-01-10)

### chore

- **geospatial:** updates spatial to geospatial & type name changes ([#720](https://github.com/cognitedata/cognite-sdk-js/issues/720)) ([2e93d8b](https://github.com/cognitedata/cognite-sdk-js/commit/2e93d8b23de443e845eef1aa8dc9200483d27e76))

### Features

- **geospatial:** updates spatial to geospatial & type name ([#727](https://github.com/cognitedata/cognite-sdk-js/issues/727)) ([ad05a88](https://github.com/cognitedata/cognite-sdk-js/commit/ad05a885b6a16bd2ae5636fd2cbed2649dad5023))

### BREAKING CHANGES

- **geospatial:** updates spatial to geospatial & type name

- chore(geospatial): updates spatial to geospatial & types updated

- feat(geospatial): adds update api to featureType

- feat(geospatial): refactor feature APIs, filter types ,aggregateEndpoint

- chore(geospatial): place geojson type in stable package

- chore(geospatial): update types for breaking API changes

- chore(geospatial): updates Geospatial type

- chore(geospatial): remove filter bifurcation

- chore(geospatial): updates searchSpec

- chore(geospatial): adds crs & js code examples

- chore(geospatial): change generic id type for retrieve

- chore(geospatial): remove output params from delete feature

- chore(geospatial): change crs endpoint

- chore(geospatial): add more filters

- chore(geospatial): fix examples
- **geospatial:** changes api name from spatial to geospatial, features to feature & featureTypes to featureType

- feat(geospatial): adds update api to featureType

- feat(geospatial): refactor feature APIs, filter types ,aggregateEndpoint

- chore(geospatial): place geojson type in stable package

- chore(geospatial): update types for breaking API changes

- chore(geospatial): updates Geospatial type

- chore(geospatial): remove filter bifurcation

- chore(geospatial): updates searchSpec

- chore(geospatial): adds crs & js code examples

- chore(geospatial): change generic id type for retrieve

- chore(geospatial): remove output params from delete feature

- chore(geospatial): change crs endpoint

- chore(geospatial): add more filters

- chore(geospatial): fix example

- chore(geospatial): fix examples

## [6.3.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@6.3.2...@cognite/sdk@6.3.3) (2022-01-10)

**Note:** Version bump only for package @cognite/sdk

## [6.3.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@6.3.1...@cognite/sdk@6.3.2) (2021-12-21)

**Note:** Version bump only for package @cognite/sdk

## [6.3.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@6.3.0...@cognite/sdk@6.3.1) (2021-12-07)

**Note:** Version bump only for package @cognite/sdk

# [6.3.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@6.2.0...@cognite/sdk@6.3.0) (2021-11-19)

### Features

- add missing properties to update model ([#718](https://github.com/cognitedata/cognite-sdk-js/issues/718)) ([516fb20](https://github.com/cognitedata/cognite-sdk-js/commit/516fb20ea4d43949edb47244c7f9040e6d127dc9))

# [6.2.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@6.1.3...@cognite/sdk@6.2.0) (2021-11-17)

### Features

- add dataSetId support to 3dModel ([#665](https://github.com/cognitedata/cognite-sdk-js/issues/665)) ([cdea5a3](https://github.com/cognitedata/cognite-sdk-js/commit/cdea5a30cc3232af75f054c6322692801bcd8e87))

## [6.1.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@6.1.2...@cognite/sdk@6.1.3) (2021-11-04)

**Note:** Version bump only for package @cognite/sdk

## [6.1.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@6.1.1...@cognite/sdk@6.1.2) (2021-10-29)

**Note:** Version bump only for package @cognite/sdk

## [6.1.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@6.1.0...@cognite/sdk@6.1.1) (2021-10-19)

**Note:** Version bump only for package @cognite/sdk

# [6.1.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@6.0.0...@cognite/sdk@6.1.0) (2021-10-12)

### Features

- add dataSetId support to template groups ([#662](https://github.com/cognitedata/cognite-sdk-js/issues/662)) ([98827bc](https://github.com/cognitedata/cognite-sdk-js/commit/98827bcdb397484508ac36923b7006c4f140a43e))

# [6.0.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@5.6.1...@cognite/sdk@6.0.0) (2021-10-12)

### Features

- **auth:** re-release auth patch ([#700](https://github.com/cognitedata/cognite-sdk-js/issues/700)) ([a53c40d](https://github.com/cognitedata/cognite-sdk-js/commit/a53c40ddd7eca5d2dee9149f5df0b2e533d19575))

### BREAKING CHANGES

- **auth:** release v6

re-release (revert reversion) of "feat(core): move authentication out of CogniteClient"
https://github.com/cognitedata/cognite-sdk-js/pull/687

This reverts commit 72e1ecb61603e0ac3926124c26f4e009df88f020.

Co-authored-by: Vegard Økland <vegard.okland@cognite.com>

## [5.6.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@5.6.0...@cognite/sdk@5.6.1) (2021-10-12)

### Bug Fixes

- **release:** undo major version release without major version bump ([#697](https://github.com/cognitedata/cognite-sdk-js/issues/697)) ([72e1ecb](https://github.com/cognitedata/cognite-sdk-js/commit/72e1ecb61603e0ac3926124c26f4e009df88f020)), closes [#687](https://github.com/cognitedata/cognite-sdk-js/issues/687)

# [5.6.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@5.5.0...@cognite/sdk@5.6.0) (2021-10-12)

### Features

- **core:** move authentication out of CogniteClient ([#687](https://github.com/cognitedata/cognite-sdk-js/issues/687)) ([879ed31](https://github.com/cognitedata/cognite-sdk-js/commit/879ed31d05dd6d6f4b691b99eaca5fa7363e96e6))

# [5.5.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@5.4.0...@cognite/sdk@5.5.0) (2021-10-07)

### Features

- **spatial:** adds spatial API to playground ([#680](https://github.com/cognitedata/cognite-sdk-js/issues/680)) ([e0b2d1d](https://github.com/cognitedata/cognite-sdk-js/commit/e0b2d1dd6ac85eb6fd8a6d6fce61e26cc909c5e7))

# [5.4.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@5.3.3...@cognite/sdk@5.4.0) (2021-09-22)

### Features

- add types for template capabilities ([#679](https://github.com/cognitedata/cognite-sdk-js/issues/679)) ([1be2218](https://github.com/cognitedata/cognite-sdk-js/commit/1be2218ce6817f289d4541f18bd2df125f14343c))

## [5.3.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@5.3.2...@cognite/sdk@5.3.3) (2021-09-20)

**Note:** Version bump only for package @cognite/sdk

## [5.3.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@5.3.1...@cognite/sdk@5.3.2) (2021-09-03)

### Bug Fixes

- remove test files in published packages ([#673](https://github.com/cognitedata/cognite-sdk-js/issues/673)) ([cf6deae](https://github.com/cognitedata/cognite-sdk-js/commit/cf6deae6d80d0bfb3b2b3e8a8db6c30a1bb1ec0a))

## [5.3.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@5.3.0...@cognite/sdk@5.3.1) (2021-08-30)

### Bug Fixes

- **stable:** external id format for fetching rows ([#664](https://github.com/cognitedata/cognite-sdk-js/issues/664)) ([a637814](https://github.com/cognitedata/cognite-sdk-js/commit/a63781461544c6fefa9e9a0db54210e6544d7600))

# [5.3.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@5.2.1...@cognite/sdk@5.3.0) (2021-08-30)

### Features

- promote templates to stable ([#661](https://github.com/cognitedata/cognite-sdk-js/issues/661)) ([def00b6](https://github.com/cognitedata/cognite-sdk-js/commit/def00b654ad0696ef2be8dcd47a94fcd099f7277))

## [5.2.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@5.2.0...@cognite/sdk@5.2.1) (2021-08-20)

**Note:** Version bump only for package @cognite/sdk

# [5.2.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@5.1.5...@cognite/sdk@5.2.0) (2021-08-18)

### Features

- add update support template instances ([#634](https://github.com/cognitedata/cognite-sdk-js/issues/634)) ([15ca576](https://github.com/cognitedata/cognite-sdk-js/commit/15ca5762a8163fba8dd87d6a69124c4cf2c5dc38))

## [5.1.5](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@5.1.4...@cognite/sdk@5.1.5) (2021-08-11)

**Note:** Version bump only for package @cognite/sdk

## [5.1.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@5.1.3...@cognite/sdk@5.1.4) (2021-08-10)

**Note:** Version bump only for package @cognite/sdk

## [5.1.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@5.1.2...@cognite/sdk@5.1.3) (2021-07-21)

**Note:** Version bump only for package @cognite/sdk

## [5.1.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@5.1.1...@cognite/sdk@5.1.2) (2021-07-21)

**Note:** Version bump only for package @cognite/sdk

## [5.1.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@5.1.0...@cognite/sdk@5.1.1) (2021-07-21)

### Bug Fixes

- revert client credentials [release] ([#618](https://github.com/cognitedata/cognite-sdk-js/issues/618)) ([08a0d8c](https://github.com/cognitedata/cognite-sdk-js/commit/08a0d8cf01105aa326e73d93c703c7fc0ee6f68d))

# [5.1.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@5.0.0...@cognite/sdk@5.1.0) (2021-07-20)

### Features

- client credentials flow [release] ([#607](https://github.com/cognitedata/cognite-sdk-js/issues/607)) ([28ed890](https://github.com/cognitedata/cognite-sdk-js/commit/28ed890ebf15da151e05cf0c487bca4b91b8ea96))

# [5.0.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@4.3.1...@cognite/sdk@5.0.0) (2021-07-15)

### Features

- **core:** add oidc auth code flow [release] ([#587](https://github.com/cognitedata/cognite-sdk-js/issues/587)) ([0cc44aa](https://github.com/cognitedata/cognite-sdk-js/commit/0cc44aa82b7d7461e8629fe2e712f743bf6c7138))

### BREAKING CHANGES

- **core:** stop silencing errors from aad

- **core:** change loginWithOAuth API signature

## [4.3.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@4.3.0...@cognite/sdk@4.3.1) (2021-07-14)

**Note:** Version bump only for package @cognite/sdk

# [4.3.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@4.2.2...@cognite/sdk@4.3.0) (2021-07-08)

### Features

- **revisions3d:** parameter to update metadata property [release] ([#609](https://github.com/cognitedata/cognite-sdk-js/issues/609)) ([920ee0b](https://github.com/cognitedata/cognite-sdk-js/commit/920ee0b6992fb7ede4421983d59546864fb791b7))
- add accessor to filter 3d nodes endpoint ([#608](https://github.com/cognitedata/cognite-sdk-js/issues/608)) ([b64e39f](https://github.com/cognitedata/cognite-sdk-js/commit/b64e39f7397ecbc460d2844192bf569f780ed9cb))

## [4.2.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@4.2.1...@cognite/sdk@4.2.2) (2021-06-30)

### Reverts

- Revert "feat!: add client credentials flow (#589)" (#604) ([12d65a4](https://github.com/cognitedata/cognite-sdk-js/commit/12d65a41e919409582d76a3a59798737808cefac)), closes [#589](https://github.com/cognitedata/cognite-sdk-js/issues/589) [#604](https://github.com/cognitedata/cognite-sdk-js/issues/604)

## [4.2.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@4.2.0...@cognite/sdk@4.2.1) (2021-06-30)

**Note:** Version bump only for package @cognite/sdk

# [4.2.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@4.1.0...@cognite/sdk@4.2.0) (2021-06-30)

### Features

- appended oidc configuration to project response ([#576](https://github.com/cognitedata/cognite-sdk-js/issues/576)) ([2fd5fa4](https://github.com/cognitedata/cognite-sdk-js/commit/2fd5fa42d41d97623c2fba350a3846efef71ad2d))
- directoryPrefix filter for files [release] ([#590](https://github.com/cognitedata/cognite-sdk-js/issues/590)) ([ae59982](https://github.com/cognitedata/cognite-sdk-js/commit/ae599825f90c7fa1222f26c86fd4f79d9915f746))
- **project:** partial project update ([#573](https://github.com/cognitedata/cognite-sdk-js/issues/573)) ([230c23e](https://github.com/cognitedata/cognite-sdk-js/commit/230c23ef45028b025b85a3f020d31ee4e3a67a97))

# [4.1.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@4.0.4...@cognite/sdk@4.1.0) (2021-05-19)

### Features

- accessor for filtering 3D Asset Mappings [BND3D-676] ([#470](https://github.com/cognitedata/cognite-sdk-js/issues/470)) ([5e4de40](https://github.com/cognitedata/cognite-sdk-js/commit/5e4de403d2a7d088af1dbf451828ccdd56402756))

## [4.0.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@4.0.3...@cognite/sdk@4.0.4) (2021-04-30)

### Bug Fixes

- add cursor to Model3DListRequest ([#537](https://github.com/cognitedata/cognite-sdk-js/issues/537)) ([9a76843](https://github.com/cognitedata/cognite-sdk-js/commit/9a768438631c36707b2ec6a268f2eec602f127d2))

## [4.0.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@4.0.2...@cognite/sdk@4.0.3) (2021-04-26)

**Note:** Version bump only for package @cognite/sdk

## [4.0.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@4.0.1...@cognite/sdk@4.0.2) (2021-04-13)

**Note:** Version bump only for package @cognite/sdk

## [4.0.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@4.0.0...@cognite/sdk@4.0.1) (2021-04-08)

**Note:** Version bump only for package @cognite/sdk

# [4.0.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@3.5.2...@cognite/sdk@4.0.0) (2021-03-25)

**Note:** Version bump only for package @cognite/sdk

## [3.5.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@3.5.1...@cognite/sdk@3.5.2) (2021-03-25)

### Bug Fixes

- revert breaking changes being published as patch ([#503](https://github.com/cognitedata/cognite-sdk-js/issues/503)) ([3b7afd9](https://github.com/cognitedata/cognite-sdk-js/commit/3b7afd94030c75b2122a8e8323678455bcef0a29))

## [3.5.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@3.5.0...@cognite/sdk@3.5.1) (2021-03-25)

**Note:** Version bump only for package @cognite/sdk

# [3.5.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@3.4.0...@cognite/sdk@3.5.0) (2021-03-24)

### Features

- extend RawDB and RawDBTable interfaces ([#502](https://github.com/cognitedata/cognite-sdk-js/issues/502)) ([e1f8020](https://github.com/cognitedata/cognite-sdk-js/commit/e1f80200f189f9913619834c46546c3cdeb8f900))

# [3.4.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@3.3.6...@cognite/sdk@3.4.0) (2021-03-16)

### Bug Fixes

- 3d node sortByNodeId correct type ([#497](https://github.com/cognitedata/cognite-sdk-js/issues/497)) ([c53f92f](https://github.com/cognitedata/cognite-sdk-js/commit/c53f92fc9ece522f77cdea5b8440328c0027152f))

### Features

- add List 3D Nodes query parameters ([#494](https://github.com/cognitedata/cognite-sdk-js/issues/494)) ([11141c5](https://github.com/cognitedata/cognite-sdk-js/commit/11141c5ef43e99d966776534933941aaec095282))
- **entitymatching:** new stable api ([#491](https://github.com/cognitedata/cognite-sdk-js/issues/491)) ([e91e707](https://github.com/cognitedata/cognite-sdk-js/commit/e91e707d5a348537c24e3e27510580072e2acf71))

## [3.3.6](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@3.3.5...@cognite/sdk@3.3.6) (2021-03-05)

**Note:** Version bump only for package @cognite/sdk

## [3.3.5](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@3.3.4...@cognite/sdk@3.3.5) (2021-03-02)

**Note:** Version bump only for package @cognite/sdk

## [3.3.4](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@3.3.2...@cognite/sdk@3.3.4) (2021-03-01)

### Bug Fixes

- **timeseries:** synthetic/query is a retryable endpoint ([#475](https://github.com/cognitedata/cognite-sdk-js/issues/475)) ([7e03ae2](https://github.com/cognitedata/cognite-sdk-js/commit/7e03ae2f06d5c998c81d9e5ab743831f8cda9ead))

## [3.3.3](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@3.3.2...@cognite/sdk@3.3.3) (2021-02-26)

**Note:** Version bump only for package @cognite/sdk

## [3.3.2](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@3.3.1...@cognite/sdk@3.3.2) (2020-12-17)

**Note:** Version bump only for package @cognite/sdk

## [3.3.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@3.3.0...@cognite/sdk@3.3.1) (2020-12-14)

### Bug Fixes

- incorrect rotation types for 3d revision ([#452](https://github.com/cognitedata/cognite-sdk-js/issues/452)) ([4bfd3a5](https://github.com/cognitedata/cognite-sdk-js/commit/4bfd3a5efc6afcd4cd03ce6115254d24f61c483d))

# [3.3.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@3.2.1...@cognite/sdk@3.3.0) (2020-11-25)

### Bug Fixes

- datasets ID scope in datasets ACLs ([a9d7a44](https://github.com/cognitedata/cognite-sdk-js/commit/a9d7a440430c09ac8141fe8cc4799a43b7b70f9c))

### Features

- **relationships:** promote new api to stable ([526becc](https://github.com/cognitedata/cognite-sdk-js/commit/526beccaeb95371bc6504da2b434b6095415a878))

## [3.2.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@3.2.0...@cognite/sdk@3.2.1) (2020-11-18)

**Note:** Version bump only for package @cognite/sdk

# [3.2.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@3.1.1...@cognite/sdk@3.2.0) (2020-11-18)

### Features

- **files:** add geoLocation property ([#445](https://github.com/cognitedata/cognite-sdk-js/issues/445)) ([f5ff945](https://github.com/cognitedata/cognite-sdk-js/commit/f5ff945ab8548b3a811baf68e0830f1af90fd8f2))

## [3.1.1](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@3.1.0...@cognite/sdk@3.1.1) (2020-09-17)

**Note:** Version bump only for package @cognite/sdk

# [3.1.0](https://github.com/cognitedata/cognite-sdk-js/compare/@cognite/sdk@3.0.0...@cognite/sdk@3.1.0) (2020-09-10)

### Bug Fixes

- exports from packages, add guide for making derived SDKs ([#421](https://github.com/cognitedata/cognite-sdk-js/issues/421)) ([a3a2eb0](https://github.com/cognitedata/cognite-sdk-js/commit/a3a2eb03645733c289591b187f19e55b5294fbc7))

### Features

- add labels for files api ([#420](https://github.com/cognitedata/cognite-sdk-js/issues/420)) ([2c0356b](https://github.com/cognitedata/cognite-sdk-js/commit/2c0356bccef4f38b0365448f7eb66230ef6c9e53))

# 3.0.0 (2020-08-03)

### Bug Fixes

- make Node3D.boundingBox optional ([#403](https://github.com/cognitedata/cognite-sdk-js/issues/403)) ([768b0d9](https://github.com/cognitedata/cognite-sdk-js/commit/768b0d96de43f5a58da4b894993f484dd19dc75f))

### Features

- **datapoints:** add unit property to retrieve response ([5187c1c](https://github.com/cognitedata/cognite-sdk-js/commit/5187c1c2c30eaecbe7089770ad5c727735f71fd3))
- removed `AssetClass`, `AssetList`, `TimeseriesClass`, `TimeSeriesList` ([9315a95](https://github.com/cognitedata/cognite-sdk-js/commit/9315a95360561429af2e6f050a1e13f9ac9a2979))
- renamed interfaces ([#388](https://github.com/cognitedata/cognite-sdk-js/issues/388)) ([7f2ef5d](https://github.com/cognitedata/cognite-sdk-js/commit/7f2ef5d83869bffa932d8bc6f25a305e25a4e954))
- **avents-aggregate:** aggregates moved to separate api ([3f7f183](https://github.com/cognitedata/cognite-sdk-js/commit/3f7f183f02f230fa3d727c6b9dfe155c526d6d2c))

### BREAKING CHANGES

- Node3D.boundingBox has always been optional in API. This commit just fixes the bug in documentation.
- **core:** Fields that can have arbitrary string names (Raw, Metadata) are no longer converted to Date objects if their names happen to be:
  `createdTime`, `lastUpdatedTime`, `uploadedTime`, `deletedTime`, `timestamp`, `sourceCreatedTime` or `sourceModifiedTime`.

- Helper classes that have been removed:
  - AssetClass
  - AssetList
  - TimeseriesClass
  - TimeSeriesList

For replacements, see [MIGRATION_GUIDE_2xx_3xx.md](https://developer.cognite.com/sdks/js/migration/#upgrade-javascript-sdk-2-x-to-3-x)

- Interfaces renamed:

  - AzureADConfigurationDTO -> AzureADConfiguration
  - DatapointsGetAggregateDatapoint -> DatapointsAggregates
  - DatapointsGetDatapoint -> Datapoints
  - DatapointsGetDoubleDatapoint -> DoubleDatapoints
  - DatapointsGetStringDatapoint -> StringDatapoints
  - DatapointsInsertProperties -> ExternalDatapoints
  - DatapointsPostDatapoint -> ExternalDatapointsQuery
  - ExternalFilesMetadata -> ExternalFileInfo
  - FilesMetadata -> FileInfo
  - GetAggregateDatapoint -> DatapointAggregate
  - GetDatapointMetadata -> DatapointInfo
  - GetDoubleDatapoint -> DoubleDatapoint
  - GetStringDatapoint -> StringDatapoint
  - GetTimeSeriesMetadataDTO -> Timeseries
  - OAuth2ConfigurationDTO -> OAuth2Configuration
  - PostDatapoint -> ExternalDatapoint
  - PostTimeSeriesMetadataDTO -> ExternalTimeseries
  - TimeSeriesSearchDTO -> TimeseriesSearchFilter
  - TimeseriesFilter -> TimeseriesFilterQuery (structure changed)
  - TimeseriesFilterProps -> TimeseriesFilter
  - UploadFileMetadataResponse -> FileUploadResponse

- Interfaces removed:

  - Filter (use TimeseriesFilter)
  - Search (use TimeseriesSearch)

- `timeseries.list()` signature is now consistent with other resource types

- **avents-aggregate:** Event aggregate methods moved to a separate api.

`client.events.aggregate(...)` -> `client.events.aggregate.count()`
`client.events.uniqueValuesAggregate(...)` -> `client.events.aggregate.uniqueValues(...)`

# [2.33.0](https://github.com/cognitedata/cognite-sdk-js/compare/v2.32.0...v2.33.0) (2020-07-09)

### Features

- **projects:** added application domains to the update object ([83c67ec](https://github.com/cognitedata/cognite-sdk-js/commit/83c67ec7c693eb4b0e7df0670299a7b27e99743e))

# [2.32.0](https://github.com/cognitedata/cognite-sdk-js/compare/v2.31.0...v2.32.0) (2020-06-26)

### Features

- **labels:** new resource type, works with assets ([e275b76](https://github.com/cognitedata/cognite-sdk-js/commit/e275b766d4ce82b264e23467dad59833679d2f53))
- **timeseries:** add synthetic timeseries query ([#375](https://github.com/cognitedata/cognite-sdk-js/issues/375)) ([72723d1](https://github.com/cognitedata/cognite-sdk-js/commit/72723d13d244c4dfb3aa556b0300f698dffcaa1e))

# [2.31.0](https://github.com/cognitedata/cognite-sdk-js/compare/v2.30.0...v2.31.0) (2020-06-23)

### Features

- **events:** unique values aggregate ([#377](https://github.com/cognitedata/cognite-sdk-js/issues/377)) ([d47152c](https://github.com/cognitedata/cognite-sdk-js/commit/d47152c4873274648e9bf525737810ef14e9572c))

# [2.30.0](https://github.com/cognitedata/cognite-sdk-js/compare/v2.29.0...v2.30.0) (2020-06-18)

### Features

- export Revision3DStatus type ([#374](https://github.com/cognitedata/cognite-sdk-js/issues/374)) ([54112f0](https://github.com/cognitedata/cognite-sdk-js/commit/54112f02a6dacd3afcbbdda529c762a156de25aa))

# [2.29.0](https://github.com/cognitedata/cognite-sdk-js/compare/v2.28.0...v2.29.0) (2020-06-08)

### Features

- **files:** create + update security categories ([#372](https://github.com/cognitedata/cognite-sdk-js/issues/372)) ([f7192ca](https://github.com/cognitedata/cognite-sdk-js/commit/f7192cae4d55192ae09d394718c755d025610ea4))

# [2.28.0](https://github.com/cognitedata/cognite-sdk-js/compare/v2.27.1...v2.28.0) (2020-05-19)

### Features

- **retrieve:** ignoreUnknownIds for events/sequences/files/timeseries ([a905795](https://github.com/cognitedata/cognite-sdk-js/commit/a905795a7bdeef49886c1cc687c1b6d8fe8fda42))

## [2.27.1](https://github.com/cognitedata/cognite-sdk-js/compare/v2.27.0...v2.27.1) (2020-04-29)

### Bug Fixes

- authentication with a token in node environment ([08b11b9](https://github.com/cognitedata/cognite-sdk-js/commit/08b11b97c5d2d7e90a5a74fc10f961ca46c8d796))

# [2.27.0](https://github.com/cognitedata/cognite-sdk-js/compare/v2.26.0...v2.27.0) (2020-04-28)

### Features

- **events:** filter for ongoing and active events ([#365](https://github.com/cognitedata/cognite-sdk-js/issues/365)) ([fb5d4e9](https://github.com/cognitedata/cognite-sdk-js/commit/fb5d4e943db17784ad5b5270a078276235416fe2))

# [2.26.0](https://github.com/cognitedata/cognite-sdk-js/compare/v2.25.0...v2.26.0) (2020-03-25)

### Features

- add 'extra' field to CogniteError ([#362](https://github.com/cognitedata/cognite-sdk-js/issues/362)) ([c263f6d](https://github.com/cognitedata/cognite-sdk-js/commit/c263f6de45f3b769416a34514335aceaa4179f0d))

# [2.25.0](https://github.com/cognitedata/cognite-sdk-js/compare/v2.24.1...v2.25.0) (2020-03-19)

### Features

- **timeseries, sequences, files:** added aggregate support ([0aa83b9](https://github.com/cognitedata/cognite-sdk-js/commit/0aa83b99b31d0c6323527317447d154d2b8b5d8c))

## [2.24.1](https://github.com/cognitedata/cognite-sdk-js/compare/v2.24.0...v2.24.1) (2020-03-11)

### Bug Fixes

- deploy pipeline ([#355](https://github.com/cognitedata/cognite-sdk-js/issues/355)) ([3ca80f2](https://github.com/cognitedata/cognite-sdk-js/commit/3ca80f2c0226ee12fb21e83944da2b7a5634528c))

# [2.24.0](https://github.com/cognitedata/cognite-sdk-js/compare/v2.23.0...v2.24.0) (2020-03-11)

### Features

- Introduce support for Data sets. Document and track data lineage, ensure data integrity, and allow 3rd parties to write their insights securely back to your Cognite Data Fusion (CDF) project. Learn more about data sets [here.](https://hub.cognite.com/product-updates/cognite-data-fusion-introducing-data-sets-369)

# [2.22.0](https://github.com/cognitedata/cognite-sdk-js/compare/v2.21.0...v2.22.0) (2020-03-05)

### Features

- **datapoints:** added ignoreUnknownIds to getAllDatapoints ([aa25062](https://github.com/cognitedata/cognite-sdk-js/commit/aa250621a860070260b024b00a06c598d687f2af))
- **datapoints:** added ignoreUnknownIds to retrieveLatest in datapoints ([60832e6](https://github.com/cognitedata/cognite-sdk-js/commit/60832e6d00ba915232433569cde9ec45312311ae))
- **events:** added type and subtype to event update ([e3fdf74](https://github.com/cognitedata/cognite-sdk-js/commit/e3fdf7407eeb6f59ba5fb8e75616b0b105a8cf6d))

# [2.21.0](https://github.com/cognitedata/cognite-sdk-js/compare/v2.20.0...v2.21.0) (2020-02-26)

### Features

- **assets-and-events:** aggregate endpoint ([#350](https://github.com/cognitedata/cognite-sdk-js/issues/350)) ([efe933f](https://github.com/cognitedata/cognite-sdk-js/commit/efe933f8f5577a32c6f93d2698025325ace20c9b))

# [2.20.0](https://github.com/cognitedata/cognite-sdk-js/compare/v2.19.0...v2.20.0) (2020-02-20)

### Features

- **assets:** ignoreUnknownIds and aggregates for retrieve(...) ([cb04c46](https://github.com/cognitedata/cognite-sdk-js/commit/cb04c46172e52bcae0409890faebecc1f50fad28))

# [2.19.0](https://github.com/cognitedata/cognite-sdk-js/compare/v2.18.0...v2.19.0) (2020-02-12)

### Features

- **assets:** added support for depth & path aggregates ([#347](https://github.com/cognitedata/cognite-sdk-js/issues/347)) ([367d163](https://github.com/cognitedata/cognite-sdk-js/commit/367d163c683d262f909d42c6685c8c4bd2ed5a35))

# [2.18.0](https://github.com/cognitedata/cognite-sdk-js/compare/v2.17.0...v2.18.0) (2020-02-11)

### Features

- add getDefaultRequestHeaders() to get headers required by the API ([c5f01eb](https://github.com/cognitedata/cognite-sdk-js/commit/c5f01eb2ac3f5938032a55ede184ec0b62ad2111))

# [2.17.0](https://github.com/cognitedata/cognite-sdk-js/compare/v2.16.2...v2.17.0) (2020-02-10)

### Bug Fixes

- correct type on deleteDatapointsEndpoint method ([32630b7](https://github.com/cognitedata/cognite-sdk-js/commit/32630b72c551991c74b9d99b50df14ac6f717264))

### Features

- return parentExternalId for assets ([#346](https://github.com/cognitedata/cognite-sdk-js/issues/346)) ([c464632](https://github.com/cognitedata/cognite-sdk-js/commit/c464632b9226ac89ac39955235a4c896c2a24c8c))

## [2.16.2](https://github.com/cognitedata/cognite-sdk-js/compare/v2.16.1...v2.16.2) (2020-01-17)

### Bug Fixes

- capitalize all HTTP method strings ([dedd4de](https://github.com/cognitedata/cognite-sdk-js/commit/dedd4de379c460b9dcdd370b44b0c4a7bdffd6a9))

## [2.16.1](https://github.com/cognitedata/cognite-sdk-js/compare/v2.16.0...v2.16.1) (2019-12-11)

### Bug Fixes

- support all query parameters on client.raw.listRows(...) ([03681cd](https://github.com/cognitedata/cognite-sdk-js/commit/03681cd9cb6823e7949f4605a404684b4c0a7556))

# [2.16.0](https://github.com/cognitedata/cognite-sdk-js/compare/v2.15.0...v2.16.0) (2019-12-11)

### Features

- support assetSubtreeIds and assetExternalIds filters ([2f34364](https://github.com/cognitedata/cognite-sdk-js/commit/2f34364af3423f4eabc9b8da7a01d1326c9b9354))
- **assets:** parentExternalIds filter ([3579640](https://github.com/cognitedata/cognite-sdk-js/commit/3579640b694ded55858b2409d6f316fab36d2f09))
- **files:** source created/modified time, rootAssetIds, assetSubtreeIds ([3886331](https://github.com/cognitedata/cognite-sdk-js/commit/388633134b5621cd4f85e07ae1d27904c807c261))
- **sequences:** assetSubtreeIds filter ([b5f7ece](https://github.com/cognitedata/cognite-sdk-js/commit/b5f7ece4ba91362fc403f80c3aa341e9c83763b7))

# [2.15.0](https://github.com/cognitedata/cognite-sdk-js/compare/v2.14.0...v2.15.0) (2019-12-02)

### Features

- add HTTP patch method to httpClient ([e37614d](https://github.com/cognitedata/cognite-sdk-js/commit/e37614d57a6f4bf2be29916960fab507d7129113))

# [2.14.0](https://github.com/cognitedata/cognite-sdk-js/compare/v2.13.0...v2.14.0) (2019-11-25)

### Features

- **capability:** add type for scope with time series ids ([5492adf](https://github.com/cognitedata/cognite-sdk-js/commit/5492adf5975e39c00210164bae3a50abf38b8d41))

# [2.13.0](https://github.com/cognitedata/cognite-sdk-js/compare/v2.12.0...v2.13.0) (2019-11-21)

### Features

- **asset mappings 3d:** add intersectsBoundingBox to filter [DEVX-185] ([a845b56](https://github.com/cognitedata/cognite-sdk-js/commit/a845b56b65c8d5fe4a699c10aead488179b4c44a))

# [2.12.0](https://github.com/cognitedata/cognite-sdk-js/compare/v2.11.1...v2.12.0) (2019-11-19)

### Features

- **events api:** support sorting [DEVX-145] ([97883ee](https://github.com/cognitedata/cognite-sdk-js/commit/97883eede2d6cdb88b466a11b12d4ae9f21cc6c8))

## [2.11.1](https://github.com/cognitedata/cognite-sdk-js/compare/v2.11.0...v2.11.1) (2019-11-18)

### Bug Fixes

- expose HttpError from main module ([12ecffa](https://github.com/cognitedata/cognite-sdk-js/commit/12ecffab8c4a2fa2ac0fe6e2d991922fbc87ea09))

# [2.11.0](https://github.com/cognitedata/cognitesdk-js/compare/v2.10.0...v2.11.0) (2019-11-09)

### Features

- add support for one-time sdk header ([ff9931f](https://github.com/cognitedata/cognitesdk-js/commit/ff9931fa5763c8ac788d6b40a451e87edd9d160c))

# [2.10.0](https://github.com/cognitedata/cognitesdk-js/compare/v2.9.0...v2.10.0) (2019-11-06)

### Features

- **assets, events:** add list partition parameter ([96db310](https://github.com/cognitedata/cognitesdk-js/commit/96db31019e87c2a73d6ab31a1ebac7137db73ed3))

# [2.9.0](https://github.com/cognitedata/cognitesdk-js/compare/v2.8.0...v2.9.0) (2019-11-06)

### Features

- **timeseries:** support advanced filters, partitions [DEVX-162] ([fbd4d69](https://github.com/cognitedata/cognitesdk-js/commit/fbd4d69db17a9e6a4920d587899762ba4615a335))

# [2.8.0](https://github.com/cognitedata/cognitesdk-js/compare/v2.7.2...v2.8.0) (2019-11-05)

### Features

- add query parameters for assets search ([#309](https://github.com/cognitedata/cognitesdk-js/issues/309)) ([438b134](https://github.com/cognitedata/cognitesdk-js/commit/438b1346b4995e8089c774132d5bf32db37906b4))

## [2.7.2](https://github.com/cognitedata/cognitesdk-js/compare/v2.7.1...v2.7.2) (2019-10-29)

### Bug Fixes

- assets acl actions enum didn't match API value [DEVX-126] ([98b3733](https://github.com/cognitedata/cognitesdk-js/commit/98b37331fd56a847b68ea4de2f721532696c4ac3))

## [2.7.1](https://github.com/cognitedata/cognitesdk-js/compare/v2.7.0...v2.7.1) (2019-10-22)

### Bug Fixes

- message serialisation for non-api errors ([b33e758](https://github.com/cognitedata/cognitesdk-js/commit/b33e7589ac6295c6d30185be5f04d5601675db3f))

# [2.7.0](https://github.com/cognitedata/cognitesdk-js/compare/v2.6.1...v2.7.0) (2019-10-21)

### Features

- added withCredentials option to send raw HTTP request to other domains ([#299](https://github.com/cognitedata/cognitesdk-js/issues/299)) ([036dedc](https://github.com/cognitedata/cognitesdk-js/commit/036dedcbb8bf3e0e5df493401207bd25f84ea801))

## [2.6.1](https://github.com/cognitedata/cognitesdk-js/compare/v2.6.0...v2.6.1) (2019-10-13)

### Bug Fixes

- **autoPagination:** failing autoPagination - recursively add .next-property ([f7208cf](https://github.com/cognitedata/cognitesdk-js/commit/f7208cf4c5ef0135112ee6ba08c8ee163c65d616))

# [2.6.0](https://github.com/cognitedata/cognitesdk-js/compare/v2.5.1...v2.6.0) (2019-10-07)

### Bug Fixes

- export asset and timeseries helper classes ([7cde371](https://github.com/cognitedata/cognitesdk-js/commit/7cde371))
- export auth-related types ([7f24e74](https://github.com/cognitedata/cognitesdk-js/commit/7f24e74))

### Features

- add metadata property to 3d models and revisions ([04bffc6](https://github.com/cognitedata/cognitesdk-js/commit/04bffc6))

## [2.5.1](https://github.com/cognitedata/cognitesdk-js/compare/v2.5.0...v2.5.1) (2019-10-07)

### Bug Fixes

- export error types from main module ([c5fc35d](https://github.com/cognitedata/cognitesdk-js/commit/c5fc35d))

# [2.5.0](https://github.com/cognitedata/cognitesdk-js/compare/v2.4.3...v2.5.0) (2019-10-02)

### Features

- **new api:** sequences api support ([00bcb4a](https://github.com/cognitedata/cognitesdk-js/commit/00bcb4a))

## [2.4.3](https://github.com/cognitedata/cognitesdk-js/compare/v2.4.2...v2.4.3) (2019-09-27)

### Bug Fixes

- support baseUrl with path ([e8670a9](https://github.com/cognitedata/cognitesdk-js/commit/e8670a9))

## [2.4.2](https://github.com/cognitedata/cognitesdk-js/compare/v2.4.1...v2.4.2) (2019-09-25)

### Bug Fixes

- **OAuth:** add warning when using OAuth without SSL ([a81b08d](https://github.com/cognitedata/cognitesdk-js/commit/a81b08d))

## [2.4.1](https://github.com/cognitedata/cognitesdk-js/compare/v2.4.0...v2.4.1) (2019-09-18)

### Bug Fixes

- convert api class methods to arrow functions ([f200589](https://github.com/cognitedata/cognitesdk-js/commit/f200589))

# [2.4.0](https://github.com/cognitedata/cognitesdk-js/compare/v2.3.1...v2.4.0) (2019-09-16)

### Features

- asset childCount-aggregates ([79129ce](https://github.com/cognitedata/cognitesdk-js/commit/79129ce))

## [2.3.1](https://github.com/cognitedata/cognitesdk-js/compare/v2.3.0...v2.3.1) (2019-09-16)

### Bug Fixes

- only data props on Timeseries and Asset class are enumerable ([a2f412d](https://github.com/cognitedata/cognitesdk-js/commit/a2f412d))

# [2.3.0](https://github.com/cognitedata/cognitesdk-js/compare/v2.2.2...v2.3.0) (2019-09-10)

### Bug Fixes

- incorrect types on timeseries filters and missing filters ([363a00b](https://github.com/cognitedata/cognitesdk-js/commit/363a00b))
- safely delete assets with chunking ([424cba9](https://github.com/cognitedata/cognitesdk-js/commit/424cba9))

### Features

- custom toString & toJSON methods for resource class ([1eae87b](https://github.com/cognitedata/cognitesdk-js/commit/1eae87b))
- expose logout.getUrl() endpoint ([a85c4a0](https://github.com/cognitedata/cognitesdk-js/commit/a85c4a0))
- expose projectId on login.status() ([00ab757](https://github.com/cognitedata/cognitesdk-js/commit/00ab757))
- list 3d nodes with property filtering ([8b85817](https://github.com/cognitedata/cognitesdk-js/commit/8b85817))
- use cached access token to skip auth flow ([b28f506](https://github.com/cognitedata/cognitesdk-js/commit/b28f506))

## [2.2.2](https://github.com/cognitedata/cognitesdk-js/compare/v2.2.1...v2.2.2) (2019-08-12)

### Bug Fixes

- **delete datapoints:** make 'exclusiveEnd' filter prop as optional ([d7f2e82](https://github.com/cognitedata/cognitesdk-js/commit/d7f2e82))
- replaced tuple type with an array for better user experience ([d65e4eb](https://github.com/cognitedata/cognitesdk-js/commit/d65e4eb))

## [2.2.1](https://github.com/cognitedata/cognitesdk-js/compare/v2.2.0...v2.2.1) (2019-08-09)

### Bug Fixes

- retry ETIMEDOUT connection failures ([b82736c](https://github.com/cognitedata/cognitesdk-js/commit/b82736c))

# [2.2.0](https://github.com/cognitedata/cognitesdk-js/compare/v2.1.0...v2.2.0) (2019-08-09)

### Features

- add timeSeries and timeSeriesList class ([#258](https://github.com/cognitedata/cognitesdk-js/issues/258)) ([2aeb12f](https://github.com/cognitedata/cognitesdk-js/commit/2aeb12f))

# [2.1.0](https://github.com/cognitedata/cognitesdk-js/compare/v2.0.4...v2.1.0) (2019-08-05)

### Features

- asset & assetList class ([66c4d5e](https://github.com/cognitedata/cognitesdk-js/commit/66c4d5e))

## [2.0.4](https://github.com/cognitedata/cognitesdk-js/compare/v2.0.3...v2.0.4) (2019-08-05)

### Bug Fixes

- added missing recursive flag to RAW deleteDatabase ([105cab0](https://github.com/cognitedata/cognitesdk-js/commit/105cab0))

## [2.0.3](https://github.com/cognitedata/cognitesdk-js/compare/v2.0.2...v2.0.3) (2019-08-01)

### Bug Fixes

- Added missing props to Event-filter ([33c7de0](https://github.com/cognitedata/cognitesdk-js/commit/33c7de0))
- Added missing rootId prop to Asset ([9e4a169](https://github.com/cognitedata/cognitesdk-js/commit/9e4a169))


<!-- SOURCE_END: cognite-sdk-js/packages\stable\CHANGELOG.md -->

================================================================================


<!-- SOURCE_START: cognite-sdk-js/packages\stable\README.md -->
## File: cognite-sdk-js/packages\stable\README.md

# Cognite Javascript SDK

The package `@cognite/sdk` provides convenient access to the stable [Cognite API](https://doc.cognitedata.com/dev/)
from applications written in client- or server-side javascript.

The SDK supports authentication through bearer tokens.
See [Authentication Guide](https://github.com/cognitedata/cognite-sdk-js/blob/v1/guides/authentication.md).

## Installation

Install the package with yarn:

```
$ yarn add @cognite/sdk
```

or npm

```
$ npm install @cognite/sdk --save
```

## Usage

```js
const { CogniteClient } = require('@cognite/sdk');
```

### Using ES modules

```js
import { CogniteClient } from '@cognite/sdk';
```

### Using typescript

The SDK is written in native typescript, so no extra types need to be defined.

## Quickstart

### Web

```js
import { CogniteClient } from '@cognite/sdk';

async function quickstart() {
  const project = 'publicdata';
  const oidcTokenProvider = async () => {
    return 'YOUR_OIDC_ACCESS_TOKEN';
  };

  const client = new CogniteClient({
    appId: 'YOUR APPLICATION NAME',
    project,
    oidcTokenProvider,
  });

  const assets = await client.assets.list().autoPagingToArray({ limit: 100 });
}
quickstart();
```

> For more details about SDK authentication see this [document](https://github.com/cognitedata/cognite-sdk-js/blob/master/guides/authentication.md).
> Also, more comprehensive intro guide can be found [here](https://docs.cognite.com/dev/guides/sdk/js/)

### Backend

```js
const { CogniteClient } = require('@cognite/sdk');

async function quickstart() {
  const client = new CogniteClient({
    appId: 'YOUR APPLICATION NAME',
    oidcTokenProvider: () => Promise.resolve('YOUR_OIDC_ACCESS_TOKEN'),
  });

  const assets = await client.assets.list().autoPagingToArray({ limit: 100 });
}
quickstart();
```

## Documentation

- [API reference documentation](https://doc.cognitedata.com/api/v1)
- [JS SDK reference documentation](https://cognitedata.github.io/cognite-sdk-js/classes/cogniteclient.html)

## Best practices

### No submodule imports

We highly recommend avoiding importing anything from internal SDK modules.

All interfaces and functions should only be imported from the top level, otherwise you might face compatibility issues when our internal structure changes.

**Bad:**

```
import { CogniteAsyncIterator } from '@cognite/sdk/dist/src/autoPagination'; // ❌
import { AssetsAPI } from '@cognite/sdk/dist/src/resources/assets/assetsApi'; // ❌

let assetsApi: AssetsAPI; // ❌
```

**Good:**

```
import { CogniteAsyncIterator } from '@cognite/sdk'; // ✅

let assetsApi: CogniteClient['assets']; // ✅
```

We recommend the usage of [eslint](https://eslint.org/docs/rules/no-restricted-imports) to ensure this best practice is enforced without having to memorize the patterns:

**.eslintrc.json:**

```
"rules": {
  "no-restricted-imports": ["error", { "patterns": ["@cognite/sdk/**"] }]
}
```

The API reference documentation contains snippets for each endpoint,
giving examples of SDK use. See also the [samples section](https://github.com/cognitedata/cognite-sdk-js#samples) in this repo.

## Guides

- [Migration guide](https://github.com/cognitedata/cognite-sdk-js/blob/master/guides/MIGRATION_GUIDE_1xx_2xx.md)
  on how to migrate from version `1.x.x` to version `2.x.x`.
- [Migration guide](https://github.com/cognitedata/cognite-sdk-js/blob/master/guides/MIGRATION_GUIDE_2xx_3xx.md) from version `2.x.x` to version `3.x.x`.


<!-- SOURCE_END: cognite-sdk-js/packages\stable\README.md -->

================================================================================


<!-- SOURCE_START: cognite-sdk-js/packages\stable\src\api\containers\README.md -->
## File: cognite-sdk-js/packages\stable\src\api\containers\README.md

# Notice on code generation for containers API
Code generation is currently unreliable in containers due to codegen not correctly
detecting all shared types across the different data modeling APIs.

Until this is resolved, follow the following process to generate new types:
1. Rename `codegen.skip.json` to `codegen.json`
2. Generate types with `yarn codegen generate-types --package=stable`
3. Copy the new instance autogenerated exports from `../exports.gen.ts` into the corresponding existing exports for instances in `../types.ts` and overwrite the old ones.
4. Resolve any new duplicate types by deleting them from the newly added export lines.
5. Delete the newly generated exports in `types.gen.ts`
6. Rename `codegen.json` to `codegen.skip.json`


<!-- SOURCE_END: cognite-sdk-js/packages\stable\src\api\containers\README.md -->

================================================================================


<!-- SOURCE_START: cognite-sdk-js/packages\stable\src\api\instances\README.md -->
## File: cognite-sdk-js/packages\stable\src\api\instances\README.md

# Notice on code generation for instances API
Code generation is currently unreliable in instances due to codegen not correctly
detecting all shared types across the different data modeling APIs.

Until this is resolved, follow the following process to generate new types:
1. Rename `codegen.skip.json` to `codegen.json`
2. Generate types with `yarn codegen generate-types --package=stable`
3. Copy the new instance autogenerated exports from `../exports.gen.ts` into the corresponding existing exports for instances in `../types.ts` and overwrite the old ones.
4. Resolve any new duplicate types by deleting them from the newly added export lines.
5. Delete the newly generated exports in `types.gen.ts`
6. Rename `codegen.json` to `codegen.skip.json`


<!-- SOURCE_END: cognite-sdk-js/packages\stable\src\api\instances\README.md -->

================================================================================


<!-- SOURCE_START: cognite-sdk-js/packages\stable\src\api\models\README.md -->
## File: cognite-sdk-js/packages\stable\src\api\models\README.md

# Notice on code generation for models API
Code generation is currently unreliable in models due to codegen not correctly
detecting all shared types across the different data modeling APIs.

Until this is resolved, follow the following process to generate new types:
1. Rename `codegen.skip.json` to `codegen.json`
2. Generate types with `yarn codegen generate-types --package=stable`
3. Copy the new instance autogenerated exports from `../exports.gen.ts` into the corresponding existing exports for instances in `../types.ts` and overwrite the old ones.
4. Resolve any new duplicate types by deleting them from the newly added export lines.
5. Delete the newly generated exports in `types.gen.ts`
6. Rename `codegen.json` to `codegen.skip.json`


<!-- SOURCE_END: cognite-sdk-js/packages\stable\src\api\models\README.md -->

================================================================================


<!-- SOURCE_START: cognite-sdk-js/packages\stable\src\api\views\README.md -->
## File: cognite-sdk-js/packages\stable\src\api\views\README.md

# Notice on code generation for views API
Code generation is currently unreliable in views due to codegen not correctly
detecting all shared types across the different data modeling APIs.

Until this is resolved, follow the following process to generate new types:
1. Rename `codegen.skip.json` to `codegen.json`
2. Generate types with `yarn codegen generate-types --package=stable`
3. Copy the new instance autogenerated exports from `../exports.gen.ts` into the corresponding existing exports for instances in `../types.ts` and overwrite the old ones.
4. Resolve any new duplicate types by deleting them from the newly added export lines.
5. Delete the newly generated exports in `types.gen.ts`
6. Rename `codegen.json` to `codegen.skip.json`


<!-- SOURCE_END: cognite-sdk-js/packages\stable\src\api\views\README.md -->

================================================================================


<!-- SOURCE_START: cognite-sdk-js/packages\template\README.md -->
## File: cognite-sdk-js/packages\template\README.md

Cognite Javascript SDK (derived)
================================
This package provides an SDK derived from `@cognite/sdk`, aka
[stable](https://github.com/cognitedata/cognite-sdk-js/blob/master/packages/stable/README.md).

It is recomended to install this package under the same name as `@cognite/sdk`.
This allows you to change SDK versions without changing your imports.
See the [beta readme](https://github.com/cognitedata/cognite-sdk-js/blob/master/packages/beta/README.md) for details.



<!-- SOURCE_END: cognite-sdk-js/packages\template\README.md -->

================================================================================


<!-- SOURCE_START: cognite-sdk-js/README.md -->
## File: cognite-sdk-js/README.md

<a href="https://cognite.com/">
    <img src="./cognite_logo.png" alt="Cognite logo" title="Cognite" align="right" height="80" />
</a>

# Cognite Javascript SDK

[![CD status](https://github.com/cognitedata/cognite-sdk-js/actions/workflows/release.yaml/badge.svg)](https://github.com/cognitedata/cognite-sdk-js/actions/workflows/release.yaml)
[![codecov](https://codecov.io/gh/cognitedata/cognite-sdk-js/branch/master/graph/badge.svg)](https://codecov.io/gh/cognitedata/cognite-sdk-js)

The Cognite js library provides convenient access to the [Cognite API](https://doc.cognitedata.com/dev/) from
applications written in client- or server-side JavaScript.

## Getting Started

This repository contains several packages for different API versions.

To get started with the stable API, see the README [here](./packages/stable/README.md).

There is also a [beta API](./packages/beta/README.md).

## Samples

There are small bare-bones javascript (and typescript) projects in the `samples/` directory.
They show how to include the cognite SDK in various project setups.
The samples' [README.md](./samples/README.md) has instructions for running the samples.

## Authentication

This section provides guidance on how to integrate and use the authentication feature for our SDK, leveraging OpenID Connect (OIDC) as the primary authentication protocol. The OIDC implementation ensures a secure and reliable user authentication process, enhancing the security of your application.

### Prerequisites
Before you begin, ensure that you have the following:

- Access to an OpenID Connect (OIDC) compatible Identity Provider (IdP) for authentication (e.g., MSAL, Auth0).
- A valid client ID and client secret provided by your OIDC IdP.
- The redirect_uri registered with your OIDC IdP for your application.
- Our SDK installed and integrated into your application.

### Setting up OIDC Authentication

- Initialize the SDK: Import and initialize the SDK in your application, providing the necessary configuration options such as the client ID, client secret, and redirect URI obtained from your OIDC IdP.
- Setup a token provider using the `oidcTokenProvider` property of SDK. Here you can provide a valid access token for the CDF API.

For code example you can check [quickstart.ts](https://github.com/cognitedata/cognite-sdk-js/blob/master/samples/nodejs/oidc-typescript/quickstart.ts#L1)

### Browser authentication

Please see [this guide](./guides/authentication.md) for a detailed guide.

## Response header & http status

Methods are designed to only return the response body. For fetching the http response status and/or header you must utilize client.getMetadata:

```ts
const createdAsset = await client.assets.create([{ name: 'My first asset' }]);
const metadata = client.getMetadata(createdAsset);

console.log(metadata.header['Access-Control-Allow-Origin']);
console.log(metadata.status);
```

## License

[Apache 2.0](https://www.apache.org/licenses/LICENSE-2.0)

## Development

The SDK is implemented as a package in a monorepo, together with core logic, beta versions and samples.

### Contributing

Contributions welcome!
For details about commiting changes, type generation, automated versioning and releases, see [Contributing](./CONTRIBUTING.md).

### Testing

This repo contains some integration tests that relies on OIDC specifically `msal-node` library. 

**Important to know**: 
- Some of the integration tests could be eventually consistent 
- Some of test cases are skipped due to expensive and heavy API calls which only need to run once
- `packages/stable/src/__tests__/api/groups.int.spec.ts` test relies on specific `testDataSetId`

Talk to any of the contributors or leave an issue and it'll get sorted.
GitHub Action will run the test and has its own secrets set.

Run tests:

```bash
yarn
yarn build
yarn test --since master
```

To run integration tests, you would have to pass the following environment variables:

- **COGNITE_PROJECT**
- **COGNITE_BASE_URL**
- **COGNITE_CLIENT_SECRET**
- **COGNITE_CLIENT_ID**
- **COGNITE_AZURE_DOMAIN**

Set the environment variable `REVISION_3D_INTEGRATION_TEST=true` to run 3D revision integration tests.

We use `vitest` to run tests, see [their documentation](https://vitest.dev/) for more information.

### Versioning

The libraries follow [Semantic Versioning](https://semver.org/).
Package versions are updated automatically and individually based on commit messages.

### CHANGELOG

Each package in the monorepo has its own changelog.

- [@cognite/sdk](./packages/stable/CHANGELOG.md)
- [@cognite/sdk-beta](./packages/beta/CHANGELOG.md)
- [@cognite/sdk-core](./packages/core/CHANGELOG.md)
- [@cognite/sdk-alpha](./packages/alpha/CHANGELOG.md)


<!-- SOURCE_END: cognite-sdk-js/README.md -->

================================================================================


<!-- SOURCE_START: cognite-sdk-js/samples\nodejs\oidc-typescript\README.md -->
## File: cognite-sdk-js/samples\nodejs\oidc-typescript\README.md

# OIDC authentication sample w typescript

Node.JS Typescript sample set up to use OIDC authentication.

## Getting Started

This kind of authentication will log you with the OIDC method.

## Prerequisite

Make sure you have read the [prerequisite-guide](../../README.md#prerequisite) before continuing.

## Install

Go to this folder in your terminal and run:

`npm install`

or with yarn:

`yarn`

## Run

`AZURE_TENANT_ID=... COGNITE_PROJECT=... CLIENT_ID=... CLIENT_SECRET=... node build/quickstart.ts`

## How to get logged by yourself?

1. Add the Cognite SDK and @azure/msal-node to your project with yarn or npm.

```sh
yarn add @cognite/sdk@6.1.1
yarn add @azure/msal-node
```

```sh
npm install @cognite/sdk@6.1.1
npm install @azure/msal-node
```

2. Create a new file called `quickstart.ts` and import all necessary things.

```sh
import { CogniteClient } from '@cognite/sdk';
import { ConfidentialClientApplication } from '@azure/msal-node';
```

3. Instantiate four consts with your project name, clientID, clientSecret and your tenantID.

```sh
const project: string = process.env.COGNITE_PROJECT!;
const clientId: string = process.env.CLIENT_ID!;
const clientSecret: string = process.env.CLIENT_SECRET!;
const azureTenant = process.env.AZURE_TENANT_ID!;
```

4. Create a function called `quickstart` with the following code:

```ts
async function quickstart() {
    const pca = new ConfidentialClientApplication({
      auth: {
        clientId,
        clientSecret,
        authority: `https://login.microsoftonline.com/${azureTenant}`,
      },
    });

    const client = new CogniteClient({
      appId: 'Cognite SDK samples',
      project,
      baseUrl: 'https://api.cognitedata.com',
      oidcTokenProvider: () =>
        pca
          .acquireTokenByClientCredential({
            scopes: ['https://api.cognitedata.com/.default'],
            skipCache: true,
          })
          .then((response) => response?.accessToken! as string),
    });

    await client.authenticate();

    const info = (await client.get('/api/v1/token/inspect')).data;

    console.log('tokenInfo', JSON.stringify(info, null, 2));

    try {
      const assets = await client.assets.list();
      console.log(assets);
    } catch (e) {
      console.log('asset error');
      console.log(e);
    } //
}
```

5. Call the `quickstart` function.

```ts
quickstart()
  .then(() => { process.exit(0); })
  .catch((err) => { console.error(err); process.exit(1); });
```

6. Build your project.

`npm run tsc`

or with yarn

`yarn tsc`

7. Finally, run your code with:

```sh
AZURE_TENANT_ID=... COGNITE_PROJECT=... CLIENT_ID=... CLIENT_SECRET=... node build/quickstart.js
```


<!-- SOURCE_END: cognite-sdk-js/samples\nodejs\oidc-typescript\README.md -->

================================================================================


<!-- SOURCE_START: cognite-sdk-js/samples\README.md -->
## File: cognite-sdk-js/samples\README.md

<a href="https://cognite.com/">
    <img src="../cognite_logo.png" alt="Cognite logo" title="Cognite" align="right" height="80" />
</a>

# JS SDK Examples

This folder contains an example on how to use the SDK in NodeJS.

For browser environments, please take a look at [this guide](../guides/authentication.md).

## Prerequisites

- Clone this repository locally. See guide [here](https://www.tutorialspoint.com/how-to-clone-a-github-repository).
- Install NodeJS on your machine. You can download NodeJS [here](https://nodejs.org/en/download/).
- Install dependencies by running `yarn`.
- Build sdk by running `yarn build`.

## Examples

- NodeJS
  - [Typescript using client credentials ](./nodejs/oidc-typescript)


<!-- SOURCE_END: cognite-sdk-js/samples\README.md -->

================================================================================
