
if (NOT FIPS_IMPORT)

    cmake_minimum_required(VERSION 3.7)
    get_filename_component(FIPS_ROOT_DIR "../fips" ABSOLUTE)
    include("${FIPS_ROOT_DIR}/cmake/fips.cmake")
project(fips-chipmunk)    
fips_setup()
    
endif()

if ((FIPS_MACOS OR FIPS_WINDOWS OR FIPS_LINUX) AND NOT (FIPS_RASPBERRYPI OR FIPS_OSX_USE_ARC OR FIPS_UWP))
	fips_begin_lib(chipmunk)
		include_directories(
				"Chipmunk2D/include/"
			)
		fips_files_ex("Chipmunk2D/src/" "*.c")
		#Compile internal files as C++
		if(FIPS_MSVC)
			set_source_files_properties(${CurSources} PROPERTIES LANGUAGE CXX)
		endif(FIPS_MSVC)
		fips_files_ex("Chipmunk2D/include/chipmunk/" "*.h")

		# Chipmunk2D 7.0.3
		set(CHIPMUNK_VERSION_MAJOR 7)
		set(CHIPMUNK_VERSION_MINOR 0)
		set(CHIPMUNK_VERSION_PATCH 3)
		set(CHIPMUNK_VERSION "${CHIPMUNK_VERSION_MAJOR}.${CHIPMUNK_VERSION_MINOR}.${CHIPMUNK_VERSION_PATCH}")
		message("Configuring Chipmunk2D version ${CHIPMUNK_VERSION}")
	fips_end_lib()
	
	if(FIPS_MSVC)
		set_target_properties(chipmunk PROPERTIES LINKER_LANGUAGE CXX)
	endif(FIPS_MSVC)
	
	if(CMAKE_C_COMPILER_ID STREQUAL "Clang")
		option(FORCE_CLANG_BLOCKS "Force enable Clang blocks" YES)
	endif()
	if(NOT FIPS_MSVC)
		set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=gnu99") # always use gnu99
		if(FORCE_CLANG_BLOCKS)
			set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fblocks")
		endif()
		set(CMAKE_C_FLAGS_RELEASE "${CMAKE_C_FLAGS_RELEASE} -ffast-math") # extend release-profile with fast-math
		set(CMAKE_C_FLAGS_DEBUG "${CMAKE_C_FLAGS_DEBUG} -Wall") # extend debug-profile with -Wall
	endif()
endif()


#TODO: Implement rest of CMakeLists for Linux.

#if(BUILD_SHARED)
#  add_library(chipmunk SHARED
#    ${chipmunk_source_files}
#  )
#  # Tell MSVC to compile the code as C++.
#  if(MSVC)
#    set_source_files_properties(${chipmunk_source_files} PROPERTIES LANGUAGE CXX)
#    set_target_properties(chipmunk PROPERTIES LINKER_LANGUAGE CXX)
#  endif(MSVC)
  # set the lib's version number
  # But avoid on Android because symlinks to version numbered .so's don't work with Android's Java-side loadLibrary.
#  if(NOT ANDROID)
#    set_target_properties(chipmunk PROPERTIES
#      SOVERSION ${CHIPMUNK_VERSION_MAJOR}
#      VERSION ${CHIPMUNK_VERSION})
#  endif(NOT ANDROID)
#  if(ANDROID OR UNIX)
#	  # need to explicitly link to the math library because the CMake/Android toolchains may not do it automatically
#	  target_link_libraries(chipmunk m)
#  endif(ANDROID OR UNIX)
#  install(TARGETS chipmunk RUNTIME DESTINATION ${BIN_INSTALL_DIR}
#                           LIBRARY DESTINATION ${LIB_INSTALL_DIR}
#                           ARCHIVE DESTINATION ${LIB_INSTALL_DIR})
#endif(BUILD_SHARED)

#if(BUILD_STATIC)
#  add_library(chipmunk_static STATIC
#    ${chipmunk_source_files}
#  )
  # Tell MSVC to compile the code as C++.
#  if(MSVC)
#    set_source_files_properties(${chipmunk_source_files} PROPERTIES LANGUAGE CXX)
#    set_target_properties(chipmunk_static PROPERTIES LINKER_LANGUAGE CXX)
#  endif(MSVC)
  # Sets chipmunk_static to output "libchipmunk.a" not "libchipmunk_static.a"
#  set_target_properties(chipmunk_static PROPERTIES OUTPUT_NAME chipmunk)
#  if(INSTALL_STATIC)
#    install(TARGETS chipmunk_static ARCHIVE DESTINATION ${LIB_INSTALL_DIR})
#  endif(INSTALL_STATIC)
#endif(BUILD_STATIC)

#if(BUILD_SHARED OR INSTALL_STATIC)
#  # FIXME: change to PUBLIC_HEADER to allow building frameworks
#  install(FILES ${chipmunk_public_header} DESTINATION include/chipmunk)
#  install(FILES ${chipmunk_constraint_header} DESTINATION include/chipmunk/constraints)
#endif(BUILD_SHARED OR INSTALL_STATIC)

if (NOT FIPS_IMPORT)
    fips_finish()
endif()