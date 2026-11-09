# gta4l on Evolution X (Android 17)

Branch `evolution-a17` is the LineageOS `lineage-24.0` device tree for
`gta4l`, prepared for Evolution X branch `cnb` (Android 17).

## Why this needs almost no changes

Evolution X is based on LineageOS 24 and maps `vendor/lineage` to
`Evolution-X/vendor_evolution`. That means:

- `lineage_gta4l.mk` keeps inheriting
  `vendor/lineage/config/common_full_tablet.mk` (served by vendor_evolution).
- The kernel is still built in-tree through `TARGET_KERNEL_SOURCE` and
  `vendor/lineage/build/tasks/kernel.mk` (also provided by vendor_evolution).
- QCOM/CAF bits (`hardware/qcom-caf/common` with its `SEPolicy.mk` linkfile,
  `device/qcom/sepolicy_vndr/legacy-um`, `vendor/qcom/opensource/usb`) come
  from Evolution X's `snippets/lineage.xml`.

## Changes compared to `lineage-24`

- `AndroidProducts.mk`: added `COMMON_LUNCH_CHOICES`.
- `evolution/local_manifest.xml`: repositories missing from the Evolution X
  manifest (`hardware/samsung`, `kernel/samsung/sm6115` and
  `hardware/dolby`, since both device trees are already in this fork).
- `gta4l-common`: Dolby Atmos support (DAP, DVL, game, VQE and spatializer)
  through `hardware/dolby`, including the audio effects/policy and media
  codecs entries.
- This README.

## Build

1. Init Evolution X (Android 17 = `cnb`):

       repo init -u https://github.com/Evolution-X/manifest -b cnb

2. Add the local manifest **before** the first sync:

       mkdir -p .repo/local_manifests
       curl -L -o .repo/local_manifests/gta4l.xml \
         https://raw.githubusercontent.com/mkvenompro/android_device_samsung_gta4l/evolution-a17/evolution/local_manifest.xml

3. Sync:

       repo sync -j8

4. Extract the proprietary blobs (`vendor/samsung/gta4l`,
   `vendor/samsung/gta4l-common`) from a matching stock/LineageOS install:

       cd device/samsung/gta4l-common && ./extract-files.py <path-to-dump>
       cd ../gta4l && ./extract-files.py <path-to-dump>

5. Build:

       source build/envsetup.sh
       lunch lineage_gta4l-userdebug
       m evolution

   The flashable zip ends up in `out/target/product/gta4l/`.

## Notes

- Do **not** run `breakfast gta4l`: it resolves dependencies from the
  LineageOS / Evolution-X-Devices organizations, where these forks do not
  exist. The local manifest above replaces it.
- The device tree is unmodified LineageOS 24 code, so if you later fix bugs
  for Evolution X, they apply to LineageOS too.
