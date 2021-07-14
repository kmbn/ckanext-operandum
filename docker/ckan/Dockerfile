FROM openknowledge/ckan-dev:2.9

# Install geo dependencies
ARG INSTALL_DIR=/etc/install-geo
WORKDIR $INSTALL_DIR
RUN : \
    && cd $INSTALL_DIR \
    && wget https://download.osgeo.org/geos/geos-3.8.1.tar.bz2 \
    && tar xjf geos-3.8.1.tar.bz2 \
    && cd geos-3.8.1 \
    && ./configure \
    && make \
    && make install
RUN : \
    && cd $INSTALL_DIR \
    && apk add sqlite sqlite-dev tiff tiff-dev curl curl-dev \
    && wget https://download.osgeo.org/proj/proj-7.1.1.tar.gz \
    && tar xzf proj-7.1.1.tar.gz \
    && cd proj-7.1.1 \
    && ./configure \
    && make \
    && make install \
    && cd /etc \
    && rm -rf $INSTALL_DIR

# ckan is installed in /srv/app/src/ckan in the ckan-dev image we're basing this image on
WORKDIR /srv/app/src/ckanext-operandum

# copy over the ckanext-operandum source
COPY . .

# might as well update pip while we're here!
RUN pip3 install --upgrade pip

# install the dependencies
RUN python3 setup.py develop && \
    pip3 install -r requirements.txt && \
    pip3 install -r dev-requirements.txt

# this entrypoint ensures our service dependencies (postgresql, solr and redis) are running before
# running the cmd
ENTRYPOINT ["/bin/bash", "docker/ckan/entrypoint.sh"]

# run the tests with coverage output
CMD ["pytest", "--cov=ckanext.new_users", "--ckan-ini=test.ini", "ckanext/operandum/tests"]
