FROM ubuntu:16.04

#================================================
# Customize sources for apt-get
#================================================
RUN  echo "deb http://archive.ubuntu.com/ubuntu xenial main universe\n" > /etc/apt/sources.list \
  && echo "deb http://archive.ubuntu.com/ubuntu xenial-updates main universe\n" >> /etc/apt/sources.list \
  && echo "deb http://security.ubuntu.com/ubuntu xenial-security main universe\n" >> /etc/apt/sources.list


#========================
# Miscellaneous packages
#========================
RUN apt-get update -qqy \
  && apt-get -qqy --no-install-recommends install \
    bzip2 \
    ca-certificates \
    curl \
    sudo \
    unzip \
    wget \
    build-essential \
    libssl-dev \
    openssl \
    libz-dev \
    libffi-dev \
    xvfb \
  && rm -rf /var/lib/apt/lists/* /var/cache/apt/*

# Configuration for XVFB server
RUN rm -f /tmp/.X*lock
# https://github.com/SeleniumHQ/docker-selenium/issues/87
ENV DBUS_SESSION_BUS_ADDRESS=/dev/null

# Default to UTF-8 file.encoding
ENV LANG C.UTF-8


#=======
# RUBY
#=======
ARG RUBY_VERSION_MAJOR=2.2
ARG RUBY_VERSION_PATCH=3
ENV RUBY_VERSION=$RUBY_VERSION_MAJOR.$RUBY_VERSION_PATCH
RUN wget https://cache.ruby-lang.org/pub/ruby/$RUBY_VERSION_MAJOR/ruby-$RUBY_VERSION.tar.gz && \
    tar zvxf ruby-$RUBY_VERSION.tar.gz && \
    cd ruby-$RUBY_VERSION && \
    ./configure && \
    make && \
    make install && \
    rm /ruby-$RUBY_VERSION.tar.gz && \
    rm -rf /ruby-$RUBY_VERSION

RUN gem install bundler --no-document -v 1.10.5


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
