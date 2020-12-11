# 
# This CMake file is a local alternate to the one
# that comes with SQLiteC++. This build maintains
# consistent run-time libraries.
# 

# ======================================================================
# Set minumum CMake version to be used
#
# Note: As a side effect this statement updates the policies in effect!
# ----------------------------------------------------------------------
cmake_minimum_required (VERSION 3.15)

# Enable use of MSVC_RUNTIME_LIBRARY
cmake_policy(SET CMP0091 NEW)

# ======================================================================
# Define project
# ----------------------------------------------------------------------
project (SQLiteCpp)

# ==================================================
# Set external directories
# --------------------------------------------------
set (sqlitecpp_dir ${PROJECT_SOURCE_DIR})

# ==================================================
# Set header files
# --------------------------------------------------
set (sqlitecpp_hdr
    "${sqlitecpp_dir}/include/SQLiteCpp/Assertion.h"
    "${sqlitecpp_dir}/include/SQLiteCpp/Backup.h"
    "${sqlitecpp_dir}/include/SQLiteCpp/Column.h"
    "${sqlitecpp_dir}/include/SQLiteCpp/Database.h"
    "${sqlitecpp_dir}/include/SQLiteCpp/Exception.h"
    "${sqlitecpp_dir}/include/SQLiteCpp/ExecuteMany.h"
    "${sqlitecpp_dir}/include/SQLiteCpp/SQLiteCpp.h"
    "${sqlitecpp_dir}/include/SQLiteCpp/Statement.h"
    "${sqlitecpp_dir}/include/SQLiteCpp/Transaction.h"
    "${sqlitecpp_dir}/include/SQLiteCpp/Utils.h"
    "${sqlitecpp_dir}/include/SQLiteCpp/VariadicBind.h")

# ==================================================
# Set source files
# --------------------------------------------------
set (sqlitecpp_src
    "${sqlitecpp_dir}/src/Backup.cpp"
    "${sqlitecpp_dir}/src/Column.cpp"
    "${sqlitecpp_dir}/src/Database.cpp"
    "${sqlitecpp_dir}/src/Exception.cpp"
    "${sqlitecpp_dir}/src/Statement.cpp"
    "${sqlitecpp_dir}/src/Transaction.cpp")

# ==================================================
# Set include directories
# --------------------------------------------------
set (sqlitecpp_inc
    "${sqlitecpp_dir}/include/SQLiteCpp"
    "${sqlitecpp_dir}/sqlite3")

include_directories (${sqlitecpp_inc})

# ======================================================================
# Set output directories. Put the libraries and binaries that get built
# into directories at the top of the build tree.
#
# *** Don't move these commands below the add_* commands! ***
# ----------------------------------------------------------------------
set (CMAKE_LIBRARY_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}/bin)
set (CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}/lib)
set (CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}/bin)

# ==================================================
# Add executable and link dependencies
# --------------------------------------------------
if (UNIX)
    set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC")
endif ()

# ======================================================================
# Define (naming of) library versions
# ----------------------------------------------------------------------
set (SQLITECPP_STATIC_LIB SQLiteCpp-static)
set (SQLITECPP_STATIC_DEBUG_LIB SQLiteCpp-static-debug)
set (SQLITECPP_SHARED_LIB SQLiteCpp)
set (SQLITECPP_SHARED_DEBUG_LIB SQLiteCpp-debug)
set (SQLITECPP_TARGETS ${SQLITECPP_STATIC_LIB} ${SQLITECPP_STATIC_DEBUG_LIB}
    ${SQLITECPP_SHARED_LIB} ${SQLITECPP_SHARED_DEBUG_LIB})

# Define sqlite3 library versions to link against
set (sqlite_dir_lib "C:/Tools/sqlite-3.32.3/lib")
set (sqlite3_static_lib "${sqlite_dir_lib}/sqlite3-static.lib")
set (sqlite3_static_debug_lib "${sqlite_dir_lib}/sqlite3-static-debug.lib")
set (sqlite3_shared_lib "${sqlite_dir_lib}/sqlite3.lib")
set (sqlite3_shared_debug_lib "${sqlite_dir_lib}/sqlite3-debug.lib")

# Build static library version
add_library (${SQLITECPP_STATIC_LIB} STATIC)
set_property (TARGET ${SQLITECPP_STATIC_LIB} PROPERTY MSVC_RUNTIME_LIBRARY "MultiThreaded")

# Build static debug library version
add_library (${SQLITECPP_STATIC_DEBUG_LIB} STATIC)
set_property (TARGET ${SQLITECPP_STATIC_DEBUG_LIB} PROPERTY MSVC_RUNTIME_LIBRARY "MultiThreadedDebug")

# Build dynamic library version
add_library (${SQLITECPP_SHARED_LIB} SHARED)
set_property (TARGET ${SQLITECPP_SHARED_LIB} PROPERTY MSVC_RUNTIME_LIBRARY "MultiThreadedDLL")

# Build dynamic debug library version
add_library (${SQLITECPP_SHARED_DEBUG_LIB} SHARED)
set_property (TARGET ${SQLITECPP_SHARED_DEBUG_LIB} PROPERTY MSVC_RUNTIME_LIBRARY "MultiThreadedDebugDLL")

# Set sources for the targets
set_property (TARGET ${SQLITECPP_STATIC_LIB} ${SQLITECPP_STATIC_DEBUG_LIB} ${SQLITECPP_SHARED_LIB} ${SQLITECPP_SHARED_DEBUG_LIB}
    PROPERTY SOURCES ${sqlitecpp_src} ${sqlitecpp_hdr})


foreach (target ${SQLITECPP_TARGETS})
    target_compile_options(${target} PRIVATE $<$<CXX_COMPILER_ID:MSVC>:/nologo>)
    set_target_properties (${target} PROPERTIES LINKER_LANGUAGE CXX)
endforeach ()

# Define libraries to link against
target_link_libraries (${SQLITECPP_SHARED_LIB} ${sqlite3_shared_lib})
target_link_libraries (${SQLITECPP_SHARED_DEBUG_LIB} ${sqlite3_shared_debug_lib})

if (UNIX)
    target_link_libraries (${SQLITECPP_STATIC_LIB} ${sqlite3_static_lib})
    target_link_libraries (${SQLITECPP_STATIC_DEBUG_LIB} ${sqlite3_static_debug_lib})
endif ()
