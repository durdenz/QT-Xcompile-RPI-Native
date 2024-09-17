# Instructions for Configuring Native Cross-Compile of QT Apps for RPI ARM64

# Host Machine
1. Clone Ubuntu 200GB Host for Xcompile Host
    - Named: QT-XCompile-RPI-Native-Host
2. Create Directories on Host Machine:
    - [x] `cd ~`
    - [x] `mkdir rpi-sysroot rpi-sysroot/usr rpi-sysroot/opt`
    - [x] `mkdir qt-host qt-raspi qt-hostbuild qtpi-build`

# RPI4
1. Install Dependencies
    - Update System to latest Updates
    - [x] `$ sudo apt update`
    - [x] `$ sudo apt full-upgrade`
    - [x] `$ sudo reboot`
    - Install Package Dependencies
    - [x] `$ sudo apt-get install libboost-all-dev libudev-dev libinput-dev libts-dev libmtdev-dev libjpeg-dev libfontconfig1-dev libssl-dev libdbus-1-dev libglib2.0-dev libxkbcommon-dev libegl1-mesa-dev libgbm-dev libgles2-mesa-dev mesa-common-dev libasound2-dev libpulse-dev gstreamer1.0-omx libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev  gstreamer1.0-alsa libvpx-dev libsrtp2-dev libsnappy-dev libnss3-dev "^libxcb.*" flex bison libxslt-dev ruby gperf libbz2-dev libcups2-dev libatkmm-1.6-dev libxi6 libxcomposite1 libfreetype6-dev libicu-dev libsqlite3-dev libxslt1-dev`
    - [x] `$ sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libx11-dev freetds-dev libpq-dev libiodbc2-dev firebird-dev libgst-dev libxext-dev libxcb1 libxcb1-dev libx11-xcb1 libx11-xcb-dev libxcb-keysyms1 libxcb-keysyms1-dev libxcb-image0 libxcb-image0-dev libxcb-shm0 libxcb-shm0-dev libxcb-icccm4 libxcb-icccm4-dev libxcb-sync1 libxcb-sync-dev libxcb-render-util0 libxcb-render-util0-dev libxcb-xfixes0-dev libxrender-dev libxcb-shape0-dev libxcb-randr0-dev libxcb-glx0-dev libxi-dev libdrm-dev libxcb-xinerama0 libxcb-xinerama0-dev libatspi2.0-dev libxcursor-dev libxcomposite-dev libxdamage-dev libxss-dev libxtst-dev libpci-dev libcap-dev libxrandr-dev libdirectfb-dev libaudio-dev libxkbcommon-x11-dev`
    - Create directory for QT installation targeting RPI
    - [x] `$ sudo mkdir /usr/local/qt6`

# Host Machine - Setting up Host Machine
1. Update Host Systen
    - [x] `sudo apt update`
    - [x] `sudo apt upgrade`
2. Install QT Dependencies
    - [x] `sudo apt-get install make cmake build-essential libclang-dev clang ninja-build gcc git bison python3 gperf pkg-config libfontconfig1-dev libfreetype6-dev libx11-dev libx11-xcb-dev libxext-dev libxfixes-dev libxi-dev libxrender-dev libxcb1-dev libxcb-glx0-dev libxcb-keysyms1-dev libxcb-image0-dev libxcb-shm0-dev libxcb-icccm4-dev libxcb-sync-dev libxcb-xfixes0-dev libxcb-shape0-dev libxcb-randr0-dev libxcb-render-util0-dev libxcb-util-dev libxcb-xinerama0-dev libxcb-xkb-dev libxkbcommon-dev libxkbcommon-x11-dev libatspi2.0-dev libgl1-mesa-dev libglu1-mesa-dev freeglut3-dev`
3. Install Cross Compiler
    - [x] `sudo apt install gcc-aarch64-linux-gnu g++-aarch64-linux-gnu`

# Set Up SSH Connection
1. Configure SSH between Host and RPI
    - Create SSH Keys on Host
        - [x] `ssh-keygen`
        - SSH into RPI from Host to force trusted host entry 
        - [x] `ssh gadiv@192.168.1.11`
        - [x] `exit`
        - Copy public key from host to RPI to enable SSH without password
        - [x] `ssh-copy-id gadiv@192.168.1.11`
        - [x] `ssh gadiv@192.168.1.11`
        - [x] `exit`
# Build SYSROOT for RPI from Host
1. On Host: Install rsync for transfer
    -[x] `sudo apt install rsync`
2. On Host: Transfer SYSROOT Directories from RPI to Host
    - [x] `cd $HOME`
    - [x] `rsync -avzS --rsync-path="rsync" --delete gadiv@192.168.1.11:/lib/* rpi-sysroot/lib`
    - [x] `mkdir $HOME/rpi-sysroot/usr`
    - [x] `rsync -avzS --rsync-path="rsync" --delete gadiv@192.168.1.11:/usr/include/* rpi-sysroot/usr/include`
    - [x] `rsync -avzS --rsync-path="rsync" --delete gadiv@192.168.1.11:/usr/lib/* rpi-sysroot/usr/lib`
    - [x] `mkdir $HOME/rpi-sysroot/opt`
    - [ ] `rsync -avzS --rsync-path="rsync" --delete gadiv@192.168.1.11:/opt/vc rpi-sysroot/opt/vc` ** No/opt/vc/ folder on RPI
# Fix SymLinks on Host
1. Install symlinks tool
    - [x] `sudo apt install symlinks`
2. Use symlinks tool to fix broken symlinks
    - [x] `cd ~`
    - [x] `symlinks -rc rpi-sysroot`
# Building QT6
1. Build QT6 on Host machine
    - [x] `cd $HOME`
    - [x] `git clone "https://codereview.qt-project.org/qt/qt5"`
    - [x] `cd qt5/`
    - [x] `git checkout 6.4.0`
    - [x] `perl init-repository -f`
    - [x] `cd ..`
    - [x] `mkdir $HOME/qt-hostbuild`
    - [x] `cd $HOME/qt-hostbuild/`
    - [x] `cmake ../qt5/ -GNinja -DCMAKE_BUILD_TYPE=Release -DQT_BUILD_EXAMPLES=OFF -DQT_BUILD_TESTS=OFF -DCMAKE_INSTALL_PREFIX=$HOME/qt-host`
    - [x] `cmake --build . --parallel 8`
    - [x] `cmake --install .`
2. Build QT6 for RPI on Host
    1. Create a toolchain file
    - [x] `cd ~`
    - [x] `code toolchain.cmake`
    2. Place the following into the toolchain.cmake file:
```cmake 
cmake_minimum_required(VERSION 3.18)
include_guard(GLOBAL)

set(CMAKE_SYSTEM_NAME Linux)
set(CMAKE_SYSTEM_PROCESSOR arm)

set(TARGET_SYSROOT /path/to/your/sysroot)
set(CMAKE_SYSROOT ${TARGET_SYSROOT})

set(ENV{PKG_CONFIG_PATH} $PKG_CONFIG_PATH:/usr/lib/aarch64-linux-gnu/pkgconfig)
set(ENV{PKG_CONFIG_LIBDIR} /usr/lib/pkgconfig:/usr/share/pkgconfig/:${TARGET_SYSROOT}/usr/lib/aarch64-linux-gnu/pkgconfig:${TARGET_SYSROOT}/usr/lib/pkgconfig)
set(ENV{PKG_CONFIG_SYSROOT_DIR} ${CMAKE_SYSROOT})

# if you use other version of gcc and g++ than gcc/g++ 11, you must change the following variables
set(CMAKE_C_COMPILER /usr/bin/aarch64-linux-gnu-gcc-11)
set(CMAKE_CXX_COMPILER /usr/bin/aarch64-linux-gnu-g++-11)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -I${TARGET_SYSROOT}/usr/include")
set(CMAKE_CXX_FLAGS "${CMAKE_C_FLAGS}")

set(QT_COMPILER_FLAGS "-march=armv8-a")
set(QT_COMPILER_FLAGS_RELEASE "-O2 -pipe")
set(QT_LINKER_FLAGS "-Wl,-O1 -Wl,--hash-style=gnu -Wl,--as-needed")

set(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)
set(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)
set(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)
set(CMAKE_FIND_ROOT_PATH_MODE_PACKAGE ONLY)
set(CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE)
set(CMAKE_BUILD_RPATH ${TARGET_SYSROOT})


include(CMakeInitializeConfigs)

function(cmake_initialize_per_config_variable _PREFIX _DOCSTRING)
  if (_PREFIX MATCHES "CMAKE_(C|CXX|ASM)_FLAGS")
    set(CMAKE_${CMAKE_MATCH_1}_FLAGS_INIT "${QT_COMPILER_FLAGS}")
        
    foreach (config DEBUG RELEASE MINSIZEREL RELWITHDEBINFO)
      if (DEFINED QT_COMPILER_FLAGS_${config})
        set(CMAKE_${CMAKE_MATCH_1}_FLAGS_${config}_INIT "${QT_COMPILER_FLAGS_${config}}")
      endif()
    endforeach()
  endif()


  if (_PREFIX MATCHES "CMAKE_(SHARED|MODULE|EXE)_LINKER_FLAGS")
    foreach (config SHARED MODULE EXE)
      set(CMAKE_${config}_LINKER_FLAGS_INIT "${QT_LINKER_FLAGS}")
    endforeach()
  endif()

  _cmake_initialize_per_config_variable(${ARGV})
endfunction()

set(XCB_PATH_VARIABLE ${TARGET_SYSROOT})

set(GL_INC_DIR ${TARGET_SYSROOT}/usr/include)
set(GL_LIB_DIR ${TARGET_SYSROOT}:${TARGET_SYSROOT}/usr/lib/aarch64-linux-gnu/:${TARGET_SYSROOT}/usr:${TARGET_SYSROOT}/usr/lib)

set(EGL_INCLUDE_DIR ${GL_INC_DIR})
set(EGL_LIBRARY ${XCB_PATH_VARIABLE}/usr/lib/aarch64-linux-gnu/libEGL.so)

set(OPENGL_INCLUDE_DIR ${GL_INC_DIR})
set(OPENGL_opengl_LIBRARY ${XCB_PATH_VARIABLE}/usr/lib/aarch64-linux-gnu/libOpenGL.so)

set(GLESv2_INCLUDE_DIR ${GL_INC_DIR})
set(GLESv2_LIBRARY ${XCB_PATH_VARIABLE}/usr/lib/aarch64-linux-gnu/libGLESv2.so)

set(gbm_INCLUDE_DIR ${GL_INC_DIR})
set(gbm_LIBRARY ${XCB_PATH_VARIABLE}/usr/lib/aarch64-linux-gnu/libgbm.so)

set(Libdrm_INCLUDE_DIR ${GL_INC_DIR})
set(Libdrm_LIBRARY ${XCB_PATH_VARIABLE}/usr/lib/aarch64-linux-gnu/libdrm.so)

set(XCB_XCB_INCLUDE_DIR ${GL_INC_DIR})
set(XCB_XCB_LIBRARY ${XCB_PATH_VARIABLE}/usr/lib/aarch64-linux-gnu/libxcb.so)
```

2. Change into the Build directory
    - [x] `cd ~/qtpi-build`
3. Run the configuration command:
    - [ ] `../qt5/configure -release -opengl es2 -nomake examples -nomake tests -qt-host-path $HOME/qt-host -extprefix $HOME/qt-raspi -prefix /usr/local/qt6 -device linux-rasp-pi4-aarch64 -device-option CROSS_COMPILE=aarch64-linux-gnu- -- -DCMAKE_TOOLCHAIN_FILE=$HOME/toolchain.cmake -DQT_FEATURE_xcb=ON -DFEATURE_xcb_xlib=ON -DQT_FEATURE_xlib=ON`
4. Proceed with the Build and Install
    - [ ] `cmake --build . --parallel 4`
    - [ ] `cmake --install .`
5. Send the Build to the RPI using rsync:
    - [ ] `rsync -avz --rsync-path="sudo rsync" /path/to/qt-raspi/* gadiv@192.168.1.11:/usr/local/qt6`
# Final Configuration on RPI
1. Setup Environment Variables
    - ssh into RPI from Host
        - [ ] `ssh gadiv@192.168.1.11`
        - [ ] `export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/qt6/lib/`
        - If you plan to run QT Application on RPI via SSH from the Host and Display on Monitor connected to RPI then Set DISPLAY Environment Variable:
        - [ ] `export $DISPLAY=:0`
# Compiling and Running QT Project
1. On Host in Terminal:
    - [ ] `cd ~/qt5/qtbase/examples/gui/analogclock/`
    - [ ] `ls ## make sure there is a CMakeLists.txt file`
    - [ ] `~/qt-raspi/bin/qt-cmake CMakeLists.txt`
    - [ ] `cmake --build . --parallel 4`
    - [ ] `cmake --install .`
2. On Host: Send Application Binary to RPI
    - [ ] `scp -r gui_analogclock <gadiv@192.168.1.11:/home/gadiv`
    - [ ] `ssh gadiv@192.168.1.11`
    - [ ] `cd ~ ## or cd to the directory where you send the binary`
3. On the RPI: Run the application
    - [ ] `cd ~`
    - [ ] `./gui_analogclock`
        
# Building and Running from QT Creator
1. TO Configure QT Creator on the host to build, deploy, and run the application on the RPI and display in a window on the host, See the last section of the link below:
    - https://wiki.qt.io/Cross-Compile_Qt_6_for_Raspberry_Pi
