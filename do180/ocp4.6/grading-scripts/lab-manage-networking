#!/bin/bash
#
# Copyright 2021 Red Hat, Inc.
#
# NAME
#     lab-manage-networking - grading/setup script for DO180
#
# SYNOPSIS
#     lab-manage-networking {start|finish}
#
#        start   - configures the environment at the start
#                  of a lab or exercise.
#        finish  - executes any administrative tasks after
#                  completion of a lab or exercise.
#
#     All functions only work on workstation
#
# DESCRIPTION
#     This script configures Guided Exercise: Loading the Database
#
# CHANGELOG
#   * Tue Mar 24 2021 Harpal Singh <harpasin@redhat.com>
#   - Added new functions to stop, rm, rmi for rootless podman.
#   * Tue Jan 29 2019 Dan Kolepp <dkolepp@redhat.com>
#   - changed name from lab-load-mysqldb to
#     lab-manage-networking, to conform to new naming
#     standards.
#   - updated verbs to start/finish from setup/cleanup
#   - container engine changed from docker to podman.
#   * Wed Jun 06 2018 Artur Glogowski <aglogows@redhat.com>
#   - changes related to version 3.9
#   * Mon Mar 27 Richard Allred <rallred@redhat.com>
#   - original code

PATH=/usr/bin:/bin:/usr/sbin:/sbin

# Initialize and set some variables
run_as_root='true'
this="manage-networking"
title="Guided Exercise: Loading the Database"
target='workstation'

# This defines which subcommands are supported (solve, reset, etc.).
# Corresponding lab_COMMAND functions must be defined.
declare -a valid_commands=(start finish)

# Additional functions for this grading script

function print_usage {
  local problem_name=$(basename $0 | sed -e 's/^lab-//')
  cat << EOF
This script controls the setup and grading of this lab.
Usage: lab ${problem_name} COMMAND
       lab ${problem_name} -h|--help

COMMAND is one of: ${valid_commands[@]}

EOF
}

function lab_start {
  print_header "Setting up ${target} for the ${title}"

  #Remove once workstation is configured with
  # registry.lab.example.com as a registry.
  check_podman_registry_config

  print_line " · Creating a host directory for the database container:"
  pad "   · Adding fcontext policy for /home/student/local/mysql"
  if sudo semanage fcontext -a -t container_file_t '/home/student/local/mysql(/.*)?'; then
    print_SUCCESS
  else
    print_line "Unable to add fcontext of 'container_file_t' for '/home/student/local/mysql(/.*)?'"
    print_FAIL
  fi

  pad "   · Creating the /home/student/local/mysql directory"
  if create_directory_rootless /home/student/local/mysql; then
    print_SUCCESS
  else
    print_FAIL
  fi


  pad "   · Apply fcontext policy to /home/student/local/mysql"
  if sudo restorecon -Rv /home/student/local/mysql; then
    print_SUCCESS
  else
    print_FAIL
  fi

  pad "   · Change owner of the /home/student/local/mysql to the mysql" && change_ownership_rootless /home/student/local/mysql

  grab_lab_files

  print_line
}


function lab_finish {
  print_header "Completing the ${title}"


  pad " · Stopping the 'mysqldb-port' container" && podman_stop_container_rootless $mysqldb-port
  pad " · Removing the 'mysqldb-port' container" && podman_rm_container_rootless mysqldb-port

  pad " · Removing the 'registry.redhat.io/rhel8/mysql-80:1' image" && podman_rm_image_rootless registry.redhat.io/rhel8/mysql-80:1

  pad " · Removing the /home/student/local/mysql directory"
  if remove_directory /home/student/local/mysql; then
    print_SUCCESS
  else
    print_FAIL
  fi

  pad " · Removing the fcontext for /home/student/local/mysql"
  if sudo semanage fcontext -C -l | grep "/home/student/local/mysql"; then
    if sudo semanage fcontext -d -t container_file_t '/home/student/local/mysql(/.*)?'; then
      print_SUCCESS
    else
      print_line "Unable to remove fcontext for /home/student/local/mysql directory!"
      print_FAIL
    fi
  else
    echo "File context for /home/student/local/mysql not found - no need to remove."
    print_SUCCESS
  fi


}

############### Don't EVER change anything below this line ###############

# Source library of functions
source /usr/local/lib/labtool.shlib
source /usr/local/lib/labtool.do180.shlib

grading_main_program "$@"
