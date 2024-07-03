# RUN apk add --no-cache curl file gcc g++ make libc-dev perl make git cmake patch sed util-linux-dev llvm-dev clang clang-analyzer doxygen gmp-dev libcrypto3 chromium font-freefont nodejs py3-setuptools py3-lxml py3-jinja2 python3 samurai xz && \
FROM alpine AS deploy

# Install build dependencies
RUN apk add --no-cache curl file git git-lfs sed xz

# Install JDK, gradle from latest-stable for security fixes
RUN apk add --no-cache -X https://dl-cdn.alpinelinux.org/alpine/latest-stable/community openjdk17-jdk && \
	curl -sL $(curl -sL https://gradle.org/releases/ | sed -nre 's/.*"([^"]+-bin\.zip)".*/\1/p' | head -n1) | unzip -d /opt -q - && \
	chmod +x /opt/gradle-*/bin/gradle && ln -s /opt/gradle-*/bin/gradle /usr/bin/gradle

# Perform tasks as basic user
RUN adduser --disabled-password jenkins
USER jenkins
WORKDIR /home/jenkins

# Initialize gradle
COPY --chown=jenkins build.gradle ./gradle-init/
RUN cd gradle-init && \
	gradle -i --no-daemon resolveDependencies --refresh-dependencies && \
	cd .. && \
	rm -rf gradle-init
