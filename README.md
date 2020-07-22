# jit.realsense
RealSense external for Max/MSP.

## Current Instructions to Build in MacOS

### Using Xcode to build/debug jit.realsense project:

- Install Xcode via AppStore
- Install Homebrew package manager.
- Install the following packages via Homebrew:

```
brew install libusb pkg-config
brew install homebrew/core/glfw3
brew install cmake
```

- Download/Clone `jit.realsense` project:
```
git clone https://github.com/blueyeti/jit.realsense.git
```
- or
```
git clone -b XcodeBuilds https://github.com/yongyiw/jit.realsense.git
```

- Initialize required git submodules (namely `librealsense` and `maxsdk`)
```
cd jit.realsense
git submodule update --init --recursive
```
- Copy the CMake folder from librealsense into jit.realsense
```
cp -a librealsense/CMake CMake
```

- Setup for build in `jit.realsense` folder

```
mkdir build && cd build
sudo xcode-select --reset
cmake .. -DBUILD_EXAMPLES=true -DBUILD_WITH_OPENMP=false -DHWM_OVER_XU=false -G Xcode
open jit.realsense.xcodeproj
```

- Add System Header Path to Build Settings of `jit.realsense` target.

```
/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/System/Library/Frameworks/CoreServices.framework/Versions/A/Frameworks/CarbonCore.framework/Versions/A/Headers
```

- Click the build button or press `Command + B`

 (You might need to comment out the line that throw sqlite3 fastmath tag error.)

After Successfully built the project - please check target file jit.realsense/build/Debug/jit.realsense is prensented (File size @21MB)

- Prepare the .mxo package for Max 8 to load

In `jit.realsense/build` folder
```
mkdir -p Debug/jit.realsense.mxo/Contents/MacOS/
cd Debug
cp ../../Info.plist ../../PkgInfo jit.realsense.mxo/Contents/
cp ../../patch/example.maxpat .
cp jit.realsense jit.realsense.mxo/Contents/MacOS/ && open example.maxpat
```
If you are running on MacOS Catalina, you should have some permission errors.
Copy the jit.realsense.mxo from /build/debug into your /Applications/Max.app/Contents/Resources/C74/externals/jitter folder and run 
```
xattr -d com.apple.quarantine /Applications/Max.app/Contents/Resources/C74/externals/jitter/jit.realsense.mxo
```
You should be able to use the jit.realsense.mxo from a new patch.
To use the example patch, just copy the everything inside the example.maxpat in a new patch.


Whenever you make changes to jit.realsense.cpp or other related header or cpp files, rebuild only the jit.realsense project (about few seconds). Then quit Max 8 and run
```
cp jit.realsense jit.realsense.mxo/Contents/MacOS/ && open example.maxpat
```

The changes should be applied in the externals.

### Older librealsense2 and Max SDK

- Branch [`rs_working_older`](https://github.com/smokhov/jit.realsense/tree/rs_working_older)
- Will need to download separately and unzip as `max-sdk` directory
under `jit.realsense/` as the submodule pointer in the original repo was broken:
   - https://github.com/jcelerier/max-sdk/tree/7c42a22a38a5edf3d69c2c1c1b77f6fd5462d174

### Latest librealsense2 and Max SDK

- Branch [`rs2020`](https://github.com/smokhov/jit.realsense/tree/rs2020)
- newest Max SDK and librealsense2 for (2)
  - branched off `rs_working_older` but with submodules pointing to the latest commits in `master` of both librealsense2 and Max SDK.

### Generic steps after pulling one of the above branches

- Clone and pull those branches in (for convenience can use
separate clones for (1) or (2), if using GitHub Desktop or similar,
make sure submodules are pulled, if not do in Terminal: `git submodule update --init --recursive`
- In Terminal in `jit.realsense/`
  ```#!bash
  mkdir build
  cd build
  cmake .. -DCMAKE_BUILD_TYPE=Debug
  cmake --build .
    (wait for awhile while librealsense2)
  mkdir -p Debug/jit.realsense.mxo/Contents/MacOS/
  cp jit.realsense Debug/jit.realsense.mxo/Contents/MacOS/
  cp ../Info.plist ../PkgInfo Debug/jit.realsense.mxo/Contents/
  cp ../patch/example.maxpat Debug/
  cd Debug
  open example.maxpat
  ```
  - The older branch may need the `librealsense2_Poller_bsd.patch` applied to `Poller_bsd.cpp` in librealsense2 to compile; newer shouldn't need it.
  -  (to restart from scratch, remove that build directory and redo the steps)
