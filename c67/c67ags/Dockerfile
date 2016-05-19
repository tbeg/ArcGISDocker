FROM david62243/c67x11

# On Build Machine Install ArcGIS Server

# Add non-root user; this should be same user as on Build Machine
ENV USER=ags USERDIR=/home/ags

# Set userid same as Build Machine
RUN useradd -u 501 -U -G wheel $USER; \
mkdir $USERDIR/arcgis; \
chown ags. $USERDIR/arcgis;

COPY ./_ESRI.properties $USERDIR/.ESRI.properties.10.4
ADD ./arcgis_server.tar.gz $USERDIR

RUN echo 'root:docker' | chpasswd;\
echo 'ags:docker' | chpasswd

EXPOSE 6080 6443 4004 4001 4002

# Switch to non-root user
USER $USER 

# Start Server and Portal services
#ENTRYPOINT /arcgis/server/startserver.sh && tail -f /arcgis/server/usr/logs/LOCALHOST/server/*.log


