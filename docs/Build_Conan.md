git clone https://github.com/MatsuriDayo/nekoray.git --recursive

then modify or replace these below files:
1. CMakeLists.txt
2. conanfile.txt/conanfile_qt5.txt/conanfile_qt64.txt (choose any one file to match QT version)
3. docs\Build_Conan.md
4. cmake\windows\windows.cmake

Windows:
1. Preparation (only first time)

   $conan profile detect --force

   $copy C:\Users\{your account}\.conan2\profiles\default C:\Users\{your account}\.conan2\profiles\Windows.Release (modify compiler.cppstd in Windows.Release： compiler.cppstd=14 ===> compiler.cppstd=17)
2. Build dependencies

   $conan install . -b missing -pr Windows.Release
3. Build nekoray

   $cd build

   $cmake .. -G "Visual Studio 17 2022" -DCMAKE_TOOLCHAIN_FILE=.\generators\conan_toolchain.cmake -DCMAKE_POLICY_DEFAULT_CMP0091=NEW -DCMAKE_POLICY_DEFAULT_CMP0057=NEW -DQT_VERSION_MAJOR=6

   $cmake --build . --config Release

Linux:
1. Preparation (only first time)

   $conan profile detect --force

   $cp ~/.conan2/profiles/default ~/.conan2/profiles/Linux.Release (confirm compiler.cppstd in Linux.Release： compiler.cppstd=gnu17 or compiler.cppstd=17)
2. Build dependencies

   $export NOT_ON_C3I=1 && conan install . -b missing -pr Linux.Release
3. Build nekoray

   $cd build

   $cmake .. -DCMAKE_TOOLCHAIN_FILE=./Release/generators/conan_toolchain.cmake -DCMAKE_POLICY_DEFAULT_CMP0091=NEW -DCMAKE_POLICY_DEFAULT_CMP0057=NEW -DCMAKE_BUILD_TYPE=Release -DQT_VERSION_MAJOR=6

   $cmake --build .

macOS:
1. Preparation (only first time)

   $conan profile detect --force

   $cp ~/.conan2/profiles/default ~/.conan2/profiles/Macos.Release (confirm compiler.cppstd in Macos.Release： compiler.cppstd=gnu17 or compiler.cppstd=17)
2. Build dependencies, choose any one command to match QT version

   $conan install conanfile_qt5.txt -b missing -pr Macos.Release (QT 5.15 on macOS 10.12 or later)

   or

   $conan install conanfile_qt64.txt -b missing -pr Macos.Release (QT 6.4 on macOS 10.14 or later)

   or

   $conan install . -b missing -pr Macos.Release (QT 6.5, only macOS 11.0 or later, because cmake_automoc_parser only run on 11.0.0 or later)
3. Build nekoray, choose any one command to match QT version

   $cd build

   $cmake .. -DCMAKE_TOOLCHAIN_FILE=./Release/generators/conan_toolchain.cmake -DCMAKE_POLICY_DEFAULT_CMP0091=NEW -DCMAKE_POLICY_DEFAULT_CMP0057=NEW -DCMAKE_BUILD_TYPE=Release -DQT_VERSION_MAJOR=5 (QT 5.15)

   or

   $cmake .. -DCMAKE_TOOLCHAIN_FILE=./Release/generators/conan_toolchain.cmake -DCMAKE_POLICY_DEFAULT_CMP0091=NEW -DCMAKE_POLICY_DEFAULT_CMP0057=NEW -DCMAKE_BUILD_TYPE=Release -DQT_VERSION_MAJOR=6 (QT 6.x)

   $cmake --build .

FreeBSD:
1. Preparation (only first time)

   $conan profile detect --force
   
   $cp ~/.conan2/profiles/default ~/.conan2/profiles/FreeBSD.Release (confirm compiler.cppstd in FreeBSD.Release： compiler.cppstd=gnu17 or compiler.cppstd=17)

   append follow lines to ~/.conan2/global.conf:
   
     tools.system.package_manager:mode = install

     tools.system.package_manager:sudo = True
2. Build dependencies

   $conan install . -b missing -pr FreeBSD.Release (build gpref library fails, I'm tracking this issue: https://github.com/conan-io/conan/issues/15077)
3. Build nekoray

   $cd build

   $cmake .. -DCMAKE_TOOLCHAIN_FILE=./Release/generators/conan_toolchain.cmake -DCMAKE_POLICY_DEFAULT_CMP0091=NEW -DCMAKE_POLICY_DEFAULT_CMP0057=NEW -DCMAKE_BUILD_TYPE=Release -DQT_VERSION_MAJOR=6

   $cmake --build .
