# gta4l on GrapheneOS (Android 17)

Branch `grapheneos-a17` is the LineageOS `lineage-24.0` device tree with the
LineageOS-only build pieces made optional, so it can be built against the
GrapheneOS `17` manifest instead of `vendor/lineage`.

## What changed compared to lineage-24

- Added an AOSP product: `aosp_gta4l` (see `aosp_gta4l.mk` and
  `AndroidProducts.mk`).
- `overlay-lineage`, Lineage Health HAL and its sepolicy are only applied to
  `lineage_*` products.
- `device/lineage/sepolicy` and `device/qcom/sepolicy_vndr` includes are now
  optional (`-include`).
- `charger_res_images_vendor` / `libavservices_minijail_vendor` (LineageOS
  only modules) are replaced with their AOSP counterparts for non-Lineage
  products.
- Added `grapheneos/local_manifest.xml` with the repositories the device tree
  needs that are not in the GrapheneOS manifest.

## Build

1. Init GrapheneOS 17:

       repo init -u https://github.com/GrapheneOS/platform_manifest.git -b 17

2. Add the local manifest:

       mkdir -p .repo/local_manifests
       cp <this file> .repo/local_manifests/gta4l.xml

3. Sync:

       repo sync -j8

4. Populate `vendor/samsung` (proprietary blobs) with the tree's
   `extract-files.py` from a matching stock/LineageOS install.

5. Kernel: this tree uses `TARGET_KERNEL_SOURCE` / `TARGET_KERNEL_CONFIG`,
   which is wired up by LineageOS' `vendor/lineage` build tasks and is NOT
   available in GrapheneOS' build. Build `kernel/samsung/sm6115` separately and
   provide the result as a prebuilt kernel/boot image (or port the tree to a
   `BOARD_KERNEL_BINARIES` prebuilt module) before flashing.

6. Build:

       source build/envsetup.sh
       lunch aosp_gta4l-cur-user      # or aosp_gta4l-cur-userdebug
       m

## Known gaps

- Kernel integration (see step 5) is not done yet.
- GrapheneOS officially supports Pixel devices only. Expect device specific
  work (firmware, kernel hardening, USB-C control, etc.) after the first
  successful build.
