cmake_minimum_required(VERSION 3.19)

# hack: add .ixx to the set of c++ extensions
# if we don't do that, cmake will exclude ixx from build
# if we also manually set LANGUAGE property of .ixx files, cmake will force compiling them as c++ sources rather than as module interface units (which MSBuild would do by default)
# CMAKE_CXX_SOURCE_FILE_EXTENSIONS variable is set when enabling CXX language by some internal cmake machinery
# this hack immediately adds ixx to the list, before cmake builds extension->language map
function(add_ixx_to_cpp_extensions var acc val clf stack)
	if(${acc} STREQUAL "MODIFIED_ACCESS")
		set(${var} ${val};ixx PARENT_SCOPE)
	endif()
endfunction()
variable_watch(CMAKE_CXX_SOURCE_FILE_EXTENSIONS add_ixx_to_cpp_extensions)

project(test-modules CXX)
add_executable(exec main.cxx a.ixx b.ixx)
target_compile_features(exec PUBLIC cxx_std_20)
#target_compile_options(exec PUBLIC /stdIfcDir [=[ $(VC_IFCPath)]=]) # note: space is important to force cmake to quote string
