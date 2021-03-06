cmake_minimum_required(VERSION 3.5)

project(contacts_cutelee)

find_package(Cutelee5 REQUIRED)

set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_AUTOMOC ON)

configure_file(cutelee_paths.h.cmake ${PROJECT_BINARY_DIR}/cutelee_paths.h)

add_executable(contacts_linguist
  main_linguist.cpp
  mainwindow.cpp
  contact.cpp
)

find_package(Qt5WebKitWidgets REQUIRED)
find_package(Qt5LinguistTools REQUIRED)

target_link_libraries(
  contacts_linguist
  Cutelee5::Templates
  Qt5::WebKitWidgets
)

execute_process(COMMAND ${CMAKE_COMMAND} -E create_symlink "${PROJECT_SOURCE_DIR}/../../scripts" "${PROJECT_SOURCE_DIR}/scripts")

set( LINGUIST_TS_ARGS COMMAND "${CMAKE_SOURCE_DIR}/scripts/extract_strings_linguist.py"
  "${CMAKE_SOURCE_DIR}/themes/linguist/bashful/*.html"
  "${CMAKE_SOURCE_DIR}/themes/linguist/sneezy/*.html"
  "${CMAKE_SOURCE_DIR}/themes/linguist/sleepy/*.html"
  ">" "${CMAKE_BINARY_DIR}/output.cpp"
  COMMAND Qt5::lupdate "${CMAKE_BINARY_DIR}/output.cpp" "-ts" )

set (CONTACTS_LOCALES "fr_FR" "de_DE" )

foreach(CONTACT_LOCALE ${CONTACTS_LOCALES})
    list(APPEND LINGUIST_TS_ARGS "${CMAKE_SOURCE_DIR}/${CONTACT_LOCALE}.ts" )
endforeach()

add_custom_target(linguist_ts
  ${LINGUIST_TS_ARGS}
    VERBATIM
)

foreach(CONTACT_LOCALE ${CONTACTS_LOCALES})
    list(APPEND _LINGUIST_QM_ARGS COMMAND Qt5::lrelease "${CMAKE_SOURCE_DIR}/${CONTACT_LOCALE}.ts" "-qm" "${CMAKE_BINARY_DIR}/contacts_${CONTACT_LOCALE}.qm" )
endforeach()

add_custom_target(linguist_qm
  ${_LINGUIST_QM_ARGS}
)
