# Clone from the CentOS 7
FROM centos:centos6

MAINTAINER Avichal Badaya

RUN yum -y clean all
RUN yum -y install mod_ssl
RUN yum -y install crypto-utils
#RUN yum -y install mod_security 
RUN yum -y install git 
RUN yum -y install sudo
RUN yum -y install wget
RUN yum -y install ntp.x86_64
RUN yum groupinstall -y 'development tools'
RUN yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel
RUN yum -y install tar
RUN cd /tmp && wget http://python.org/ftp/python/2.7.6/Python-2.7.6.tar.xz && tar xf Python-2.7.6.tar.xz && cd Python-2.7.6 && ./configure --prefix=/usr/local --enable-unicode=ucs4 --enable-shared LDFLAGS="-Wl,-rpath /usr/local/lib" && make && make altinstall
RUN cd /tmp && wget https://bitbucket.org/pypa/setuptools/raw/bootstrap/ez_setup.py && python2.7 ez_setup.py
RUN easy_install-2.7 virtualenv
RUN cd /opt && mkdir Project && cd Project && virtualenv . && . bin/activate && pip install mezzanine && mezzanine-project project_name && cd project_name && python manage.py createdb --noinput
ADD settings.py /opt/Project/project_name/project_name/
RUN cd /tmp && wget http://nginx.org/packages/centos/6/noarch/RPMS/nginx-release-centos-6-0.el6.ngx.noarch.rpm && rpm -ivh nginx-release-centos-6-0.el6.ngx.noarch.rpm
RUN yum -y install nginx
COPY nginx.conf /etc/nginx/nginx.conf
RUN chkconfig nginx on
RUN service nginx restart
EXPOSE 80
EXPOSE 22
EXPOSE 8000
CMD cd /opt/Project/ && . bin/activate  && cd project_name/ && python manage.py runserver 0.0.0.0:80 && tail -F /opt/Project/access.log
