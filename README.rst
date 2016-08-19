rust |travis|
=============

.. |travis| image:: https://travis-ci.org/ruslo/ansible-rust.svg?branch=master
  :target: https://travis-ci.org/ruslo/ansible-rust/builds

Ansible role for Installing Rust on Linux using ``.tar.gz`` archives instead
of ``rustup.sh`` script.

Role Variables
--------------

Version of Rust
===============

======================== =============== ===================
Name                     Description     Example
======================== =============== ===================
rust_version             Version of Rust ``1.10.0``
rust_architecture        Architecture    ``x86_64`` ``i686``
rust_archive_sha256      hashsum         ``f189303d...``
rust_std_archive_sha256  hashsum         ``41b54148...``
======================== =============== ===================

Calculating hashsum:

.. code-block:: shell

  > wget https://static.rust-lang.org/dist/rust-1.10.0-x86_64-unknown-linux-gnu.tar.gz
  > openssl sha256 rust-1.10.0-x86_64-unknown-linux-gnu.tar.gz
  SHA256(rust-1.10.0-x86_64-unknown-linux-gnu.tar.gz)= f189303d52b37c8bb694b9d9739ae73ffa926cbdeffde1d5d6a5c6e811940293

  > wget https://static.rust-lang.org/dist/rust-std-1.10.0-x86_64-unknown-linux-gnu.tar.gz
  > openssl sha256 rust-std-1.10.0-x86_64-unknown-linux-gnu.tar.gz
  SHA256(rust-std-1.10.0-x86_64-unknown-linux-gnu.tar.gz)= 41b541148a2f595c6b6d5b26e1aabfc3c090fe45686bb272c13e20d4aba03c44

Destination directory
=====================

=========== =================================================== ====================
Name        Description                                         Example
=========== =================================================== ====================
rust_user   Username whose home will be used as destination     ``travis``
rust_dir    Set directory explicitly                            ``/home/travis/opt``
rust_clean  Clean directory before download                     ``True``
=========== =================================================== ====================

Output variables
================

=============== =================
Name            Description
=============== =================
rust_rustc_path Path to ``rustc``
rust_cargo_path Path to ``cargo``
=============== =================

Example Playbook
----------------

List dependency in requirements file:

.. code-block:: none

  > cat requirements.txt
  ruslo.rust

Add custom path for roles

.. code-block:: none

  > cat ansible.cfg
  [defaults]
  roles_path=_3rdParty/roles

Install dependencies:

.. code-block:: none

  > ansible-galaxy install -r requirements.txt -p _3rdParty/roles

Using defaults:

.. code-block:: yaml

  ---
  - hosts: localhost
    roles:
      - role: ruslo.rust
        rust_dir: /home/travis/_install

Using custom version:

.. code-block:: yaml

  ---
  - hosts: localhost
    roles:
      - role: ruslo.rust
        rust_version: 1.10.0
        rust_architecture: i686
        rust_archive_sha256: be93dd2b80a97f2877679950d56990628c6547b953294f16bf6d69c18a39edc0
        rust_std_archive_sha256: fd5aa600c3c6c5a147fb146652335b8b30c79a3b555f5cb0ab8568f80d467112

Using output variables in task:

.. code-block:: yaml

  ---
  - name: Run cargo
    command: cargo --version
    environment:
      PATH: "{{ rust_rustc_path }}:{{ rust_cargo_path }}:{{ ansible_env['PATH'] }}"
    register: x

  - name: Cargo version
    debug:
      msg: "{{ x.stdout_lines }}"

Note that if repo is cloned by git you need to set destination directory name to ``rust``
since it must be a role name (see ``ansible.cfg``):

.. code-block:: shell

  > git clone https://github.com/ruslo/ansible-rust rust

License
-------

`BSD <https://github.com/ruslo/ansible-rust/blob/master/LICENSE>`__

Author Information
------------------

Ruslan Baratov <ruslan_baratov@yahoo.com>
