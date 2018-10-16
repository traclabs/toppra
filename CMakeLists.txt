cmake_minimum_required(VERSION 2.8.3)
project(toppra)
find_package(catkin REQUIRED)
catkin_metapackage()

add_custom_command(OUTPUT requirements_installed.txt
  COMMAND pip install -r ${CMAKE_CURRENT_SOURCE_DIR}/requirements.txt --user && touch requirements_installed.txt
)

add_custom_target(run ALL
  COMMAND python setup.py install --user
  WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/
  DEPENDS requirements_installed.txt)

