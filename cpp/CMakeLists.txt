if (SUPERNOVA)
    include_directories(${SC_PATH}/external_libraries/nova-tt)
    # actually just boost.atomic
    include_directories(${SC_PATH}/external_libraries/boost)
endif()

include_directories(${CMAKE_SOURCE_DIR}/)
include_directories(scfft_old/)

include_directories(${SC_PATH}/common)
include_directories(${SC_PATH}/plugin_interface)

include_directories(${SC_PATH}/include/plugin_interface)
include_directories(${SC_PATH}/include/common)
include_directories(${SC_PATH}/external_libraries/nova-simd)
include_directories(${SC_PATH}/server/plugins) # for FFT_UGens.h

# old-style directory layout
include_directories(${SC_PATH}/common/Headers/plugin_interface)
include_directories(${SC_PATH}/common/Headers/common)
include_directories(${SC_PATH}/common/Source/plugins) # for FFT_UGens.h

#-------
# macros
#-------

macro(GET_GCC_VERSION VAR)
    if (CMAKE_COMPILER_IS_GNUCC)
        execute_process(
            COMMAND ${CMAKE_CXX_COMPILER} --version OUTPUT_VARIABLE OUTPUT)
        string(REGEX REPLACE
            ".* ([0-9]\\.[0-9]\\.[0-9]) .*" "\\1" ${VAR} ${OUTPUT})
    endif()
endmacro(GET_GCC_VERSION)


macro(FIND_AND_BUILD_PLUGINS DIR)
    file(GLOB PLUGIN_SRC RELATIVE "${CMAKE_CURRENT_SOURCE_DIR}" "${DIR}/*.cpp")
    foreach(plugin ${PLUGIN_SRC})
        get_filename_component(basename ${plugin} NAME_WE)
        BUILD_PLUGIN(${basename} ${plugin} "" "${DIR}")
    endforeach(plugin)
endmacro()


# Hacky: OSX plugins should install in PLUGIN_DIR
macro(BUILD_PLUGIN PLUGIN_NAME PLUGIN_SOURCES PLUGIN_LIBS PLUGIN_DIR)
    add_library(${PLUGIN_NAME} MODULE ${PLUGIN_SOURCES})
    list(APPEND plugins ${PLUGIN_NAME})
    target_link_libraries(${PLUGIN_NAME} ${PLUGIN_LIBS})

    if(APPLE)
        install(TARGETS ${PLUGIN_NAME}
                DESTINATION ${INSTALL_DESTINATION}/${PLUGIN_DIR})
    endif()

    if(SUPERNOVA)
        add_library(${PLUGIN_NAME}_supernova MODULE ${PLUGIN_SOURCES})
        target_link_libraries(${PLUGIN_NAME}_supernova ${PLUGIN_LIBS})

        list(APPEND supernova_plugins ${PLUGIN_NAME}_supernova)
        set_property(TARGET ${PLUGIN_NAME}_supernova
                     PROPERTY COMPILE_DEFINITIONS SUPERNOVA)
        if(APPLE)
            install(TARGETS ${PLUGIN_NAME}_supernova
                    DESTINATION ${INSTALL_DESTINATION}/${PLUGIN_DIR})
        endif()
    endif()
endmacro()


set(CMAKE_SHARED_MODULE_PREFIX "")
set(PLUGIN_EXTENSION ".so")

if (APPLE OR WIN32)
    set(CMAKE_SHARED_MODULE_SUFFIX ".scx")
    set(PLUGIN_EXTENSION ".scx")
endif()

set(INSTALL_DESTINATION "berlach_plugins")

BUILD_PLUGIN(BeatTrack2
"BeatTrack2.cpp"
"" "plugins")

BUILD_PLUGIN(BeatTrack
"BeatTrack.cpp"
"" "plugins")

BUILD_PLUGIN(BinaryOpUGens
"BinaryOpUGens.cpp"
"" "plugins")

BUILD_PLUGIN(ChaosUGens
"ChaosUGens.cpp"
"" "plugins")

BUILD_PLUGIN(Convolution
"Convolution.cpp"
"" plugins)

BUILD_PLUGIN(DelayUGens
"DelayUGens.cpp"
"" "plugins")

BUILD_PLUGIN(DemandUGens
"DemandUGens.cpp"
"" "plugins")

#BUILD_PLUGIN(DiskIO_UGens
#"DiskIO_UGens.cpp"
#"" "plugins")

BUILD_PLUGIN(DynNoiseUGens
"DynNoiseUGens.cpp"
"" "plugins")

BUILD_PLUGIN(FeatureDetection
"FeatureDetection.cpp"
"" "plugins")

BUILD_PLUGIN(FFT_UGens
"FFT_UGens.cpp"
"" "plugins")

BUILD_PLUGIN(FilterUGens
"FilterUGens.cpp"
"" "plugins")

BUILD_PLUGIN(GendynUGens
"GendynUGens.cpp"
"" "plugins")

BUILD_PLUGIN(GrainUGens
"GrainUGens.cpp"
"" "plugins")

BUILD_PLUGIN(IOUGens
"IOUGens.cpp"
"" "plugins")

BUILD_PLUGIN(KeyTrack
"KeyTrack.cpp"
"" "plugins")

BUILD_PLUGIN(LFUGens
"LFUGens.cpp"
"" "plugins")

BUILD_PLUGIN(Loudness
"Loudness.cpp"
"" "plugins")

BUILD_PLUGIN(ML
"ML.cpp"
"" "plugins")

BUILD_PLUGIN(MulAddUGens
"MulAddUGens.cpp"
"" "plugins")

BUILD_PLUGIN(NoiseUGens
"NoiseUGens.cpp"
"" "plugins")

BUILD_PLUGIN(Onsets
"Onsets.cpp"
"" "plugins")

BUILD_PLUGIN(OscUGens
"OscUGens.cpp"
"" "plugins")

BUILD_PLUGIN(PanUGens
"PanUGens.cpp"
"" "plugins")

BUILD_PLUGIN(PartitionedConvolution
"PartitionedConvolution.cpp"
"" "plugins")

BUILD_PLUGIN(PhysicalModelingUGens
"PhysicalModelingUGens.cpp"
"" "plugins")

BUILD_PLUGIN(PV_ThirdParty
"PV_ThirdParty.cpp"
"" "plugins")

BUILD_PLUGIN(PV_UGens
"PV_UGens.cpp"
"" "plugins")

BUILD_PLUGIN(ReverbUGens
"ReverbUGens.cpp"
"" "plugins")

BUILD_PLUGIN(TestUGens
"TestUGens.cpp"
"" "plugins")

#BUILD_PLUGIN(TriggerUGens
#"TriggerUGens.cpp"
#"" "plugins")

BUILD_PLUGIN(UnaryOpUGens
"UnaryOpUGens.cpp"
"" "plugins")
