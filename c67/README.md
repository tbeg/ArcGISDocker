### These c67 folders are WebGIS Docker Images using CentOS 6.7 

Started with a Docker Build VM (VirtualBox)

* 4GB RAM, 100GB HD, 2CPU, 2 Network (NAT and  HostOnly)
* Di not name the server (use default localhost.localdomain)
* Min install of CentOS 6.7
* Group install X11
* Install gettext, dos2unix, and wget
* Install gc, perl, kernel-devel to install VirtualBox Guest Additions
* Install system-config-network-tui and system-config-firewall-tui
* Install epel-release and docker-io (This is Docker)

Created a ags user 
* Followed instruction like http://davidssysadminnotes.blogspot.com/2015/12/installing-esri-stack-for-geoevent-linux.html
* ArcGIS User (ags)
* Set Handles and Processes Limits for ags




### NOTES:
- Using tar with permissions results in a much smaller image. The image adding the folder then using chmod and chown to ags was over 9GB; whereas, using tar the image was 6GB
- The initial tar takes some time; however, building the docker image appears to be quicker than using the untarred folder.
- You'll also need to copy /home/ags/.ESRI.properities.localhost.localdomain.10.4 file to the docker folder; renamed to _Esri.properties
- In the Dockerfile set the userid "501" to match the user id on your build machine (cat /etc/passwd)


