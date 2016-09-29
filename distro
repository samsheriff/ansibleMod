#!/usr/bin/python

""" Documentation """

DOCUMENTATION = """
File name: secretserver

Purpose: Ansible Module that reads from SecretServer
         via passwordless server authenticated interface.
         It can get a text secret and/or download an attachment.

-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-
                              #######################
Ansible Task USAGE example to | retrieve a password |:
                              #######################
    # Secret ID
  - name: read_password_id
    register: read_password
    no_log: True  # Hides ss_out when playbook runs with -vvvv
    secretserver: >
        secret_id=9999
        field_name=Password
        manual_mode={{ manual_mode }}
        manual_secret={{ manual_secret }}

    # Search term (when Secret ID is not known)
  - name: read_password_search
    register: read_password_search
    no_log: True
    secretserver: >
        search_term="Team Automation"
        field_name=Password
        manual_mode={{ manual_mode }}
        manual_secret={{ manual_secret }}

  # Use the results
  - register: use_password
    name:     use_password
    command:  echo {{ read_password.ss_out }}
    # ... but PLEASE don't echo your passwords!


For Manual Password use, ansible-playbook invocation will need two sets of extra variables:
    ansible-plabook\
     -i inventory/hosts\
      playbook/playbook.pb\
       --extra-vars "manual_mode=True"\
        --extra-vars "manual_secret=aManualPassword"
-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-
                              ###################
Ansible Task USAGE example to | download a file | attachment off a secret:
    # Using Secret ID         ###################
  - name: dl_file_id
    register: dl_file_id
    secretserver: >
        secret_id=9999
        field_name="My File"
        output_file=/home/user/Downloads/myfile
        manual_mode={{ manual_mode }}
        manual_secret={{ manual_secret }}

  - name: dl_file_search
    register: dl_file_search
    secretserver: >
        search_term="Team Automation"
        field_name="My File Attachment"
        output_file=/home/user/Downloads/myfileattachment
        manual_mode={{ manual_mode }}
        manual_secret={{ manual_secret }}
-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-<>-


Authors: Ladislav Toldy, Mahdi Torabi, Larry Fast, Sam Sharifian
Date created: May 07, 2016
Date modified: Sep 08, 2016
Ansible Version: 2.0.1.0

CodeQuality: Low - starting to get the hang of module design
             A usable Module Template is starting to emerge
             A usable test system and TDD process is coming together

See also: https://wiki.globalrelay.net/display/AT/Unit+Testing+for+Ansible+Code+and+Modules

Tested with 2.0.1.0 on desktop and deploy2
PyTest Suite Invocation:
    ansible-playbook \
    -i inventories/all_envs/hosts_generated \
    tests/secretserver/secretserver_unit.pb

CONTAINS COPIED CODE - jar_wrapper is copied from:
https://stash.globalrelay.net/projects/DEVO/repos/at_misc/browse/devtools/secret_server
"""

import datetime
import platform
import os
import sys


# ---------------
# Begin Mainline


def main():
    start = datetime.datetime.now()

    module = AnsibleModule(
        argument_spec=dict(
            os_type=dict(required=False,
                         type='str',
                         choices=['linux', 'bsd', 'win'],
                         default='linux'),
            postfix=dict(required=False)
        )
    )

    try:
        results = dict(
            filename=None,
            changed=False,
            module_params=module.params,
            start=str(start),
            rc=0
        )

        node_os = module.params['os_type']
        os_dist = platform.dist()
        os_full_name = "_".join(os_dist).replace(" ", "-")
        if module.params['postfix']:
            os_full_name += module.params['postfix']
        results['filename'] = os_full_name

        results['rc'] = 0

    except Exception, e:
        results['ExceptionHandlerError'] = "If you see this text, there was an error in the Exception Handler."
        results['warnings'] = str(e).splitlines()
        results['msg'] = results['warnings'][0]
        results['failed'] = True
        results['rc'] = 1
        del results['ExceptionHandlerError']
    finally:
        end = datetime.datetime.now()
        delta = end - start
        results['end'] = str(end)
        results['delta'] = str(delta)
        print json.dumps(results)
        sys.exit(results['rc'])


from ansible.module_utils.basic import *

if __name__ == '__main__':
    main()
