cmake_minimum_required(VERSION 3.10.2)

project(pam_install)

find_package(ament_cmake REQUIRED)
find_package(cmake_always_do REQUIRED)

if(PAM_INSTALL)

  # macro for setting the "install_prefix"
  # variable to ~/.mpi-is/pam/install
  macro(SET_INSTALL_PREFIX)
    execute_process(COMMAND "bash"
    WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/local_install
    INPUT_FILE install_directory
    TIMEOUT 5
    OUTPUT_VARIABLE install_prefix)
    ALWAYS_DO("installation prefix set to ${install_prefix}")
  endmacro()

  # macro for setting the "python_install_directory"
  # variable to the dist folder of the current python3
  macro(SET_PYTHON_INSTALL_DIRECTORY)
    execute_process(COMMAND "bash"
    WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/local_install
    INPUT_FILE python_install_directory
    TIMEOUT 5
    OUTPUT_VARIABLE python_install_directory)
    ALWAYS_DO("python install set to ${python_install_directory}")
  endmacro()

  # macro for generating pam_install
  # (from pam_install.in)
  macro(GENERATE_PAM_INSTALL)
    # path to the colcon install folder (i.e. from
    # where the files should be copied)
    set(colcon_install "${CMAKE_INSTALL_PREFIX}")
    # python executable that will be called by pam_install
    # to list the folders and files to install
    set(list_file_path "${CMAKE_CURRENT_SOURCE_DIR}/local_install/list_files")
    # for configuration of list_files
    set(config_file_path "${CMAKE_CURRENT_SOURCE_DIR}/local_install/install_excluded.json")
    # configuring the pam_install executable file
    set(pam_install_template ${CMAKE_CURRENT_SOURCE_DIR}/local_install/pam_install.in)
    set(pam_install ${CMAKE_CURRENT_SOURCE_DIR}/tmp/pam_install)
    configure_file(${pam_install_template}
      ${pam_install}
      @ONLY)
    ALWAYS_DO("generating pam_install")
  endmacro()

  # macro for calling the pam_install executable
  macro(RUN_PAM_INSTALLATION)
    execute_process(COMMAND "bash"
    WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/tmp
    INPUT_FILE pam_install
    TIMEOUT 5)
    ALWAYS_DO("installing files from ${colcon_install}")
  endmacro()
  
  # setting the variable $install_directory,
  # where all the files will be installed
  # (~/.mpi-is/pam/install)
  SET_INSTALL_PREFIX()

  # setting the variable $python_install directory,
  # where the python files will be installed.
  # It should be in the dist folder of the current python
  SET_PYTHON_INSTALL_DIRECTORY()

  # generating the pam_install executable,
  # which will install the files
  GENERATE_PAM_INSTALL()
  
  # executing the generated file
  RUN_PAM_INSTALLATION()

endif()

ament_package()
  



