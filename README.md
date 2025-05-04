# Orange Pi 5 Ultra — Enable eMMC via Overlay

[Armbian_community_25.5.0-trunk.444_Orangepi5-max_bookworm_vendor_6.1.115_minimal.img.xz](https://github.com/armbian/community/releases/download/25.5.0-trunk.444/Armbian_community_25.5.0-trunk.444_Orangepi5-max_bookworm_vendor_6.1.115_minimal.img.xz)

---

## Table of Contents

- [Create Device Tree Overlay](#create-device-tree-overlay)
- [Install Overlay](#install-overlay)
- [Configure `armbianEnv.txt`](#configure-armbianenvtxt)

---

## Create Device Tree Overlay

Create a file named `emmc-enable-overlay.dts` with the following content:

```dts
/dts-v1/;
/plugin/;

/ {
    compatible = "rockchip,rk3588";

    fragment@0 {
        target-path = "/mmc@fe2c0000";
        __overlay__ {
            status = "okay";

            bus-width = <8>;
            cap-mmc-highspeed;
            mmc-hs200-1_8v;
            mmc-hs400-1_8v;
            non-removable;
            disable-wp;
            supports-emmc;

            pinctrl-names = "default", "idle";
            pinctrl-0 = <&emmc_clk &emmc_cmd &emmc_bus8 &emmc_rstnout &emmc_data_strobe>;
            pinctrl-1 = <&emmc_clk &emmc_cmd &emmc_bus8 &emmc_rstnout>;

            vmmc-supply = <&vcc_3v3_sd_s0>;
            vqmmc-supply = <&vccio_sd_s0>;
        };
    };
};
```

---

## Install Overlay

To compile:

```bash
dtc -I dts -O dtb -o rockchip-rk3588-emmc-enable.dtbo emmc-enable-overlay.dts
```

To install:

```bash
sudo cp rockchip-rk3588-emmc-enable.dtbo /boot/dtb/rockchip/overlay/
```

---

## Configure `armbianEnv.txt`

Open `/boot/armbianEnv.txt` and add:

```ini
overlay_prefix=rockchip-rk3588
overlays=emmc-enable
```

If `overlays=` already exists, append `emmc-enable` with a space.
