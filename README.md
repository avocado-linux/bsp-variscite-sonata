# bsp-variscite-sonata

Carrier board support for the **Variscite Sonata-Board** — the carrier the
**DART-MX8M-PLUS** i.MX8M Plus dev kit (VAR-STK/DVK-DT8M-PLUS) ships on.

This is the carrier BSP: it installs the kernel modules the DART SOM and the
Sonata-Board hardware use, plus WiFi/BT firmware for both DART-MX8M-PLUS wireless
options (Broadcom BCM4339/BCM4373 Sterling-LWB and NXP IW612) and the i.MX8MP
audio DSP firmware. It selects the Sonata device tree
(`imx8mp-var-dart-sonata.dtb`), the default for the `imx8mp-var-dart` machine.

Upstream, Variscite ships a single Yocto machine (`imx8mp-var-dart`) for the
whole i.MX8MP SOM line, with carriers selected by device tree at boot:

- **DART-MX8M-PLUS** → **Sonata-Board** (this extension; legacy DART kits used
  the DT8MCustomBoard).
- **VAR-SOM-MX8M-PLUS** (SODIMM) → **Symphony-Board** — a separate
  variscite-namespaced `bsp-*` carrier extension (planned).

The two modules (DART, VAR-SOM/SODIMM) start as two BSP extensions; longer term a
target override can let one carrier extension serve multiple modules.

## Using this extension

```yaml
default_target: imx8mp-var-dart

extensions:
  avocado-bsp-variscite-sonata:
    source:
      type: package
      version: "*"

runtimes:
  dart:
    extensions:
      - avocado-bsp-variscite-sonata
```

To start from just the SOM and target a custom carrier, override the device tree
(`devicetree_file` / `fdt_overlays` via avocado config) and trim the module set.

## Notes

- **Module set:** pins the explicit set observed via `lsmod` on a booted
  DART-MX8M-PLUS (CAAM crypto, WiFi/BT, CAN, audio, ISP, RTC, touch, USB-C PD),
  not the full `kernel-modules` meta-package.
- **Wireless:** the DART-MX8M-PLUS ships with either a Broadcom (Sterling-LWB,
  BCM4339/BCM4373) or an NXP IW612 WiFi/BT module; firmware for both is shipped
  so one image covers either SOM build. WiFi works on both. **Bluetooth on
  Broadcom SOMs** currently fails its HCI reset because the Sonata DTB configures
  the BT UART as NXP IW612 (`bluetooth_iw61x` → `btnxpuart`); a Broadcom BT node
  (`hci_uart`/`btbcm`), e.g. a `som_has_wbe`-gated DTB or overlay, is needed.
- The `imx8mp-var-dart` machine builds the uncompressed `Image` (the Avocado
  u-boot env `booti`s it directly) and the Sonata DART DTBs only — not the full
  ~70-entry Variscite SOM/carrier/camera catalog.
