FROM centos:7

# Install FTS
RUN yum install -y epel-release.noarch
RUN curl https://fts-repo.web.cern.ch/fts-repo/fts3-prod-el7.repo > /etc/yum.repos.d/fts3-prod-el7.repo
RUN curl https://dmc-repo.web.cern.ch/dmc-repo/dmc-el7.repo > /etc/yum.repos.d/dmc-el7.repo
RUN yum upgrade -y
RUN yum install -y git mysql multitail gfal2-plugin* fts-server fts-client fts-rest fts-monitoring fts-mysql fts-msg
RUN yum clean all

# Setup FTS security
RUN curl -O http://uaf-10.t2.ucsd.edu/~aaarora/packages/osg-ca-generator-1.5.0.tar.gz && \
    tar -xvf osg-ca-generator-1.5.0.tar.gz && \
    cd osg-ca-generator-1.5.0 && \
    make install && \
    ln -s /usr/local/bin/python3.8 /usr/bin/python3

# Database configuration for FTS server
RUN chmod +x /usr/share/fts/fts-database-upgrade.py

# Configuration for FTSREST and FTSMON
COPY fts3rest.conf /etc/httpd/conf.d/fts3rest.conf
RUN echo "" > /etc/httpd/conf.d/ssl.conf &&\
    echo "" > /etc/httpd/conf.d/autoindex.conf &&\
    echo "" > /etc/httpd/conf.d/userdir.conf &&\
    echo "" > /etc/httpd/conf.d/welcome.conf &&\
    echo "" > /etc/httpd/conf.d/zgridsite.conf

# FTS monitoring ActiveMQ configuration
COPY fts-msg-monitoring.conf /etc/fts3/fts-msg-monitoring.conf

# Entrypoint waiting script for MySQL
COPY wait-for-it.sh /usr/local/bin/wait-for-it.sh
RUN chmod +x /usr/local/bin/wait-for-it.sh

# Shortcut for logfiles
COPY logshow /usr/local/bin/logshow
RUN chmod +x /usr/local/bin/logshow
RUN touch /var/log/fts3/fts3server.log
RUN chown -R fts3:fts3 /var/log/fts3/fts3server.log
RUN touch /var/log/fts3rest/fts3rest.log
RUN chown -R fts3:fts3 /var/log/fts3rest

# Startup
COPY startup.sh /usr/local/sbin/
CMD ["/usr/local/sbin/startup.sh"]
