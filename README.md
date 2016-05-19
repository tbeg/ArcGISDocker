### These c67 folders are WebGIS Docker Images using CentOS 6.7 

Started with a Docker Build VM (VirtualBox)

* 4GB RAM, 100GB HD, 2CPU, 2 Network (NAT and  HostOnly)
* Di not name the server (use default localhost.localdomain)
* Min install of CentOS 6.7  (This version is fully supported by ArcGIS Products)
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
- Sometimes when working (especially with Marathon/Mesos) you'll get dozens of Docker containers. You can list them all with "docker ps -a".  You can remove all containers with "docker rm $(docker ps -a -q)".  If you want to just remove containers associated with a specific image. For example all containers build from image c67ags you can run this "docker rm $(docker ps -a | grep c67ags | awk '{ print $1 }') 


### Installation 
#### Accessing 

Add entries to your hosts file



127.0.0.1       localhost ip-10-0-3-198.us-west-1.compute.internal ags.compute.internal prtl.compute.internal ds.compute.internal


sudo ssh -i RicardoDCOSCluster.pem -L 6443:10.0.3.198:6443 -L 7443:10.0.3.198:7443 -L 80:10.0.3.198:80 -L 443:10.0.3.198:443 core@52.53.224.120


ArcGIS Server: 
https://ip-10-0-3-198.us-west-1.compute.internal/arcgis/manager
https://ip-10-0-3-198.us-west-1.compute.internal/arcgis/admin
https://ip-10-0-3-198.us-west-1.compute.internal/arcgis/rest

Portal: https://ip-10-0-3-198.us-west-1.compute.internal/portal/home


#### Installation and Setup 
The following was used to deploy and configure the docker images on 10.0.3.198.

Moved the tar.gz files up to server /var/lib/core <<  Creatd this folder as root and set core as owner

/var/lib parition had about 30G of space.  The other partitions are too small.

gunzip each tar.gz
docker load -i file.tar 
rm file.tar   

It was necessary to remove the tar files from 10.0.3.198 otherwise the partition would be very full.


c67ags << ArcGIS Server
c67wa  << Web Adaptor with Apache Tomcat and Apache HTTP
c67ds << ArcGIS Data Store
c67prtl << ArcGIS Portal



#### Web Adaptor (wa)

docker run -it -p 80:80 -p 443:443 -p 8080:8080 --name wa --hostname $HOSTNAME c67wa

hostname
ip-10-0-3-198.us-west-1.compute.internal

su -

openssl req -x509 -nodes -newkey rsa:2048 -keyout server.key -out server.crt -days 365
 Generating a 2048 bit RSA private key
 ..........................+++
 ..............................+++
 writing new private key to 'server.key'
 -----
 You are about to be asked to enter information that will be incorporated
 into your certificate request.
 What you are about to enter is what is called a Distinguished Name or a DN.
 There are quite a few fields but you can leave some blank
 For some fields there will be a default value,
 If you enter '.', the field will be left blank.
 -----
 Country Name (2 letter code) [XX]:US
 State or Province Name (full name) []:CA
 Locality Name (eg, city) [Default City]:Redlands
 Organization Name (eg, company) [Default Company Ltd]:Esri
 Organizational Unit Name (eg, section) []:PS
 Common Name (eg, your name or your server's hostname) []:ip-10-0-3-198.us-west-1.compute.internal
 Email Address []:david@yourcompany.com

cp server.key /etc/pki/tls/private/
chmod 600 /etc/pki/tls/private/server.key 
cp server.crt /etc/pki/tls/certs/
chmod 600 /etc/pki/tls/certs/server.crt

chkconfig httpd on
service httpd start


vi /etc/httpd/conf.d/proxy.conf

ProxyRequests off
ProxyPass /arcgis ajp://localhost:8009/arcgis
ProxyPassReverse /arcgis ajp://localhost:8009/arcgis
ProxyPass /portal ajp://localhost:8009/portal
ProxyPassReverse /portal ajp://localhost:8009/portal

:wq


service httpd restart

exit
exit

docker start wa

To start/stop tomcat
docker exec wa /opt/tomcat/bin/startup.sh
docker exec wa /opt/tomcat/bin/shutdown.sh

To log back in:
docker exec -it wa /bin/bash

#### ArcGIS Server (ags)
docker run -it -p 6080:6080 -p 6443:6443 --name ags --hostname ags.compute.internal c67ags

su -

vi /etc/hosts
Add
10.0.3.198      ds.compute.internal prtl.compute.internal
:wq


Create AGS Site

Update connection to AWS

On your computer update /etc/hosts


127.0.0.1       localhost ip-10-0-3-198.us-west-1.compute.internal ags.compute.internal prtl.compute.internal ds.compute.internal


sudo ssh -i RicardoDCOSCluster.pem -L 6443:10.0.3.198:6443 -L 7443:10.0.3.198:7443 -L 80:10.0.3.198:80 -L 443:10.0.3.198:443 core@52.53.224.120


From Browser on local workstation
https://ip-10-0-3-198.us-west-1.compute.internal:6443/arcgis/manager

Create New Site

siteadmin/siteadmin



Deploy Wa

docker exec -it wa /bin/bash

su - 

vi /etc/hosts
Add
10.0.3.198 ags.compute.internal prtl.compute.internal ds.compute.internal
:wq

exit


cp /home/ags/arcgis/webadaptor10.4/java/arcgis.war /opt/tomcat/webapps/

/opt/tomcat/bin/startup.sh 
Using CATALINA_BASE:   /opt/tomcat
Using CATALINA_HOME:   /opt/tomcat
Using CATALINA_TMPDIR: /opt/tomcat/temp
Using JRE_HOME:        /usr
Using CLASSPATH:       /opt/tomcat/bin/bootstrap.jar:/opt/tomcat/bin/tomcat-juli.jar
Tomcat started.


cd /home/ags/arcgis/webadaptor10.4/java/tools/


./configurewebadaptor.sh -m server -w http://ip-10-0-3-198.us-west-1.compute.internal/arcgis/webadaptor -g https://ags.compute.internal:6443 -u siteadmin -p siteadmin -a true


Successfully Registered.


You should now be able to access site via:

https://ip-10-0-3-198.us-west-1.compute.internal/arcgis/rest/


#### ArcGIS Data Store (ds)


docker run -it -p 2443:2443 -p 9876:9876 --name ds --hostname ds.compute.internal c67ds


exit

docker start ds

docker exec -it ds /bin/bash

su -
vi /etc/hosts
add
10.0.3.198 ags.compute.internal prtl.compute.internal
:wq

exit



/home/ags/arcgis/datastore/startdatastore.sh

/home/ags/arcgis/datastore/tools/configuredatastore.sh https://ags.compute.internal:6443/arcgis/admin siteadmin siteadmin /home/ags/arcgis/data --stores spatiotemporal


#### ArcGIS Portal (prtl)
docker run -it -p 7080:7080 -p 7443:7443 --name prtl --hostname prtl.compute.internal c67prtl

su -

vi /etc/hosts
Add
10.0.3.198      ags.compute.internal ds.compute.internal
:wq

exit
exit

docker start prtl

docker exec prtl /home/ags/arcgis/portal/startportal.sh

From Browser on Workstation

https://prtl.compute.internal:7443

Export the certificate 

Create New Portal
siteadmin/siteadmin
Fav Book: Hobbit
Name: David 
email: david@yourcompany.com


Deploy wa for portal

docker exec -it wa /bin/bash

cp /home/ags/arcgis/webadaptor10.4/java/arcgis.war /opt/tomcat/webapps/portal.war


cd /home/ags/arcgis/webadaptor10.4/java/tools/


./configurewebadaptor.sh -m portal -w https://ip-10-0-3-198.us-west-1.compute.internal/portal/webadaptor -g https://prtl.compute.internal:7443 -u siteadmin -p siteadmin -a true


Successfully Registered.


You should now be able to access site via:

https://ip-10-0-3-198.us-west-1.compute.internal/portal/home

Registed https://ip-10-0-3-198.us-west-1.compute.internal as Federated Server.
- Logged into https://ip-10-0-3-198.us-west-1.compute.internal/portal/home
- Navidated to My Organization -> Servers (Edit Settings)
- Used https://ip-10-0-3-198.us-west-1.compute.internal/arcgis https://ip-10-0-3-198.us-west-1.compute.internal:6443/arcgis for AGS 

Imported Portal Certificate into AGS
- https://ip-10-0-3-198.us-west-1.compute.internal:6443/arcgis/admin (login)
- Navigate to Machine -> AGS.COMPUTE.INTERNAL -> ssl certificates
- Added the certificate exported from 7443 earlier; give it a name (e.g. prtl 7443)






