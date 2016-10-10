find_package(PythonInterp)
find_package(Java)

include(CMakePackageConfigHelpers)

if(WIN32)
  set(CONFIG_INSTALL_DIR CMake)
else()
  set(CONFIG_INSTALL_DIR lib${LIB_SUFFIX}/${PROJECT_NAME}/cmake)
endif()

if(PYTHONINTERP_FOUND)
  set(python_args PYTHON_SOURCES python_install_sources)
endif()
if(JAVA_FOUND)
  set(java_args JAVA_SOURCES java_sources)
endif()

lcm_wrap_types(
  C_SOURCES c_sources
  C_HEADERS c_install_headers
  CPP_HEADERS cpp_install_headers
  CREATE_C_AGGREGATE_HEADER
  CREATE_CPP_AGGREGATE_HEADER
  ${python_args}
  ${java_args}
  atlas_command_t.lcm
  force_torque_t.lcm
  gps_data_t.lcm
  gps_satellite_info_list_t.lcm
  gps_satellite_info_t.lcm
  image_metadata_t.lcm
  images_t.lcm
  image_sync_t.lcm
  image_t.lcm
  ins_t.lcm
  joint_angles_t.lcm
  joint_state_t.lcm
  kvh_raw_imu_batch_t.lcm
  kvh_raw_imu_t.lcm
  planar_lidar_t.lcm
  pointcloud2_t.lcm
  pointcloud_t.lcm
  pointfield_t.lcm
  pose_t.lcm
  position_3d_t.lcm
  quaternion_t.lcm
  raw_t.lcm
  rigid_transform_t.lcm
  robot_state_t.lcm
  robot_urdf_t.lcm
  sensor_status_t.lcm
  six_axis_force_torque_array_t.lcm
  six_axis_force_torque_t.lcm
  system_status_t.lcm
  twist_t.lcm
  utime_t.lcm
  vector_3d_t.lcm
  viewer_command_t.lcm
  viewer_draw_t.lcm
  viewer_geometry_data_t.lcm
  viewer_link_data_t.lcm
  viewer_load_robot_t.lcm
)

add_library(robotlocomotion-lcmtypes ${c_sources})
target_include_directories(robotlocomotion-lcmtypes INTERFACE
  $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}>)
target_link_libraries(robotlocomotion-lcmtypes lcm)

lcm_install_headers(
  ${c_install_headers}
  ${cpp_install_headers}
  DESTINATION include/lcmtypes
)

add_library(robotlocomotion-lcmtypes-cpp INTERFACE)
target_include_directories(robotlocomotion-lcmtypes-cpp INTERFACE
  $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}>)

install(TARGETS robotlocomotion-lcmtypes robotlocomotion-lcmtypes-cpp
  EXPORT ${PROJECT_NAME}Targets
  RUNTIME DESTINATION bin
  LIBRARY DESTINATION lib${LIB_SUFFIX}
  ARCHIVE DESTINATION lib${LIB_SUFFIX}
  INCLUDES DESTINATION include/lcmtypes
)

if(PYTHONINTERP_FOUND)
  lcm_install_python(${python_install_sources})
endif()

if(JAVA_FOUND)
  include(UseJava)

  add_jar(robotlocomotion-lcmtypes-jar
    OUTPUT_NAME robotlocomotion-lcmtypes
    INCLUDE_JARS lcm-java
    SOURCES ${java_sources}
  )

  install_jar(robotlocomotion-lcmtypes-jar share/java)

  set(ROBOTLOCOMOTION-LCMTYPES_INCLUDE_JAVA
    "include(\${CMAKE_CURRENT_LIST_DIR}/${PROJECT_NAME}JavaTargets.cmake)"
  )
  export_jars(
    FILE ${PROJECT_BINARY_DIR}/${PROJECT_NAME}JavaTargets.cmake
    TARGETS robotlocomotion-lcmtypes-jar
  )
  install_jar_exports(
    FILE ${PROJECT_NAME}JavaTargets.cmake
    DESTINATION ${CONFIG_INSTALL_DIR}
    TARGETS robotlocomotion-lcmtypes-jar
  )
endif()

configure_package_config_file(
  ${PROJECT_SOURCE_DIR}/${PROJECT_NAME}Config.cmake.in
  ${PROJECT_BINARY_DIR}/${PROJECT_NAME}Config.cmake
  INSTALL_DESTINATION ${CONFIG_INSTALL_DIR}
)

# Exported targets for build directory
export(
  TARGETS robotlocomotion-lcmtypes robotlocomotion-lcmtypes-cpp
  FILE ${PROJECT_BINARY_DIR}/${PROJECT_NAME}Targets.cmake
)

# Exported targets for installation
install(EXPORT ${PROJECT_NAME}Targets
  DESTINATION ${CONFIG_INSTALL_DIR}
  FILE ${PROJECT_NAME}Targets.cmake
)

# Install package configuration files
install(FILES
  ${PROJECT_BINARY_DIR}/${PROJECT_NAME}Config.cmake
  DESTINATION ${CONFIG_INSTALL_DIR}
)
