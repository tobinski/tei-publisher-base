# TEI publisher lib image
This image contains an exist-db with the tei-publisher library. You can use this image to build you an image with your own TEI-publisher app

## Build an image for your own app
After you finished your tei-publisher app and wants to deploy it as a container you can just use this image as a base to build on.

```dockerfile
# A dockerfile may looks like this
USER root

ENV TEI_PUBLISHER_VERSION 2.7.0
ENV ANT_VERSION 1.10.5
ENV ANT_HOME /etc/ant-${ANT_VERSION}

WORKDIR /tmp

RUN apt-get update && apt-get install -y  nodejs \
 git \ 
 ant \ 
 wget 

RUN wget http://www-us.apache.org/dist/ant/binaries/apache-ant-${ANT_VERSION}-bin.tar.gz \
    && mkdir ant-${ANT_VERSION} \
    && tar -zxvf apache-ant-${ANT_VERSION}-bin.tar.gz \
    && mv apache-ant-${ANT_VERSION} ${ANT_HOME} \
    && rm apache-ant-${ANT_VERSION}-bin.tar.gz \
    && rm -rf ant-${ANT_VERSION} \
    && rm -rf ${ANT_HOME}/manual \
    && unset ANT_VERSION

ENV PATH ${PATH}:${ANT_HOME}/bin

# Build dodis app
FROM builder as your-app
WORKDIR /tmp/your-app
COPY . .
RUN ant

# Build final container
FROM tobinski/tei-publisher-lib:${TEI_PUBLISHER_VERSION}
COPY --from=your-app /tmp/your-app/build/*.xar /exist/autodeploy
``` 
