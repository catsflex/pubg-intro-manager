cmake_minimum_required(VERSION 3.31)
set(PROJECT_NAME pubg-intro-manager)
set(EXECUTABLE_NAME "PUBG Intro Manager")
project(${PROJECT_NAME} LANGUAGES CXX)

# Pure C++23
set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Source files
add_executable(${PROJECT_NAME}
	main.c++
	assets/resources.rc
)

# Headers
target_include_directories(${PROJECT_NAME} PRIVATE "${CMAKE_CURRENT_SOURCE_DIR}/headers")

# Modules
set(CMAKE_CXX_SCAN_FOR_MODULES ON)
target_sources(${PROJECT_NAME} PUBLIC FILE_SET CXX_MODULES FILES
	modules/win32_utils.c++m
	modules/intro_manager.c++m
	modules/console_ui.c++m
	modules/my_utils.c++m
	modules/logger.c++m
)

# Executable properties
set_target_properties(${PROJECT_NAME} PROPERTIES
	OUTPUT_NAME ${EXECUTABLE_NAME}
)

if(MSVC)
	
	# Admin rights needed
	target_link_options(${PROJECT_NAME} PRIVATE
		"/MANIFESTUAC:level='requireAdministrator' uiAccess='false'"
	)
	
	# Static linking
	set_property(TARGET ${PROJECT_NAME} PROPERTY
		MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>"
	)
	
	# Global compile options
	target_compile_options(${PROJECT_NAME} PRIVATE
		/permissive- /std:c++latest
	)
	
	# Debug compile options
	target_compile_options(${PROJECT_NAME} PRIVATE
		$<$<CONFIG:Debug>:/W4 /Zi /Od>
	)
	
	# Release compile options
	target_compile_options(${PROJECT_NAME} PRIVATE
		$<$<CONFIG:Release>:/O2 /Oi /GL /Gy /GR- /EHsc>
	)
	target_link_options(${PROJECT_NAME} PRIVATE
		$<$<CONFIG:Release>:/LTCG /OPT:REF /OPT:ICF>
	)
endif()
