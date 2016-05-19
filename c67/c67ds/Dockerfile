FROM c67x11

# On Build Machine Install ArcGIS Server

# Add non-root user; this should be same user as on Build Machine
ENV USER=ags USERDIR=/home/ags

# Set userid same as Build Machine
RUN useradd -u 501 -U -G wheel $USER; \
mkdir $USERDIR/arcgis; \
chown ags. $USERDIR/arcgis;

ADD ./arcgis_datastore.tar.gz $USERDIR

RUN echo 'root:docker' | chpasswd;\
echo 'ags:docker' | chpasswd

EXPOSE 2443 9876

# Switch to non-root user
USER $USER 

