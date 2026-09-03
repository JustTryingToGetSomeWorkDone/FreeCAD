<a href="https://freecad.org"><img src="/src/Gui/Icons/freecad.svg" height="100px" width="100px"></a>

### Your own 3D Parametric Modeler

[Website](https://www.freecad.org) •
[Documentation](https://wiki.freecad.org) •
[Forum](https://forum.freecad.org/) •
[Bug tracker](https://github.com/FreeCAD/FreeCAD/issues) •
[Git repository](https://github.com/FreeCAD/FreeCAD) •
[Blog](https://blog.freecad.org)


[![Release](https://img.shields.io/github/release/freecad/freecad.svg)](https://github.com/freecad/freecad/releases/latest) [![Crowdin](https://d322cqt584bo4o.cloudfront.net/freecad/localized.svg)](https://crowdin.com/project/freecad)

Historical Python package store prototype
-----------------------------------------

> [!IMPORTANT]
> This branch is an experimental integration test. It is not an official
> FreeCAD packaging mechanism and is not intended for production use.

This branch tests a metadata-driven Python dependency model for applications
that embed CPython. It is designed to prevent an unrelated package upgrade in
a user or system Python installation from silently changing the Python
packages loaded by FreeCAD.

The model keeps package releases side-by-side in an additive historical store:

```text
~/.python/packages/
    PySide6/
        6.8.3/
    requests/
        2.32.5/
```

FreeCAD declares its direct Python requirements in `pyproject.toml` and records
the tested, resolved dependency closure in `pylock.toml`. The experimental
[CPython historical-store branch](https://github.com/JustTryingToGetSomeWorkDone/cpython/tree/metadata-historical-store-prototype-3.11)
uses that metadata while resolving otherwise ordinary imports such as
`import requests`.

### Why this exists

FreeCAD embeds Python and loads native Qt bindings. A package found in a user
site directory can be a different version from the Qt, PySide, or Shiboken
libraries used to build FreeCAD, potentially causing import failures or native
crashes. Application-specific package directories and virtual environments can
isolate the problem, but they duplicate packages and still require environment
management.

This prototype instead asks whether project metadata and a permanent,
versioned store can provide deterministic imports without replacing historical
versions or changing unversioned Python syntax. A compatible ordinary
installation remains usable; the store is selected when the ordinary
distribution does not satisfy the project metadata.

### Changes in this branch

The executable FreeCAD source code itself is unchanged. Besides this README
explanation, the branch adds only:

- `pyproject.toml`, declaring the direct Python dependencies tested by this
  integration;
- `pylock.toml`, pinning the resolved versions, artifacts, and hashes used by
  the test.

The direct dependencies currently declared are `defusedxml`, `PySide6`,
`requests`, `scour`, and `shiboken6`. Their transitive dependencies are recorded
in the lock file.

### Running the integration test

Build FreeCAD against the modified CPython 3.11 branch and populate the store
with the versions recorded in `pylock.toml` using the companion experimental
pip implementation. The pip changes are separate and are not included in this
repository.

Because FreeCAD is an embedded host, explicitly point the interpreter at this
project's metadata when launching it:

```bash
export CPYTHON_INSTALL=/path/to/custom-cpython-install
export FREECAD_SOURCE=/path/to/FreeCAD
export FREECAD_BUILD=/path/to/FreeCAD-build

env \
    LD_LIBRARY_PATH="$CPYTHON_INSTALL/lib${LD_LIBRARY_PATH:+:$LD_LIBRARY_PATH}" \
    PYTHONHISTORICALPROJECT="$FREECAD_SOURCE" \
    "$FREECAD_BUILD/bin/FreeCAD"
```

`LD_LIBRARY_PATH` is needed when the custom `libpython` is installed in a
nonstandard library directory. `PYTHONPATH` pointing to the modified pip source
is needed only when invoking that pip checkout; it is not part of the FreeCAD
runtime configuration.

### Prototype boundaries

This integration does not replace FreeCAD's package manager, redesign Python
packaging, download missing packages at import time, or solve native ABI and
shared-library compatibility. It also does not demonstrate simultaneous use of
multiple releases of the same import name in one process. The current metadata
is an integration-test dependency set, not a claim that every optional Python
dependency used by every FreeCAD workbench has been catalogued.

<img src="/.github/images/partdesign.png" width="800"/>

Overview
--------

* **Freedom to build what you want**  FreeCAD is an open-source parametric 3D 
modeler made primarily to design real-life objects of any size. 
Parametric modeling allows you to easily modify your design by going back into 
your model history to change its parameters. 

* **Create 3D from 2D and back** FreeCAD lets you sketch geometry-constrained
 2D shapes and use them as a base to build other objects.
 It contains many components to adjust dimensions or extract design details from 
 3D models to create high quality production-ready drawings.

* **Designed for your needs** FreeCAD is designed to fit a wide range of uses
including product design, mechanical engineering and architecture,
whether you are a hobbyist, programmer, experienced CAD user, student or teacher.

* **Cross platform** FreeCAD runs on Windows, macOS and Linux operating systems.

* **Underlying technology**
    * **OpenCASCADE** A powerful geometry kernel, the most important component of FreeCAD
    * **Coin3D library** Open Inventor-compliant 3D scene representation model
    * **Python** FreeCAD offers a broad Python API
    * **Qt** Graphical user interface built with Qt


Installing
----------

Precompiled packages for stable releases are available for Windows, macOS and Linux on the
[latest releases page](https://github.com/FreeCAD/FreeCAD/releases/latest).

On most Linux distributions, FreeCAD is also directly installable from the 
software center application.

For weekly development releases visit the [releases page](https://github.com/FreeCAD/FreeCAD/releases/).

Other options are described on the [wiki Download page](https://wiki.freecad.org/Download).

Compiling
---------

See the [Developers Handbook – Getting Started](https://freecad.github.io/DevelopersHandbook/gettingstarted/)
for build instructions.


Reporting Issues
---------

To report an issue please:

- Consider posting to the [Forum](https://forum.freecad.org), [Discord](https://discord.com/invite/w2cTKGzccC) channel, or [Reddit](https://www.reddit.com/r/FreeCAD) to verify the issue; 
- Search the existing [issues](https://github.com/FreeCAD/FreeCAD/issues) for potential duplicates; 
- Use the most updated stable or [development versions](https://github.com/FreeCAD/FreeCAD/releases/) of FreeCAD; 
- Post version info from `Help > About FreeCAD > Copy to clipboard`; 
- Restart FreeCAD in safe mode `Help > Restart in safe mode` and try to reproduce the issue again. If the issue is resolved it can be fixed by deleting the FreeCAD config files.
- Start recording a macro `Macro > Macro recording...` and repeat all steps. Stop recording after the issue occurs and upload the saved macro or copy the macro code in the issue; 
- Post a Step-By-Step explanation on how to recreate the issue; 
- Upload an example file (FCStd as ZIP file) to demonstrate the problem; 

For more details see:

- [Bug Tracker](https://github.com/FreeCAD/FreeCAD/issues)
- [Reporting Issues and Requesting Features](https://github.com/FreeCAD/FreeCAD/issues/new/choose)
- [Contributing](https://github.com/FreeCAD/FreeCAD/blob/main/CONTRIBUTING.md)
- [Help Forum](https://forum.freecad.org/viewforum.php?f=3)

> [!NOTE]
The [FPA](https://fpa.freecad.org) offers developers the opportunity
to apply for a grant to work on projects of their choosing. Check
[jobs and funding](https://blog.freecad.org/jobs/) to know more.


Usage & Getting Help
--------------------

The FreeCAD wiki contains documentation on 
general FreeCAD usage, Python scripting, and development.
View these pages for more information:

- [Getting started](https://wiki.freecad.org/Getting_started)
- [Features list](https://wiki.freecad.org/Feature_list)
- [Frequent questions](https://wiki.freecad.org/FAQ/en)
- [Workbenches](https://wiki.freecad.org/Workbenches)
- [Scripting](https://wiki.freecad.org/Power_users_hub)
- [Developers Handbook](https://freecad.github.io/DevelopersHandbook/)

The [FreeCAD forum](https://forum.freecad.org) is a great place
to find help and solve specific problems when learning to use FreeCAD.

---

<p>This project receives generous infrastructure support from
  <a href="https://www.digitalocean.com/">
    <img src="https://opensource.nyc3.cdn.digitaloceanspaces.com/attribution/assets/SVG/DO_Logo_horizontal_blue.svg" width="91px">
  </a> and <a href="https://www.kipro-pcb.com/">KiCad Services Corp.</a>
</p>
