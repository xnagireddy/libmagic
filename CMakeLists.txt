cmake_minimum_required(VERSION 3.2)

project(magic VERSION 5.18)

include(ExternalProject)

ExternalProject_Add(
    magic-autotools
    SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}
    CONFIGURE_COMMAND ./autogen.sh
    COMMAND ./configure --prefix=${CMAKE_CURRENT_SOURCE_DIR}/_install
    BUILD_COMMAND make
    BUILD_IN_SOURCE 1
    INSTALL_COMMAND make install
)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DHAVE_CONFIG_H")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DHAVE_CONFIG_H")

add_library(
    magic STATIC
	./src/magic.c
	./src/apprentice.c
	./src/softmagic.c
	./src/ascmagic.c
	./src/encoding.c
	./src/compress.c
	./src/is_tar.c
	./src/readelf.c
	./src/print.c
	./src/fsmagic.c
	./src/funcs.c
	./src/apptype.c
	./src/cdf.c
	./src/cdf_time.c
	./src/readcdf.c
	./src/strlcpy.c
	./src/strlcat.c
	./src/fmtcheck.c
)

add_dependencies(magic magic-autotools)

include_directories(.)

set(config_install_dir "lib/cmake/${PROJECT_NAME}")
set(include_install_dir "include")

set(generated_dir "${CMAKE_CURRENT_BINARY_DIR}/generated")

set(version_config "${generated_dir}/${PROJECT_NAME}ConfigVersion.cmake")
set(project_config "${generated_dir}/${PROJECT_NAME}Config.cmake")
set(TARGETS_EXPORT_NAME "${PROJECT_NAME}Targets")
set(namespace "${PROJECT_NAME}::")

include(CMakePackageConfigHelpers)

write_basic_package_version_file(
    "${version_config}" COMPATIBILITY SameMajorVersion
)

configure_package_config_file(
    "cmake/Config.cmake.in"
    "${project_config}"
    INSTALL_DESTINATION "${config_install_dir}"
)

install(
    TARGETS magic
    EXPORT "${TARGETS_EXPORT_NAME}"
    LIBRARY DESTINATION "lib"
    ARCHIVE DESTINATION "lib"
    RUNTIME DESTINATION "bin"
    INCLUDES DESTINATION "${include_install_dir}"
)

install(
    FILES
    ./src/magic.h
    DESTINATION "${include_install_dir}/."
)

install(
    FILES "${project_config}" "${version_config}"
    DESTINATION "${config_install_dir}"
)

install(
    EXPORT "${TARGETS_EXPORT_NAME}"
    NAMESPACE "${namespace}"
    DESTINATION "${config_install_dir}"
)



