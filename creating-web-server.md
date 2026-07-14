hello!

```bash
sestatus
# if you are using centos, rhel , fedora [enforced mode] has already enabled.
# we will make only firewall settings to up the apache web server.
 

dnf install httpd -y | systemctl enable --now httpd

# creating html codes.
echo <h1> web server test page ! </h1> > index.html

ls -Z index.html
# to this check selinux security context. not should be admin_home_t

mv index.html /var/www/html
#moving this file to www folders.

# we have to change file type for selinux policies.

restorecon -v 
