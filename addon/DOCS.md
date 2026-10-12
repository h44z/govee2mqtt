# Installation and configuration

1. In Home Assistant OS, open **Settings → Add-ons → Add-on Store → ⋮ → Repositories**.
2. Add `https://github.com/h44z/govee2mqtt`.
3. Install **Govee to MQTT Bridge**. AMD64 and ARM64 are supported.
   Installation builds a pinned source revision locally and needs internet
   access to download the source, Rust dependencies, and base images. Allow
   several minutes for the first build and sufficient free memory and disk space.
4. Configure the Home Assistant MQTT integration. Install and start the
   Mosquitto broker add-on, or use an existing MQTT broker.
5. In this add-on's **Configuration** tab, enable **Show unused optional
   configuration options**. For Mosquitto, leave the MQTT options blank;
   the broker address and credentials are discovered automatically.
   For an external broker, set `mqtt_host` and, if needed, `mqtt_port`,
   `mqtt_username`, and `mqtt_password`.
6. Configure `govee_email` and `govee_password` for Govee cloud/IoT support,
   and `govee_api_key` for the official API. For LAN control, enable LAN Control
   for each supported device in the Govee app. Cloud credentials are optional
   when using LAN control alone.
7. Save the configuration and start the add-on. Check **Logs** for broker
   connection and device discovery. Devices appear under the MQTT integration.

Use **Open Web UI** to access the bridge's status page on port 8056. Host
networking is enabled for Govee LAN discovery. Persistent cache data is stored
in `/data`; device configuration is stored in the add-on configuration directory.

## Updating the fork

The source commit is pinned in `addon/Dockerfile` under `ARG GOVEE_SOURCE_REF`.
When releasing source changes, run `scripts/apply-tag.sh` to update both that
commit and `addon/config.yaml`'s version, then publish the changes to GitHub.
Home Assistant uses the version to detect available updates. CI builds both
architectures using the commit under test.
