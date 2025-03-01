cmake_minimum_required(VERSION 3.16)
cmake_policy(VERSION 3.16)

# Enable policy to not use RPATH settings for install_name on macOS.
if(POLICY CMP0068)
  cmake_policy(SET CMP0068 NEW)
endif()

# Instruct CMake to run moc automatically when needed.
set(CMAKE_AUTOMOC ON)

# Consider changing the project name to something relevant for you.
project(LibQuotientBindings)

option(USE_POETRY "Use poetry package manager" ON)
option(USE_PREBUILD_LIBQUOTIENT "Use prebuilt quotient" OFF)
SET(QT_PATH "" CACHE STRING "Path to qt directory")
SET(PYTHON_ENV_VERSION "3.9" CACHE STRING "Python env version(e.g. 3.9)")
set(CMAKE_PREFIX_PATH ${CMAKE_PREFIX_PATH} ${QT_PATH}/lib/cmake/Qt6 ${QT_PATH}/lib/cmake/)

# ================================ General configuration ======================================

# Set CPP standard to C++11 minimum.
set(CMAKE_CXX_STANDARD 11)

# The name of the generated bindings module (as imported in Python)
set(bindings_library "PyQuotient")

# library for which we will create bindings(its name is libQuotient, but cmake project name is 'Quotient')
set(target_library "Quotient")
if(USE_PREBUILD_LIBQUOTIENT)
    add_library(Quotient STATIC IMPORTED GLOBAL)
    SET_TARGET_PROPERTIES(Quotient PROPERTIES IMPORTED_LOCATION ${CMAKE_SOURCE_DIR}/${bindings_library}/libQuotient${CMAKE_STATIC_LIBRARY_SUFFIX})
else()
    add_subdirectory(${bindings_library}/libQuotient)
endif()

# The header file with all the types and functions for which bindings will be generated.
# Usually it simply includes other headers of the library you are creating bindings for.
set(wrapped_header ${CMAKE_SOURCE_DIR}/${bindings_library}/bindings.h)

# The typesystem xml file which defines the relationships between the C++ types / functions
# and the corresponding Python equivalents.
set(typesystem_file ${CMAKE_SOURCE_DIR}/${bindings_library}/typesystems/typesystem_index.xml)

# C++ files that will be generated by shiboken. This includes the module wrapper
# and a '.cpp' file per C++ type. These are needed for generating the module shared
# library.
# generated_wrappers is autogenerated, do not change it manually
# generated wrappers
set(generated_wrappers
quotient_searchjob_groupvalue_wrapper.h
quotient_setpresencejob_wrapper.cpp
quotient_thirdpartylocation_wrapper.cpp
quotient_crosssigningkey_wrapper.h
quotient_searchjob_roomeventscriteria_wrapper.h
quotient_geteventcontextjob_wrapper.cpp
quotient_getjoinedroomsjob_wrapper.h
quotient_requesttokentoresetpasswordemailjob_wrapper.cpp
quotient_geteventcontextjob_wrapper.h
quotient_getcontentoverridenamejob_wrapper.cpp
quotient_getoneroomeventjob_wrapper.cpp
quotient_getpushruleactionsjob_wrapper.h
quotient_getwhoisjob_connectioninfo_wrapper.cpp
quotient_getprotocolsjob_wrapper.cpp
quotient_getcapabilitiesjob_roomversionscapability_wrapper.h
quotient_getroomstatejob_wrapper.cpp
quotient_getversionsjob_wrapper.h
quotient_getroomstatejob_wrapper.h
quotient_getpresencejob_wrapper.cpp
quotient_requestdata_wrapper.h
quotient_postpusherjob_wrapper.h
quotient_add3pidjob_wrapper.cpp
quotient_setpushruleenabledjob_wrapper.cpp
quotient_thirdpartyprotocol_wrapper.cpp
quotient_sendtodevicejob_wrapper.h
quotient_setroomtagjob_wrapper.h
quotient_deletedevicesjob_wrapper.cpp
quotient_changepasswordjob_wrapper.cpp
quotient_post3pidsjob_wrapper.h
quotient_deletepushrulejob_wrapper.cpp
quotient_setroomstatewithkeyjob_wrapper.h
quotient_knockroomjob_wrapper.cpp
quotient_getroomstatewithkeyjob_wrapper.cpp
quotient_getuserprofilejob_wrapper.h
quotient_sendtodevicejob_wrapper.cpp
quotient_changepasswordjob_wrapper.h
quotient_getkeyschangesjob_wrapper.cpp
quotient_inviteuserjob_wrapper.h
quotient_getcapabilitiesjob_wrapper.cpp
quotient_redacteventjob_wrapper.cpp
quotient_querykeysjob_unsigneddeviceinfo_wrapper.h
quotient_ispushruleenabledjob_wrapper.h
quotient_redirecttoidpjob_wrapper.cpp
pyquotient_module_wrapper.cpp
quotient_getprotocolmetadatajob_wrapper.h
quotient_requesttokentoresetpasswordemailjob_wrapper.h
quotient_getroomeventsjob_wrapper.cpp
quotient_getversionsjob_wrapper.cpp
quotient_querypublicroomsjob_wrapper.cpp
quotient_querykeysjob_deviceinformation_wrapper.cpp
quotient_bind3pidjob_wrapper.cpp
quotient_pushruleset_wrapper.cpp
quotient_user_wrapper.cpp
quotient_searchjob_groupings_wrapper.h
quotient_requestopenidtokenjob_wrapper.h
quotient_searchuserdirectoryjob_wrapper.h
quotient_getpushersjob_pusherdata_wrapper.cpp
quotient_getdisplaynamejob_wrapper.cpp
quotient_setaccountdatajob_wrapper.h
quotient_getpushrulesjob_wrapper.cpp
quotient_connection_wrapper.cpp
quotient_postpusherjob_pusherdata_wrapper.cpp
quotient_getcapabilitiesjob_changepasswordcapability_wrapper.cpp
quotient_postpusherjob_wrapper.cpp
quotient_searchjob_wrapper.cpp
quotient_getaccountdatajob_wrapper.h
quotient_getavatarurljob_wrapper.cpp
quotient_setaccountdatajob_wrapper.cpp
quotient_requesttokento3pidemailjob_wrapper.cpp
quotient_getoneroomeventjob_wrapper.h
quotient_updatedevicejob_wrapper.cpp
quotient_getaccountdataperroomjob_wrapper.h
quotient_basejob_wrapper.h
quotient_publicroomschunk_wrapper.cpp
quotient_pushruleset_wrapper.h
quotient_uploadcontentjob_wrapper.cpp
quotient_createroomjob_stateevent_wrapper.cpp
quotient_getconfigjob_wrapper.cpp
quotient_updateappserviceroomdirectoryvisibilityjob_wrapper.cpp
quotient_getjoinedmembersbyroomjob_wrapper.cpp
quotient_joinroomjob_wrapper.cpp
quotient_sendmessagejob_wrapper.h
quotient_device_wrapper.h
quotient_filetransferinfo_wrapper.cpp
quotient_post3pidsjob_threepidcredentials_wrapper.h
quotient_unbind3pidfromaccountjob_wrapper.cpp
quotient_ispushruleenabledjob_wrapper.cpp
quotient_getcapabilitiesjob_wrapper.h
quotient_unbanjob_wrapper.cpp
quotient_requesttokentoregistermsisdnjob_wrapper.h
quotient_uploadcrosssigningkeysjob_wrapper.h
quotient_reportcontentjob_wrapper.cpp
quotient_getpushrulejob_wrapper.h
quotient_setpushruleactionsjob_wrapper.cpp
quotient_gettokenownerjob_wrapper.h
quotient_deactivateaccountjob_wrapper.cpp
quotient_getcontentoverridenamejob_wrapper.h
quotient_querykeysjob_wrapper.cpp
quotient_setpushrulejob_wrapper.cpp
quotient_getloginflowsjob_loginflow_wrapper.h
quotient_requesttokento3pidemailjob_wrapper.h
quotient_getroomtagsjob_tag_wrapper.h
quotient_definefilterjob_wrapper.h
quotient_getpublicroomsjob_wrapper.h
quotient_querypublicroomsjob_wrapper.h
quotient_getpushersjob_wrapper.cpp
quotient_setroomstatewithkeyjob_wrapper.cpp
quotient_getloginflowsjob_wrapper.h
quotient_logoutjob_wrapper.cpp
quotient_postreceiptjob_wrapper.h
quotient_settypingjob_wrapper.cpp
quotient_homeserverinformation_wrapper.cpp
quotient_sendmessagejob_wrapper.cpp
quotient_searchjob_categories_wrapper.cpp
quotient_getjoinedmembersbyroomjob_wrapper.h
quotient_setpresencejob_wrapper.h
quotient_discoveryinformation_wrapper.h
quotient_pushcondition_wrapper.cpp
quotient_setaccountdataperroomjob_wrapper.cpp
quotient_getuserprofilejob_wrapper.cpp
quotient_gettokenownerjob_wrapper.cpp
quotient_getpublicroomsjob_wrapper.cpp
quotient_geturlpreviewjob_wrapper.h
quotient_getpushersjob_pusherdata_wrapper.h
quotient_getpushruleactionsjob_wrapper.cpp
quotient_querykeysjob_unsigneddeviceinfo_wrapper.cpp
quotient_getroomtagsjob_wrapper.cpp
quotient_getmembersbyroomjob_wrapper.h
quotient_getconfigjob_wrapper.h
quotient_getwhoisjob_connectioninfo_wrapper.h
quotient_getpushersjob_wrapper.h
quotient_getturnserverjob_wrapper.cpp
quotient_setreadmarkerjob_wrapper.cpp
quotient_openidtoken_wrapper.h
quotient_useridentifier_wrapper.h
quotient_room_wrapper.h
quotient_fieldtype_wrapper.cpp
quotient_basejob_status_wrapper.cpp
quotient_searchjob_groupvalue_wrapper.cpp
quotient_postreceiptjob_wrapper.cpp
quotient_getcontentjob_wrapper.h
quotient_deleteroomtagjob_wrapper.cpp
quotient_ssosession_wrapper.cpp
quotient_thirdpartyprotocol_wrapper.h
quotient_roomfilter_wrapper.h
quotient_delete3pidfromaccountjob_wrapper.cpp
quotient_queryuserbyprotocoljob_wrapper.h
quotient_setpushruleenabledjob_wrapper.h
quotient_searchjob_categories_wrapper.h
quotient_requestopenidtokenjob_wrapper.cpp
quotient_settings_wrapper.cpp
quotient_searchjob_includeeventcontext_wrapper.h
quotient_filetransferinfo_wrapper.h
quotient_getjoinedmembersbyroomjob_roommember_wrapper.cpp
quotient_thirdpartysigned_wrapper.cpp
quotient_connectiondata_wrapper.cpp
quotient_pushrule_wrapper.cpp
quotient_requesttokentoregisteremailjob_wrapper.h
quotient_user_wrapper.h
quotient_delete3pidfromaccountjob_wrapper.h
quotient_joinroombyidjob_wrapper.h
quotient_thirdpartylocation_wrapper.h
quotient_authenticationdata_wrapper.cpp
quotient_createroomjob_invite3pid_wrapper.h
quotient_getpushersjob_pusher_wrapper.cpp
quotient_getaccount3pidsjob_thirdpartyidentifier_wrapper.h
quotient_setreadmarkerjob_wrapper.h
quotient_kickjob_wrapper.cpp
quotient_requesttokenresponse_wrapper.cpp
quotient_getjoinedroomsjob_wrapper.cpp
quotient_getwhoisjob_deviceinfo_wrapper.cpp
quotient_getroomidbyaliasjob_wrapper.h
quotient_queryuserbyidjob_wrapper.cpp
quotient_emailvalidationdata_wrapper.h
quotient_getpushersjob_pusher_wrapper.h
quotient_getaccount3pidsjob_wrapper.h
quotient_querylocationbyprotocoljob_wrapper.cpp
quotient_add3pidjob_wrapper.h
quotient_setroomvisibilityondirectoryjob_wrapper.h
quotient_identityserverinformation_wrapper.h
quotient_filter_wrapper.cpp
quotient_getcontentjob_wrapper.cpp
quotient_setroomvisibilityondirectoryjob_wrapper.cpp
quotient_querykeysjob_wrapper.h
quotient_getroomidbyaliasjob_wrapper.cpp
quotient_postpusherjob_pusherdata_wrapper.h
quotient_msisdnvalidationdata_wrapper.h
quotient_getcontentthumbnailjob_wrapper.cpp
quotient_deleteroomtagjob_wrapper.h
quotient_querylocationbyaliasjob_wrapper.h
quotient_banjob_wrapper.cpp
quotient_setaccountdataperroomjob_wrapper.h
quotient_settings_wrapper.h
quotient_searchjob_wrapper.h
quotient_setpushrulejob_wrapper.h
quotient_getroomvisibilityondirectoryjob_wrapper.h
quotient_requesttokento3pidmsisdnjob_wrapper.cpp
quotient_getroomtagsjob_wrapper.h
quotient_reportcontentjob_wrapper.h
quotient_searchjob_groupings_wrapper.cpp
quotient_protocolinstance_wrapper.cpp
quotient_room_wrapper.cpp
quotient_loginjob_wrapper.h
quotient_publicroomsresponse_wrapper.h
quotient_getroomstatewithkeyjob_wrapper.h
quotient_thirdpartyuser_wrapper.cpp
quotient_unbanjob_wrapper.h
quotient_post3pidsjob_wrapper.cpp
quotient_getaccountdataperroomjob_wrapper.cpp
quotient_ssosession_wrapper.h
quotient_discoveryinformation_wrapper.cpp
quotient_fieldtype_wrapper.h
quotient_getfilterjob_wrapper.cpp
quotient_getmembersbyroomjob_wrapper.cpp
quotient_getcapabilitiesjob_roomversionscapability_wrapper.cpp
quotient_filter_wrapper.h
quotient_loginjob_wrapper.cpp
quotient_connectiondata_wrapper.h
quotient_getaccount3pidsjob_wrapper.cpp
quotient_setroomtagjob_wrapper.cpp
quotient_redacteventjob_wrapper.h
quotient_getroomeventsjob_wrapper.h
quotient_devicekeys_wrapper.cpp
quotient_searchuserdirectoryjob_wrapper.cpp
quotient_getjoinedmembersbyroomjob_roommember_wrapper.h
quotient_knockroomjob_wrapper.h
quotient_updatedevicejob_wrapper.h
quotient_devicekeys_wrapper.h
quotient_setroomaliasjob_wrapper.h
quotient_device_wrapper.cpp
quotient_publicroomschunk_wrapper.h
quotient_querypublicroomsjob_filter_wrapper.h
quotient_geturlpreviewjob_wrapper.cpp
quotient_forgetroomjob_wrapper.h
quotient_requesttokentoresetpasswordmsisdnjob_wrapper.cpp
quotient_deletedevicejob_wrapper.cpp
quotient_getwhoisjob_sessioninfo_wrapper.cpp
quotient_peekeventsjob_wrapper.cpp
quotient_checkusernameavailabilityjob_wrapper.h
quotient_queryuserbyidjob_wrapper.h
quotient_pushrule_wrapper.h
quotient_inviteby3pidjob_wrapper.cpp
quotient_registerjob_wrapper.h
quotient_getnotificationsjob_wrapper.cpp
quotient_getpushrulejob_wrapper.cpp
quotient_checkusernameavailabilityjob_wrapper.cpp
quotient_searchjob_includeeventcontext_wrapper.cpp
quotient_getpresencejob_wrapper.h
quotient_homeserverinformation_wrapper.h
quotient_post3pidsjob_threepidcredentials_wrapper.cpp
quotient_uploadkeysjob_wrapper.cpp
quotient_requesttokentoregisteremailjob_wrapper.cpp
quotient_connection_wrapper.h
quotient_setpushruleactionsjob_wrapper.h
quotient_unbind3pidfromaccountjob_wrapper.h
quotient_forgetroomjob_wrapper.cpp
quotient_getdevicesjob_wrapper.cpp
quotient_updateappserviceroomdirectoryvisibilityjob_wrapper.h
quotient_requestdata_wrapper.cpp
quotient_eventfilter_wrapper.cpp
quotient_inviteuserjob_wrapper.cpp
quotient_leaveroomjob_wrapper.h
quotient_getdisplaynamejob_wrapper.h
quotient_createroomjob_wrapper.h
quotient_settypingjob_wrapper.h
quotient_authenticationdata_wrapper.h
quotient_getfilterjob_wrapper.h
quotient_logoutalljob_wrapper.cpp
quotient_wrapper.cpp
quotient_upgraderoomjob_wrapper.cpp
quotient_getwellknownjob_wrapper.cpp
quotient_roomeventfilter_wrapper.h
quotient_identityserverinformation_wrapper.cpp
quotient_requesttokentoresetpasswordmsisdnjob_wrapper.h
quotient_queryuserbyprotocoljob_wrapper.cpp
quotient_publicroomsresponse_wrapper.cpp
quotient_getloginflowsjob_wrapper.cpp
quotient_openidtoken_wrapper.cpp
quotient_networkaccessmanager_wrapper.h
quotient_getdevicesjob_wrapper.h
quotient_querypublicroomsjob_filter_wrapper.cpp
quotient_getdevicejob_wrapper.h
quotient_requesttokentoregistermsisdnjob_wrapper.cpp
quotient_setavatarurljob_wrapper.h
quotient_createroomjob_invite3pid_wrapper.cpp
quotient_getprotocolsjob_wrapper.h
quotient_registerjob_wrapper.cpp
quotient_thirdpartyuser_wrapper.h
quotient_setdisplaynamejob_wrapper.h
quotient_uploadcrosssigningsignaturesjob_wrapper.cpp
quotient_redirecttoidpjob_wrapper.h
quotient_joinroombyidjob_wrapper.cpp
quotient_getlocalaliasesjob_wrapper.h
quotient_getwellknownjob_wrapper.h
quotient_getnotificationsjob_wrapper.h
quotient_inviteby3pidjob_wrapper.h
quotient_thirdpartysigned_wrapper.h
quotient_banjob_wrapper.h
quotient_getprotocolmetadatajob_wrapper.cpp
quotient_basejob_status_wrapper.h
quotient_emailvalidationdata_wrapper.cpp
quotient_querylocationbyaliasjob_wrapper.cpp
quotient_uploadcontentjob_wrapper.h
quotient_getavatarurljob_wrapper.h
quotient_redirecttossojob_wrapper.h
quotient_searchjob_userprofile_wrapper.cpp
quotient_wrapper.h
quotient_getaccount3pidsjob_thirdpartyidentifier_wrapper.cpp
quotient_getroomvisibilityondirectoryjob_wrapper.cpp
quotient_getwhoisjob_wrapper.h
quotient_basejob_wrapper.cpp
quotient_pushcondition_wrapper.h
quotient_getkeyschangesjob_wrapper.h
quotient_requesttokenresponse_wrapper.h
quotient_leaveroomjob_wrapper.cpp
quotient_querylocationbyprotocoljob_wrapper.h
quotient_definefilterjob_wrapper.cpp
quotient_roomfilter_wrapper.cpp
quotient_searchuserdirectoryjob_user_wrapper.cpp
quotient_searchjob_group_wrapper.h
quotient_deletedevicejob_wrapper.h
quotient_deleteroomaliasjob_wrapper.cpp
quotient_createroomjob_wrapper.cpp
quotient_getlocalaliasesjob_wrapper.cpp
quotient_getwhoisjob_deviceinfo_wrapper.h
quotient_getdevicejob_wrapper.cpp
quotient_logoutalljob_wrapper.h
quotient_getroomtagsjob_tag_wrapper.cpp
quotient_setavatarurljob_wrapper.cpp
quotient_deleteroomaliasjob_wrapper.h
quotient_eventfilter_wrapper.h
quotient_deletepushrulejob_wrapper.h
quotient_getturnserverjob_wrapper.h
quotient_uploadcrosssigningsignaturesjob_wrapper.h
quotient_searchjob_roomeventscriteria_wrapper.cpp
quotient_getcapabilitiesjob_capabilities_wrapper.h
quotient_crosssigningkey_wrapper.cpp
quotient_getwhoisjob_sessioninfo_wrapper.h
quotient_getaccountdatajob_wrapper.cpp
quotient_searchjob_group_wrapper.cpp
quotient_claimkeysjob_wrapper.h
quotient_createroomjob_stateevent_wrapper.h
quotient_getpushrulesjob_wrapper.h
quotient_deactivateaccountjob_wrapper.h
quotient_protocolinstance_wrapper.h
quotient_uploadkeysjob_wrapper.h
quotient_msisdnvalidationdata_wrapper.cpp
pyquotient_python.h
quotient_kickjob_wrapper.h
quotient_searchjob_userprofile_wrapper.h
quotient_getwhoisjob_wrapper.cpp
quotient_peekeventsjob_wrapper.h
quotient_getcapabilitiesjob_capabilities_wrapper.cpp
quotient_useridentifier_wrapper.cpp
quotient_claimkeysjob_wrapper.cpp
quotient_uploadcrosssigningkeysjob_wrapper.cpp
quotient_searchuserdirectoryjob_user_wrapper.h
quotient_setroomaliasjob_wrapper.cpp
quotient_getcontentthumbnailjob_wrapper.h
quotient_bind3pidjob_wrapper.h
quotient_getloginflowsjob_loginflow_wrapper.cpp
quotient_roomeventfilter_wrapper.cpp
quotient_deletedevicesjob_wrapper.h
quotient_redirecttossojob_wrapper.cpp
quotient_getcapabilitiesjob_changepasswordcapability_wrapper.h
quotient_logoutjob_wrapper.h
quotient_setdisplaynamejob_wrapper.cpp
quotient_networkaccessmanager_wrapper.cpp
quotient_joinroomjob_wrapper.h
quotient_querykeysjob_deviceinformation_wrapper.h
quotient_requesttokento3pidmsisdnjob_wrapper.h
quotient_upgraderoomjob_wrapper.h
quotient_stateeventbase_wrapper.cpp
quotient_stateeventbase_wrapper.h
quotient_event_wrapper.cpp
quotient_event_wrapper.h
quotient_roomevent_wrapper.cpp
quotient_roomevent_wrapper.h
quotient_roomeventptr_wrapper.cpp
quotient_roomeventptr_wrapper.h
quotient_eventstatus_wrapper.cpp
quotient_eventstatus_wrapper.h
quotient_eventitembase_wrapper.cpp
quotient_eventitembase_wrapper.h
quotient_timelineitem_wrapper.cpp
quotient_timelineitem_wrapper.h
quotient_pendingeventitem_wrapper.cpp
quotient_pendingeventitem_wrapper.h
)
# generated wrappers end

foreach(wrapper IN LISTS generated_wrappers)
    list(APPEND generated_sources ${bindings_library}/${wrapper})
endforeach()


# ================================== GTAD (based on libQuotient config) ======================
include(FeatureSummary)

# Configure API files generation
set(CSAPI_DIR csapi)
set(FULL_CSAPI_DIR lib/${CSAPI_DIR})
set(CSAPI_TYPESYSTEM_DIR ${CMAKE_SOURCE_DIR}/${bindings_library}/typesystems/${CSAPI_DIR}/)
set(ASAPI_DEF_DIR application-service/definitions)
set(ISAPI_DEF_DIR identity/definitions)

if (GTAD_PATH)
    get_filename_component(ABS_GTAD_PATH "${GTAD_PATH}" REALPATH)
endif ()
if (MATRIX_DOC_PATH)
    get_filename_component(ABS_API_DEF_PATH "${MATRIX_DOC_PATH}/api" REALPATH)
endif ()
if (ABS_GTAD_PATH AND ABS_API_DEF_PATH)
    message( STATUS "Using GTAD at ${ABS_GTAD_PATH}" )
    message( STATUS "Using API files at ${ABS_API_DEF_PATH}" )
    set(API_GENERATION_ENABLED 1)
    if (NOT CLANG_FORMAT)
        set(CLANG_FORMAT clang-format)
    endif()
    get_filename_component(ABS_CLANG_FORMAT "${CLANG_FORMAT}" PROGRAM)
    if (ABS_CLANG_FORMAT)
        set(API_FORMATTING_ENABLED 1)
        message( STATUS "clang-format is at ${ABS_CLANG_FORMAT}")
    else ()
        message( STATUS "${CLANG_FORMAT} is NOT FOUND; API files won't be reformatted")
    endif ()

    set(FULL_CSAPI_SRC_DIR ${ABS_API_DEF_PATH}/client-server)
    file(GLOB_RECURSE API_DEFS RELATIVE ${PROJECT_SOURCE_DIR}
        ${FULL_CSAPI_SRC_DIR}/*.yaml
        ${ABS_API_DEF_PATH}/${ASAPI_DEF_DIR}/*.yaml
        ${ABS_API_DEF_PATH}/${ISAPI_DEF_DIR}/*.yaml
    )
    add_custom_target(py-generate-unformatted-api
        ${ABS_GTAD_PATH} --config ${CMAKE_SOURCE_DIR}/gtad/gtad.yaml --out ${CSAPI_TYPESYSTEM_DIR}
            ${FULL_CSAPI_SRC_DIR}
            old_sync.yaml- room_initial_sync.yaml- # deprecated
            key_backup.yaml- # immature and buggy in terms of API definition
            sync.yaml- # we have a better handcrafted implementation
        WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/${bindings_library}/libQuotient/lib
        SOURCES ${CMAKE_SOURCE_DIR}/gtad/gtad.yaml
                ${CMAKE_SOURCE_DIR}/gtad/typesystem.xml.mustache
                ${API_DEFS}
        VERBATIM
    )
    add_custom_target(py-update-api DEPENDS generate-unformatted-api)
    if (ABS_CLANG_FORMAT)
        set(CLANG_FORMAT_ARGS -i -sort-includes ${CLANG_FORMAT_ARGS})
        # FIXME: the list of files should be produced after GTAD has run.
        # For now it's produced at CMake invocation. If file() hasn't found
        # any files at that moment, clang-format will be called with an empty
        # list of files and choke. Taking outfiles.txt from GTAD could be
        # an option but this, too, must be done during the build stage, and
        # there's no crossplatform way to use the contents of a file as
        # input for a build target command.
        file(GLOB_RECURSE api_ALL_SRCS CONFIGURE_DEPENDS
             ${FULL_CSAPI_DIR}/*.*
             lib/${ASAPI_DEF_DIR}/*.*
             lib/${ISAPI_DEF_DIR}/*.*)
        if (api_ALL_SRCS)
            add_custom_target(format-api
                ${ABS_CLANG_FORMAT} ${CLANG_FORMAT_ARGS} ${api_ALL_SRCS}
                DEPENDS generate-unformatted-api
                VERBATIM)
            add_dependencies(update-api format-api)
        endif()
    endif()
endif()
add_feature_info(EnableApiCodeGeneration "${API_GENERATION_ENABLED}"
                 "build target update-api")
add_feature_info(EnableApiFormatting "${API_FORMATTING_ENABLED}"
                 "formatting of generated API files with clang-format")


# ================================== Shiboken detection ======================================
if(USE_POETRY)
    set(python_interpreter poetry run python3)
    execute_process(
            COMMAND poetry env use ${PYTHON_ENV_VERSION}
            WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
    )
endif()

# Use provided python interpreter if given.
if(NOT python_interpreter)
    find_program(python_interpreter "python")
endif()
message(STATUS "Using python interpreter: ${python_interpreter}")

# Macro to get various pyside / python include / link flags and paths.
# Uses the not entirely supported utils/pyside_config.py file.
macro(pyside_config option output_var)
    if(${ARGC} GREATER 2)
        set(is_list ${ARGV2})
    else()
        set(is_list "")
    endif()

    execute_process(
      COMMAND ${python_interpreter} "pyside_config.py"
              ${option}
      OUTPUT_VARIABLE ${output_var}
      OUTPUT_STRIP_TRAILING_WHITESPACE
      WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
    )

    if ("${${output_var}}" STREQUAL "")
        message(FATAL_ERROR "Error: Calling pyside_config.py ${option} returned no output.")
    endif()
    if(is_list)
        string (REPLACE " " ";" ${output_var} "${${output_var}}")
    endif()
endmacro()

# Query for the shiboken generator path, Python path, include paths and linker flags.
pyside_config(--shiboken-module-path shiboken_module_path)
pyside_config(--shiboken-generator-path shiboken_generator_path)
pyside_config(--python-include-path python_include_dir)
pyside_config(--shiboken-generator-include-path shiboken_include_dir 1)
pyside_config(--shiboken-module-shared-libraries-cmake shiboken_shared_libraries 0)
pyside_config(--python-link-flags-cmake python_linking_data 0)

set(shiboken_path "${shiboken_generator_path}/shiboken6${CMAKE_EXECUTABLE_SUFFIX}")
if(NOT EXISTS ${shiboken_path})
    message(FATAL_ERROR "Shiboken executable not found at path: ${shiboken_path}")
endif()


# ==================================== RPATH configuration ====================================


# =============================================================================================
# !!! (The section below is deployment related, so in a real world application you will want to
# take care of this properly with some custom script or tool).
# =============================================================================================
# Enable rpaths so that the built shared libraries find their dependencies.
set(CMAKE_SKIP_BUILD_RPATH FALSE)
set(CMAKE_BUILD_WITH_INSTALL_RPATH TRUE)
set(CMAKE_INSTALL_RPATH ${shiboken_module_path} ${CMAKE_CURRENT_SOURCE_DIR})
set(CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE)
# =============================================================================================
# !!! End of dubious section.
# =============================================================================================


# =============================== CMake target - target_library ===============================


# Define the sample shared library for which we will create bindings.
# set(${target_library}_sources ../lib/avatar.cpp)
# add_library(${target_library} SHARED ${${target_library}_sources})
# set_property(TARGET ${target_library} PROPERTY PREFIX "")

# Needed mostly on Windows to export symbols, and create a .lib file, otherwise the binding
# library can't link to the sample library.
# target_compile_definitions(${target_library} PRIVATE BINDINGS_BUILD)


# ====================== Shiboken target for generating binding C++ files  ====================
find_package(Qt6 COMPONENTS Core Gui Network REQUIRED)

execute_process(
        COMMAND ${python_interpreter} -c "import PySide6, os; print(os.path.dirname(PySide6.__file__))"
        ${option}
        OUTPUT_VARIABLE pyside6_path
        OUTPUT_STRIP_TRAILING_WHITESPACE
        WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
)

get_target_property(QTCORE_INCLUDES Qt6::Core INTERFACE_INCLUDE_DIRECTORIES)
get_target_property(QTCORE_PRIVATE_INCLUDES Qt6::CorePrivate INTERFACE_INCLUDE_DIRECTORIES)
get_target_property(QTGUI_INCLUDES Qt6::Gui INTERFACE_INCLUDE_DIRECTORIES)
get_target_property(QTGUI_PRIVATE_INCLUDES Qt6::GuiPrivate INTERFACE_INCLUDE_DIRECTORIES)
get_target_property(QTNETWORK_INCLUDES Qt6::Network INTERFACE_INCLUDE_DIRECTORIES)
get_target_property(QTNETWORK_PRIVATE_INCLUDES Qt6::NetworkPrivate INTERFACE_INCLUDE_DIRECTORIES)

set(ADDITIONAL_INCLUDES)
foreach(dir ${QTCORE_INCLUDES} ${QTCORE_PRIVATE_INCLUDES} ${QTGUI_INCLUDES} ${QTGUI_PRIVATE_INCLUDES} ${QTNETWORK_INCLUDES} ${QTNETWORK_PRIVATE_INCLUDES})
    list(APPEND ADDITIONAL_INCLUDES -I${dir})
endforeach()

# On macOS, check if Qt is a framework build. This affects how include paths should be handled.
get_target_property(QtCore_is_framework Qt6::Core FRAMEWORK)
if (QtCore_is_framework)
    get_target_property(qt_core_library_location Qt6::Core LOCATION)
    get_filename_component(qt_core_library_location_dir "${qt_core_library_location}" DIRECTORY)
    get_filename_component(lib_dir "${qt_core_library_location_dir}/../" ABSOLUTE)
    list(APPEND ADDITIONAL_INCLUDES "--framework-include-paths=${lib_dir}")
endif()

# Set up the options to pass to shiboken.
set(shiboken_options
    --generator-set=shiboken
    --enable-parent-ctor-heuristic
    # --enable-return-value-heuristic
    --use-isnull-as-nb_nonzero
    --avoid-protected-hack
    --enable-pyside-extensions
    --no-suppress-warnings
    --debug-level=medium # full
    -I${CMAKE_SOURCE_DIR}
     ${ADDITIONAL_INCLUDES}
    -I${CMAKE_SOURCE_DIR}/${bindings_library}/libQuotient/lib/ # is needed for CSAPI generation
    -T${CMAKE_SOURCE_DIR}
    -T${pyside6_path}/typesystems
    --output-directory=${CMAKE_CURRENT_BINARY_DIR}
    )

set(generated_sources_dependencies ${wrapped_header}) # ${typesystem_file}

# Add custom target to run shiboken to generate the binding cpp files.
add_custom_command(OUTPUT ${generated_sources}
                    COMMAND ${CMAKE_COMMAND} -E env
                    "LD_LIBRARY_PATH=${QT_PATH}/lib/" # TODO: make dynamic
                    ${shiboken_path}
                    ${shiboken_options} ${wrapped_header} ${typesystem_file}
                    DEPENDS ${generated_sources_dependencies}
                    IMPLICIT_DEPENDS CXX ${wrapped_header}
                    WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
                    COMMENT "Running generator for ${typesystem_file}.")


# =============================== CMake target - bindings_library =============================


# Set the cpp files which will be used for the bindings library.
set(${bindings_library}_sources ${generated_sources} PyQuotient/bindings.h)

# temporary solution
# TODO: use cmake rpath handling mechanisms
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wl,-rpath,'${pyside6_path}'")

# Define and build the bindings library.
add_library(${bindings_library} MODULE ${${bindings_library}_sources})

# Apply relevant include and link flags.
target_include_directories(${bindings_library} PRIVATE ${python_include_dir})
target_include_directories(${bindings_library} PRIVATE ${shiboken_include_dir})
target_include_directories(${bindings_library} PRIVATE ${CMAKE_SOURCE_DIR})
target_include_directories(${bindings_library} PRIVATE
    ${pyside6_path}/include/
    ${pyside6_path}/include/QtCore/
    ${pyside6_path}/include/QtGui/
    ${pyside6_path}/include/QtNetwork/
    ${CMAKE_SOURCE_DIR}/${bindings_library}/libQuotient/lib/ # is needed for CSAPI
)

target_link_libraries(${bindings_library} PRIVATE ${shiboken_shared_libraries})
target_link_libraries(${bindings_library} PRIVATE ${target_library})
target_link_libraries(${bindings_library} PRIVATE Qt6::Core Qt6::Gui Qt6::Network)

add_library(pyside STATIC IMPORTED GLOBAL)
# TODO: change hardcoded name
if(UNIX AND NOT APPLE)
    SET_TARGET_PROPERTIES(pyside PROPERTIES IMPORTED_LOCATION ${pyside6_path}/libpyside6.abi3.so.6.1)
else(APPLE)
    SET_TARGET_PROPERTIES(pyside PROPERTIES IMPORTED_LOCATION ${pyside6_path}/libpyside6.abi3.6.1.dylib)
endif()
target_link_libraries(${bindings_library} PRIVATE pyside)

# Adjust the name of generated module.
set_property(TARGET ${bindings_library} PROPERTY PREFIX "")
set_property(TARGET ${bindings_library} PROPERTY OUTPUT_NAME
             "${bindings_library}${PYTHON_EXTENSION_SUFFIX}")
set_target_properties(${bindings_library} PROPERTIES
             LIBRARY_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/${bindings_library})


if(WIN32)
    set_property(TARGET ${bindings_library} PROPERTY SUFFIX ".pyd")
endif()

# Make sure the linker doesn't complain about not finding Python symbols on macOS.
if(APPLE)
  set_target_properties(${bindings_library} PROPERTIES LINK_FLAGS "-undefined dynamic_lookup")
endif(APPLE)

# Find and link to the python import library only on Windows.
# On Linux and macOS, the undefined symbols will get resolved by the dynamic linker
# (the symbols will be picked up in the Python executable).
if (WIN32)
    list(GET python_linking_data 0 python_libdir)
    list(GET python_linking_data 1 python_lib)
    find_library(python_link_flags ${python_lib} PATHS ${python_libdir} HINTS ${python_libdir})
    target_link_libraries(${bindings_library} PRIVATE ${python_link_flags})
endif()


# ================================= Dubious deployment section ================================

set(windows_shiboken_shared_libraries)

if(WIN32)
    # =========================================================================================
    # !!! (The section below is deployment related, so in a real world application you will
    # want to take care of this properly (this is simply to eliminate errors that users usually
    # encounter.
    # =========================================================================================
    # Circumvent some "#pragma comment(lib)"s in "include/pyconfig.h" which might force to link
    # against a wrong python shared library.

    set(python_versions_list 3 36 37 38 39)
    set(python_additional_link_flags "")
    foreach(ver ${python_versions_list})
        set(python_additional_link_flags
            "${python_additional_link_flags} /NODEFAULTLIB:\"python${ver}_d.lib\"")
        set(python_additional_link_flags
            "${python_additional_link_flags} /NODEFAULTLIB:\"python${ver}.lib\"")
    endforeach()

    set_target_properties(${bindings_library}
                           PROPERTIES LINK_FLAGS "${python_additional_link_flags}")

    # Compile a list of shiboken shared libraries to be installed, so that
    # the user doesn't have to set the PATH manually to point to the PySide6 package.
    foreach(library_path ${shiboken_shared_libraries})
        string(REGEX REPLACE ".lib$" ".dll" library_path ${library_path})
        file(TO_CMAKE_PATH ${library_path} library_path)
        list(APPEND windows_shiboken_shared_libraries "${library_path}")
    endforeach()
    # =========================================================================================
    # !!! End of dubious section.
    # =========================================================================================
endif()

# =============================================================================================
# !!! (The section below is deployment related, so in a real world application you will want to
# take care of this properly with some custom script or tool).
# =============================================================================================
# Install the library and the bindings module into the source folder near the main.py file, so
# that the Python interpeter successfully imports the used module.
if(NOT USE_PREBUILD_LIBQUOTIENT)
    install(TARGETS ${bindings_library} ${target_library}
            LIBRARY DESTINATION ${CMAKE_CURRENT_SOURCE_DIR}
            RUNTIME DESTINATION ${CMAKE_CURRENT_SOURCE_DIR}
            )
endif()
install(FILES ${windows_shiboken_shared_libraries} DESTINATION ${CMAKE_CURRENT_SOURCE_DIR})
# =============================================================================================
# !!! End of dubious section.
# =============================================================================================
