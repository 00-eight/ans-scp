# ans-scp
## Summary
Ansible Playbook to dynamically generate a SystemConfiguration Profile based on platform model, then push the SCP to iDRAC using Redfish API and monitor progress to completion.

## Usage
```
ansible-playbook -i <inventory.ini> site.yml
```
*before* using you must edit the following
- ans-scp/lab.ini
  - Add iDRAC IPs to the inventory
- ans-scp/group_vars/lab.yml
  - Set iDRAC Username and Password
- ans-scp/roles/scp/defaults/main.yml
  - Define BIOS.Setup.1.1 attributes for platforms you intended to configure using predefined structure

## Project structure
```
│   readme.md
│
└───ans-scp
    │   lab.ini                         # Inventory File ::- Edit add list of iDRAC IPs
    │   site.yml
    │
    ├───group_vars
    │       all
    │       lab.yml                     # GroupVars ::- Edit add iDRAC Username and Password
    │
    └───roles
        └───scp
            ├───defaults
            │       main.yml            # YAML ::- Edit add platform - Define Platform Model and BIOS.Setup.1-1 Attributes
            │
            ├───tasks
            │       import_scp.yml
            │       main.yml
            │
            └───templates
                    scp_base.j2
```

## Implementation Details
1. Identify System Model from redfish/v1/Systems/System.Embedded.1
2. Generate SCP using Jinja Template 
  - Index System Model into /roles/scp/defaults/main.yml
3. Import System Config Profile redfish/v1/Managers/iDRAC.Embedded.1/Actions/Oem/EID_674_Manager.ImportSystemConfiguration
4. Monitor Job /redfish/v1/Managers/iDRAC.Embedded.1/Jobs/<JID>
  - <JID> is returned from ImportSystemConfiguration response headers.location
5. Repeat System Config Profile Import to ensure all attributes are applied due to attribute dependecies
6. Monitor Job

## Limitations
Only do BIOS.Setup.1.1 attributes

## More Info
[iDRAC9 Redfish API Guide](https://topics-cdn.dell.com/pdf/idrac9-lifecycle-controller-v4x-series_api-guide_en-us.pdf)
[Server COnfiguration XML File](https://downloads.dell.com/solutions/general-solution-resources/White%20Papers/Server%20Configuration%20XML%20File.pdf)
[Ansible Docs](https://docs.ansible.com/ansible/latest/index.html)
