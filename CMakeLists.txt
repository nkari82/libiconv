##
## CMake support for libiconv
##

cmake_minimum_required(VERSION 3.0.0)
project(Libiconv)

include(CheckFunctionExists)
include(CheckIncludeFiles)
include(CheckTypeSize)
include(CheckSymbolExists)

#
# Options
#
set(BUILD_SHARED_LIBS OFF CACHE BOOL "Build shared libraries")

set(EILSEQ "84")
set(ICONV_CONST "const")
set(HAVE_WCHAR_T "1")
CHECK_FUNCTION_EXISTS(alloca HAVE_ALLOCA)
CHECK_INCLUDE_FILES(alloca.h HAVE_ALLOCA_H)
CHECK_FUNCTION_EXISTS(canonicalize_file_name HAVE_CANONICALIZE_FILE_NAME)
CHECK_FUNCTION_EXISTS(CFLocaleCopyCurrent HAVE_CFLOCALECOPYCURRENT)
CHECK_FUNCTION_EXISTS(CoreFoundation HAVE_CFPREFERENCESCOPYAPPVALUE)
CHECK_FUNCTION_EXISTS(dcgettext HAVE_DCGETTEXT)
CHECK_FUNCTION_EXISTS(canonicalize_file_name HAVE_DECL_CANONICALIZE_FILE_NAME)
CHECK_FUNCTION_EXISTS(clearerr_unlocked HAVE_DECL_CLEARERR_UNLOCKED)
CHECK_FUNCTION_EXISTS(feof_unlocked HAVE_DECL_FEOF_UNLOCKED)
CHECK_FUNCTION_EXISTS(ferror_unlocked HAVE_DECL_FERROR_UNLOCKED)
CHECK_FUNCTION_EXISTS(fflush_unlocked HAVE_DECL_FFLUSH_UNLOCKED)
CHECK_FUNCTION_EXISTS(fgets_unlocked HAVE_DECL_FGETS_UNLOCKED)
CHECK_FUNCTION_EXISTS(fputc_unlocked HAVE_DECL_FPUTC_UNLOCKED)
CHECK_FUNCTION_EXISTS(fputs_unlocked HAVE_DECL_FPUTS_UNLOCKED)
CHECK_FUNCTION_EXISTS(fread_unlocked HAVE_DECL_FREAD_UNLOCKED)
CHECK_FUNCTION_EXISTS(fwrite_unlocked HAVE_DECL_FWRITE_UNLOCKED)
CHECK_FUNCTION_EXISTS(getchar_unlocked HAVE_DECL_GETCHAR_UNLOCKED)
CHECK_FUNCTION_EXISTS(getc_unlocked HAVE_DECL_GETC_UNLOCKED)
CHECK_FUNCTION_EXISTS(putchar_unlocked HAVE_DECL_PUTCHAR_UNLOCKED)
CHECK_FUNCTION_EXISTS(putc_unlocked HAVE_DECL_PUTC_UNLOCKED)
CHECK_FUNCTION_EXISTS(strerror HAVE_DECL_STRERROR)
CHECK_FUNCTION_EXISTS(strerror_r HAVE_DECL_STRERROR_R)
CHECK_INCLUDE_FILES(dlfcn.h HAVE_DLFCN_H)
CHECK_FUNCTION_EXISTS(environ HAVE_ENVIRON_DECL)
CHECK_INCLUDE_FILES(errno.h HAVE_ERRNO_H)
CHECK_FUNCTION_EXISTS(getcwd HAVE_GETCWD)
CHECK_FUNCTION_EXISTS(getc_unlocked HAVE_GETC_UNLOCKED)
CHECK_FUNCTION_EXISTS(gettext HAVE_GETTEXT)
set(HAVE_ICONV ON)
CHECK_INCLUDE_FILES(inttypes.h HAVE_INTTYPES_H)
CHECK_INCLUDE_FILES(langinfo.h HAVE_LANGINFO_CODESET)
CHECK_TYPE_SIZE("long long int" HAVE_LONG_LONG_INT)
CHECK_INCLUDE_FILES(mach-o/dyld.h HAVE_MACH_O_DYLD_H)
IF (WIN32)
UNSET(HAVE_MALLOC_POSIX)
ELSE (WIN32)
SET(HAVE_MALLOC_POSIX ON)
ENDIF (WIN32)
CHECK_FUNCTION_EXISTS(mbrtowc HAVE_MBRTOWC)
CHECK_FUNCTION_EXISTS(mbsinit HAVE_MBSINIT)
CHECK_FUNCTION_EXISTS(memmove HAVE_MEMMOVE)
CHECK_INCLUDE_FILES(memory.h HAVE_MEMORY_H)
CHECK_FUNCTION_EXISTS(pathconf HAVE_PATHCONF)
CHECK_INCLUDE_FILES(random.h HAVE_RANDOM_H)
CHECK_FUNCTION_EXISTS(readlink HAVE_READLINK)
CHECK_INCLUDE_FILES(search.h HAVE_SEARCH_H)
CHECK_FUNCTION_EXISTS(setenv HAVE_SETENV)
CHECK_FUNCTION_EXISTS(setlocale HAVE_SETLOCALE)
CHECK_INCLUDE_FILES(signal.h HAVE_SIGNAL_H)
SET(HAVE_SIGNED_SIG_ATOMIC_T 0)
SET(HAVE_SIGNED_WCHAR_T 0)
SET(HAVE_SIGNED_WINT_T 0)
CHECK_SYMBOL_EXISTS(sigset_t signal.h HAVE_SIGSET_T)
CHECK_INCLUDE_FILES(stdbool.h HAVE_STDBOOL_H)
CHECK_INCLUDE_FILES(stdint.h HAVE_STDINT_H)
CHECK_INCLUDE_FILES(stdio.h HAVE_STDIO_H)
CHECK_INCLUDE_FILES(stdlib.h HAVE_STDLIB_H)
CHECK_INCLUDE_FILES(strerror_r HAVE_STRERROR_R)
CHECK_INCLUDE_FILES(strings.h HAVE_STRINGS_H)
CHECK_INCLUDE_FILES(string.h HAVE_STRING_H)
UNSET(HAVE_STRUCT_RANDOM_DATA)
CHECK_INCLUDE_FILES(sys/bitypes.h HAVE_SYS_BITYPES_H)
CHECK_INCLUDE_FILES(sys/inttypes.h HAVE_SYS_INTTYPES_H)
CHECK_INCLUDE_FILES(sys/param.h HAVE_SYS_PARAM_H)
CHECK_INCLUDE_FILES(sys/socket.h HAVE_SYS_SOCKET_H)
CHECK_INCLUDE_FILES(sys/stat.h HAVE_SYS_STAT_H)
CHECK_INCLUDE_FILES(sys/types.h HAVE_SYS_TYPES_H)
CHECK_FUNCTION_EXISTS(tsearch HAVE_TSEARCH)
CHECK_INCLUDE_FILES(unistd.h HAVE_UNISTD_H)
CHECK_TYPE_SIZE("unsigned long long int" HAVE_UNSIGNED_LONG_LONG_INT)
SET(HAVE_VISIBILITY 0)
CHECK_INCLUDE_FILES(wchar.h HAVE_WCHAR_H)
CHECK_SYMBOL_EXISTS(wchar_t wchar.h HAVE_WCHAR_T)
CHECK_FUNCTION_EXISTS(wcrtomb HAVE_WCRTOMB)
IF (WIN32)
SET(HAVE_WINSOCK2_H ON)
ELSE (WIN32)
UNSET(HAVE_WINSOCK2_H)
ENDIF (WIN32)
CHECK_TYPE_SIZE("wint_t" HAVE_WINT_T)
CHECK_FUNCTION_EXISTS(_NSGetExecutablePath HAVE__NSGETEXECUTABLEPATH)

# Config file
configure_file(
  ${Libiconv_SOURCE_DIR}/config.h.cmake
  ${Libiconv_BINARY_DIR}/config.h
)

include_directories(${Libiconv_SOURCE_DIR} ${Libiconv_BINARY_DIR})

macro(header_injection_hack)
  include(CMakeParseArguments)
  set(options)
  set(oneValueArgs LIBRARY_NAME INPUT OUTPUT)
  set(multiValueArgs)
  cmake_parse_arguments(hack
    "${options}" "${oneValueArgs}" "${multiValueArgs}" ${ARGN}
  )
  string(TOUPPER ${hack_LIBRARY_NAME} hack_library_name_upper)
  set(HAVE_VISIBILITY
  "WIN32
	#if !defined(LIBICONV_STATIC)
		#if defined(${hack_LIBRARY_NAME}_EXPORTS)
			#define ${hack_library_name_upper}_DLL_EXPORTED __declspec(dllexport)
		#else
			#define ${hack_library_name_upper}_DLL_EXPORTED __declspec(dllimport)
		#endif
	#else
		#define ${hack_library_name_upper}_DLL_EXPORTED
	#endif
#elif true")
  configure_file(
    ${hack_INPUT}
    ${hack_OUTPUT}
    )
endmacro()
#
# Build libcharset
#
set(libname libcharset)
set(libcharset_source_dir ${Libiconv_SOURCE_DIR}/libcharset)

if(WIN32)
  add_definitions(-DLIBDIR)
endif()

# Hack: to be able to configure the localcharset.h.build.in correctly, we
# inject code in the variable have visibility
header_injection_hack(
  LIBRARY_NAME ${libname}
  INPUT ${Libiconv_SOURCE_DIR}/libcharset/include/localcharset.h.build.in
  OUTPUT ${Libiconv_BINARY_DIR}/localcharset.h
  )

set(${libname}_sources
  ${Libiconv_SOURCE_DIR}/srclib/relocatable.c
  ${Libiconv_SOURCE_DIR}/srclib/relocatable.h
  ${libcharset_source_dir}/lib/localcharset.c
  ${Libiconv_BINARY_DIR}/localcharset.h
)

if(BUILD_SHARED_LIBS)
add_library(${libname} SHARED ${${libname}_sources})
else()
add_definitions(-DLIBICONV_STATIC)
add_library(${libname} STATIC ${${libname}_sources})
endif()
list(APPEND Libiconv_TARGETS ${libname})
list(APPEND Libiconv_headers ${Libiconv_BINARY_DIR}/localcharset.h)

#
# Build libiconv
#
set(libname libiconv)
set(USE_MBSTATE_T "USE_MBSTATE_T")
set(BROKEN_WCHAR_H "BROKEN_WCHAR_H")
header_injection_hack(
  LIBRARY_NAME ${libname}
  INPUT ${Libiconv_SOURCE_DIR}/include/iconv.h.build.in
  OUTPUT ${Libiconv_BINARY_DIR}/iconv.h
  )

list(APPEND simple_header_config
  uniwidth
  unitypes
  )
foreach(header ${simple_header_config})
  configure_file(
    ${Libiconv_SOURCE_DIR}/srclib/${header}.in.h
    ${Libiconv_BINARY_DIR}/${header}.h
    )
endforeach()

set(${libname}_sources
  ${Libiconv_SOURCE_DIR}/lib/iconv.c
  ${Libiconv_BINARY_DIR}/iconv.h
)

if(BUILD_SHARED_LIBS)
add_library(${libname} SHARED ${${libname}_sources})
else()
add_definitions(-DLIBICONV_STATIC)
add_library(${libname} STATIC ${${libname}_sources})
endif()
target_link_libraries(${libname} libcharset)
list(APPEND Libiconv_TARGETS ${libname})
list(APPEND Libiconv_headers ${Libiconv_BINARY_DIR}/iconv.h)

#
# Export targets
#
install(TARGETS ${Libiconv_TARGETS}
  RUNTIME DESTINATION bin
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
)

install(FILES ${Libiconv_headers} DESTINATION include)

set(Libiconv_TARGETS_FILE ${Libiconv_BINARY_DIR}/LibiconvTargets.cmake)
export(TARGETS ${Libiconv_TARGETS} FILE ${Libiconv_TARGETS_FILE})

configure_file(
  ${Libiconv_SOURCE_DIR}/LibiconvConfig.cmake.in
  ${Libiconv_BINARY_DIR}/LibiconvConfig.cmake
  @ONLY
)
