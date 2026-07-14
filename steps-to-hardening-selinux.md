### what is Security Enhanced Linux (SELinux) ?
it provides an additional layer of system security. its operation logic is May <subject> do <action> to <object>?
for example ; may a web serer access files in users'home directories? 

SElinux operates in three modes.

Enforcing : 

Permissive: 

Disable: 

Meanwhile in RHEL, Fedora and CentOS 8 SElinux comes in enforcing mode by default.
check the SElinux mode run: 
```bash
sestatus
```
you will see like this output :
```bash
SELinux status:                 enabled
SELinuxfs mount:                /sys/fs/selinux
SELinux root directory:         /etc/selinux
Loaded policy name:             targeted
Current mode:                   enforcing
Mode from config file:          enforcing
Policy MLS status:              enabled
Policy deny_unknown status:     allowed
Max kernel policy version:      31
```

if you wanna tempoarily or permanently disable selinux , when temporarily disabled it willl be re-enabled system restart. when permanently disabled you will need to re-enable it.

setenforce 0 


targeted policy (kernel_t)


<img width="535" height="149" alt="image" src="https://github.com/user-attachments/assets/843faf51-76a9-49b0-9489-4c62f08f43ee" />

ls -Z /var/www 

system_u : object_r : httpd_sys_content_t : s0 html
first block system user
second object role 
last block s0 sensivity(mls)

configuration files in the etc directory. 
