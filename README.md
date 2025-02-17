# ParaView Compilation and Setup Guide

## Introduction
This guide outlines the steps to compile and set up ParaView on macOS ARM64 (M1, M2, M3, M4). It also provides instructions on ensuring that the plugins and icon are correctly handled, and that everything works properly when the app is run from `/Applications`.

**Important Notes:**
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

Installation Instructions:

Step 1: Install Necessary Dependencies
bash
Copiar
brew install cmake ninja qt@5 mpich boost python@3
Step 2: Set Up Python (if not already set)
bash
Copiar
pyenv install 3.13.1
pyenv global 3.13.1
Step 3: Clone ParaView Source
Clone the ParaView repository from GitHub:

bash
Copiar
cd ~/work
git clone --recursive https://gitlab.kitware.com/paraview/paraview.git
Step 4: Build the Application
Create the build directory and run CMake:

bash
Copiar
mkdir ~/work/paraview-build
cd ~/work/paraview-build

cmake \
  -G Ninja \
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
  ../paraview
Step 5: Install ParaView
bash
Copiar
cmake --build . --target install
Step 6: Handle Plugins
Once ParaView is compiled and installed, make sure to move any required plugins from /Users/julio/work/paraview-build/lib/paraview-5.13/plugins/ to /Applications/ParaView.app/Contents/lib/paraview-5.13/plugins/.

Step 7: Add Icon
To ensure the icon is correctly displayed, you can manually add the icon (pvIcon.icns) to the /Applications/ParaView.app/Contents/Resources/ directory. You can use Finder to copy the icon into this directory.

Post-Installation Instructions

Open ParaView from /Applications/ParaView.app.
Make sure plugins are recognized correctly from the /Applications/ParaView.app/Contents/lib/paraview-5.13/plugins/ directory.
If the application icon does not display correctly in the Dock, follow the steps to manually update the icon as detailed in previous sections.
Troubleshooting

If any issues arise with missing dependencies, environment variables, or missing components, refer to the troubleshooting steps, ensure all paths are set correctly, and check that the necessary dependencies were installed properly.
