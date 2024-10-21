# freebsd_mysql

![](https://i.imgur.com/waxVImv.png)
### [View all Roadmaps](https://github.com/nholuongut/all-roadmaps) &nbsp;&middot;&nbsp; [Best Practices](https://github.com/nholuongut/all-roadmaps/blob/main/public/best-practices/) &nbsp;&middot;&nbsp; [Questions](https://www.linkedin.com/in/nholuong/)
<br/>


## Requirements

### Roles

* nholuong.ansible_lib

### Collections

* ansible.posix
* community.general
* community.mysql

### Others

See *defaults/main.yml*


## Role Variables

* See *defaults/main.yml* and examples in *vars/main.yml*
* MySQL version less then 5.7 needs the file *bsd_mysql_secret_local_file* with the root password
* By default the server is disabled *bsd_mysql_enable: False*
* freebsd_flavors_enable (default: False). This variable enables the flavors stored in
  *freebsd_flavors*. By default the flavors are disabled. This means that the default flavors from
  */etc/make.conf* will be installed. Enable this variable only if
  *freebsd_install_method=packages*. Ports won't recognize the flavors and the installation will
  crash. See the variables *bsd_mysql_packages* in *defaults/main.yml*.


## Workflow

1) Change shell to /bin/sh

```
shell> ansible dbserver -e 'ansible_shell_type=csh ansible_shell_executable=/bin/csh' \
       -a 'sudo pw usermod freebsd -s /bin/sh'
```

2) Install the role and collections

```
shell> ansible-galaxy role install nholuong.freebsd_mysql
shell> ansible-galaxy collection install community.general
shell> ansible-galaxy collection install community.mysql
shell> ansible-galaxy collection install ansible.posix
```

3) Fit variables, e.g. in vars/main.yml

```
shell> editor nholuong.freebsd_mysql/vars/main.yml
```

4) Create playbook and inventory

```
shell> cat mysql.yml

- hosts: dbserver
  roles:
    - nholuong.freebsd_mysql
```

```
shell> cat hosts
[dbserver]
<SERVER1-IP-OR-FQDN>
<SERVER2-IP-OR-FQDN>
[dbserver:vars]
ansible_connection=ssh
ansible_user=freebsd
ansible_become=yes
ansible_become_user=root
ansible_become_method=sudo
ansible_python_interpreter=/usr/local/bin/python3.7
ansible_perl_interpreter=/usr/local/bin/perl
```

5) Install and configure MySQL

In development, test the role step by step


* Create directories and files

```
shell> ansible-playbook mysql.yml -t bsd_mysql_directories
shell> ansible-playbook mysql.yml -t bsd_mysql_files
```

* Test sanity

```
shell> ansible-playbook mysql.yml -t bsd_mysql_sanity
```

* Review variables

```
shell> ansible-playbook mysql.yml -t bsd_mysql_debug -e bsd_mysql_debug=true
```

* Install packages

```
shell> ansible-playbook mysql.yml -t bsd_mysql_packages
```

* Apply patches

Review patches in the *file* directory and fit the variable *bsd_mysql_patches* to your
needs. Default v80 is secure initialization (--initialize) and output of *mysql_create_auth_tables*
to console.

```
shell>  ansible-playbook mysql.yml -t bsd_mysql_patches -e bsd_mysql_patch_backup=true
```

* Configure mycnf.yml

```
shell> ansible-playbook freebsd-mysql.yml -t bsd_mysql_mycnf -e bsd_mysql_conf_backup=true
```

* Configure rc.conf

Review the list of options *bsd_mysql_rcconf*. Default for version 80 is *mysql_args: "--skip-new
--log-error"*. MySQL should be enabled *bsd_mysql_enable: true*. The service should start,
initialize databases and create temporary root password. Optionally see the log
*/var/db/mysql/${hostname}.err*.

```
shell> ansible-playbook freebsd-mysql.yml -t bsd_mysql_rcconf -e bsd_mysql_conf_backup=true
```

* Test existence of files that contain temporary root password

This task is tagged *never* and shall be run on demand only to test the temporary root password

```
shell> ansible-playbook mysql.yml -t bsd_mysql_assert
```

If the test fails find the HOWTO sections in the message.

* Change root password

Store temporary root password in the local file. Change the root password to
*bsd_mysql_secret*. This task is tagged *never* and shall be run on demand only to change the
temporary root password

```
shell> ansible-playbook mysql.yml -t bsd_mysql_secret
```

* Run check mode and show the differences

```
shell> ansible-playbook mysql.yml -CD
```

* Execute the playbook

```
shell> ansible-playbook mysql.yml
```

* Execute the playbook again. Test the playbook is idempotent.

```
shell> ansible-playbook mysql.yml
```

To reconfigure any parameter it's sufficient to select particular tasks by tags when the server is
running. The role should be idempotent. It's possible to repeatedly run the whole role if necessary.


# 🚀 I'm are always open to your feedback.  Please contact as bellow information:
### [Contact ]
* [Name: nho Luong]
* [Skype](luongutnho_skype)
* [Github](https://github.com/nholuongut/)
* [Linkedin](https://www.linkedin.com/in/nholuong/)
* [Email Address](luongutnho@hotmail.com)

![](https://i.imgur.com/waxVImv.png)
![](Donate.png)
[![ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/nholuong)

# License
* Nho Luong (c). All Rights Reserved.🌟
