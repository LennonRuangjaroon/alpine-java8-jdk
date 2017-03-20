# docker-alpine-java8-jdk

### This is java image based on Alpine Linux and JAVA8 JDK

- [Alpine Linux](https://hub.docker.com/_/alpine/)

#### - Update curl
	$ apk add --update curl

#### - Install glibc-2.21 which is a hard dependency of Java 8. and see https://github.com/mesosphere/kubernetes-mesos/issues/801
	$ curl -Ls https://github.com/sgerrand/alpine-pkg-glibc/releases/download/2.21-r2/glibc-2.21-r2.apk > /tmp/glibc-2.21-r2.apk &&\
		apk add --allow-untrusted /tmp/glibc-2.21-r2.apk

#### - Download the Oracle JRE using tricks in this SO article.
	$ curl -Ls https://github.com/sgerrand/alpine-pkg-glibc/releases/download/2.21-r2/glibc-2.21-r2.apk > /tmp/glibc-2.21-r2.apk &&\
	    apk add --allow-untrusted /tmp/glibc-2.21-r2.apk &&\
	    mkdir -p /opt &&\
	    curl -jkLH "Cookie: oraclelicense=accept-securebackup-cookie" -o java.tar.gz\
	    http://download.oracle.com/otn-pub/java/jdk/${JAVA_VERSION_MAJOR}u${JAVA_VERSION_MINOR}-b${JAVA_VERSION_BUILD}/${JAVA_URL_ELEMENT}/${JAVA_PACKAGE}-${JAVA_VERSION_MAJOR}u${JAVA_VERSION_MINOR}-linux-x64.tar.gz &&\
	    echo "$JAVA_SHA256_SUM  java.tar.gz" | sha256sum -c - &&\
	    gunzip -c java.tar.gz | tar -xf - -C /opt && rm -f java.tar.gz &&\

#### - Remove spurious folders not needed (like jre/lib).
	$ rm -rf /opt/jre/lib/plugin.jar \
         /opt/jre/lib/ext/jfxrt.jar \
         /opt/jre/bin/javaws \
         /opt/jre/lib/javaws.jar \
         /opt/jre/lib/desktop \
         /opt/jre/plugin \
         /opt/jre/lib/deploy* \
         /opt/jre/lib/*javafx* \
         /opt/jre/lib/*jfx* \
         /opt/jre/lib/amd64/libdecora_sse.so \
         /opt/jre/lib/amd64/libprism_*.so \
         /opt/jre/lib/amd64/libfxplugins.so \
         /opt/jre/lib/amd64/libglass.so \
         /opt/jre/lib/amd64/libgstreamer-lite.so \
         /opt/jre/lib/amd64/libjavafx*.so \
         /opt/jre/lib/amd64/libjfx*.so &&\
      apk del curl &&\
      rm -rf /var/cache/apk/*

#### - Set the proper environment variables.
	ENV JAVA_HOME /opt/jre
	ENV PATH ${PATH}:${JAVA_HOME}/bin

#### - Usage
	docker run -it --rm wrlennon/alpine-java8-jdk java -version