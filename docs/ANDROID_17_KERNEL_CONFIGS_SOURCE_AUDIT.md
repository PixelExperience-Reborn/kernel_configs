# PixelExperience-Reborn Android 17 — kernel/configs source audit

**2026-11-05 | P2 matrix row 155 | Source port complete; device GKI/boot/VTS tests OPEN**

## Verified provenance

| Component | Exact source commit |
| --- | --- |
| Historic PE `seventeen` | `13d21442369967a49a72e955934d286ebfa4973d` |
| Official AOSP `kernel/configs` `android-17.0.0_r1` | `553fd8d034110b4a63834e676533f5cc4c5e0089` |
| Official AOSP17 tree | `eb946e282af9337ec6ddaf2b2d6351034fd48f4a` |
| Reviewed two-parent PE/AOSP merge | `f2c269aa40331e6a11d21f9011b56cca1a076f98` |

[Merge GitHub Actions #42009734056 — SUCCESS](https://github.com/PixelExperience-Reborn/kernel_configs/actions/runs/42009734056).

The dry-run confirmed a shared Git history, so a genuine two-parent Git merge was possible. The merge restores the **exact official AOSP17 source tree**, except for the narrow additional PE CI workflow. Historic PE changes remain reachable in the first-parent lineage, but are **not installed as active Android17 requirements**.

The old PE `p/`, `q/`, `r/` fragments concern Android P/Q/R launch kernels and included old 4.4/4.9/5.4 compatibility choices. These fragments are absent from Google's official AOSP17 `kernel/configs` tree and were deliberately not retained in the modern active source. The AOSP17 source includes `Android.bp`, `approved-ogki-builds.xml`, `kernel-lifetimes.xml`, `s/android-5.10` and `t/android-5.10`, plus the current official build/verification tools. This does **not** authorize modifying the phone's boot image or kernel config.

## Device reference and validation boundary

The *read-only* `LF52406/mondrian_android_device_xiaomi_sm8450-common` `lineage-24.0` `BoardConfigCommon.mk` sets `BOARD_USES_GENERIC_KERNEL_IMAGE := true`, `TARGET_KERNEL_SOURCE := kernel/xiaomi/sm8450`, and combines:
- `gki_defconfig`
- `vendor/waipio_GKI.config`
- `vendor/xiaomi_GKI.config`
- `vendor/$(PRODUCT_DEVICE)_GKI.config`
- `vendor/debugfs.config`

This is a Linux **5.10** Taro/Waipio device graph with external modules, device trees, boot header v4 and vendor ramdisk/DLKM requirements. Android platform `kernel/configs` repository does not replace these device-specific fragments, module versioning, board configuration or kernel ABI.

The Android17 source configuration can contain launch-version-specific requirements. **Do not infer the correct GKI generation or that a kernel 5.10 is eligible for Android17 launch solely from this source merge**. Android17 platform boot/upgrade compatibility, kernel KMI/ABI, FCM/VINTF and vendor API policies must be evaluated for mondrian's actual shipped/upgrade status.

## Remaining acceptance gates

1. Pin the reviewed fork commit in one **isolated P2 manifest profile** under `kernel/configs`, replacing the official checkout in that profile only; preserve official AOSP17 baseline in earlier profiles.
2. Validate profile with official `repo manifest`, no duplicate checkout paths, and exact SHAs.
3. Generate the actual merged mondrian 5.10 `.config` from all device fragments and inspect `scripts/kconfig` / `kernel/configs` validation (including required BPF, namespaces, cgroups, security, filesystem, kernel modules and non-debuggable constraints), without changing working product trees.
4. Run actual Android17 Soong/Make source graph, VTS/CTS kernel tests, SELinux enforcing, VINTF/FCM compatibility and board-specific recovery/boot/dynamic partition tests. Validate GKI module/KMI and vendor_boot/DTBO correctness.
5. Do not conflate the presence of 5.10 fragments with device compatibility, Android17 security patch eligibility, kernel LTS support or completed OTA/recovery work.

**Status:** verified AOSP17 exact-source two-parent merge; no full A17 compilation, VTS, physical boot, or runtime kernel validation was performed. Historic `seventeen` and the functioning MistOS/LineageOS trees were untouched.

**Next row 156 (`manifest`) is intentionally not started here.**
