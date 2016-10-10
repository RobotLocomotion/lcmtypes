if(NOT JAVA_FOUND)
  message(STATUS "Java not found: not building Java LCM-SPY plugins")
  return()
endif()
if(NOT TARGET lcm-java)
  message(STATUS "lcm-java not found: not building Java LCM-SPY plugins")
  return()
endif()

message(STATUS "Found Java and lcm-java: building Java LCM-SPY plugins")

set(src_dir ${CMAKE_CURRENT_SOURCE_DIR}/src)
set(jar_fname lcmspy_plugins_robotlocomotion.jar)

set(java_sources
  src/robotlocomotion_spy/ImagePlugin.java
  src/robotlocomotion_spy/PlanarLidarPlugin.java
)

add_jar(lcmspy_plugins_robotlocomotion
  INCLUDE_JARS lcm-java robotlocomotion-lcmtypes-jar
  SOURCES ${java_sources}
)

install_jar(lcmspy_plugins_robotlocomotion share/java)
