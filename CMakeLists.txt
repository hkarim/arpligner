# cmake -DCMAKE_TOOLCHAIN_FILE=$VCPKG_ROOT/scripts/buildsystems/vcpkg.cmake . -B cmake-build
# cmake --build cmake-build --target help
# cmake --build cmake-build --config Release --target Arpligner_VST3


cmake_minimum_required(VERSION 3.15)

project(Arpligner VERSION 0.0.1)

# on macOS Sonoma and LLVM Clang 17.0.6, CMake invokes `ar` and `ranlib` under `/usr/bin` which causes corrupt libs.
# force the use of `llvm-ar` and `llvm-ranlib` if we are using LLVM
# export LLVM=$(brew --prefix llvm)
if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang")
    message("LLVM : $ENV{LLVM}")
    set(CMAKE_AR "$ENV{LLVM}/bin/llvm-ar" CACHE FILEPATH "LLVM ar" FORCE)
    set(CMAKE_RANLIB "$ENV{LLVM}/bin/llvm-ranlib" CACHE FILEPATH "LLVM ranlib" FORCE)
    message("AR : ${CMAKE_AR}")
    message("RANLIB : ${CMAKE_RANLIB}")
endif ()

#set(CMAKE_VERBOSE_MAKEFILE ON)
set(CMAKE_CXX_STANDARD 20)
# disable some JUCE objective-c code warnings
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-receiver-forward-class -Wno-objc-method-access")

# uncomment the following line if you have checked out JUCE locally in the current directory
#add_subdirectory(JUCE)

# use the following if you've installed JUCE globally or using a package manager, `vcpkg` for example
find_package(JUCE CONFIG REQUIRED)

set(JUCE_COPY_PLUGIN_AFTER_BUILD true)

juce_add_plugin(Arpligner
    COMPANY_NAME Ywen
    IS_SYNTH TRUE
    NEEDS_MIDI_INPUT TRUE 
    NEEDS_MIDI_OUTPUT TRUE
    #IS_MIDI_EFFECT TRUE
    # EDITOR_WANTS_KEYBOARD_FOCUS TRUE/FALSE
    COPY_PLUGIN_AFTER_BUILD TRUE
    PLUGIN_MANUFACTURER_CODE YW00               # A four-character manufacturer id with at least one upper-case character
    PLUGIN_CODE Yarp                            # A unique four-character plugin id with exactly one upper-case character
                                                # GarageBand 10.3 requires the first letter to be upper-case, and the remaining letters to be lower-case
    FORMATS VST3                                # The formats to build. Other valid formats are: AAX Unity VST AU AUv3
    PRODUCT_NAME "Arpligner"                    # The name of the final executable, which can differ from the target name
)        

juce_generate_juce_header(Arpligner)

target_sources(Arpligner
    PRIVATE
        Source/Arp.cpp
        Source/ChordStore.cpp
        Source/PluginProcessor.cpp)

target_compile_definitions(Arpligner
    PUBLIC
        # JUCE_WEB_BROWSER and JUCE_USE_CURL would be on by default, but you might not need them.
        JUCE_WEB_BROWSER=0  # If you remove this, add `NEEDS_WEB_BROWSER TRUE` to the `juce_add_plugin` call
        JUCE_USE_CURL=0     # If you remove this, add `NEEDS_CURL TRUE` to the `juce_add_plugin` call
        JUCE_VST3_CAN_REPLACE_VST2=0)

target_link_libraries(Arpligner
    PRIVATE
        juce::juce_audio_utils
    PUBLIC
        juce::juce_recommended_config_flags
        juce::juce_recommended_lto_flags
        juce::juce_recommended_warning_flags)
