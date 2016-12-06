FROM buildpack-deps:jessie-scm

#================================================
# Customize sources for apt-get
#================================================
#RUN  echo "deb http://archive.ubuntu.com/ubuntu xenial main universe\n" > /etc/apt/sources.list \
#  && echo "deb http://archive.ubuntu.com/ubuntu xenial-updates main universe\n" >> /etc/apt/sources.list \
#  && echo "deb http://archive.ubuntu.com/ubuntu xenial-updates main universe\n" >> /etc/apt/sources.list \
#  && echo "deb http://security.ubuntu.com/ubuntu xenial-security main universe\n" >> /etc/apt/sources.list

RUN  echo "deb http://httpredir.debian.org/debian jessie main\n" > /etc/apt/sources.list \
  && echo "deb http://httpredir.debian.org/debian jessie-updates main\n" >> /etc/apt/sources.list \
  && echo "deb http://httpredir.debian.org/debian jessie-backports main\n" >> /etc/apt/sources.list

#========================
# Miscellaneous packages
#========================
RUN apt-get update -qqy \
  && apt-get -qqy --no-install-recommends --fix-missing install \
    bzip2 \
    curl \
    sudo \
    unzip \
    wget \
    build-essential \
    xvfb \
  && rm -rf /var/lib/apt/lists/* /var/cache/apt/*

# Configuration for XVFB server
RUN rm -f /tmp/.X*lock
# https://github.com/SeleniumHQ/docker-selenium/issues/87
ENV DBUS_SESSION_BUS_ADDRESS=/dev/null

# Default to UTF-8 file.encoding
ENV LANG C.UTF-8

#======
# Java
#======
ENV CA_CERTIFICATES_JAVA_VERSION 20140324
RUN set -x \
	&& apt-get update -qqy \
	&& apt-get -qqy --fix-missing install \
		openjdk-8-jdk \
		ca-certificates-java="$CA_CERTIFICATES_JAVA_VERSION" \
	&& rm -rf /var/lib/apt/lists/* /var/cache/apt/*

RUN /var/lib/dpkg/info/ca-certificates-java.postinst configure

#========
# Galen
#========

ARG GALEN_VERSION=2.3.0
RUN curl -SLO "https://github.com/galenframework/galen/releases/download/galen-$GALEN_VERSION/galen-bin-$GALEN_VERSION.zip" \
	&& unzip "galen-bin-$GALEN_VERSION.zip" \
	&& cp galen-bin-${GALEN_VERSION:-2.3.0}/* . \
	&& ./install.sh \
	&& rm "galen-bin-$GALEN_VERSION.zip"

#============================================
# Google Chrome
#============================================
# can specify versions by CHROME_VERSION;
#  e.g. google-chrome-stable=53.0.2785.101-1
#       google-chrome-beta=53.0.2785.92-1
#       google-chrome-unstable=54.0.2840.14-1
#       latest (equivalent to google-chrome-stable)
#       google-chrome-beta  (pull latest beta)
#============================================
ARG CHROME_VERSION="google-chrome-stable"
RUN wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | apt-key add - \
  && echo "deb http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google-chrome.list \
  && apt-get update -qqy \
  && apt-get -qqy install \
    ${CHROME_VERSION:-google-chrome-stable} \
  && rm /etc/apt/sources.list.d/google-chrome.list \
  && rm -rf /var/lib/apt/lists/*

#=================================
# Chrome Launch Script Modication
#=================================
COPY chrome_launcher.sh /opt/google/chrome/google-chrome
RUN chmod +x /opt/google/chrome/google-chrome

# check https://hub.docker.com/r/robcherry/docker-chromedriver/~/dockerfile/

#==================
# Chrome webdriver
#==================
ARG CHROME_DRIVER_VERSION=2.24
RUN wget --no-verbose -O /tmp/chromedriver_linux64.zip https://chromedriver.storage.googleapis.com/$CHROME_DRIVER_VERSION/chromedriver_linux64.zip \
  && rm -rf /opt/selenium/chromedriver \
  && unzip /tmp/chromedriver_linux64.zip -d /opt/selenium \
  && rm /tmp/chromedriver_linux64.zip \
  && mv /opt/selenium/chromedriver /opt/selenium/chromedriver-$CHROME_DRIVER_VERSION \
  && chmod 755 /opt/selenium/chromedriver-$CHROME_DRIVER_VERSION \
  && ln -fs /opt/selenium/chromedriver-$CHROME_DRIVER_VERSION /usr/bin/chromedriver

RUN mkdir -p /tmp/src/app
WORKDIR /tmp/src/app

# Define default command.
CMD ["bash"]
