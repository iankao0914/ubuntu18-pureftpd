FROM ubuntu:18.04
MAINTAINER Ian Kao <m94720065@gmail.com.tw>
ARG DEBIAN_FRONTEND=noninteractive
\
# Change the source list and install common pkgs.
RUN cp /etc/apt/sources.list /etc/apt/sources.list.bak && \
  sed -i 's/archive.ubuntu.com/free.nchc.org.tw/g' /etc/apt/sources.list && \
  sed -i 's/# deb-src/deb-src/g' /etc/apt/sources.list && \
  dpkg-divert --local --rename --add /sbin/initctl && \
  ln -sf /bin/true /sbin/initctl && \
  apt-get update && \
  apt-get install -y --no-install-recommends \
    apt-utils \
    cron \
    locales \
    vim \
    ssh \
    snmp \
    curl \
    openssl \
    ca-cacert \
    net-tools \
    iputils-ping \
    traceroute \
    rsyslog \
    dpkg-dev \
    debhelper \
    openbsd-inetd \
    dialog \
    quota \
    rsync \
    sudo \
    supervisor && \
  apt-get clean && \
\
# install pure-ftpd.
  apt-get -y build-dep pure-ftpd && \
  mkdir /tmp/pure-ftpd/ && \
  cd /tmp/pure-ftpd/ && \
  apt-get source pure-ftpd && \
  cd pure-ftpd-* && \
  sed -i '/^optflags=/ s/$/ --without-capabilities --with-tls --with-certfile=\/etc\/ssl\/private\//g' ./debian/rules && \
  dpkg-buildpackage -b -uc && \
  dpkg -i /tmp/pure-ftpd/pure-ftpd-common*.deb && \
  dpkg -i /tmp/pure-ftpd/pure-ftpd_*.deb && \
  apt-mark hold pure-ftpd pure-ftpd-common && \
  apt-get clean && \
\
# Create OS account.
  useradd -m -s /bin/bash ftpadmin && \
  echo 'ftpadmin:changeme' | chpasswd && \
  echo "ftpadmin ALL=(root) ALL" >> /etc/sudoers && \
\
# General settings.
  echo "/bin/false" >> /etc/shells && \
  echo "set number" >> /etc/vimrc && \
  echo "export VISIBLE=now" >> /etc/profile && \
  mkdir /var/run/sshd && \
  sed -ri 's/^#?PermitRootLogin\s+.*/PermitRootLogin no/' /etc/ssh/sshd_config && \
  sed -ri 's/UsePAM yes/#UsePAM yes/g' /etc/ssh/sshd_config && \
  sed 's/session\s*required\s*pam_loginuid.so/session optional pam_loginuid.so/g' -i /etc/pam.d/sshd && \
  sed -i 's/module(load="imklog"/#module(load="imklog"/g' /etc/rsyslog.conf && \
  mkdir -p /root/.ssh && \
  touch /root/.ssh/authorized_keys && \
  chmod 700 /root/.ssh && \
  rm -rf /var/lib/apt/lists/* && \
  rm -f /etc/ssh/ssh_host_rsa_key && \
  rm -f /etc/ssh/ssh_host_rsa_key.pub && \
  ssh-keygen -f /etc/ssh/ssh_host_rsa_key -N '' -t rsa && \
  ssh-keygen -f /etc/ssh/ssh_host_dsa_key -N '' -t dsa && \
  localedef -i zh_TW -c -f UTF-8 -A /usr/share/locale/locale.alias zh_TW.UTF-8
\
ENV LANG zh_TW.utf8
ADD supervisord.conf /etc/supervisor/conf.d/
\
EXPOSE 21-22 40000-40200
CMD ["supervisord", "-n"]