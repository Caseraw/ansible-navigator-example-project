# Example project for `ansible-navigator`

This repo is just a sample to get familiar with `ansible-navigator` and the
rest of the Ansible Automation Platform.

# Usage

Clone this repo to your workspace and manipulate the files to make your own
changes for your project.

```shell
$ git clone https://github.com/Caseraw/ansible-navigator-example-project.git
```

## Structure

```shell
.
├── ansible-navigator-logs                      [1]
│   └── ansible-navigator.log
├── ansible-playbook-artifacts                  [2]
│   └── ping-localhost-artifact-...xxx...json
├── ansible-runner-artifacts                    [3]
│   ├── artifacts
│   └── project
├── inventory                                   [4]
│   └── hosts.yml
├── playbooks                                   [5]
│   └── ping-localhost.yml
├── ansible.cfg                                 [6]
├── ansible-navigator.yml                       [7]
└── README.md
```

1. A directory to store logs. Configurable item in the navigator coniguration.
2. A directory to store playbook artifacts. Records of each playbook run for
   troubleshoot and replay purposes. Configurable item in the navigator
   coniguration.
3. A directory to store runner artifacts. Runtime files and folders that get
   populated during the navigator execution. Configurable item in the navigator
   coniguration.
4. A directory that contains the [Ansible inventory](https://docs.ansible.com/ansible/latest/user_guide/index.html#working-with-inventory), static and/or dynamic.
5. A directory that contains the [Ansible playbooks](https://docs.ansible.com/ansible/latest/user_guide/index.html#writing-tasks-plays-and-playbooks).
6. The [Ansible configuration](https://docs.ansible.com/ansible/latest/installation_guide/intro_configuration.html) file.
7. The [Ansible Navigator configuration](https://ansible-navigator.readthedocs.io/en/latest/settings/#the-ansible-navigator-settings-file) file.

> 1, 2 and 3 have been set in this example for development purposes. These
  directories can contain a lot of files and data. Therefore it's ignored in
  the git source control. Make sure to keep an eye out and clean these up
  regularly. If needed, turn them off in the navigator configuration file.

## Navigator settings

If the project does not contain an `ansible-navigator.yml` configuration file,
the defaults will be used. Just like the `ansible.cfg`, prior to the actual run
, it looks for a file path that leads to the settings file. In sequence, first
in the environment variable `ANSIBLE_NAVIGATOR_CONFIG`. Then the current
working directory `./ansible-navigator.<ext>` and finally in the root directory
of the user's home `~/.ansible-navigator.<ext>`.

For more on the navigator settings check the [docs](https://access.redhat.com/documentation/en-us/red_hat_ansible_automation_platform/2.1/html-single/ansible_navigator_creator_guide/index#assembly-settings-navigator_ansible-navigator).

When developing I have come to find the following settings usefull (personal
opinion). Play with the settings to find your own personal preferences or what
works for your project.

```yaml
ansible-navigator:
  mode: stdout
  execution-environment:
    container-engine: podman
    image: quay.io/kasra_amirsarvari/ansible-custom-ee-example@sha256:f416a410d42dc1afd2c45a29652b2fc76f488f2ab4933064b2e4e1fc0856aa0e
    pull-policy: missing
  ansible:
    config: ansible.cfg
    inventories:
      - $PWD/inventory
  ansible-runner:
    artifact-dir: ansible-runner-artifacts
    rotate-artifacts-count: 1
  logging:
    append: False
    file: $PWD/ansible-navigator-logs/ansible-navigator.log
    level: warning
  playbook-artifact:
    enable: True
    save-as: $PWD/ansible-playbook-artifacts/{playbook_name}-artifact-{ts_utc}.json
```

Explaining the above navigator settings:

- `ansible-navigator.mode` (optional)
  - I like the old-school terminal standard out view.
- `ansible-navigator.execution-environment` (preferable)
  - Be in control and reduce repetition on which execution environment is used
  during the project. When dealing with multiple execution environments within
  a single project, provide the image URL as a navigator command line argument
  `--eei`.
- `ansible-navigator.ansible` (preferable)
  - Be in control of the configuration and override the inherited configuration
  in the execution environment. Can be useful during development. It's
  preferable to bake this in the execution environment with `ansible-builder`.
  Reduce the repetition of providing the inventory. When dealing with multiple
  inventories, make sure to separate them in order to address them separately.
  Use the navigator command line argument `-i` with a path to point to the
  inventory to use.
- `ansible-navigator.ansible-runner` (optional)
  - I like to see artifacts of how the navigator uses the `ansible-runner`. To
  gain more insights of what goes on under the surface.
- `ansible-navigator.logging` (preferable)
  - Be more in control of the logs. The defaults don't work for me personally.
- `ansible-navigator.playbook-artifact` (preferable)
  - Get insights of the previous playbook runs by replaying them for insights
  and troubleshooting purposes.

# Resources

For more information follow these links:

- [Automation content navigator](https://www.ansible.com/blog/whats-new-in-ansible-automation-platform-2-automation-content-navigator)
- [Introduction to Ansible Navigator](https://www.redhat.com/en/events/webinar/introduction-to-ansible-navigator-a-text-based-front-end-interface)
- [Authoring content quickly using ansible-navigator](https://www.youtube.com/watch?v=Upisw3Pv294)
- [Ansible Navigator docs](https://ansible-navigator.readthedocs.io/)
- [Ansible Runner docs](https://ansible-runner.readthedocs.io/en/stable/)
- [Ansible Builder docs](https://ansible-builder.readthedocs.io/en/stable/)
- [Red Hat Ansible Automation Platform 2.1](https://access.redhat.com/documentation/en-us/red_hat_ansible_automation_platform/2.1)
- [Red Hat Ansible Automation Platform 2.1 - Ansible Navigator Creator Guide](https://access.redhat.com/documentation/en-us/red_hat_ansible_automation_platform/2.1/html-single/ansible_navigator_creator_guide/index)
- [Ansible Automation Platform 2 - Presentation/Workshop](https://aap2.demoredhat.com/decks/ansible_rhel.pdf)
- [Ansible Collection index](https://docs.ansible.com/ansible/latest/collections/index.html)
- [Ansible Modules and Plugins index](https://docs.ansible.com/ansible/latest/collections/all_plugins.html)