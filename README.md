# Ansible Collection - adlytaibi.sgws

This collection contains two roles that are intended to configure a NetApp StorageGRID. The following tasks can be performed:

### Grid role's plays:
- Authorize to get a grid token
- Install signed SSL certificates for API management
- Install signed SSL certificates for Storage management
- Setup an identity provider
- Create a local or federated admin groups for grid
- Create a local admin users for grid

### Tenant role's plays:
- Create new tenant
- Authorize to get a tenant token (using new created or existing accountid)
- Setup an identity provider for tenant
- Create a local or federated groups for tenant
- Create a local users for tenant
- Generate S3 keys for tenant
- Create bucket for tenant

### Note: 
- Setting up identity provider is a pre-requisite to creating federated groups.
- Ability to create federated groups and local groups in one run.
- Local users to a federated group will be ignored.

Requirements
------------

- [Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html) needs to be installed.
- [Galaxy](https://galaxy.ansible.com/adlytaibi/sgws)

Role Variables
--------------

Assuming you're in the collection's directory `~/.ansible/collections/ansible_collections/adlytaibi/sgws`.
Settable variables for the role are in `roles/{sg_grid,sg_tenant}/vars/main.yml`. Passwords and credentials are incorporated using [ansible-vault](https://docs.ansible.com/ansible/latest/cli/ansible-vault.html) or [read below](#using-ansible-vault). Other variables are under `roles/{sg_grid,sg_tenant}/vars/` for their respective tasks.

Installing signed API management SSL certificates requires three files `mgmt_cert.pem`, `mgmt_cert.key` and `chain.pem` to be in `roles/sg_grid/files`. Storage API SSL certificate files are `stor_cert.pem`, `stor_cert.key` and `chain.pem`. I added helper scripts [read below](#helper-scripts).

Turn on or off plays in `roles/{sg_grid,sg_tenant}/tasks/main.yml` by commenting or uncommenting the respective lines.

Since there is prerequisite work to perform prior to using some plays. SSL certificate and identity provider play are commented out by default.

Dependencies
------------

There are no dependencies.

Example Playbook
----------------

```bash
# ansible-playbook --vault-password-file ~/.passwd ~/.ansible/collections/ansible_collections/adlytaibi/sgws/storagegrid.yml

PLAY [sgws] ********************************************************************

TASK [Gathering Facts] *********************************************************
ok: [localhost]

TASK [sg_grid : include_vars] **************************************************
ok: [localhost]

TASK [sg_grid : Get grid authorization token] **********************************
ok: [localhost]

TASK [sg_grid : Load admin group variables] ************************************
ok: [localhost]

TASK [sg_grid : Create grid user groups and users] *****************************
included: /root/.ansible/collections/ansible_collections/adlytaibi/sgws/roles/sg_grid/tasks/sgws_group.yml for localhost
included: /root/.ansible/collections/ansible_collections/adlytaibi/sgws/roles/sg_grid/tasks/sgws_group.yml for localhost

TASK [sg_grid : Check if group "Developers" exists] ****************************
ok: [localhost]

TASK [sg_grid : Create new administrators "Developers" group] ******************
ok: [localhost]

TASK [sg_grid : Create users part of the group] ********************************
included: /root/.ansible/collections/ansible_collections/adlytaibi/sgws/roles/sg_grid/tasks/sgws_users.yml for localhost

TASK [sg_grid : Load local admin users' variables] *****************************
ok: [localhost]

TASK [sg_grid : Create and set password for admin users] ***********************
included: /root/.ansible/collections/ansible_collections/adlytaibi/sgws/roles/sg_grid/tasks/sgws_user.yml for localhost
included: /root/.ansible/collections/ansible_collections/adlytaibi/sgws/roles/sg_grid/tasks/sgws_user.yml for localhost

TASK [sg_grid : Check if user "Developer Test User" exists] ********************
ok: [localhost]

TASK [sg_grid : Create new administrator user "Developer Test User"] ***********
ok: [localhost]

TASK [sg_grid : Set password for administrator user "Developer Test User"] *****
ok: [localhost]

TASK [sg_grid : Check if user "Storage Test User" exists] **********************
skipping: [localhost]

TASK [sg_grid : Create new administrator user "Storage Test User"] *************
skipping: [localhost]

TASK [sg_grid : Set password for administrator user "Storage Test User"] *******
skipping: [localhost]

TASK [sg_grid : Check if group "Storage" exists] *******************************
ok: [localhost]

TASK [sg_grid : Create new administrators "Storage" group] *********************
ok: [localhost]

TASK [sg_grid : Create users part of the group] ********************************
included: /root/.ansible/collections/ansible_collections/adlytaibi/sgws/roles/sg_grid/tasks/sgws_users.yml for localhost

TASK [sg_grid : Load local admin users' variables] *****************************
ok: [localhost]

TASK [sg_grid : Create and set password for admin users] ***********************
included: /root/.ansible/collections/ansible_collections/adlytaibi/sgws/roles/sg_grid/tasks/sgws_user.yml for localhost
included: /root/.ansible/collections/ansible_collections/adlytaibi/sgws/roles/sg_grid/tasks/sgws_user.yml for localhost

TASK [sg_grid : Check if user "Developer Test User" exists] ********************
skipping: [localhost]

TASK [sg_grid : Create new administrator user "Developer Test User"] ***********
skipping: [localhost]

TASK [sg_grid : Set password for administrator user "Developer Test User"] *****
skipping: [localhost]

TASK [sg_grid : Check if user "Storage Test User" exists] **********************
ok: [localhost]

TASK [sg_grid : Create new administrator user "Storage Test User"] *************
ok: [localhost]

TASK [sg_grid : Set password for administrator user "Storage Test User"] *******
ok: [localhost]

TASK [sg_tenant : Load account variables] **************************************
ok: [localhost]

TASK [sg_tenant : Gather accounts list] ****************************************
ok: [localhost]

TASK [sg_tenant : Looking if account "Widgets Unlimited" exists] ***************
skipping: [localhost]

TASK [sg_tenant : Create new storage tenant account "Widgets Unlimited"] *******
ok: [localhost]

TASK [sg_tenant : Get org authorization token] *********************************
ok: [localhost]

TASK [sg_tenant : Load tenant's user group variables] **************************
ok: [localhost]

TASK [sg_tenant : Create tenant's user groups and users] ***********************
included: /root/.ansible/collections/ansible_collections/adlytaibi/sgws/roles/sg_tenant/tasks/sgws_org_group.yml for localhost
included: /root/.ansible/collections/ansible_collections/adlytaibi/sgws/roles/sg_tenant/tasks/sgws_org_group.yml for localhost

TASK [sg_tenant : Check if tenant's group "Developers" exists] *****************
ok: [localhost]

TASK [sg_tenant : Create new tenant's "Developers" group] **********************
ok: [localhost]

TASK [sg_tenant : Create users part of the group] ******************************
included: /root/.ansible/collections/ansible_collections/adlytaibi/sgws/roles/sg_tenant/tasks/sgws_org_users.yml for localhost

TASK [sg_tenant : Load local tenant's users' variables] ************************
ok: [localhost]

TASK [sg_tenant : Create and set password for tenant's users] ******************
included: /root/.ansible/collections/ansible_collections/adlytaibi/sgws/roles/sg_tenant/tasks/sgws_org_user.yml for localhost
included: /root/.ansible/collections/ansible_collections/adlytaibi/sgws/roles/sg_tenant/tasks/sgws_org_user.yml for localhost

TASK [sg_tenant : Check if user "Developer Test User" exists] ******************
ok: [localhost]

TASK [sg_tenant : Create new tenant user "Developer Test User"] ****************
ok: [localhost]

TASK [sg_tenant : Set password for tenant user "Developer Test User"] **********
ok: [localhost]

TASK [sg_tenant : Check if user "Apps Test User" exists] ***********************
skipping: [localhost]

TASK [sg_tenant : Create new tenant user "Apps Test User"] *********************
skipping: [localhost]

TASK [sg_tenant : Set password for tenant user "Apps Test User"] ***************
skipping: [localhost]

TASK [sg_tenant : Check if tenant's group "Apps" exists] ***********************
ok: [localhost]

TASK [sg_tenant : Create new tenant's "Apps" group] ****************************
ok: [localhost]

TASK [sg_tenant : Create users part of the group] ******************************
included: /root/.ansible/collections/ansible_collections/adlytaibi/sgws/roles/sg_tenant/tasks/sgws_org_users.yml for localhost

TASK [sg_tenant : Load local tenant's users' variables] ************************
ok: [localhost]

TASK [sg_tenant : Create and set password for tenant's users] ******************
included: /root/.ansible/collections/ansible_collections/adlytaibi/sgws/roles/sg_tenant/tasks/sgws_org_user.yml for localhost
included: /root/.ansible/collections/ansible_collections/adlytaibi/sgws/roles/sg_tenant/tasks/sgws_org_user.yml for localhost

TASK [sg_tenant : Check if user "Developer Test User" exists] ******************
skipping: [localhost]

TASK [sg_tenant : Create new tenant user "Developer Test User"] ****************
skipping: [localhost]

TASK [sg_tenant : Set password for tenant user "Developer Test User"] **********
skipping: [localhost]

TASK [sg_tenant : Check if user "Apps Test User" exists] ***********************
ok: [localhost]

TASK [sg_tenant : Create new tenant user "Apps Test User"] *********************
ok: [localhost]

TASK [sg_tenant : Set password for tenant user "Apps Test User"] ***************
ok: [localhost]

TASK [sg_tenant : Load S3 access key for tenant variables] *********************
ok: [localhost]

TASK [sg_tenant : Check if an S3 key exists] ***********************************
ok: [localhost]

TASK [sg_tenant : Generate S3 keys] ********************************************
ok: [localhost]

TASK [sg_tenant : Destination directory to store S3 keys] **********************
changed: [localhost]

TASK [sg_tenant : Save generated S3 keys] **************************************
changed: [localhost]

TASK [sg_tenant : S3 keys file] ************************************************
ok: [localhost] => {
    "msg": "/root/s3keys/86239829431473757511_root_4H595K94Y1FWYU7YGJCG"
}

TASK [sg_tenant : Load bucket for tenant variables] ****************************
ok: [localhost]

TASK [sg_tenant : Check if bucket "bucket-1" exists within tenant] *************
ok: [localhost]

TASK [sg_tenant : Create bucket "bucket-1"] ************************************
ok: [localhost]

PLAY RECAP *********************************************************************
localhost                  : ok=58   changed=2    unreachable=0    failed=0    skipped=13   rescued=0    ignored=0
```

Using ansible-vault
-------------------

Feel free to make it your own, here is only to get you started.
The file `~/.passwd` contains the vault password.

```bash
# echo -n NetApp123 > ~/.passwd
```

Using the above vault password, you can encrypt any value and integrate into `yml` files.

```bash
# echo -n netapp01|ansible-vault encrypt --vault-password-file ~/.passwd > vault.txt
# cat vault.txt
$ANSIBLE_VAULT;1.1;AES256
62383435316236346137383364373438623661653665363939616231376464643762333364663733
3430313661363761386536373135663733323764666561630a383736363562363864393037646531
61383566333332356339376561336239396634626433666434306566386134343031653339333531
3964356263643134610a346138616566643533346365326662343762346432386563393331306239
3835
```

If you wish to verify the encrypted values.

```bash
# cat vault.txt|ansible-vault decrypt --vault-password-file ~/.passwd
Decryption successful
netapp01
```

Helper Scripts
--------------

The scripts directory located `~/.ansible/collections/ansible_collections/adlytaibi/sgws/files/scripts` contains mgmt_ssl.cnf and stor_ssl.cnf files for your convenience, modify them to match your Grid management interface and your Gateway accordingly.
Change directory to where the SSL keys will be stored. Execute the two scripts that will create a private key and CSR.

```bash
# cd ~/.ansible/collections/ansible_collections/adlytaibi/sgws/files/
# scripts/mgmt_sign
# scripts/stor_sign
```

After you sign the SSL certificates, make sure you rename them to mgmt_cert.pem and stor_cert.pem accordingly and copy them to `~/.ansible/collections/ansible_collections/adlytaibi/sgws/roles/sg_grid/files` directory along with the chain bundle `certnew.p7b`. You can now execute `bundle` script that will convert the chain from p7b to pem.

```bash
# scripts/bundle
```

These are the files that will be used by Ansible.

```bash
# chain.pem      CA bundle
# mgmt_cert.pem  Signed cert for API management
# mgmt_cert.key  Private key for the above cert
# stor_cert.pem  Signed cert for Storage management
# stor_cert.key  Private key for the above cert
```

License
-------

GPL

Author Information
------------------

- Adly Taibi

