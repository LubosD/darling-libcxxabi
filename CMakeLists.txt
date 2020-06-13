project(libcxxabi)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -nostdinc -std=c++11")
set(CMAKE_SHARED_LINKER_FLAGS "-nostdlib")

include_directories(BEFORE ${CMAKE_CURRENT_SOURCE_DIR}/../libcxx/include)

add_definitions(-D__DARWIN_NON_CANCELABLE=1 -D_LIBCPP_HAS_NO_LIBRARY_ALIGNED_ALLOCATION=1)

set(cxxabi_sources
	src/abort_message.cpp
	src/cxa_aux_runtime.cpp
	src/cxa_default_handlers.cpp
	src/cxa_demangle.cpp
	src/cxa_exception.cpp
	src/cxa_exception_storage.cpp
	src/cxa_guard.cpp
	src/cxa_handlers.cpp
	#src/cxa_noexception.cpp
	src/cxa_personality.cpp
	src/cxa_thread_atexit.cpp
	src/cxa_unexpected.cpp
	src/cxa_vector.cpp
	src/cxa_virtual.cpp
	src/fallback_malloc.cpp
	src/private_typeinfo.cpp
	src/stdlib_exception.cpp
	src/stdlib_new_delete.cpp
	src/stdlib_stdexcept.cpp
	src/stdlib_typeinfo.cpp
)

add_darling_object_library(cxxabi_obj ${cxxabi_sources})
make_fat(cxxabi_obj)

set(DYLIB_INSTALL_NAME "/usr/lib/libc++abi.dylib")
set(DYLIB_COMPAT_VERSION "1.0.0")
set(DYLIB_CURRENT_VERSION "1.0.0")

add_darling_static_library(cxxabi_static SOURCES $<TARGET_OBJECTS:cxxabi_obj>)
make_fat(cxxabi_static)

add_darling_library(cxxabi SHARED $<TARGET_OBJECTS:cxxabi_obj>)
target_link_libraries(cxxabi system)
set_target_properties(cxxabi PROPERTIES OUTPUT_NAME "c++abi")
#set_property(TARGET cxxabi APPEND_STRING PROPERTY LINK_FLAGS " -Wl,-undefined,dynamic_lookup") # Needed for weakly imported symbols
make_fat(cxxabi)

install(TARGETS cxxabi DESTINATION libexec/darling/usr/lib)

