FROM ubuntu:latest

ENV DEBIAN_FRONTEND=noninteractive
ENV LANG C.UTF-8
ENV AIL_HOME /opt/AIL
ENV AIL_BIN ${AIL_HOME}/bin
ENV AIL_FLASK ${AIL_HOME}/var/www
# ENV AIL_REDIS ${AIL_HOME}/redis/src
# ENV AIL_ARDB ${AIL_HOME}/ardb/src
# ENV AIL_VENV ${AIL_HOME}/AILENV
ENV PATH=${AIL_HOME}:${AIL_BIN}:${AIL_FLASK}:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

RUN ln -fs /usr/share/zoneinfo/UTC /etc/localtime

# Make sure that all updates are in place
RUN apt-get clean && apt-get update -y && apt-get upgrade -y \
        && apt-get dist-upgrade -y && apt-get autoremove -y


# Install needed packages
RUN apt-get install -y autoconf automake build-essential cmake g++ graphviz libadns1 \
    libadns1-dev libev-dev libffi-dev libfreetype6-dev libfuzzy-dev libgmp-dev libsnappy-dev \
    libssl-dev libtool p7zip-full python3-dev screen sudo tor unzip virtualenv wget tzdata \
    python3-pip git

RUN dpkg-reconfigure --frontend noninteractive tzdata

# python stuff
RUN pip3 install -U pip numpy requests tk nose PyYAML python-tlsh DomainClassifier PySocks asciimatics bcrypt beautifulsoup4 colorama crcmod dnspython flask flask-login 'git+https://github.com/D4-project/BGP-Ranking.git/#egg=pybgpranking&subdirectory=client' https://github.com/saffsd/langid.py/archive/master.zip https://github.com/trolldbois/python-cymru-services/archive/master.zip https://github.com/trolldbois/python3-adns/archive/master.zip ipaddress ipython langid logbook matplotlib mmh3 networkx nltk numpy phonenumbers psutil pubsublogger pybloomfiltermmap pycountry==18.12.8 pylibinjection python-Levenshtein python-magic pyzmq redis==2.10.6 scrapy scrapy-splash sflock ssdeep terminaltables textblob texttable thehive4py whoosh zmq git+https://github.com/MISP/PyTaxonomies  git+https://github.com/MISP/PyMISPGalaxies pymisp

# Adding sudo command
RUN useradd -m docker && echo "docker:docker" | chpasswd && adduser docker sudo
RUN echo "root ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers



# Installing AIL dependencies
RUN mkdir /opt/AIL
ADD . /opt/AIL

# 2. build+install FAUP, tlsh pgpdump, NLTK, update-thirdparty flask
WORKDIR /opt/AIL

# Installing Web dependencies,
# remove all the parts below if you dont need the Web UI
WORKDIR /opt/AIL/var/www
# RUN ./update_thirdparty.sh
RUN wget -q http://dygraphs.com/dygraph-combined.js -O ./static/js/dygraph-combined.js
ENV SBADMIN_VERSION='3.3.7'
ENV BOOTSTRAP_VERSION='4.2.1'
ENV FONT_AWESOME_VERSION='5.7.1'
ENV D3_JS_VERSION='5.5.0'
RUN rm -rf temp && mkdir temp
RUN wget -q https://github.com/twbs/bootstrap/releases/download/v${BOOTSTRAP_VERSION}/bootstrap-${BOOTSTRAP_VERSION}-dist.zip -O temp/bootstrap${BOOTSTRAP_VERSION}.zip
RUN wget -q https://github.com/BlackrockDigital/startbootstrap-sb-admin/archive/v${SBADMIN_VERSION}.zip -O temp/${SBADMIN_VERSION}.zip
RUN wget -q https://github.com/BlackrockDigital/startbootstrap-sb-admin-2/archive/v${SBADMIN_VERSION}.zip -O temp/${SBADMIN_VERSION}-2.zip
RUN wget -q https://github.com/FortAwesome/Font-Awesome/archive/v4.7.0.zip -O temp/FONT_AWESOME_4.7.0.zip
RUN wget -q https://github.com/FortAwesome/Font-Awesome/archive/5.7.1.zip -O temp/FONT_AWESOME_${FONT_AWESOME_VERSION}.zip
RUN wget -q https://github.com/d3/d3/releases/download/v${D3_JS_VERSION}/d3.zip -O  temp/d3_${D3_JS_VERSION}.zip
# dateRangePicker
RUN wget -q https://github.com/moment/moment/archive/2.24.0.zip -O temp/moment.zip
RUN wget -q https://github.com/longbill/jquery-date-range-picker/archive/v0.20.0.zip -O temp/daterangepicker.zip

RUN unzip -qq temp/bootstrap${BOOTSTRAP_VERSION}.zip -d temp/
RUN unzip -qq temp/${SBADMIN_VERSION}.zip -d temp/
RUN unzip -qq temp/${SBADMIN_VERSION}-2.zip -d temp/
RUN unzip -qq temp/FONT_AWESOME_4.7.0.zip -d temp/
RUN unzip -qq temp/FONT_AWESOME_${FONT_AWESOME_VERSION}.zip -d temp/
RUN unzip -qq temp/d3_${D3_JS_VERSION}.zip -d temp/

RUN unzip -qq temp/moment.zip -d temp/
RUN unzip -qq temp/daterangepicker.zip -d temp/

RUN mv temp/bootstrap-${BOOTSTRAP_VERSION}-dist/js/bootstrap.min.js ./static/js/bootstrap4.min.js
RUN mv temp/bootstrap-${BOOTSTRAP_VERSION}-dist/js/bootstrap.min.js.map ./static/js/bootstrap.min.js.map
RUN mv temp/bootstrap-${BOOTSTRAP_VERSION}-dist/css/bootstrap.min.css ./static/css/bootstrap4.min.css
RUN mv temp/bootstrap-${BOOTSTRAP_VERSION}-dist/css/bootstrap.min.css.map ./static/css/bootstrap4.min.css.map

RUN mv temp/startbootstrap-sb-admin-${SBADMIN_VERSION} temp/sb-admin
RUN mv temp/startbootstrap-sb-admin-2-${SBADMIN_VERSION} temp/sb-admin-2
RUN mv temp/Font-Awesome-4.7.0 temp/font-awesome

RUN rm -rf ./static/webfonts/
RUN mv temp/Font-Awesome-${FONT_AWESOME_VERSION}/css/all.min.css ./static/css/font-awesome.min.css
RUN mv temp/Font-Awesome-${FONT_AWESOME_VERSION}/webfonts ./static/webfonts

RUN rm -rf ./static/js/plugins
RUN mv temp/sb-admin/js/* ./static/js/

RUN rm -rf ./static/fonts/ ./static/font-awesome/
RUN mv temp/sb-admin/fonts/ ./static/
RUN mv temp/font-awesome/ ./static/

RUN rm -rf ./static/css/plugins/
RUN mv temp/sb-admin/css/* ./static/css/
RUN mv temp/sb-admin-2/dist/css/* ./static/css/
RUN mv temp/jquery-date-range-picker-0.20.0/dist/daterangepicker.min.css ./static/css/

RUN mv temp/d3.min.js ./static/js/
RUN mv temp/moment-2.24.0/min/moment.min.js ./static/js/
RUN mv temp/jquery-date-range-picker-0.20.0/dist/jquery.daterangepicker.min.js ./static/js/

RUN rm -rf temp

ENV JQVERSION="3.4.1"
RUN wget -q http://code.jquery.com/jquery-${JQVERSION}.js -O ./static/js/jquery.js

#Ressources for dataTable
RUN wget -q https://cdn.datatables.net/1.10.20/js/jquery.dataTables.min.js -O ./static/js/jquery.dataTables.min.js
RUN wget -q https://cdn.datatables.net/plug-ins/1.10.20/integration/bootstrap/3/dataTables.bootstrap.css -O ./static/css/dataTables.bootstrap.css
RUN wget -q https://cdn.datatables.net/plug-ins/1.10.20/integration/bootstrap/3/dataTables.bootstrap.js -O ./static/js/dataTables.bootstrap.js

RUN wget -q https://cdn.datatables.net/1.10.20/css/dataTables.bootstrap4.min.css -O ./static/css/dataTables.bootstrap.min.css
RUN wget -q https://cdn.datatables.net/1.10.20/js/dataTables.bootstrap4.min.js -O ./static/js/dataTables.bootstrap.min.js

#Ressources for bootstrap popover
ENV POPPER_VERSION="2.0.6"
RUN wget -q https://unpkg.com/@popperjs/core@${POPPER_VERSION}/dist/umd/popper.min.js -O ./static/js/popper.min.js
RUN wget -q https://unpkg.com/@popperjs/core@${POPPER_VERSION}/dist/umd/popper.min.js.map -O ./static/js/popper.min.js.map

#Ressource for graph
RUN wget -q https://raw.githubusercontent.com/flot/flot/958e5fd43c6dff4bab3e1fd5cb6109df5c1e8003/jquery.flot.js -O ./static/js/jquery.flot.js
RUN wget -q https://raw.githubusercontent.com/flot/flot/958e5fd43c6dff4bab3e1fd5cb6109df5c1e8003/jquery.flot.pie.js -O ./static/js/jquery.flot.pie.js
RUN wget -q https://raw.githubusercontent.com/flot/flot/958e5fd43c6dff4bab3e1fd5cb6109df5c1e8003/jquery.flot.time.js -O ./static/js/jquery.flot.time.js
RUN wget -q https://raw.githubusercontent.com/flot/flot/958e5fd43c6dff4bab3e1fd5cb6109df5c1e8003/jquery.flot.stack.js -O ./static/js/jquery.flot.stack.js

#Ressources for sparkline and canvasJS and slider
RUN wget -q http://omnipotent.net/jquery.sparkline/2.1.2/jquery.sparkline.min.js -O ./static/js/jquery.sparkline.min.js
RUN mkdir temp
RUN wget -q http://canvasjs.com/fdm/chart/ -O temp/canvasjs.zip
RUN unzip -qq temp/canvasjs.zip -d temp/
RUN mv temp/jquery.canvasjs.min.js ./static/js/jquery.canvasjs.min.js

RUN wget -q https://jqueryui.com/resources/download/jquery-ui-1.12.1.zip -O temp/jquery-ui.zip
RUN unzip -qq temp/jquery-ui.zip -d temp/
RUN mv temp/jquery-ui-1.12.1/jquery-ui.min.js ./static/js/jquery-ui.min.js
RUN mv temp/jquery-ui-1.12.1/jquery-ui.min.css ./static/css/jquery-ui.min.css
RUN rm -rf temp

RUN mkdir -p ./static/image
WORKDIR ./static/image
RUN wget -q https://www.circl.lu/assets/images/logos/AIL.png -O AIL.png
RUN wget -q https://www.circl.lu/assets/images/logos/AIL-logo.png -O AIL-logo.png

WORKDIR /opt/AIL


RUN git clone https://github.com/cvandeplas/pystemon.git /opt/pystemon
#RUN ./other_installers/docker/pystemon/install.sh
# RUN pip install -r /opt/pystemon/requirements.txt
#RUN pip install -r /opt/AIL/crawler_requirements.txt

COPY ./other_installers/docker/docker_start.sh /docker_start.sh
ENTRYPOINT ["/bin/bash", "/docker_start.sh"]
