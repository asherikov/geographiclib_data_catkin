cmake_minimum_required(VERSION 3.5.0)
project(geographiclib_data_catkin)


set(GEOGRAPHICLIB_DATA         "${CMAKE_INSTALL_PREFIX}/share/GeographicLib" CACHE PATH "Installation path")
set(${PROJECT_NAME}_CACHE_ROOT ""                                            CACHE PATH "Caching directory, build directory by default.")


if(NOT ${PROJECT_NAME}_CACHE_ROOT)
    if("$ENV{CCWS_CACHE}" STREQUAL "")
        set(${PROJECT_NAME}_CACHE_ROOT "${CMAKE_CURRENT_BINARY_DIR}")
    else()
        set(${PROJECT_NAME}_CACHE_ROOT "$ENV{CCWS_CACHE}")
    endif()
endif()

set(${PROJECT_NAME}_PATH "${CMAKE_CURRENT_LIST_DIR}/scripts")
set(${PROJECT_NAME}_CACHE_DIR "${${PROJECT_NAME}_CACHE_ROOT}/geographiclib_data")


add_custom_target(
    ${PROJECT_NAME}_makedir
    COMMAND ${CMAKE_COMMAND} -E make_directory "${${PROJECT_NAME}_CACHE_DIR}"
)

add_custom_target(
    ${PROJECT_NAME}_geoids ALL
    "${${PROJECT_NAME}_PATH}/geographiclib-get-geoids" -p "${${PROJECT_NAME}_CACHE_DIR}" egm96-5
    DEPENDS ${PROJECT_NAME}_makedir
)
add_custom_target(
    ${PROJECT_NAME}_gravity ALL
    "${${PROJECT_NAME}_PATH}/geographiclib-get-gravity" -p "${${PROJECT_NAME}_CACHE_DIR}" egm96
    DEPENDS ${PROJECT_NAME}_makedir
)
add_custom_target(
    ${PROJECT_NAME}_magnetic ALL
    "${${PROJECT_NAME}_PATH}/geographiclib-get-magnetic" -p "${${PROJECT_NAME}_CACHE_DIR}" emm2015
    DEPENDS ${PROJECT_NAME}_makedir
)


foreach(DIR geoids gravity magnetic)
    install(DIRECTORY "${${PROJECT_NAME}_CACHE_DIR}/${DIR}"
        DESTINATION "${GEOGRAPHICLIB_DATA}"
        USE_SOURCE_PERMISSIONS
    )
endforeach()
