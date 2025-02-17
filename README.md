```markdown
# ParaView Compilation and Setup Guide

## Introduction
This guide outlines the steps to compile and set up ParaView on macOS ARM64 (M1, M2, M3, M4). It also provides instructions on ensuring that the plugins and icon are correctly handled, and that everything works properly when the app is run from `/Applications`.

**Important Notes**:
- `/julio/` refers to your username, and you should replace it with your actual username where applicable.
- This guide assumes you're compiling and testing ParaView from a non-system directory, using `~/work/paraview` and `~/work/paraview-build`.

## Prerequisites
Before starting the compilation, ensure the following are installed:

- [Homebrew](https://brew.sh/)
- [CMake](https://cmake.org/install/)
- [Qt5](https://www.qt.io/)
- [pyenv](https://github.com/pyenv/pyenv)

### Verifying Dependencies
Run the following command to check if the necessary dependencies are already installed:

```bash
brew list | grep -E 'cmake|qt@5|mpich|boost|python@3'
```

If they are not installed, use the following commands to install them:

```bash
brew install cmake qt@5 mpich boost python@3
```

### Verify Installed Versions

Ensure the versions are correct:

```bash
cmake --version
mpicc --version
```

### Python Setup
Make sure Python 3.13.1 is installed using `pyenv`:

```bash
pyenv versions
pyenv global 3.13.1
```

## Compilation Steps

### 1. Clone ParaView Repository

Clone the ParaView repository to a new folder (e.g., `~/work/paraview`):

```bash
git clone --recursive https://gitlab.kitware.com/paraview/paraview.git ~/work/paraview
```

### 2. Create a Build Directory

Create a separate directory for the build to avoid any conflicts:

```bash
mkdir ~/work/paraview-build
cd ~/work/paraview-build
```

### 3. Configure ParaView with CMake

Run the following CMake command to configure the build, replacing `/julio/` with your actual username:

```bash
cmake \
  -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_INSTALL_PREFIX=/Applications/ParaView.app/Contents \
  -DQt5_DIR=/opt/homebrew/opt/qt@5/lib/cmake/Qt5 \
  -DPARAVIEW_USE_MPI=ON \
  -DMPI_CXX_COMPILER=$(which mpicxx) \
  -DMPI_C_COMPILER=$(which mpicc) \
  -DPARAVIEW_ENABLE_PYTHON=ON \
  -DPARAVIEW_BUILD_QT_GUI=ON \
  -DBOOST_ROOT=/opt/homebrew/opt/boost \
  -DPARAVIEW_PYTHON_EXECUTABLE=/Users/julio/.pyenv/versions/3.13.1/bin/python3 \
  -DPYTHON_INCLUDE_DIR=/Users/julio/.pyenv/versions/3.13.1/include/python3.13 \
  -DPYTHON_LIBRARY=/Users/julio/.pyenv/versions/3.13.1/lib/libpython3.13.dylib \
  -DCMAKE_PREFIX_PATH=/Applications/ParaView.app/Contents/Frameworks \
  -DCMAKE_LIBRARY_PATH=/Applications/ParaView.app/Contents/Frameworks/lib \
  -DPARAVIEW_PLUGIN_PATH=/Applications/ParaView.app/Contents/MacOS/plugins \
  ~/work/paraview
```

### 4. Compile ParaView

Once CMake has finished configuring, compile ParaView using `cmake`:

```bash
cmake --build . --target install
```

### 5. Install ParaView

After the compilation completes successfully, install ParaView:

```bash
sudo cmake --install .
```

### 6. Verifying Installation

To verify the installation, check the following:

1. Run the application from the terminal:

    ```bash
    open /Applications/ParaView.app
    ```

2. Verify that all required plugins are correctly placed inside the application bundle:

    ```bash
    ls /Applications/ParaView.app/Contents/MacOS/plugins
    ```

    **Note:** The plugins will initially appear in `/Users/julio/work/paraview-build/lib/paraview-5.13/plugins/ArrowGlyph/ArrowGlyph.so`, but they should be correctly placed inside the application in `/Applications/ParaView.app/Contents/lib/paraview-5.13/plugins/`.

### 7. Verifying Python Integration

To ensure ParaView is integrated with Python, open Python and verify that the ParaView module is available:

```bash
python3
>>> from paraview.simple import *
>>> print(paraview.__version__)
```

### 8. Icon Handling

To ensure the correct icon appears, follow these steps:

1. Place the icon (`pvIcon.icns`) inside the `Resources` folder of ParaView's `.app`:

    ```bash
    cp /path/to/your/icon/pvIcon.icns ~/work/paraview-build/bin/paraview.app/Contents/Resources/
    ```

2. Use Finder to right-click on the application, go to "Get Info", click the small icon at the top, and paste your custom icon.

## Conclusion

You should now have ParaView properly installed and running from `/Applications/ParaView.app`. Ensure that all the plugins are correctly installed and the icon is set. If you run into any further issues, check the system logs or the application's error messages for more information.

Happy computing!
```
