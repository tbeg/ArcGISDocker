### ArcGIS Portal on CentOS 6.7
This Image starts with a c67x11 and adds ArcGIS Portal

- Copied Installation Media and License to build server to /home/ags and set ags as owner
- Extracted (tar xvzf) the Installation Media
- ./Setup -m silent -l yes -a /home/ags/LicenseFile  
- Installed in default folder
- tar the installation folder
  - # cd /home/ags
  - # tar pcvzf /root/c67wa/arcgis_portal.tar.gz arcgis/portal
- docker build -t c67prtl .

- docker save -o c67prtl.tar c67prtl
- gzip c67prtl.tar.gz

- Copy the gz file to the server you want to install on

- gunzip c67prtl.tar.gz
- docker load -i c67prtl.tar

- docker run -it -p 7080:7080 -p 7443:7443 --name ags --hostname $HOSTNAME c67prtl /bin/bash
  - Manage as needed
  - exit 
- docker start prtl
- docker exec ags /home/ags/arcgis/portal/startportal.sh
- docker exec -it ags /bin/bash





