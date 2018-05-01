FROM jenkinsci/slave:alpine

MAINTAINER eric <ericworkdongdong@gmail.com>

USER root

ENV GLIBC_VERSION "2.25-r0"

# Install required dependencies
RUN apk --update add --no-cache --virtual=.build-dependencies wget curl tar unzip ca-certificates bash && \
	wget https://raw.githubusercontent.com/sgerrand/alpine-pkg-glibc/master/sgerrand.rsa.pub -O /etc/apk/keys/sgerrand.rsa.pub && \
	wget https://github.com/sgerrand/alpine-pkg-glibc/releases/download/${GLIBC_VERSION}/glibc-${GLIBC_VERSION}.apk -O /tmp/glibc.apk && \
	wget https://github.com/sgerrand/alpine-pkg-glibc/releases/download/${GLIBC_VERSION}/glibc-bin-${GLIBC_VERSION}.apk -O /tmp/glibc-bin.apk && \
	apk add --no-cache /tmp/glibc.apk /tmp/glibc-bin.apk && \
	rm -rf /tmp/* && \
	rm -rf /var/cache/apk/*

# Everything will be installed in the directory but jdk.
ENV SDK_HOME /usr/local
# Download and unzip Android SDK
ENV ANDROID_HOME ${SDK_HOME}/android-sdk-linux
ENV ANDROID_SDK ${SDK_HOME}/android-sdk-linux
ENV ANDROID_SDK_MANAGER ${SDK_HOME}/android-sdk-linux/tools/bin/sdkmanager

ENV ANDROID_SDK_VERSION r26.1.2
ENV ANDROID_SDK_URL https://dl.google.com/android/repository/sdk-tools-linux-4333796.zip
RUN curl -sSL "${ANDROID_SDK_URL}" -o tools_${ANDROID_SDK_VERSION}-linux.zip \
    && unzip tools_${ANDROID_SDK_VERSION}-linux.zip -d ${ANDROID_HOME} \
  && rm -rf tools_${ANDROID_SDK_VERSION}-linux.zip
  
ENV PATH ${ANDROID_HOME}/tools:${ANDROID_HOME}/tools/bin:$ANDROID_HOME/platform-tools:$PATH

# Install Android SDK Components
ENV ANDROID_COMPONENTS "tools" \
                       "platform-tools" \
                       "build-tools;26.0.2" \
                       "platforms;android-26" 

ENV GOOGLE_COMPONENTS "extras;android;m2repository" \
                       "extras;google;m2repository" \
                       "extras;google;google_play_services" 
                       
ENV CONSTRAINT_LAYOUT "extras;m2repository;com;android;support;constraint;constraint-layout;1.0.2"\
                       "extras;m2repository;com;android;support;constraint;constraint-layout-solver;1.0.2"

# Accept licenses before installing components, no need to echo y for each component
# License is valid for all the standard components in versions installed from this file
# Non-standard components: MIPS system images, preview versions, GDK (Google Glass) and Android Google TV require separate licenses, not accepted there
RUN yes | ${ANDROID_SDK_MANAGER} --licenses && \
    ${ANDROID_SDK_MANAGER}  ${ANDROID_COMPONENTS} \
                            ${GOOGLE_COMPONENTS} \
                            ${CONSTRAINT_LAYOUT}  

RUN chown -R jenkins ${ANDROID_HOME}
USER jenkins
