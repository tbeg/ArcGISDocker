### ArcGIS Data Store on CentOS 6.7
This Image starts with a c67x11 and adds ArcGIS Data Store

- Copied Installation Media and License to build server to /home/ags and set ags as owner
- Extracted (tar xvzf) the Installation Media
- ./Setup -m silent -l yes 
- Installed in default folder
- tar the installation folder
  - # cd /home/ags
  - # tar pcvzf /root/c67ds/arcgis_datastore.tar.gz arcgis/datastore
- docker build -t c67ds .

- docker save -o c67ds.tar c67ds
- gzip c67ds.tar.gz

- Copy the gz file to the server you want to install on

- gunzip c67ds.tar.gz
- docker load -i c67ds.tar

- docker run -it -p 2443:2443 -p 9876:9876 --name ags --hostname $HOSTNAME c67ds /bin/bash
  - Manage as needed
  - exit 
- docker start ds
- docker exec ags /home/ags/arcgis/datastore/startdatastore.sh
- docker exec -it ags /bin/bash





