FROM centos:6.7

# Add non-root user; this should be same user as on Build Machine
ENV USER=ags USERDIR=/home/ags

# Set userid same as Build Machine
RUN useradd -u 501 -U -G wheel $USER; \
mkdir $USERDIR/arcgis; \
chown ags. $USERDIR/arcgis; \
yum install -y java-1.8.0-openjdk iproute wget which httpd mod_ssl;

ADD ./arcgis_wa.tar.gz $USERDIR
ADD ./tomcat.tar.gz /opt

RUN echo 'root:docker' | chpasswd;\
echo 'ags:docker' | chpasswd

EXPOSE 8080

# Switch to non-root user
USER $USER 

