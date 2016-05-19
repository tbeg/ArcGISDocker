### ArcGIS Server on CentOS 6.7
This Image starts with a c67x11 and adds ArcGIS Server.

- Copied Installation Media and License to build server to /home/ags and set ags as owner
- Extracted (tar xvzf) the Installation Media
- ./Setup -m silent -l yest -a /home/ags/LicenseFile.ecp  
- Installed in default folder
- tar the installation folder
  - # cd /home/ags
  - # tar pcvzf /root/c67wa/arcgis_server.tar.gz arcgis/server
- docker build -t c67ags .

- docker save -o c67ags.tar c67ags
- gzip c67ags.tar.gz

- Copy the gz file to the server you want to install on

- gunzip c67ags.tar.gz
- docker load -i c67ags.tar

- docker run -it -p 6443:6443 --name ags --hostname $HOSTNAME c67ags /bin/bash
  - Manage as needed
  - exit 
- docker start ags
- docker exec ags /home/ags/arcgis/server/startserver.sh
- docker exec -it ags /bin/bash




