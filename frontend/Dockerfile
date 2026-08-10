# syntax=docker/dockerfile:1.7

FROM node:22.11.0-bookworm AS android-builder

ARG ANDROID_CMDLINE_TOOLS_VERSION=15859902
ARG BUILD_TYPE=debug

ENV DEBIAN_FRONTEND=noninteractive \
    JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64 \
    ANDROID_HOME=/opt/android-sdk \
    ANDROID_SDK_ROOT=/opt/android-sdk \
    PATH=/opt/android-sdk/cmdline-tools/latest/bin:/opt/android-sdk/platform-tools:${PATH}

# React Native Android build prerequisites
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        openjdk-17-jdk \
        ca-certificates \
        curl \
        git \
        unzip \
    && rm -rf /var/lib/apt/lists/*

# Android command-line tools
RUN mkdir -p "${ANDROID_SDK_ROOT}/cmdline-tools" \
    && curl -fsSL \
        "https://dl.google.com/android/repository/commandlinetools-linux-${ANDROID_CMDLINE_TOOLS_VERSION}_latest.zip" \
        -o /tmp/android-commandlinetools.zip \
    && unzip -q /tmp/android-commandlinetools.zip -d /tmp/android-commandlinetools \
    && mkdir -p "${ANDROID_SDK_ROOT}/cmdline-tools/latest" \
    && mv /tmp/android-commandlinetools/cmdline-tools/* \
        "${ANDROID_SDK_ROOT}/cmdline-tools/latest/" \
    && rm -rf /tmp/android-commandlinetools /tmp/android-commandlinetools.zip

# Versions are aligned with the current EDF-FE android/build.gradle.
RUN yes | sdkmanager --licenses >/dev/null \
    && sdkmanager \
        "platform-tools" \
        "platforms;android-36" \
        "build-tools;36.0.0" \
        "ndk;27.1.12297006" \
        "cmake;3.22.1"

WORKDIR /workspace

# Cache npm dependencies separately from application source.
COPY package.json package-lock.json ./
RUN npm ci

COPY . .

RUN chmod +x android/gradlew \
    && mkdir -p /artifacts \
    && case "${BUILD_TYPE}" in \
        debug) \
            cd android \
            && ./gradlew --no-daemon assembleDebug \
            && cp app/build/outputs/apk/debug/*.apk /artifacts/ \
            ;; \
        release) \
            cd android \
            && ./gradlew --no-daemon assembleRelease bundleRelease \
            && cp app/build/outputs/apk/release/*.apk /artifacts/ \
            && cp app/build/outputs/bundle/release/*.aab /artifacts/ \
            ;; \
        *) \
            echo "Unsupported BUILD_TYPE=${BUILD_TYPE}. Use debug or release." >&2 \
            && exit 1 \
            ;; \
    esac

# The final image contains build artifacts only.
# With BuildKit:
# docker build --output type=local,dest=./dist .
FROM scratch AS artifacts
COPY --from=android-builder /artifacts/ /
