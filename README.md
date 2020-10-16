restic role
=========
[![License](https://img.shields.io/badge/license-Apache-green.svg?style=flat)](https://raw.githubusercontent.com/lean-delivery/ansible-role-restic/master/LICENSE)
[![Build Status](https://travis-ci.org/lean-delivery/ansible-role-restic.svg?branch=master)](https://travis-ci.org/lean-delivery/ansible-role-restic)
[![Build Status](https://gitlab.com/lean-delivery/ansible-role-restic/badges/master/pipeline.svg)](https://gitlab.com/lean-delivery/ansible-role-restic/pipelines)
[![Galaxy](https://img.shields.io/badge/galaxy-lean__delivery.restic-blue.svg)](https://galaxy.ansible.com/lean_delivery/restic)
![Ansible](https://img.shields.io/ansible/role/d/role_id.svg)
![Ansible](https://img.shields.io/badge/dynamic/json.svg?label=min_ansible_version&url=https%3A%2F%2Fgalaxy.ansible.com%2Fapi%2Fv1%2Froles%2Frole_id%2F&query=$.min_ansible_version)

A role to install and configure [Restic](https://restic.readthedocs.io/en/latest/index.html).

Requirements
------------

There are no requirements for this role.

Role Variables
--------------

List of variables:
* restic_version: version of app to download
* restic_runas: default user to run the job. Can be altered in job definition (see below)
* restic_keep_schedule: default schedule to keep snapshots. This schdule is default but can be altered per job (see below)
** restic_keep_schedule.daily: how many daily snapshots to keep
** restic_keep_schedule.weekly: how many weekly snapshots to keep
** restic_keep_schedule.monthly: how many monthly snapshots to keep
* restic_global_pre: global script executes before each backup script run
* restic_global_post: global script executes after each backup script run
* restic_jobs: jobs definition
* restic_password: password to encrypt backup repository

All of those variables (except restic_jobs) are optional and have reasonable defaults

Job definition
--------------

To start backing up you need to define one or more jobs. Jobs parameters

Required:
* name: name of job
* src: source to copy
* dest: destination to copy. Playbook will init the repository on destination and create file `.{{name}}.initialized` in backup script folder.

Optional parameters:
* password: repository password (default is copied from `restic_password` variable)
* exclude: list of files/paths to exclude. You can use relative or full paths here. GLOBs are also allowed
* keep_schedule: schedule to keep images. This parameter overwrites default one but not combine! So if you set `keep_schedule.daily: 7` - you'll have only 7 daily snapshots.
* job_pre: script to execute before backup process starts
* job_post: script to execute after job ends (but before cleanup starts)

Dependencies
------------

There are no depedencies for this role

Example Playbook
----------------

Example to use restic with Amazon S3. You need to have an S3 bucket.

    - name: restic test
      hosts:
        - srv.local
      roles:
        - logan.restic
      vars:
        restic_password: "AAABBBCCCXXXYYYZZZ"
        restic_global_pre: |
          export AWS_ACCESS_KEY_ID="ABCDEFG"
          export AWS_SECRET_ACCESS_KEY="ZYXWVUT"
        restic_global_post: |
          unset AWS_ACCESS_KEY_ID
          unset AWS_SECRET_ACCESS_KEY
        restic_jobs:
        - name: etc
          src: /etc
          dest: "s3:https://s3.amazonaws.com/restic-demo/etc"

License
-------
Apache

Author Information
------------------

authors:
  - Paul Rudnitskiy <me@prudnitskiy.pro>
