ansible-role-tripleo-overcloud-prep-containers
=========

[WIP ROLE]  This role walks through the developer setup for a tripleo deployment with a containerized compute.
The developer documentation can be found here: https://etherpad.openstack.org/p/tripleo-containers-work

The instructions below use the master branch from delorean that has been vetted by CI, and then updates
any tripleo rpm to the latest version available from delorean.  It should be the same content as what
currently runs in tripleo-ci

This also git checks out https://git.openstack.org/openstack/tripleo-heat-templates
with refspec: refs/changes/59/330659/43 , this can be updated in config/general_config/containers_minimal.yml


Requirements
------------

https://github.com/openstack/tripleo-quickstart/blob/master/README.rst


overcloud-prep-containers variables
--------------

* working_dir: /home/stack
* containerized_compute: false
* overcloud_prep_containers_script: overcloud-prep-containers.sh.j2
* containers_default_parameters: container-default-parameters.yaml.j2
* overcloud_prep_containers_log: overcloud_prep_containers.log
* container_image: CentOS-Atomic-Host-7-GenericCloud.qcow2
* container_url: "http://cloud.centos.org/centos/7/atomic/images/{{ container_image}}.gz"
* undercloud_network_cidr: 192.168.24.0/24
* ctl_plane_ip: "{{undercloud_network_gateway|default(undercloud_network_cidr|nthhost(1))}}"

overcloud-prep-config variables
-------------------------------

* overcloud_templates_path: /home/stack/tripleo-heat-templates
* overcloud_templates_repo: https://git.openstack.org/openstack/tripleo-heat-templates
* overcloud_templates_refspec: refs/changes/59/330659/43

tripleo-quickstart variables
----------------------------

* see config/general_config/containers_minimal.yml



Dependencies
------------

These dependencies are accounted for in the unmerged tripleo-quickstart review https://review.openstack.org/#/c/393348/

* Depends-On: https://review.openstack.org/#/c/393348/
* Depends-On: https://review.gerrithub.io/#/c/300328/

How to Execute:
---------------
Review https://github.com/openstack/tripleo-quickstart/blob/master/README.rst::

    git clone https://github.com/openstack/tripleo-quickstart.git
    cd tripleo-quickstart
    git-review -d I676b429cab920516a151b124fca2e26dd5c5e87b

    export WD=/var/tmp/containers
    export VIRTHOST=<virthost>

    ./quickstart.sh --no-clone --working-dir $WD --teardown all --requirements quickstart-extras-requirements.txt --playbook quickstart-extras.yml --config $PWD/config/general_config/containers_minimal.yml --tags all  --release master-tripleo-ci $VIRTHOST

How to Execute with Additional gerrit reviews
---------------------------------------------

This will install a local delorean instance and build the reviews into the undercloud/overcloud
Example change https://review.openstack.org/#/c/396460/

STEPS::

    export GERRIT_HOST=review.openstack.org
    export GERRIT_BRANCH=master
    export GERRIT_CHANGE_ID=396460
    export GERRIT_PATCHSET_REVISION=3ea99ef27f60157699c13acb64f88d2cd03d237b

    # Note.. FOR RHEL VIRTHOST's
    * ensure mock is installed on the virthost *, for rhel it comes from epel.. then remove the epel repo

    # Build the yum repo in  /home/stack of the $VIRTHOST
    ./quickstart.sh \
    --no-clone \
    --working-dir $WD \
    --teardown all \
    --requirements quickstart-extras-requirements.txt \
    --playbook dlrn-gate.yml \
    --config $PWD/config/general_config/containers_minimal.yml \
    --extra-vars compressed_gating_repo="/home/stack/gating_repo.tar.gz" \
    --tags all  \
    --release master-tripleo-ci \
    $VIRTHOST

    # Consume the local delorean repo in addition to the normal deployment
    ./quickstart.sh \
    --no-clone \
    --working-dir $WD \
    --teardown none \
    --retain-inventory \
    --requirements quickstart-extras-requirements.txt \
    --playbook quickstart-extras.yml \
    --config $PWD/config/general_config/containers_minimal.yml \
    --extra-vars compressed_gating_repo="/home/stack/gating_repo.tar.gz" \
    --skip-tags provision \
    --tags all  \
    --release master-tripleo-ci \
    $VIRTHOST



License
-------

Apache 2.0

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
