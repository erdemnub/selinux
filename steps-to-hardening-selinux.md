### what is Security Enhanced Linux (SELinux) ?
it provides an additional layer of system security. its operation logic is May <subject> do <action> to <object>?
for example ; may a web serer access files in users'home directories? 

SElinux operates in three modes.

**Enforcing** : the highest security level on selinux. any unauthorized access or action that violates the policy is immediately blocked.
  
-- all blocked actions are recorded in the systems logs (/var/log/audit)
this mode is default, most secure , and officially recommended mode for production environments.


**Permissive**: this policy is not block any actions. 
any activity that would normally be blocked in Enforcing mode is allowed to run but a warning/log is entry is created. 
--This mode is primarily used for troubleshooting, debugging application errors or developing new security rules.

**Disable**: the security mechanism is comletely turned of at the kernel level. No rules are checked no security violations are logged. 

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
to this tempoarily change mode to selinux : 
```bash
setenforce 0 
```
> it will change your selinux policy to  "permissive"

if you want hardening selinux policy full protection you have to run : 
```bash
setenforce 1
```

to permanently disable or change selinux policy follow this steps:

(if required sudo) {nano /etc/sysconfig/selinux} 
> SELINUX=disabled (save and exit)
> reboot


run :
```bash
sestatus
```
we can check the status of selinux when your system up.
```bash
selinux status : disabled
```

**Process analyzing**
First, we have to see running programs(process) using the this command run:

```bash
ps -auxZ
```
this output: 
```bash
system_u : system_r : kernel_t : s0
```

system_u (**user**): shows that this process belongs to the system backend
system_r (**role**) the _r means **role**. it proves this is a running system service or process.
kernel_t (**domain**) the _t stands for **type** but when appliedd to a process it is called a domain. 
s0 (**sensivity): the base multi-level security clearance. 






targeted policy (kernel_t)


<img width="535" height="149" alt="image" src="https://github.com/user-attachments/assets/843faf51-76a9-49b0-9489-4c62f08f43ee" />

ls -Z /var/www 

system_u : object_r : httpd_sys_content_t : s0 html
first block system user
second object role 
last block s0 sensivity(mls)

configuration files in the etc directory. 



**SYSADMİNTİP MANAGENİNG NETWORK PORTS **

network ports also have a context.
```bash
semanage port -l
```

for example the see http ports 
```bash
semanage port -l | grep http
<img width="657" height="144" alt="image" src="https://github.com/user-attachments/assets/587d5602-7fae-48cc-a451-46f17b1dfa5e" />
```

if you starting up apache nginx , it could attach to ports 80,81,443 488,8008,8009,8443,9000. 

**all ports below 1024 (such as 80, 443, 22, etc.) can only be opened by the "root" (the highest-level administrator) user.**
The encrypted (HTTPS) equivalent of 8080 is also usually port 8443.

http_cache_port (like nginx reverse proxy.{8080,8118,8123,10001-10010}). 

**how to grant selinux permissions to an app without any permissive mode? **
- method 1 using selinux have built-in on/off switches.
Common scenarios; if your front-end Nginx needs to pass traffic to your back-end Apache, you don't turn off SELinux

```bash
# allow nginx/apache to connect to backend services over the network (Required for Reverse Proxy)
sudo setsebool -P httpd_can_network_connect 1
# Allow your web server to connect to a database (MySQL/PostgreSQL)
sudo setsebool -P httpd_can_network_connect_db 1

# The -P flag makes the setting permanent so it survives a system reboot.
```

If your application (like a Node.js or Python app) functions as a web service but wants to listen on a non-standard port like 5000, SELinux will block it by default. Instead of going permissive, you add that port directly to the web server rulebook:

```bash
# Legally add port 5000 to the web server (http_port_t) group
sudo semanage port -a -t http_port_t -p tcp 5000
```
**Generating Custom Policies from Logs**

if your application is doing something unique and you keep getting "Permission Denied" errors, those blocks are recorded in the security logs. You can scan these logs and generate a custom, tailor-made security policy for that specific app:

Review what SELinux blocked and see the suggested rule
sudo ausearch -c 'your_app_name' --raw | audit2allow -m my_custom_rule

Compile the exact block into a permanent policy module packet 
sudo ausearch -c 'your_app_name' --raw | audit2allow -M my_custom_policy
 
Inject this custom policy module back into the system:
sudo semodule -i my_custom_policy.pp


Frequently asked questions (FAQ)
-if selinux is disabled, is system security completely at risk? 
-Yes when selinux is disabled, kernel-level access controls for applicatons are removed.Therefore, it is recommended to disable it only in necessary situations.

what is difference between selinux and apparmor?
-selinux comes by default red hat and centos- based systems, while apparmor is mor common using by debian/ubuntu systems. 

















