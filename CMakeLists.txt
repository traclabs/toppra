cmake_minimum_required(VERSION 2.8.3)
project(toppra)
find_package(catkin REQUIRED)
catkin_package()

if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang")
  set_property(
    GLOBAL PROPERTY
    RULE_LAUNCH_CUSTOM [=[env CFLAGS="-Wno-cpp -Wno-deprecated-register"]=]
    )
else()
  set_property(
    GLOBAL PROPERTY
    RULE_LAUNCH_CUSTOM [=[env CFLAGS="-Wno-cpp -Wno-unused-result"]=]
    )
endif()
      
add_custom_command(OUTPUT requirements_installed.txt
  COMMAND pip3 install -r ${CMAKE_CURRENT_SOURCE_DIR}/requirements.txt --user && touch requirements_installed.txt
)

add_custom_target(setup ALL
  COMMAND /bin/sh -c "if [ ! -f ${CMAKE_BINARY_DIR}/setup.py ] || [ ! -f ${CMAKE_BINARY_DIR}/_CythonUtils.pyx ] || [ ! -f ${CMAKE_BINARY_DIR}/cy_seidel_solverwrapper.pyx ] || [ ${CMAKE_BINARY_DIR}/setup.py -ot setup.py ] || [ ${CMAKE_BINARY_DIR}/_CythonUtils.pyx -ot toppra/_CythonUtils.pyx ] || [ ${CMAKE_BINARY_DIR}/cy_seidel_solverwrapper.pyx -ot toppra/solverwrapper/cy_seidel_solverwrapper.pyx ] ; then python3 -W ignore setup.py build -f && cp setup.py ${CMAKE_BINARY_DIR}/setup.py; cp toppra/_CythonUtils.pyx ${CMAKE_BINARY_DIR}/_CythonUtils.pyx; cp toppra/solverwrapper/cy_seidel_solverwrapper.pyx ${CMAKE_BINARY_DIR}/cy_seidel_solverwrapper.pyx; fi; python3 setup.py install -f --user"
  WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
  DEPENDS requirements_installed.txt
  VERBATIM)
