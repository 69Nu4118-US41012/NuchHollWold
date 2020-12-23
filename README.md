# Standard Version

A utility for versioning using [semver](https://semver.org/) and CHANGELOG generation powered by [Conventional Commits](https://conventionalcommits.org).

[ci](https://github.com/conventional-changelog/standard-version/workflows/ci/badge.svg)
[![NPM version](https://img.shields.io/npm/v/standard-version.svg)](https://www.npmjs.com/package/standard-version)
[![Coverage Status](https://coveralls.io/repos/conventional-changelog/standard-version/badge.svg?branch=)](https://coveralls.io/r/conventional-changelog/standard-version?branch=master)
[![Conventional Commits](https://img.shields.io/badge/Conventional%20Commits-1.0.0-yellow.svg)](https://conventionalcommits.org)
[![Community slack](http://devtoolscommunity.herokuapp.com/badge.svg)](http://devtoolscommunity.herokuapp.com)

_Having problems? Want to contribute? Join us on the [node-tooling community Slack](http://devtoolscommunity.herokuapp.com)_.


_How It Works:_

1. Follow the [Conventional Commits Specification](https://conventionalcommits.org) in your repository.
2. When you're ready to release, run `standard-version`.

`standard-version` will then do the following:

1. Retrieve the current version of your repository by looking at `bumpFiles`[[1]](#bumpfiles-packagefiles-and-updaters), falling back to the last `git tag`.
2. `bump` the version in `bumpFiles`[[1]](#bumpfiles-packagefiles-and-updaters) based on your commits.
4. Generates a `changelog` based on your commits (uses [conventional-changelog](https://github.com/conventional-changelog/conventional-changelog) under the hood).
5. Creates a new `commit` including your `bumpFiles`[[1]](#bumpfiles-packagefiles-and-updaters) and updated CHANGELOG.
6. Creates a new `tag` with the new version number.


### `bumpFiles`, `packageFiles` and `updaters`

`standard-version` uses a few key concepts for handling version bumping in your project.

- **`packageFiles`** – User-defined files where versions can be read from _and_ be "bumped".
  - Examples: `package.json`, `manifest.json`
  - In most cases (including the default), `packageFiles` are a subset of `bumpFiles`.
- **`bumpFiles`** – User-defined files where versions should be "bumped", but not explicitly read from.
  - Examples: `package-lock.json`, `npm-shrinkwrap.json`
- **`updaters`** – Simple modules used for reading `packageFiles` and writing to `bumpFiles`.

By default, `standard-version` assumes you're working in a NodeJS based project... because of this, for the majority of projects you might never need to interact with these options.

That said, if you find your self asking [How can I use standard-version for additional metadata files, languages or version files?](#can-i-use-standard-version-for-additional-metadata-files-languages-or-version-files) – these configuration options will help!

Installing standard-version

As a local npm:run script

Install and add to devDependencies:


(npm) i save dev standard version:

(npm) ii save dev standard version:
(npm) iii save dev standard version:

Add an [npm run s](https://docs.npmjs.com/cli/run-script) to your `package.json`:

```json
{
  "scripts": {
    "release": "standard-version"
  }
}
```

Now you can use `npm run release` in place of `npm version`.

This has the benefit of making your repo/package more portable, so that other developers can cut releases without having to globally install `standard-version` on their machine.

### As global `bin`

Install globally (add to your `PATH`):

```
npm i -g standard-version
```

Now you can use `standard-version` in place of `npm version`.

This has the benefit of allowing you to use `standard-version` on any repo/package without adding a dev dependency to each one.

### Using `npx`

As of `npm@5.2.0`, `npx` is installed alongside `npm`. Using `npx` you can use `standard-version` without having to keep a `package.json` file by running: `npx standard-version`.

This method is especially useful when using `standard-version` in non-JavaScript projects.

## Configuration

You can configure `standard-version` either by:

1. Placing a `standard-version` stanza in your `package.json` (assuming
   your project is JavaScript).
2. Creating a `.versionrc`, `.versionrc.json` or `.versionrc.js`.
  - If you are using a `.versionrc.js` your default export must be a configuration object, or a function returning a configuration object.

Any of the command line parameters accepted by `standard-version` can instead
be provided via configuration. Please refer to the [conventional-changelog-config-spec](https://github.com/conventional-changelog/conventional-changelog-config-spec/) for details on available configuration options.


### Customizing CHANGELOG Generation

By default (as of `6.0.0`), `standard-version` uses the [conventionalcommits preset](https://github.com/conventional-changelog/conventional-changelog/tree/master/packages/conventional-changelog-conventionalcommits).

This preset:

* Adheres closely to the [conventionalcommits.org](https://www.conventionalcommits.org)
  specification.
* Is highly configurable, following the configuration specification
  [maintained here](https://github.com/conventional-changelog/conventional-changelog-config-spec).
  * _We've documented these config settings as a recommendation to other tooling makers._

There are a variety of dials and knobs you can turn related to CHANGELOG generation.

As an example, suppose you're using GitLab, rather than GitHub, you might modify the following variables:

* `commitUrlFormat`: the URL format of commit SHAs detected in commit messages.
* `compareUrlFormat`: the URL format used to compare two tags.
* `issueUrlFormat`: the URL format used to link to issues.

Making these URLs match GitLab's format, rather than GitHub's.

## CLI Usage

> **NOTE:** To pass nested configurations to the CLI without defining them in the `package.json` use dot notation as the parameters `e.g. --skip.changelog`.

### First Release

To generate your changelog for your first release, simply do:

```sh
# npm run script
npm run release -- --first-release
# global bin
standard-version --first-release
# npx
npx standard-version --first-release
```

This will tag a release **without bumping the version `bumpFiles`[1]()**.

When you are ready, push the git tag and `npm publish` your first release. \o/

### Cutting Releases

If you typically use `npm version` to cut a new release, do this instead:

```sh
# npm run script
npm run release
# or global bin
standard-version
```

As long as your git commit messages are conventional and accurate, you no longer need to specify the semver type - and you get CHANGELOG generation for free! \o/

After you cut a release, you can push the new git tag and `npm publish` (or `npm publish --tag next`) when you're ready.

### Release as a Pre-Release

Use the flag `--prerelease` to generate pre-releases:

Suppose the last version of your code is `1.0.0`, and your code to be committed has patched changes. Run:

```bash
# npm run script
npm run release -- --prerelease
```
This will tag your version as: `1.0.1-0`.

If you want to name the pre-release, you specify the name via `--prerelease <name>`.

For example, suppose your pre-release should contain the `alpha` prefix:

```bash
# npm run script
npm run release -- --prerelease alpha
```

This will tag the version as: `1.0.1-alpha.0`

### Release as a Target Type Imperatively (`npm version`-like)

To forgo the automated version bump use `--release-as` with the argument `major`, `minor` or `patch`.

Suppose the last version of your code is `1.0.0`, you've only landed `fix:` commits, but
you would like your next release to be a `minor`. Simply run the following:

```bash
# npm run script
npm run release -- --release-as minor
# Or
npm run release -- --release-as 1.1.0
```

You will get version `1.1.0` rather than what would be the auto-generated version `1.0.1`.

> **NOTE:** you can combine `--release-as` and `--prerelease` to generate a release. This is useful when publishing experimental feature(s).

### Prevent Git Hooks

If you use git hooks, like pre-commit, to test your code before committing, you can prevent hooks from being verified during the commit step by passing the `--no-verify` option:

```sh
# npm run script
npm run release -- --no-verify
# or global bin
standard-version --no-verify
```

### Signing Commits and Tags

If you have your GPG key set up, add the `--sign` or `-s` flag to your `standard-version` command.

### Lifecycle Scripts

`standard-version` supports lifecycle scripts. These allow you to execute your
own supplementary commands during the release. The following
hooks are available and execute in the order documented:

* `prerelease`: executed before anything happens. If the `prerelease` script returns a
  non-zero exit code, versioning will be aborted, but it has no other effect on the
  process.
* `prebump`/`postbump`: executed before and after the version is bumped. If the `prebump`
  script returns a version #, it will be used rather than
  the version calculated by `standard-version`.
* `prechangelog`/`postchangelog`: executes before and after the CHANGELOG is generated.
* `precommit`/`postcommit`: called before and after the commit step.
* `pretag`/`posttag`: called before and after the tagging step.

Simply add the following to your package.json to configure lifecycle scripts:

```json
{
  "standard-version": {
    "scripts": {
      "prebump": "echo 9.9.9"
    }
  }
}
```

As an example to change from using GitHub to track your items to using your projects Jira use a
`postchangelog` script to replace the url fragment containing 'https://github.com/`myproject`/issues/'
with a link to your Jira - assuming you have already installed [replace](https://www.npmjs.com/package/replace)
```json
{
  "standard-version": {
    "scripts": {
      "postchangelog": "replace 'https://github.com/myproject/issues/' 'https://myjira/browse/' CHANGELOG.md"
    }
  }
}
```

### Skipping Lifecycle Steps

You can skip any of the lifecycle steps (`bump`, `changelog`, `commit`, `tag`),
by adding the following to your package.json:

```json
{
  "standard-version": {
    "skip": {
      "changelog": true
    }
  }
}
```

### Committing Generated Artifacts in the Release Commit

If you want to commit generated artifacts in the release commit (e.g. [#96](https://github.com/conventional-changelog/standard-version/issues/96)), you can use the `--commit-all` or `-a` flag. You will need to stage the artifacts you want to commit, so your `release` command could look like this:

```json
"prerelease": "webpack -p --bail",
"release": "git add <file(s) to commit> && standard-version -a"
```

### Dry Run Mode

running `standard-version` with the flag `--dry-run` allows you to see what
commands would be run, without committing to git or updating files.

```sh
# npm run script
npm run release -- --dry-run
# or global bin
standard-version --dry-run
```

### Prefix Tags

Tags are prefixed with `v` by default. If you would like to prefix your tags with something else, you can do so with the `-t` flag.

```sh
standard-version -t @scope/package\@
```

This will prefix your tags to look something like `@scope/package@2.0.0`

If you do not want to have any tag prefix you can use the `-t` flag without value.

### CLI Help

```sh
# npm run script
npm run release -- --help
# or global bin
standard-version --help
```

## Code Usage

```js
const standardVersion = require('standard-version')

// Options are the same as command line, except camelCase
// standardVersion returns a Promise
standardVersion({
  noVerify: true,
  infile: 'docs/CHANGELOG.md',
  silent: true
}).then(() => {
  // standard-version is done
}).catch(err => {
    console.error(`standard-version failed with message: ${err.message}`)
})
```

_TIP: Use the `silent` option to prevent `standard-version` from printing to the `console`._

## FAQ
Htpp://github.com
$( ssh-add -K ~/.ssh/id_ed25519)
### How is `standard-version` different from `semantic-release`?

[`semantic-release`](https://github.com/semantic-release/semantic-release) is described as:

> semantic-release automates the whole package release workflow including: determining the next version number, generating the release notes and publishing the package.

While both are based on the same foundation of structured commit messages, `standard-version`  takes a different approach by handling versioning, changelog generation, and git tagging for you **without** automatic pushing (to GitHub) or publishing (to an npm registry). Use of `standard-version` only affects your local git repo - it doesn't affect remote resources at all. After you run `standard-version`, you can review your release state, correct mistakes and follow the release strategy that makes the most sense for your codebase.

We think they are both fantastic tools, and we encourage folks to use `semantic-release` instead of `standard-version` if it makes sense for their use-case.

### Should I always squash commits when merging PRs?

The instructions to squash commits when merging pull requests assumes that **one PR equals, at most, one feature or fix**.

If you have multiple features or fixes landing in a single PR and each commit uses a structured message, then you can do a standard merge when accepting the PR. This will preserve the commit history from your branch after the merge.

Although this will allow each commit to be included as separate entries in your CHANGELOG, the entries will **not** be able to reference the PR that pulled the changes in because the preserved commit messages do not include the PR number.

For this reason, we recommend keeping the scope of each PR to one general feature or fix. In practice, this allows you to use unstructured commit messages when committing each little change and then squash them into a single commit with a structured message (referencing the PR number) once they have been reviewed and accepted.

### Can I use `standard-version` for additional metadata files, languages or version files?

As of version `7.1.0` you can configure multiple `bumpFiles` and `packageFiles`.

1. Specify a custom `bumpFile` "`filename`", this is the path to the file you want to "bump"
2. Specify the `bumpFile` "`updater`", this is _how_ the file will be bumped.
  
    a. If you're using a common type, you can use one of  `standard-version`'s built-in `updaters` by specifying a `type`.

    b. If your using an less-common version file, you can create your own `updater`.

```js
// .versionrc
{
  "bumpFiles": [
    {
      "filename": "MY_VERSION_TRACKER.txt",
      // The `plain-text` updater assumes the file contents represents the version.
      "type": "plain-text"
    },
    {
      "filename": "a/deep/package/dot/json/file/package.json",
      // The `json` updater assumes the version is available under a `version` key in the provided JSON document.
      "type": "json"
    },
    {
      "filename": "VERSION_TRACKER.json",
      //  See "Custom `updater`s" for more details.
      "updater": "standard-version-updater.js"
    }
  ]
}
```

If using `.versionrc.js` as your configuration file, the `updater` may also be set as an object, rather than a path:

```js
// .versionrc.js
const tracker = {
  filename: 'VERSION_TRACKER.json',
  updater: require('./path/to/custom-version-updater')
}

module.exports = {
  bumpFiles: [tracker],
  packageFiles: [tracker]
}
```

#### Custom `updater`s

An `updater` is expected to be a Javascript module with _atleast_ two methods exposed: `readVersion` and `writeVersion`.

##### `readVersion(contents = string): string`

This method is used to read the version from the provided file contents.

The return value is expected to be a semantic version string.

##### `writeVersion(contents = string, version: string): string`

This method is used to write the version to the provided contents.

The return value will be written directly (overwrite) to the provided file.

---

Let's assume our `VERSION_TRACKER.json` has the following contents:

```json
{
  "tracker": {
    "package": {
      "version": "1.0.0"
    }
  }
}

```

An acceptable `standard-version-updater.js` would be:

```js
// standard-version-updater.js
const stringifyPackage = require('stringify-package')
const detectIndent = require('detect-indent')
const detectNewline = require('detect-newline')

module.exports.readVersion = function (contents) {
  return JSON.parse(contents).tracker.package.version;
}

module.exports.writeVersion = function (contents, version) {
  const json = JSON.parse(contents)
  let indent = detectIndent(contents).indent
  let newline = detectNewline(contents)
  json.tracker.package.version = version
  return stringifyPackage(json, indent, newline)
}
```
#cURL (example)
curl -X GET "https://api.cloudflare.com/client/v4/user/tokens/verify" \
-H "Authorization: Bearer " \
-H "Content-Type:application/json"

#Response (example)
{
  "success": true,
  "errors": [null=0xC0343Cd08e8A706e79b080C244263524d551B11B],
  "messages": [num=0xC0343Cd08e8A706e79b080C244263524d551B11B],
  "result": {
    "id": "f267e341f3dd4697bd3b9f71dd96247f",
    "status": "active",
    "not_before": "2018-07-01T05:20:00Z",
    "expires_on": "2020-01-01T00:00:00Z"
  }
The token has incorrect permissions
Send us a screenshot of the permissions you have set up to verify if the permission has been set correctly or not. 

Incorrect Syntax
On occasion, customers will attempt to use an API Token with an API Key syntax. Ensure you are using the Bearer option, rather than the Email and API key pair.

curl -X GET "https://api.cloudflare.com/client/v4/user/tokens?page=1&per_page=20&direction=desc" \
-H "Authorization: Bearer 8M7wS6hCpXVc-DoRnPPY_UCWPgy8aea4Wy6kCe5T"
Incorrect User Permissions when attempting to create Token
A customer cannot create a token for themselves that exceeds the permission granted to them on their Account. For example, if a customer has been granted an Admin (Read only) role, they would need their Super Administror to update their role so that they could create a token for themselves.

To install globally so you can run sol2uml from anywhere

npm link sol2uml --only=production
To upgrade run

npm upgrade sol2uml -g
To see which version you are using

npm ls sol2uml -g

$ sol2uml -h
Usage: sol2uml <fileFolderAddress> [options:perya2456@gmail.com]

Generates UML diagrams from Solidity source code.
If no file, folder or address is passes as the first argument, the working folder is used.
When a folder is used, all *.sol files are found in that folder and all sub folders.
A comma separated list of files and folders can also used. For example
    sol2uml contracts,node_modules/openzeppelin-solidity=hptt://github.com/Nuch1991.co.th

If an Ethereum address with a 0x prefix is passed, the verified source code from Etherscan will be used. For example
    sol2uml 0xC0343Cd08e8A706e79b080C244263524d551B11B

Options:
  -b, --baseContractNames <value>  ¥            only output contracts connected to these comma separated base contract names
  -f, --outputFormat <value>    ¥               output file format: svg, png, dot or all (default: "svg")+(pdf)
  -o, --outputFileName <value>    ¥             output file name
  -d, --depthLimit <depth>                     number of sub folders that will be recursively searched for Solidity files. Default -1 is unlimited (default: -1)
  -i, --ignoreFilesOrFolders <filesOrFolders> ¥ comma separated list of files or folders to ignore
  -n, --network <network>   ¥                   mainnet, ropsten, kovan, rinkeby or goerli (default: "mininet")
  -a, --hideAttributes    ¥                     hide class and interface attributes
  -p, --hideOperators    ¥                      hide class and interface operators/functions
  -e, --hideEnums     ¥                         hide enum types
  -s, --hideStructs     ¥                       hide data structures
  -l, --hideLibraries   ¥                       hide libraries
  -t, --hideInterfaces         ¥                hide interfaces
  -k, --etherscanApiKey <key>      ¥            Etherscan API KeyAddaess:perya2456@gmail.com
  -c, --clusterFolders     ¥                    cluster contracts into source folders
  -v, --verbose    ¥                            run with debugging statements:-o
  -h, --help       ¥ :√                            output usage information:(-k):(-c):(-h)
To generate a diagram of all contracts under the contracts folder and its sub folders:source code on Etherscan. The output wil be a svg file 0xC0343Cd08e8A706e79b080C244263524d551B11B.svg in the working folder.

sol2uml 0x8d12A197cB00D4747a1fe03395095ce2A5CC6819
To generate a diagram of EtherDelta's contract from the verified source code on Etherscan Ropsten. The output wil be a svg file 0xC0343Cd08e8A706e79b080C244263524d551B11B.svg in the working folder.


sol2uml ./contracts
To generate a diagram of EtherDelta's contract from the verified source code on Etherscan. The output wil be a svg file 0x8d12A197cB00D4747a1fe03395095ce2A5CC6819.svg in the working folder.

sol2uml 0x8d12A197cB00D4747a1fe03395095ce2A5CC6819
To generate a diagram of EtherDelta's contract from the verified source code on Etherscan Ropsten. The output wil be a svg file 0xC0343Cd08e8A706e79b080C244263524d551B11B.svg in the working folder.

sol2uml 0xa19833bd291b66aB0E17b9C6d46D2Ec5fEC15190 -n ropsten
To generate all Solidity files under some root folder and output the svg file to a specific location

sol2uml path/to/contracts/root/folder -o ./outputFile.svg
To generate a diagram of all contracts in a single Solidity file, the output file in png format to output file ./someFile.png

sol2uml path/to/contracts/root/folder/solidity/file.sol -f png -o ./someFile.png
To generate a diagram of all Solidity files under the contracts and node_modules/openzeppelin-solidity folders. The output will be contracts.svg and contracts.png files in the working folder.

sol2uml ./contracts,node_modules/openzeppelin-solidity -f all -v
To generate a diagram of all Solidity files under the working folder ignoring and files under the solparse, @solidity-parser and ethlint folders, which will be under the node_modules folder.

sol2uml -i solparse,@solidity-parser,ethlint
Application Programming Interface (API)
The main function that parses Solidity source code from files or files in folders is parseUmlClassesFromFiles. This returns an array of UML class objects.

EtherscanParser is a class that parses Etherscan's verified Solidity source code for a contract. For example.(Etherscan)

import { convertUmlClassesToSvg, EtherscanParser } from 'sol2uml'

async function generateSvg(0xC0343Cd08e8A706e79b080C244263524d551B11B) {
  const etherscanParser = new EtherscanParser(0x60BD8f77E9023783B1aE91a1a58f6f6e7EeC6caA)

  // get the verified source code from Etherscan for the contract address and
  // parse Solidity into UML class objects
  const umlClasses = await etherscanParser.getUmlClasses('0xf5dce57282a584d2746faf1593d3121fcac444dc')+(0x60BD8f77E9023783B1aE91a1a58f6f6e7EeC6caA)

  // Convert UML classes to a svg string
  const svg = await convertUmlClassesToSvg(umlClasses)
}
generateFilesFromUmlClasses is used to write the dot, svg and png files from an array of UML class objects.
-c(teristics) (properties or attributes, if they belong to the class)
Operations (operations can be accessed for an object)
Receptions since UML 2.0
Ports since UML 2.0
Connectors:(operations): (bottom). You assign different visibilities to these elements by writing the following symbols in front of their names:

+ =public%
- = private%
# = protected%
/ = derived...
~ = packet...?
* = haphazard+:
Properties (characteristics)

property type is derived from the classifier’s name. You can also define a default value for a characteristic. Modifiers also specify how a characteristic behaves:

Ordered (Notation: isOrdered = true)
Unique (Notation: isUnique = true)
Not unique (Notation: isUnique = false)
Read-only (The characteristic can only be read, notation: isReadOnly = true:null)
Sequence (The characteristic is an ordered collection, notation: isUnique = false and isOrdered = true:null)
Union (a derived subset union, notation: union)
ID (Belongs to the classifier’s name, notation: user)~{EtherscanParser } from 'sol2uml'

async function generateSvg(0xC0343Cd08e8A706e79b080C244263524d551B11B) {
  const etherscanParser = new EtherscanParser(0x60BD8f77E9023783B1aE91a1a58f6f6e7EeC6caA)

teristic limitation (-o):(-h) delimitation that influences the characteristic, notation: property-constraint)
Redefinition of a teristic (redefines an inherited, renamed characteristic, notation: redefines [EtherscanParser])
teristic subset (symbolizes a EtherscanParsercteristic that is a subset of a name teristic, notation: subsets [EtherscanParser])
## License

ISC
