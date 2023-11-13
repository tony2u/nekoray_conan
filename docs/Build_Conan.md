git clone https://github.com/MatsuriDayo/nekoray.git --recursive
then replace these below files:
1. CMakeLists.txt
2. conanfile.txt/conanfile_qt5.txt/conanfile_qt64.txt
3. docs\Build_Conan.md
4. cmake\windows\windows.cmake

Windows:
1. Preparation (only first time)
   conan profile detect --force
   copy C:\Users\tony2u\.conan2\profiles\default C:\Users\tony2u\.conan2\profiles\Windows.Release (modify compiler.cppstd in Windows.Release： compiler.cppstd=14 ===> compiler.cppstd=17, compiler=msvc)
2. Build dependencies
   conan install . -b missing -pr Windows.Release
3. Build nekoray
   cd build
   cmake .. -G "Visual Studio 17 2022" -DCMAKE_TOOLCHAIN_FILE=.\generators\conan_toolchain.cmake -DCMAKE_POLICY_DEFAULT_CMP0091=NEW -DCMAKE_POLICY_DEFAULT_CMP0057=NEW -DQT_VERSION_MAJOR=6
   cmake --build . --config Release

or using LLVM & MSVC Clang
1. Preparation (only first time)
   conan profile detect --force
   copy C:\Users\tony2u\.conan2\profiles\default C:\Users\tony2u\.conan2\profiles\clang (confirm compiler and compiler.cppstd in clang： compiler.cppstd=17, compiler=clang)
2. Build dependencies
   set NOT_ON_C3I=1 & conan install . -b missing -pr clang
3. Build nekoray
   cd build
   cmake .. -G Ninja -DCMAKE_TOOLCHAIN_FILE=.\Release\generators\conan_toolchain.cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_C_COMPILER=bcc64 -DCMAKE_CXX_COMPILER=bcc64 -DQT_VERSION_MAJOR=6
   ninja all -j 4

or using Embarcadero RadStudio
1. Preparation (only first time)
   conan profile detect --force
   copy C:\Users\tony2u\.conan2\profiles\default C:\Users\tony2u\.conan2\profiles\bcc64 (confirm compiler and compiler.cppstd in bcc64： compiler.cppstd=17, compiler=clang)
2. Build dependencies
   set NOT_ON_C3I=1 & conan install . -b missing -pr bcc64
3. Build nekoray
   cd build
   cmake .. -G Ninja -DCMAKE_TOOLCHAIN_FILE=.\Release\generators\conan_toolchain.cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_C_COMPILER=bcc64 -DCMAKE_CXX_COMPILER=bcc64 -DQT_VERSION_MAJOR=6
   ninja all -j 4

Linux:
1. Preparation (only first time)
   conan profile detect --force
   cp ~/.conan2/profiles/default ~/.conan2/profiles/Linux.Release (confirm compiler.cppstd in Linux.Release： compiler.cppstd=gnu17 or compiler.cppstd=17)
2. Build dependencies
   export NOT_ON_C3I=1 && conan install . -b missing -pr Linux.Release
   (qt/6.5.3: Invalid: qt is not supported on gcc11 and clang >= 12 on C3I until conan-io/conan-center-index#13472 is fixed)
3. Build nekoray
   cd build
   cmake .. -DCMAKE_TOOLCHAIN_FILE=./Release/generators/conan_toolchain.cmake -DCMAKE_POLICY_DEFAULT_CMP0091=NEW -DCMAKE_POLICY_DEFAULT_CMP0057=NEW -DCMAKE_BUILD_TYPE=Release -DQT_VERSION_MAJOR=6
   cmake --build .

macOS:
1. Preparation (only first time)
   conan profile detect --force (only first time)
   cp ~/.conan2/profiles/default ~/.conan2/profiles/Macos.Release (confirm compiler.cppstd in Macos.Release： compiler.cppstd=gnu17 or compiler.cppstd=17)
2. Build dependencies
   conan install conanfile_qt5.txt -b missing -pr Macos.Release  #QT 5.15 on macOS 10.12 or later
   or
   conan install conanfile_qt64.txt -b missing -pr Macos.Release #QT 6.4 on macOS 10.14 or later
   or
   conan install . -b missing -pr Macos.Release                  #QT 6.5, only macOS 11.0 or later, because cmake_automoc_parser only run on 11.0.0 or later
3. Build nekoray
   cd build
   cmake .. -DCMAKE_TOOLCHAIN_FILE=./Release/generators/conan_toolchain.cmake -DCMAKE_POLICY_DEFAULT_CMP0091=NEW -DCMAKE_POLICY_DEFAULT_CMP0057=NEW -DCMAKE_BUILD_TYPE=Release -DQT_VERSION_MAJOR=5 #QT 5.15
   or
   cmake .. -DCMAKE_TOOLCHAIN_FILE=./Release/generators/conan_toolchain.cmake -DCMAKE_POLICY_DEFAULT_CMP0091=NEW -DCMAKE_POLICY_DEFAULT_CMP0057=NEW -DCMAKE_BUILD_TYPE=Release -DQT_VERSION_MAJOR=6 #QT 6.x
   cmake --build .

FreeBSD:
conanfile.txt
[requires]
protobuf/3.21.4
yaml-cpp/0.7.0
zxing-cpp/2.0.0

[generators]
CMakeDeps
CMakeToolchain

[layout]
cmake_layout

modified conan python file:
~/.local/lib/python3.9/site-packages/conan/tools/gnu/autotools.py
def make(self, target=None, args=None):
...
if jobs=="-j1":
    jobs = ""

modified source files:
1. ~/Projects/nekoray/3rdparty/qjs/cutils.h
-#else
+#elif !defined(__FreeBSD__)
#include <alloca.h>
#endif
2. ~/Projects/nekoray/3rdparty/qjs/quickjs-libc.c
参照：/usr/ports/lang/quickjs/files/patch-quickjs-libc.c
 #include <sys/ioctl.h>
 #include <sys/wait.h>

-#if defined(__APPLE__)
+#if defined(__FreeBSD__)
+extern char **environ;
+#endif
+
+#if defined(__APPLE__) || defined(__FreeBSD__)
 typedef sig_t sighandler_t;
+#endif
+#if defined(__APPLE__)
 #if !defined(environ)
 #include <crt_externs.h>
 #define environ (*_NSGetEnviron())
3. ~/Projects/nekoray/sys/AutoRun.cpp
+#ifdef Q_OS_FREEBSD
+void AutoRun_SetEnabled(bool enable) {}
+bool AutoRun_IsEnabled() { return false; }
#endif
4. ~/Projects/nekoray/3rdparty/qv2ray/v2/components/proxy/QvProxyConfigurator.cpp
void SetSystemProxy(int httpPort, int socksPort) {
-#else
+#elif defined(Q_OS_MACOS)
...
+#elif defined(Q_OS_FREEBSD)
#endif

void ClearSystemProxy() {
-#else
+#elif defined(Q_OS_MACOS)
...
+#elif defined(Q_OS_FREEBSD)
#endif

1. Preparation (only first time)
   conan profile detect --force (only first time)
   cp ~/.conan2/profiles/default ~/.conan2/profiles/FreeBSD.Release (confirm compiler.cppstd in FreeBSD.Release： compiler.cppstd=gnu17 or compiler.cppstd=17)
   append two lines to ~/.conan2/global.conf:
   tools.system.package_manager:mode = install
   tools.system.package_manager:sudo = True
2. Build dependencies
    setenv NOT_ON_C3I 1
    conan install conanfile_qt5.txt -b missing -pr FreeBSD.Release  #QT 5.15 on FreeBSD, qt5, qt5-linguist, qt5-svg, qt5-x11extras
    or
    conan install . -b missing -pr FreeBSD.Release -c tools.build:jobs=1 #QT 6.5 on FreeBSD, qt6-base, qt6-svg, qt6-tools
3. Build nekoray
   cd build
   cmake .. -DCMAKE_TOOLCHAIN_FILE=./Release/generators/conan_toolchain.cmake -DCMAKE_POLICY_DEFAULT_CMP0091=NEW -DCMAKE_POLICY_DEFAULT_CMP0057=NEW -DCMAKE_BUILD_TYPE=Release -DQT_VERSION_MAJOR=6
   cmake --build .
