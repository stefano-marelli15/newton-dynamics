# Copyright (c) <2014-2017> <Newton Game Dynamics>
#
# This software is provided 'as-is', without any express or implied
# warranty. In no event will the authors be held liable for any damages
# arising from the use of this software.
#
# Permission is granted to anyone to use this software for any purpose,
# including commercial applications, and to alter it and redistribute it
# freely.

cmake_minimum_required(VERSION 3.4.0)

set (projectName "dContainers")
message (${projectName})

#source and header files
file(GLOB CPP_SOURCE *.h *.cpp)
source_group(TREE "${CMAKE_CURRENT_SOURCE_DIR}/" FILES ${CPP_SOURCE})

if(NEWTON_BUILD_SHARED_LIBS)
    if(MSVC)
        add_definitions(-D_DCONTAINERS_DLL)
        add_definitions(-D_DCONTAINERS_EXPORT)
    endif(MSVC)
    add_library(${projectName} SHARED ${CPP_SOURCE})
else(NEWTON_BUILD_SHARED_LIBS)
    if (MSVC)
        add_definitions(-D_NEWTON_STATIC_LIB)
    endif(MSVC)

    add_library(${projectName} STATIC ${CPP_SOURCE})
endif(NEWTON_BUILD_SHARED_LIBS)

target_include_directories(${projectName} PUBLIC . ../dMath)
if (NEWTON_BUILD_PROFILER)
    target_link_libraries (${projectName} dProfiler)
endif()

if (MSVC)
    if(CMAKE_VS_MSBUILD_COMMAND OR CMAKE_VS_DEVENV_COMMAND)
	    set_target_properties(${projectName} PROPERTIES COMPILE_FLAGS "/YudContainersStdAfx.h")
        set_source_files_properties(dContainersStdAfx.cpp PROPERTIES COMPILE_FLAGS "/YcdContainersStdAfx.h")
    endif()

	if (NEWTON_BUILD_SANDBOX_DEMOS)
		add_custom_command(
			TARGET ${projectName} POST_BUILD
			COMMAND ${CMAKE_COMMAND}
			ARGS -E copy $<TARGET_FILE:${projectName}> ${PROJECT_BINARY_DIR}/applications/demosSandbox/${CMAKE_CFG_INTDIR}/$<TARGET_FILE_NAME:${projectName}>)
	endif ()
endif(MSVC)

install(TARGETS ${projectName}
       LIBRARY DESTINATION lib
       ARCHIVE DESTINATION lib
       RUNTIME DESTINATION bin)

install(FILES ${HEADERS} DESTINATION include/${projectName})

