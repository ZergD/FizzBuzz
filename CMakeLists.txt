cmake_minimum_required(VERSION 3.5)
project(Fizzbuzz LANGUAGES CXX)

# Add local project module path so CMake includes custom CMake modules.
list(APPEND CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/cmake/Modules")
#
# include(CTest)
# finds boost, triggers an error otherwise
find_package(Boost 1.65 REQUIRED COMPONENTS unit_test_framework)
enable_testing()

message("** BOOST_PATH : ${BOOST_PATH}")

# if (NOT DEFINED BOOST_INCLUDE_DIRS)
#     message(FATAL_ERROR "BOOST_INCLUDE_DIRS unset. Please set Boost_ROOT_DIR to the location of boost sources")
# else()
#     message(STATUS "Compiling boost from ${BOOST_INCLUDE_DIRS}.")
# endif()

# set(BOOST_INCLUDE_DIRS $boost_installation_prefix/include)

add_library(fizzBuzz
	src/fizzBuzzLib/include/fizzBuzz.h
	src/fizzBuzzLib/src/fizzBuzz.cpp
)
target_include_directories(fizzBuzz
	PUBLIC
		src/fizzBuzzLib/include/
		src/fizzBuzzLib/src/
)
target_compile_options(fizzBuzz
	PRIVATE
		$<$<OR:$<CXX_COMPILER_ID:Clang>,$<CXX_COMPILER_ID:AppleClang>,$<CXX_COMPILER_ID:GNU>>:
			-Werror -Wall -Wextra>
		$<$<CXX_COMPILER_ID:MSVC>:
			/W4>
)

# Added library counter
add_library(fizzBuzzProxy
	src/fizzBuzzLib/include/fizzBuzzProxy.h
	src/fizzBuzzLib/src/fizzBuzzProxy.cpp
)
target_include_directories(fizzBuzzProxy
	PUBLIC
		src/fizzBuzzLib/include/
		src/fizzBuzzLib/src/
)
target_compile_options(fizzBuzzProxy
	PRIVATE
		$<$<OR:$<CXX_COMPILER_ID:Clang>,$<CXX_COMPILER_ID:AppleClang>,$<CXX_COMPILER_ID:GNU>>:
			-Werror -Wall -Wextra>
		$<$<CXX_COMPILER_ID:MSVC>:
			/W4>
)


#
# Create executable that uses the hello lib to say Hello.
#
add_executable(say_hello_to_charly
	src/main.cpp
)
target_link_libraries(say_hello_to_charly
	PRIVATE
		fizzBuzz
		fizzBuzzProxy
)

target_compile_options(say_hello_to_charly
	PRIVATE
		$<$<OR:$<CXX_COMPILER_ID:Clang>,$<CXX_COMPILER_ID:AppleClang>,$<CXX_COMPILER_ID:GNU>>:
			-Werror -Wall -Wextra>
		$<$<CXX_COMPILER_ID:MSVC>:
			/W4>
)

#
# Create a target for executing the Boost unit tests.
#
 add_executable(unit_tests
 	tests/fizzBuzzTests.cpp
 )
 target_include_directories(unit_tests
 	PUBLIC
		src/fizzBuzzLib/include/
 		tests/
    PRIVATE
        ${Boost_INCLUDE_DIRS}
 )
target_compile_definitions(unit_tests
    PRIVATE
        "BOOST_TEST_DYN_LINK=1"
)
# indicates the link paths
 target_link_libraries(unit_tests
	fizzBuzz
	fizzBuzzProxy
	${Boost_UNIT_TEST_FRAMEWORK_LIBRARY}
 )
 target_compile_options(unit_tests
 	PRIVATE
 		$<$<OR:$<CXX_COMPILER_ID:Clang>,$<CXX_COMPILER_ID:AppleClang>,$<CXX_COMPILER_ID:GNU>>:
 			-Werror -Wall -Wextra>
 		$<$<CXX_COMPILER_ID:MSVC>:
 			/W4>
 )

add_test(NAME test1 COMMAND unit_tests)

