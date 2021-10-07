#!/usr/bin/bash
#
# Copyright 2021 Red Hat, Inc.
#
# NAME
#     lab-container-create - setup script for DO180
#
# SYNOPSIS
#     lab-container-create {start|finish}
#
#        start   - configures the environment at the start of a lab or exercise.
#        finish  - executes any administrative tasks after completion of a lab or exercise.
#
#     All functions only work on workstation
#
# DESCRIPTION
#     This script configures the Guided Exercise: Creating a MySQL Database Instance
#
# CHANGELOG
#   * Fri Apr 23 2021 Federico Fapitalle <ffapital@redhat.com>
#   - initial version

PATH=/usr/bin:/bin:/usr/sbin:/sbin

# Initialize and set some variables
run_as_root='true'
this='container-rootless'
target='workstation'
title='Guided Exercise: Exploring root and rootless containers'

# This defines which subcommands are supported (start, finish, etc.).
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

  check_podman_registry_config
}

function lab_finish {
  print_header "Completing the ${title}"
}

############### Don't EVER change anything below this line ###############

# Source library of functions
source /usr/local/lib/labtool.shlib
source /usr/local/lib/labtool.do180.shlib

grading_main_program "$@"
