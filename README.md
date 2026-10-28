# Stack-Automation-NetApp

ONTAP storage setup packaged for Stack Automation.

```
assets/ansible/netapp-ontap/   one playbook per task group
blueprints/ontap-storage-setup.yaml
graphics/netapp.svg
instructions/ontap-storage-setup.md
```

One blueprint chains the grains. A new task is a new playbook plus a grain in that blueprint, not a new blueprint.
