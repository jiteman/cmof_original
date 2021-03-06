cmake_minimum_required( VERSION 3.7.1 )

project( cmof_original C CXX )


JMSD_SHOW_PROJECT_HEADER()


include( ${JMSD_COMPONENT_CMAKE_SETTINGS_PATH}/jmsd-testing-cmof-set-compiler-settings.cmake )
set( JMSD_INCLUDED_PROJECT_ROOT ${JMSD_COMPONENT_SOURCE_ROOT_PATH}/cutf )
set( JMSD_THIS_PROJECT_ROOT ${JMSD_COMPONENT_SOURCE_ROOT_PATH}/cmof )

## include dependencies
set( ${PROJECT_NAME}_DEPENDENCY_DIRS_VAR
	${JMSD_PLATFORM_SOURCES}
#	${tpc_google_test_DEPENDENCY_DIRS}
	${JMSD_INCLUDED_PROJECT_ROOT}
	${JMSD_THIS_PROJECT_ROOT} )
list( REMOVE_DUPLICATES ${PROJECT_NAME}_DEPENDENCY_DIRS_VAR )
include_directories( ${${PROJECT_NAME}_DEPENDENCY_DIRS_VAR} )


## this project headers and sources enumeration section
file( GLOB_RECURSE header_and_source_files
	${JMSD_INCLUDED_PROJECT_ROOT}/*.h*
	${JMSD_INCLUDED_PROJECT_ROOT}/*.c*
	${JMSD_THIS_PROJECT_ROOT}/*.h*
	${JMSD_THIS_PROJECT_ROOT}/*.c* )

set( JMSD_THIS_PROJECT_FILES_TO_REMOVE
	"gtest-type-util.h.pump"
	"gmock-generated-actions.h.pump"
	"gmock-generated-function-mockers.h.pump"
	"gmock-generated-matchers.h.pump" )
JMSD_REMOVE_FILES_FROM_THE_LIST( header_and_source_files JMSD_THIS_PROJECT_FILES_TO_REMOVE )

add_library( ${PROJECT_NAME} SHARED ${header_and_source_files} )


## definition section
set( ${PROJECT_NAME}_BUILD_DEFINITIONS_VAR
#	${tpc_google_test_LINK_DEFINITIONS}
	-DGTEST_CREATE_SHARED_LIBRARY )
JMSD_LIST_TO_STRING( ${PROJECT_NAME}_BUILD_DEFINITIONS_VAR )
JMSD_STRING_REMOVE_DUPLICATES( ${PROJECT_NAME}_BUILD_DEFINITIONS_VAR )
set( ${PROJECT_NAME}_LINK_DEFINITIONS_VAR
#	${tpc_google_test_LINK_DEFINITIONS}
	-DGTEST_LINKED_AS_SHARED_LIBRARY )
JMSD_LIST_TO_STRING( ${PROJECT_NAME}_LINK_DEFINITIONS_VAR )
JMSD_STRING_REMOVE_DUPLICATES( ${PROJECT_NAME}_LINK_DEFINITIONS_VAR )
add_definitions( "${${PROJECT_NAME}_BUILD_DEFINITIONS_VAR}" )


## required to be able to be linked into shared libraries.
set_target_properties( ${PROJECT_NAME} PROPERTIES POSITION_INDEPENDENT_CODE ON )
set_target_properties( ${PROJECT_NAME} PROPERTIES VERSION 0.1.0 )
set_target_properties( ${PROJECT_NAME} PROPERTIES SOVERSION 0.1 )


## project target section
if ( UNIX )
	set( ${PROJECT_NAME}_DEPENDENCY_LIBS_VAR
		"pthread" )
else()
	set( ${PROJECT_NAME}_DEPENDENCY_LIBS_VAR
		"" )
endif()
#set( ${PROJECT_NAME}_DEPENDENCY_LIBS_VAR
#	${tpc_google_test_DEPENDENCY_LIBS}
#	tpc_google_test )
#	)
list( REMOVE_DUPLICATES ${PROJECT_NAME}_DEPENDENCY_LIBS_VAR )
target_link_libraries( ${PROJECT_NAME} ${${PROJECT_NAME}_DEPENDENCY_LIBS_VAR} )


## Expose public includes to other subprojects through cache variable.
include( ${JMSD_CMAKE_SETTINGS_PATH}/set-expose-dependencies.cmake )
JMSD_SHOW_PROJECT_FOOTER()
