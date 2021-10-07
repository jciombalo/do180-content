#!/usr/bin/bash
#
# Copyright 2021 Red Hat, Inc.
#
# NAME
#    lab-manage-lifecycle - setup script for DO180
#
# SYNOPSIS
#     lab-manage-lifecycle {start|finish}
#
#        start   - configures the environment at the start of a lab or exercise.
#        finish  - executes any administrative tasks after completion of a lab or exercise.
#
#     All functions only work on workstation
#
# DESCRIPTION
#     This script configures the Guided Exercise: Managing a MySQL Container
#
# CHANGELOG
#   * Tue Mar 23 2021 Harpal Singh <harpasin@redhat.com>
#   - Changed functions to stop, rm, rmi for rootless podman.
#   * Thu Jan 31 2019 Jordi Sola <jordisola@redhat.com>
#   - Updated to use podman commands. Verbs refactoring.
#   * Web Jun 2018 Artur Glogowski <aglogows@redhat.com>
#   - changes related to version 3.9
#   * Fri Mar 24 2017 Richard Allred <rallred@redhat.com>
#   - original code


PATH=/usr/bin:/bin:/usr/sbin:/sbin

# Initialize and set some variables
run_as_root='true'
this="manage-lifecycle"
title="Guided Excercise: Managing a MySQL Container"
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

  grab_lab_files
}

function lab_finish {
  print_header "Completing the ${title}"

  pad " · Stopping 'mysql' container" && podman_stop_container_rootless mysql
  pad " · Stopping 'mysql-2' container" && podman_stop_container_rootless mysql-2

  pad " · Removing 'mysql' container" && podman_rm_container_rootless mysql
  pad " · Removing 'mysql-2' container" && podman_rm_container_rootless mysql-2
  pad " · Removing 'mysql-db' container" && podman_rm_container_rootless mysql-db

  pad " · Removing 'registry.redhat.io/rhel8/mysql-80:1' image" && podman_rm_image_rootless registry.redhat.io/rhel8/mysql-80:1
  pad " · Removing the project directory"
  if remove_directory /home/student/DO180/labs/manage-lifecycle; then
    print_SUCCESS
  else
    print_FAIL
  fi
  pad " · Removing the solution directory"
  if remove_directory /home/student/DO180/solutions/manage-lifecycle; then
    print_SUCCESS
  else
    print_FAIL
  fi
}

############### Don't EVER change anything below this line ###############

# Source library of functions
source /usr/local/lib/labtool.shlib
source /usr/local/lib/labtool.do180.shlib

grading_main_program "$@"
