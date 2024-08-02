# RUN apk add --no-cache curl file gcc g++ make libc-dev perl make git cmake patch sed util-linux-dev llvm-dev clang clang-analyzer doxygen gmp-dev libcrypto3 chromium font-freefont nodejs py3-setuptools py3-lxml py3-jinja2 python3 samurai xz && \
FROM alpine AS deploy

# Install build dependencies
RUN apk add --no-cache curl file git git-lfs sed sudo xz

# Install JDK, gradle, maven, and sonar-scanner from latest-stable for security fixes
RUN apk add --no-cache -X https://dl-cdn.alpinelinux.org/alpine/latest-stable/community openjdk17-jdk && \
	curl -sL $(curl -sL https://gradle.org/releases/ | sed -nre 's/.*"([^"]+-bin\.zip)".*/\1/p' | head -n1) | unzip -d /opt -q - && \
	chmod +x /opt/gradle-*/bin/gradle && ln -s /opt/gradle-*/bin/gradle /usr/bin/gradle && \
	curl -sL $(curl -sL https://maven.apache.org/download.html | sed -nre 's/.*"([^"]+-bin\.tar\.gz)".*/\1/p' | head -n1) | tar xzf - -C /opt && \
	ln -s /opt/apache-maven-*/bin/mvn /usr/bin/mvn && ln -s /opt/apache-maven-*/bin/mvnDebug /usr/bin/mvnDebug && \
	apk add --no-cache -X http://dl-cdn.alpinelinux.org/alpine/edge/testing sonar-scanner

# Perform tasks as basic user
RUN adduser --disabled-password jenkins && \
	mkdir -p /etc/sudoers.d && \
	echo "jenkins ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/jenkins && \
	chmod 0440 /etc/sudoers.d/jenkins

USER jenkins
WORKDIR /home/jenkins

# Initialize gradle
COPY --chown=jenkins build.gradle ./gradle-init/
RUN cd gradle-init && \
	gradle -i --no-daemon resolveDependencies --refresh-dependencies && \
	cd .. && \
	rm -rf gradle-init
