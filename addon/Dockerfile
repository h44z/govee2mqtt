ARG BUILD_FROM=ghcr.io/home-assistant/base-debian:bookworm
FROM rust:1-bookworm AS builder

# Supervisor uses addon/ as its build context, so fetch a pinned source
# revision instead of trying to COPY files from the repository parent.
ARG GOVEE_SOURCE_REF=4c3ff4e69cd662dbc164c982672fc808e950785a
ARG BUILD_VERSION
ENV GOVEE_CI_TAG=${BUILD_VERSION}
RUN apt-get update && apt-get install -y --no-install-recommends \
    cmake libssl-dev pkg-config \
    && rm -rf /var/lib/apt/lists/*
ADD https://github.com/h44z/govee2mqtt/archive/${GOVEE_SOURCE_REF}.tar.gz /tmp/source.tar.gz
WORKDIR /source
RUN tar -xzf /tmp/source.tar.gz --strip-components=1 -C /source \
    && rm /tmp/source.tar.gz \
    && cargo build --locked --release --bin govee

FROM $BUILD_FROM
ARG BUILD_ARCH
ARG BUILD_VERSION
LABEL io.hass.version="${BUILD_VERSION}" \
    io.hass.type="app" \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.name="Govee to MQTT Bridge" \
    io.hass.description="Control Govee Devices" \
    io.hass.url="https://github.com/h44z/govee2mqtt" \
    org.opencontainers.image.title="Home Assistant Add-on: Govee to MQTT Bridge" \
    org.opencontainers.image.description="Bridge Govee devices to the Home Assistant MQTT Integration." \
    org.opencontainers.image.source="https://github.com/h44z/govee2mqtt" \
    org.opencontainers.image.licenses="MIT"
RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates libssl3 \
    && rm -rf /var/lib/apt/lists/*
COPY run.sh /run.sh
COPY --from=builder /source/target/release/govee /app/govee
COPY --from=builder /source/assets /app/assets/
COPY --from=builder /source/AmazonRootCA1.pem /app/AmazonRootCA1.pem
RUN chmod +x /run.sh
WORKDIR /app
CMD [ "/run.sh" ]
