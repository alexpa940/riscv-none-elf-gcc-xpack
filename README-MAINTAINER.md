[![license](https://img.shields.io/github/license/xpack-dev-tools/riscv-none-elf-gcc-xpack)](https://github.com/xpack-dev-tools/riscv-none-elf-gcc-xpack/blob/xpack/LICENSE)
[![GitHub issues](https://img.shields.io/github/issues/xpack-dev-tools/riscv-none-elf-gcc-xpack.svg)](https://github.com/xpack-dev-tools/riscv-none-elf-gcc-xpack/issues/)
[![GitHub pulls](https://img.shields.io/github/issues-pr/xpack-dev-tools/riscv-none-elf-gcc-xpack.svg)](https://github.com/xpack-dev-tools/riscv-none-elf-gcc-xpack/pulls)

# Maintainer info

## Prerequisites

The build scripts run on GNU/Linux and macOS. The Windows binaries are
generated on Intel GNU/Linux, using [mingw-w64](https://mingw-w64.org).

For GNU/Linux the prerequisites are:

- `npm` (shipped with Node.js; installed via nvm, not the system package manager)
- `xpm` (installed via `npm`)
- `docker`
- `git` (installed via the system package manager)

For macOS the prerequisites are:

- `npm` (shipped with Node.js; installed via nvm)
- `xpm` (installed via `npm`)
- the Command Line Tools

For details on installing them, please read the
[XBB prerequisites page](https://xpack.github.io/xbb/prerequisites/).

## Get project sources

The project is hosted on GitHub:

- <https://github.com/xpack-dev-tools/riscv-none-elf-gcc-xpack.git>

To clone the stable branch (`xpack`), run the following commands in a
terminal (on Windows use the _Git Bash_ console):

```sh
rm -rf ~/Work/xpacks/riscv-none-elf-gcc-xpack.git && \
git clone https://github.com/xpack-dev-tools/riscv-none-elf-gcc-xpack.git \
  ~/Work/xpacks/riscv-none-elf-gcc-xpack.git
```

For development purposes, clone the `xpack-develop` branch:

```sh
rm -rf ~/Work/xpacks/riscv-none-elf-gcc-xpack.git && \
mkdir -p ~/Work/xpacks && \
git clone \
  --branch xpack-develop \
  https://github.com/xpack-dev-tools/riscv-none-elf-gcc-xpack.git \
  ~/Work/xpacks/riscv-none-elf-gcc-xpack.git
```

Or, if the repo was already cloned:

```sh
git -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git pull
```

## Get helper sources

The project has a dependency to a common **helper**; clone the
`xpack-develop` branch and link it to the central xPacks store:

```sh
rm -rf ~/Work/xpacks/xbb-helper-xpack.git && \
mkdir -p ~/Work/xpacks && \
git clone \
  --branch xpack-develop \
  https://github.com/xpack-dev-tools/xbb-helper-xpack.git \
  ~/Work/xpacks/xbb-helper-xpack.git && \
xpm link -C ~/Work/xpacks/xbb-helper-xpack.git
```

Or, if the repo was already cloned:

```sh
git -C ~/Work/xpacks/xbb-helper-xpack.git pull
xpm link -C ~/Work/xpacks/xbb-helper-xpack.git
```

## Release schedule

The xPack GNU RISC-V Embedded GCC release schedule will follow the
[GNU](https://gcc.gnu.org/releases.html) release schedule.

## How to make new releases

Before starting the build, perform some checks and tweaks.

### Download the build scripts repo

The build scripts are available in the `scripts` folder of the
[`xpack-dev-tools/riscv-none-elf-gcc-xpack`](https://github.com/xpack-dev-tools/riscv-none-elf-gcc-xpack)
Git repo.

To download them on a new machine, clone the `xpack-develop` branch,
as seen above.

### Check Git

- switch to the `xpack-develop` branch
- pull new changes
- if needed, merge the `xpack` branch

### Update helper & other dependencies

Check the latest versions at <https://github.com/xpack-dev-tools/> and
update the dependencies in `package.json`.

### Identify the main GCC version

Determine the GCC version (like `12.2.0`) and update the `scripts/VERSION`
file; the format is `12.2.0-3`. The fourth digit is the number of the
the xPack GNU RISC-V Embedded GCC release number of this version.

### Update versions in `README` files

- update version in `README-MAINTAINER.md`
- update version in `README.md`

### Fix possible open issues

Check GitHub issues and pull requests:

- <https://github.com/xpack-dev-tools/riscv-none-elf-gcc-xpack/issues/>

and fix them; assign them to a milestone (like `12.2.0-3`).

### Check `README.md`

Normally `README.md` should not need changes, but better check.
Information related to the new version should not be included here,
but in the version specific release page.

### Update `CHANGELOG.md`

- open the `CHANGELOG.md` file
- check if all previous fixed issues are in
- add a new entry like _* v12.2.0-3 prepared_
- commit with a message like _prepare v12.2.0-3_

### Update the version specific code

- open the `scripts/versioning.sh` file
- add a new `if` with the new version before the existing code
- update the versions, branch names and commit ids

## Build

The builds currently run on 5 dedicated machines (Intel GNU/Linux,
Arm 32 GNU/Linux, Arm 64 GNU/Linux, Intel macOS and Apple Silicon macOS).

### Development run the build scripts

Before the real build, run test builds on all platforms.

#### Visual Studio Code

All actions are defined as **xPack actions** and can be conveniently
triggered via the VS Code graphical interface, using the
[xPack extension](https://marketplace.visualstudio.com/items?itemName=ilg-vscode.xpack).

#### Temporarily disable multilib

In the `scripts/application.sh` enable the `WITHOUT_MULTILIB="y"` definition.

#### Patches

Possibly add binutils & gdb patches.

#### Intel macOS

For Intel macOS, first run the build on the development machine
(`wksi`, a recent macOS):

Update the build scripts (or clone them at the first use):

```sh
git -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git pull

xpm run deep-clean -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git
```

If the helper is also under development and needs changes,
update it too:

```sh
git -C ~/Work/xpacks/xbb-helper-xpack.git pull
```

Install project dependencies:

```sh
xpm run install -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git
```

If the writable helper is used,
link it in the place of the read-only package:

```sh
xpm link -C ~/Work/xpacks/xbb-helper-xpack.git

xpm run link-deps -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git
```

For repeated builds, clean the build folder and install de
build configuration dependencies:

```sh
xpm run deep-clean --config darwin-x64  -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git

xpm install --config darwin-x64 -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git
```

Run the native build:

```sh
xpm run build-develop --config darwin-x64 -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git
```

The build takes about 1h30 (without multilibs).

When functional, push the `xpack-develop` branch to GitHub.

Run the native build on the production machine
(`xbbmi`, an older macOS);
start a VS Code remote session, or connect with a terminal:

```sh
caffeinate ssh xbbmi
```

Repeat the same steps as before.

```sh
git -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git pull && \
xpm run install -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git && \
git -C ~/Work/xpacks/xbb-helper-xpack.git pull && \
xpm link -C ~/Work/xpacks/xbb-helper-xpack.git && \
xpm run link-deps -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git && \
xpm run deep-clean --config darwin-x64  -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git && \
xpm install --config darwin-x64 -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git
xpm run build-develop --config darwin-x64 -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git
```

About 55 minutes later, the output of the build script is a compressed
archive and its SHA signature, created in the `deploy` folder:

```console
$ ls -l ~/Work/xpacks/riscv-none-elf-gcc-xpack.git/build/darwin-x64/deploy
total 197288
-rw-r--r--  1 ilg  staff  93798413 Feb  1 12:52 xpack-riscv-none-elf-gcc-12.2.0-3-darwin-x64.tar.gz
-rw-r--r--  1 ilg  staff       118 Feb  1 12:52 xpack-riscv-none-elf-gcc-12.2.0-3-darwin-x64.tar.gz.sha
```

#### Apple Silicon macOS

Run the native build on the production machine
(`xbbma`, an older macOS);
start a VS Code remote session, or connect with a terminal:

```sh
caffeinate ssh xbbma
```

Update the build scripts (or clone them at the first use):

```sh
git -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git pull && \
xpm run install -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git && \
git -C ~/Work/xpacks/xbb-helper-xpack.git pull && \
xpm link -C ~/Work/xpacks/xbb-helper-xpack.git && \
xpm run link-deps -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git && \
xpm run deep-clean --config darwin-arm64  -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git && \
xpm install --config darwin-arm64 -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git
xpm run build-develop --config darwin-arm64 -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git
```

About 25 minutes later, the output of the build script is a compressed
archive and its SHA signature, created in the `deploy` folder:

```console
$ ls -l ~/Work/xpacks/riscv-none-elf-gcc-xpack.git/build/darwin-arm64/deploy
total 196792
-rw-r--r--  1 ilg  staff  90622384 Feb  1 12:11 xpack-riscv-none-elf-gcc-12.2.0-3-darwin-arm64.tar.gz
-rw-r--r--  1 ilg  staff       120 Feb  1 12:11 xpack-riscv-none-elf-gcc-12.2.0-3-darwin-arm64.tar.gz.sha
```

#### Intel GNU/Linux

Run the docker build on the production machine (`xbbli`);
start a VS Code remote session, or connect with a terminal:

```sh
caffeinate ssh xbbli
```

##### Build the GNU/Linux binaries

Update the build scripts (or clone them at the first use):

```sh
git -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git pull && \
xpm run install -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git && \
git -C ~/Work/xpacks/xbb-helper-xpack.git pull && \
xpm link -C ~/Work/xpacks/xbb-helper-xpack.git && \
xpm run link-deps -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git && \
xpm run deep-clean --config linux-x64 -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git && \
xpm run docker-prepare --config linux-x64 -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git && \
xpm run docker-link-deps --config linux-x64 -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git
xpm run docker-build-develop --config linux-x64 -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git
```

About 30 minutes later, the output of the build script is a compressed
archive and its SHA signature, created in the `deploy` folder:

```console
$ ls -l ~/Work/xpacks/riscv-none-elf-gcc-xpack.git/build/linux-x64/deploy
total 102308
-rw-r--r-- 1 ilg ilg 104756842 Feb  1 10:19 xpack-riscv-none-elf-gcc-12.2.0-3-linux-x64.tar.gz
-rw-r--r-- 1 ilg ilg       117 Feb  1 10:19 xpack-riscv-none-elf-gcc-12.2.0-3-linux-x64.tar.gz.sha
```

##### Build the Windows binaries

Clean the build folder and prepare the docker container:

```sh
git -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git pull && \
xpm run install -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git && \
git -C ~/Work/xpacks/xbb-helper-xpack.git pull && \
xpm link -C ~/Work/xpacks/xbb-helper-xpack.git && \
xpm run link-deps -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git && \
xpm run deep-clean --config win32-x64 -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git && \
xpm run docker-prepare --config win32-x64 -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git && \
xpm run docker-link-deps --config win32-x64 -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git
xpm run docker-build-develop --config win32-x64 -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git
```

About 30 minutes later, the output of the build script is a compressed
archive and its SHA signature, created in the `deploy` folder:

```console
$ ls -l ~/Work/xpacks/riscv-none-elf-gcc-xpack.git/build/win32-x64/deploy
total 109320
-rw-r--r-- 1 ilg ilg 111937715 Feb  1 10:48 xpack-riscv-none-elf-gcc-12.2.0-3-win32-x64.zip
-rw-r--r-- 1 ilg ilg       114 Feb  1 10:48 xpack-riscv-none-elf-gcc-12.2.0-3-win32-x64.zip.sha
```

#### Arm GNU/Linux 64-bit

Run the docker build on the production machine (`xbbla64`);
start a VS Code remote session, or connect with a terminal:

```sh
caffeinate ssh xbbla64
```

Update the build scripts (or clone them at the first use):

```sh
git -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git pull && \
xpm run install -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git && \
git -C ~/Work/xpacks/xbb-helper-xpack.git pull && \
xpm link -C ~/Work/xpacks/xbb-helper-xpack.git && \
xpm run link-deps -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git && \
xpm run deep-clean --config linux-arm64 -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git && \
xpm run docker-prepare --config linux-arm64 -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git && \
xpm run docker-link-deps --config linux-arm64 -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git
xpm run docker-build-develop --config linux-arm64 -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git
```

About 3h30 later, the output of the build script is a compressed
archive and its SHA signature, created in the `deploy` folder:

```console
$ ls -l ~/Work/xpacks/riscv-none-elf-gcc-xpack.git/build/linux-arm64/deploy
total 98020
-rw-r--r-- 1 ilg ilg 100361089 Feb  1 13:16 xpack-riscv-none-elf-gcc-12.2.0-3-linux-arm64.tar.gz
-rw-r--r-- 1 ilg ilg       119 Feb  1 13:16 xpack-riscv-none-elf-gcc-12.2.0-3-linux-arm64.tar.gz.sha
```

#### Arm GNU/Linux 32-bit

Run the docker build on the production machine (`xbbla32`);
start a VS Code remote session, or connect with a terminal:

```sh
caffeinate ssh xbbla32
```

Update the build scripts (or clone them at the first use):

```sh
git -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git pull && \
xpm run install -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git && \
xpm run link-deps -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git && \

xpm run deep-clean --config linux-arm -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git && \
xpm run docker-prepare --config linux-arm -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git && \
xpm run docker-link-deps --config linux-arm -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git
xpm run docker-build-develop --config linux-arm -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git
```

About 3h15 later, the output of the build script is a compressed
archive and its SHA signature, created in the `deploy` folder:

```console
$ ls -l ~/Work/xpacks/riscv-none-elf-gcc-xpack.git/build/linux-arm/deploy
total 92456
-rw-r--r-- 1 ilg ilg 94666972 Feb  1 13:04 xpack-riscv-none-elf-gcc-12.2.0-3-linux-arm.tar.gz
-rw-r--r-- 1 ilg ilg      117 Feb  1 13:04 xpack-riscv-none-elf-gcc-12.2.0-3-linux-arm.tar.gz.sha
```

### Files cache

The XBB build scripts use a local cache such that files are downloaded only
during the first run, later runs being able to use the cached files.

However, occasionally some servers may not be available, and the builds
may fail.

The workaround is to manually download the files from an alternate
location (like
<https://github.com/xpack-dev-tools/files-cache/tree/master/libs>),
place them in the XBB cache (`Work/cache`) and restart the build.

## Run the CI build

The automation is provided by GitHub Actions and three self-hosted runners.

### Temporarily disable multi-lib

It is recommended to do **a first run without the multi-libs**
(see the `application.sh` file), test it,
and, when ready, rerun the full build.

### Generate the GitHub workflows

Run the `generate-workflows` to re-generate the
GitHub workflow files; commit and push if necessary.

### Start the self-hosted runners

- on the development machine (`wksi`) open ssh sessions to the build
machines (`xbbma`, `xbbli`, `xbbla64` and `xbbla32`):

```sh
caffeinate ssh xbbma
caffeinate ssh xbbli
caffeinate ssh xbbla64
caffeinate ssh xbbla32
```

Start the runners on all machines:

```sh
screen -S ga

~/actions-runners/xpack-dev-tools/run.sh &

# Ctrl-a Ctrl-d
```

For `xbbli` & `xbbla64` start two runners:

```sh
~/actions-runners/xpack-dev-tools/1/run.sh &
~/actions-runners/xpack-dev-tools/2/run.sh &
```

### Push the build scripts

- push the `xpack-develop` branch to GitHub
- possibly push the helper project too

From here it'll be cloned on the production machines.

### Check for disk space

Check if the build machines have enough free space and eventually
do some cleanups.

### Manually trigger the build GitHub Actions

To trigger the GitHub Actions builds, use the xPack actions:

- `trigger-workflow-build-xbbli`
- `trigger-workflow-build-xbbla64`
- `trigger-workflow-build-xbbla32`
- `trigger-workflow-build-xbbmi`
- `trigger-workflow-build-xbbma`

This is equivalent to:

```sh
bash ~/Work/xpacks/riscv-none-elf-gcc-xpack.git/xpacks/xpack-dev-tools-xbb-helper/github-actions/trigger-workflow-build.sh --machine xbbli
bash ~/Work/xpacks/riscv-none-elf-gcc-xpack.git/xpacks/xpack-dev-tools-xbb-helper/github-actions/trigger-workflow-build.sh --machine xbbla64
bash ~/Work/xpacks/riscv-none-elf-gcc-xpack.git/xpacks/xpack-dev-tools-xbb-helper/github-actions/trigger-workflow-build.sh --machine xbbla32
bash ~/Work/xpacks/riscv-none-elf-gcc-xpack.git/xpacks/xpack-dev-tools-xbb-helper/github-actions/trigger-workflow-build.sh --machine xbbmi
bash ~/Work/xpacks/riscv-none-elf-gcc-xpack.git/xpacks/xpack-dev-tools-xbb-helper/github-actions/trigger-workflow-build.sh --machine xbbma
```

These scripts require the `GITHUB_API_DISPATCH_TOKEN` variable to be present
in the environment, and the organization `PUBLISH_TOKEN` to be visible in the
Settings → Action →
[Secrets](https://github.com/xpack-dev-tools/riscv-none-elf-gcc-xpack/settings/secrets/actions)
page.

These commands use the `xpack-develop` branch of this repo.

### Re-enable multi-lib

- comment out `XBB_APPLICATION_WITHOUT_MULTILIB` in `application.sh`
- commit and push the repo

#### Test multilib builds

Multilib builds take too long to run tests on all platforms,
run them only on the fast machines, like `xbbma` and `xbbli`.

### Manually trigger the multilib build GitHub Actions

To trigger the GitHub Actions builds, use the same xPack actions as before.

## Durations & results

The full builds take about 14 hours (3h30 without multi-libs):

- `xbbmi`: 6h20 (1h00)
- `xbbma`: 2h22 (0h23)
- `xbbli`: 2h51 (2h49 Linux, 2h51 Windows; without multi-libs: 0h37 Linux, 0h33 Windows)
- `xbbla64`: 13h33 (3h30)
- `xbbla32`: 14h00 (3h15)

The workflows results and logs are available from the
[Actions](https://github.com/xpack-dev-tools/riscv-none-elf-gcc-xpack/actions/) page.

The resulting binaries are available for testing from
[pre-releases/test](https://github.com/xpack-dev-tools/pre-releases/releases/tag/test/).

## Testing

### CI tests

The automation is provided by GitHub Actions.

On the macOS machine (`xbbmi`) open a ssh sessions to the Arm/Linux
test machine `xbbla`:

```sh
caffeinate ssh xbbla
```

Start both runners (to allow the 32/64-bit tests to run in parallel):

```sh
~/actions-runners/xpack-dev-tools/1/run.sh &
~/actions-runners/xpack-dev-tools/2/run.sh &
```

To trigger the GitHub Actions tests, use the xPack actions:

- `trigger-workflow-test-prime`
- `trigger-workflow-test-docker-linux-intel`
- `trigger-workflow-test-docker-linux-arm`

These are equivalent to:

```sh
bash ~/Work/xpacks/riscv-none-elf-gcc-xpack.git/xpacks/xpack-dev-tools-xbb-helper/github-actions/trigger-workflow-test-prime.sh
bash ~/Work/xpacks/riscv-none-elf-gcc-xpack.git/xpacks/xpack-dev-tools-xbb-helper/github-actions/trigger-workflow-test-docker-linux-intel.sh
bash ~/Work/xpacks/riscv-none-elf-gcc-xpack.git/xpacks/xpack-dev-tools-xbb-helper/github-actions/trigger-workflow-test-docker-linux-arm.sh
```

These scripts require the `GITHUB_API_DISPATCH_TOKEN` variable to be present
in the environment.

These actions use the `xpack-develop` branch of this repo and the
[pre-releases/test](https://github.com/xpack-dev-tools/pre-releases/releases/tag/test/)
binaries.

The tests results are available from the
[Actions](https://github.com/xpack-dev-tools/riscv-none-elf-gcc-xpack/actions/) page.

Since GitHub Actions provides a single version of macOS, the
multi-version macOS tests run on Travis.

To trigger the Travis test, use the xPack action:

- `trigger-travis-macos`

This is equivalent to:

```sh
bash ~/Work/xpacks/riscv-none-elf-gcc-xpack.git/xpacks/xpack-dev-tools-xbb-helper/github-actions/trigger-travis-macos.sh
```

This script requires the `TRAVIS_COM_TOKEN` variable to be present
in the environment.

The test results are available from
[Travis CI](https://app.travis-ci.com/github/xpack-dev-tools/riscv-none-elf-gcc-xpack/builds/).

### Manual tests

To download the pre-released archive for the specific platform
and run the tests, use:

```sh
git -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git pull
xpm run install -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git
xpm run test-pre-release -C ~/Work/xpacks/riscv-none-elf-gcc-xpack.git
```

For even more tests, on each platform (MacOS, GNU/Linux, Windows),
download the archive from
[pre-releases/test](https://github.com/xpack-dev-tools/pre-releases/releases/tag/test/)
and check the binaries.

On macOS, remove the `com.apple.quarantine` flag:

```sh
xattr -dr com.apple.quarantine ${HOME}/Downloads/xpack-*
```

- rename the version
  folder, by replacing a dash with a space; this will test paths with spaces;
  on Windows the current paths always use spaces, so renaming is not needed
- clone this repo locally; on Windows use the Git console

```sh
rm -rf ~/Work/xpacks/riscv-none-elf-gcc-xpack.git && \
mkdir -p ~/Work/xpacks && \
git clone \
  --branch xpack-develop \
  https://github.com/xpack-dev-tools/riscv-none-elf-gcc-xpack.git \
  ~/Work/xpacks/riscv-none-elf-gcc-xpack.git
```

- in a separate workspace, Import → General → Existing Projects into Workspace
  the Eclipse projects available in the
  `tests/eclipse` folder of the build repo; more details in the
  [README.md](https://github.com/xpack-dev-tools/riscv-none-elf-gcc-xpack/blob/xpack/tests/eclipse/README.md)
- define the **Eclipse** → **Preferences...** → **MCU** →
  **Workspace RISC-V Toolchain path** to use the `Downloads`
  temporary location
- to test the compiler: for all projects
  - remove all build folders, or **Clean all**
  - build all configs, with the hammer, in `riscv-h1b-fs`
  - build all configs, with the hammer, in `riscv-h1b-fs-lib`; this should
    also run the builds in `riscv-static-lib`
- to test the debugger: for all OpenOCD debug configurations
  - start the OpenOCD debug session,
  - single step a few lines (Step Over)
  - start continuous run (Resume)
  - halt (Suspend)
  - start (Resume)
  - stop (Terminate)
  - (don't miss the LTO cases, since in the past they had problems)
- to test the Python debugger, start it with `--version`

## Create a new GitHub pre-release draft

- in `CHANGELOG.md`, add the release date and a message like _* v12.2.0-3 released_
- commit with _CHANGELOG update_
- check and possibly update the `templates/body-github-release-liquid.md`
- push the `xpack-develop` branch
- run the xPack action `trigger-workflow-publish-release`

The workflows results and logs are available from the
[Actions](https://github.com/xpack-dev-tools/riscv-none-elf-gcc-xpack/actions/) page.

The result is a
[draft pre-release](https://github.com/xpack-dev-tools/riscv-none-elf-gcc-xpack/releases/)
tagged like **v12.2.0-3** (mind the dash in the middle!) and
named like **xPack GNU RISC-V Embedded GCC v12.2.0-3** (mind the dash),
with all binaries attached.

- edit the draft and attach it to the `xpack-develop` branch (important!)
- save the draft (do **not** publish yet!)

## Prepare a new blog post

- check and possibly update the `templates/body-jekyll-release-*-liquid.md`
- run the xPack action `generate-jekyll-post`; this will leave a file
on the Desktop.

In the `xpack/web-jekyll` GitHub repo:

- select the `develop` branch
- copy the new file to `_posts/releases/riscv-none-elf-gcc`

If any, refer to closed
[issues](https://github.com/xpack-dev-tools/riscv-none-elf-gcc-xpack/issues/).

## Update the preview Web

- commit the `develop` branch of `xpack/web-jekyll` GitHub repo;
  use a message like _xPack GNU RISC-V Embedded GCC v12.2.0-3 released_
- push to GitHub
- wait for the GitHub Pages build to complete
- the preview web is <https://xpack.github.io/web-preview/news/>

## Create the pre-release

- go to the GitHub [Releases](https://github.com/xpack-dev-tools/riscv-none-elf-gcc-xpack/releases/) page
- perform the final edits and check if everything is fine
- temporarily fill in the _Continue Reading »_ with the URL of the
  web-preview release
- **keep the pre-release button enabled**
- do not enable Discussions yet
- publish the release

Note: at this moment the system should send a notification to all clients
watching this project.

## Update the READMEs listings and examples

- check and possibly update the `ls -l` output
- check and possibly update the output of the `--version` runs
- check and possibly update the output of `tree -L 2`
- commit changes

## Check the list of links

- open the `package.json` file
- check if the links in the `bin` property cover the actual binaries
- if necessary, also check on Windows

## Update package.json binaries

- select the `xpack-develop` branch
- run the xPack action `update-package-binaries`
- open the `package.json` file
- check the `baseUrl:` it should match the file URLs (including the tag/version);
  no terminating `/` is required
- from the release, check the SHA & file names
- compare the SHA sums with those shown by `cat *.sha`
- check the executable names
- commit all changes, use a message like
  _package.json: update urls for 12.2.0-3 release_ (without _v_)

## Publish on the npmjs.com server

- select the `xpack-develop` branch
- check the latest commits `npm run git-log`
- update `CHANGELOG.md`, add a line like _* v12.2.0-3.1 published on npmjs.com_
- commit with a message like _CHANGELOG: publish npm v12.2.0-3.1_
- `npm pack` and check the content of the archive, which should list
  only the `package.json`, the `README.md`, `LICENSE` and `CHANGELOG.md`;
  possibly adjust `.npmignore`
- `npm version 12.2.0-3.1`; the first 4 numbers are the same as the
  GitHub release; the fifth number is the npm specific version
- the commits and the tag should have been pushed by the `postversion` script;
  if not, push them with `git push origin --tags`
- `npm publish --tag next` (use `npm publish --access public`
  when publishing for the first time; add the `next` tag)

After a few moments the version will be visible at:

- <https://www.npmjs.com/package/@xpack-dev-tools/riscv-none-elf-gcc?activeTab=versions>

## Test if the binaries can be installed with xpm

Run the xPack action `trigger-workflow-test-xpm`, this
will install the package via `xpm install` on all supported platforms.

The tests results are available from the
[Actions](https://github.com/xpack-dev-tools/riscv-none-elf-gcc-xpack/actions/) page.

## Update the repo

- merge `xpack-develop` into `xpack`
- push to GitHub

## Tag the npm package as `latest`

When the release is considered stable, promote it as `latest`:

- `npm dist-tag ls @xpack-dev-tools/riscv-none-elf-gcc`
- `npm dist-tag add @xpack-dev-tools/riscv-none-elf-gcc@12.2.0-3.1 latest`
- `npm dist-tag ls @xpack-dev-tools/riscv-none-elf-gcc`

In case the previous version is not functional and needs to be unpublished:

- `npm unpublish @xpack-dev-tools/riscv-none-elf-gcc@12.2.0-3.1`

## Update the Web

- in the `master` branch, merge the `develop` branch
- wait for the GitHub Pages build to complete
- the result is in <https://xpack.github.io/news/>
- remember the post URL, since it must be updated in the release page

## Create the final GitHub release

- go to the GitHub [Releases](https://github.com/xpack-dev-tools/riscv-none-elf-gcc-xpack/releases/) page
- check the download counter, it should match the number of tests
- add a link to the Web page `[Continue reading »]()`; use an same blog URL
- remove the _tests only_ notice
- **disable** the **pre-release** button
- click the **Update Release** button

## Share on Twitter

- in a separate browser windows, open [TweetDeck](https://tweetdeck.twitter.com/)
- using the `@xpack_project` account
- paste the release name like **xPack GNU RISC-V Embedded GCC v12.2.0-3 released**
- paste the link to the Web page
  [release](https://xpack.github.io/riscv-none-elf-gcc/releases/)
- click the **Tweet** button

## Remove the pre-release binaries

- go to <https://github.com/xpack-dev-tools/pre-releases/releases/tag/test/>
- remove the test binaries

## Clean the work area

Run the xPack action `trigger-workflow-deep-clean`, this
will remove the build folders on all supported platforms.

The results are available from the
[Actions](https://github.com/xpack-dev-tools/riscv-none-elf-gcc-xpack/actions/) page.

## Announce to RISC-V community

Add a new topic in the **Announcements** category of the
[RISC-V forums]<https://groups.google.com/a/groups.riscv.org/g/sw-dev>).

```console
Subject: xPack GNU RISC-V Embedded GCC v12.2.0-3 released

Version 12.2.0-3 is a new release of the xPack GNU RISC-V Embedded GCC; it follows the GNU GCC release.

https://xpack.github.io/blog/2022/05/14/riscv-none-elf-gcc-v11-3-0-1-released/
```
