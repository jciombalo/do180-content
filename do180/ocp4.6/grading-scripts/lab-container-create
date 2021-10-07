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
#   * Fri Mar 19 2021 Federico Fapitalle <ffapital@redhat.com>
#   - run podman as the student user
#   * Mon Nov 09 2020 Michael Phillips <miphilli@redhat.com>
#   - altered the finish function again to revert to previous state (with the addition of --format '{{.ID}}')
#   * Fri Nov 06 2020 Michael Phillips <miphilli@redhat.com>
#   - adjusted the finish function to use grep instead of --filter.
#   * Tue Jan 22 2019 Eduardo Ramirez <eramirez@redhat.com>
#   - Update to OCP4
#
#   * Thu Apr 06 2017 Fernando Lozano <flozano@redhat.com>
#   - original code

PATH=/usr/bin:/bin:/usr/sbin:/sbin

# Initialize and set some variables
run_as_root='true'
this='container-create'
target='workstation'
title='Guided Exercise: Creating a MySQL database instance'

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
  pad " · Creating create_table.txt file"
  mkdir -p ${solutions}/${this}
  cat > ${solutions}/${this}/create_table.txt << EOF
CREATE TABLE Projects (id int NOT NULL, name varchar(255) DEFAULT NULL, code varchar(255) DEFAULT NULL, PRIMARY KEY (id));
EOF
  if [ -d ${solutions}/${this} ] && [ -f ${solutions}/${this}/create_table.txt ]; then
   chown -R ${user}:${user} ${solutions}/${this}
   print_SUCCESS
  else
   print_FAIL
  fi
}

function lab_finish {
  print_header "Completing the ${title}"

  pad ' · Removing "mysql-basic" container'
  podman_rm_container_rootless "mysql-basic"

  pad ' · Removing "registry.redhat.io/rhel8/mysql-80:1" image'
  podman_rm_image_rootless "registry.redhat.io/rhel8/mysql-80:1"
}

############### Don't EVER change anything below this line ###############

# Source library of functions
source /usr/local/lib/labtool.shlib
source /usr/local/lib/labtool.do180.shlib

grading_main_program "$@"
