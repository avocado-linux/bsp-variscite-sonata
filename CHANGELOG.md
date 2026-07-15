# Changelog

All notable changes to avocado-bsp-variscite-sonata are documented in this file.
The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.1.0]

### Added
- Initial release: carrier board support for the Variscite Sonata-Board — the
  DART-MX8M-PLUS i.MX8M Plus dev kit carrier — as a carrier extension on the
  `imx8mp-var-dart` SOM target.
- Explicit kernel module set observed via `lsmod` on a booted DART-MX8M-PLUS on
  the Sonata-Board: NXP CAAM crypto, WiFi/BT, dual/triple CAN (FlexCAN +
  MCP251xFD), audio (WM8904 codec / SAI / XCVR / HDMI), ISP, RTC, resistive
  touch, and USB-C PD.
- WiFi/BT firmware for both DART-MX8M-PLUS wireless options so one image covers
  either SOM build: Broadcom BCM4339 / BCM4373 (Sterling-LWB, including the
  Variscite board NVRAM) and NXP IW612.
- i.MX8MP audio DSP firmware (SPDIF/eARC transceiver, async sample-rate
  converter) and the static wireless regulatory database.
- The boot-time `avocado-devicetree` overlay service (composes `devicetree_file`
  / `fdt_overlays` from avocado config onto the Sonata base DTB).
- CI via the shared `avocado-linux/actions` reusable workflows.

### Known issues
- Bluetooth on Broadcom-module SOMs: the Sonata device tree configures the
  wireless as NXP IW612 (`bluetooth_iw61x` → `btnxpuart`), so on a Broadcom
  (Sterling-LWB / BCM4339) SOM the BT HCI reset times out. WiFi works on both
  variants; BT on Broadcom SOMs needs a Broadcom BT device-tree node
  (`hci_uart` / `btbcm`), e.g. a `som_has_wbe`-gated DTB or overlay.
