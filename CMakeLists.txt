#Root CMakeLists.txt

cmake_minimum_required(VERSION 3.14)
project(ZipArchive
        VERSION 2.6.0
        LANGUAGES C OBJC
)

add_compile_definitions(ZLIB_COMPAT)
add_compile_definitions(HAVE_WZAES)

add_library(${PROJECT_NAME}
            SSZipArchive/SSZipArchive.m
            SSZipArchive/SSZipArchive.h
            SSZipArchive/SSZipCommon.h
            SSZipArchive/include/ZipArchive.h

            SSZipArchive/minizip/compat/ioapi.c
            SSZipArchive/minizip/compat/ioapi.h
            SSZipArchive/minizip/compat/unzip.c
            SSZipArchive/minizip/compat/unzip.h
            SSZipArchive/minizip/compat/zip.c
            SSZipArchive/minizip/compat/zip.h

            SSZipArchive/minizip/mz_crypt_apple.c
            SSZipArchive/minizip/mz_crypt.c
            SSZipArchive/minizip/mz_crypt.h
            SSZipArchive/minizip/mz_os_posix.c
            SSZipArchive/minizip/mz_os.c
            SSZipArchive/minizip/mz_os.h
            SSZipArchive/minizip/mz_strm_buf.c
            SSZipArchive/minizip/mz_strm_buf.h
            SSZipArchive/minizip/mz_strm_mem.c
            SSZipArchive/minizip/mz_strm_mem.h
            SSZipArchive/minizip/mz_strm_os_posix.c
            SSZipArchive/minizip/mz_strm_os.h
            SSZipArchive/minizip/mz_strm_pkcrypt.c
            SSZipArchive/minizip/mz_strm_pkcrypt.h
            SSZipArchive/minizip/mz_strm_split.c
            SSZipArchive/minizip/mz_strm_split.h
            SSZipArchive/minizip/mz_strm_wzaes.c
            SSZipArchive/minizip/mz_strm_wzaes.h
            SSZipArchive/minizip/mz_strm_zlib.c
            SSZipArchive/minizip/mz_strm_zlib.h
            SSZipArchive/minizip/mz_strm.c
            SSZipArchive/minizip/mz_strm.h
            SSZipArchive/minizip/mz_zip_rw.c
            SSZipArchive/minizip/mz_zip_rw.h
            SSZipArchive/minizip/mz_zip.c
            SSZipArchive/minizip/mz_zip.h
            SSZipArchive/minizip/mz.h
)

target_link_libraries(${PROJECT_NAME} PRIVATE 
                        ${FOUNDATION_LIBRARY})

target_include_directories(${PROJECT_NAME}
    PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/SSZipArchive/include>
        $<INSTALL_INTERFACE:include>
    PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}/SSZipArchive/minizip
)

# Install rules
include(GNUInstallDirs)
install(TARGETS ${PROJECT_NAME}
    EXPORT ${PROJECT_NAME}Targets
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
    INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)

# Install headers
install(DIRECTORY SSZipArchive/include/
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)

# Export targets
install(EXPORT ${PROJECT_NAME}Targets
    FILE ${PROJECT_NAME}Targets.cmake
    NAMESPACE ${PROJECT_NAME}::
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME}
)

# Create and install package config
include(CMakePackageConfigHelpers)
write_basic_package_version_file(
    "${PROJECT_NAME}ConfigVersion.cmake"
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY SameMajorVersion
)

configure_package_config_file(
    "${CMAKE_CURRENT_SOURCE_DIR}/cmake/${PROJECT_NAME}Config.cmake.in"
    "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake"
    INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME}
)

install(FILES
    "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake"
    "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}ConfigVersion.cmake"
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME}
)

# Export from build tree
export(EXPORT ${PROJECT_NAME}Targets
    FILE "${CMAKE_CURRENT_BINARY_DIR}/cmake/${PROJECT_NAME}Targets.cmake"
    NAMESPACE ${PROJECT_NAME}::
)
