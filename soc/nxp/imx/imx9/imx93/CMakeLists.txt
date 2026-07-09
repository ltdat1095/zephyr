# SPDX-License-Identifier: Apache-2.0

if(CONFIG_SOC_MIMX9352_A55)
  zephyr_include_directories(.)
  zephyr_include_directories(a55)
  zephyr_sources(a55/soc.c)
  zephyr_sources_ifdef(CONFIG_ARM_MMU a55/mmu_regions.c)
  zephyr_sources_ifdef(CONFIG_MEMC_MCUX_FLEXSPI a55/flexspi.c)

  zephyr_sources(common_clock_set.c)
  set(SOC_LINKER_SCRIPT ${ZEPHYR_BASE}/include/zephyr/arch/arm64/scripts/linker.ld CACHE INTERNAL "")
elseif(CONFIG_SOC_MIMX9352_M33)
  zephyr_include_directories(.)
  zephyr_include_directories(m33)
  zephyr_sources(m33/soc.c)
  zephyr_sources(common_clock_set.c)
  set(SOC_LINKER_SCRIPT ${CMAKE_CURRENT_SOURCE_DIR}/m33/linker.ld CACHE INTERNAL "")
  zephyr_library_sources_ifdef(CONFIG_SOC_RESET_HOOK m33/imx93_m33_startup.S)
  # MobTurret: define CPU_<PART_NUMBER>_cm33 so the MCUXpresso SDK's
  # fsl_device_registers.h picks the right core-specific header
  # (MIMX9352_*_cm33.h). Without this, fsl_device_registers.h
  # errors out with "No valid CPU defined!". Derive from the
  # CONFIG_SOC_PART_NUMBER Kconfig variable which is set per board
  # (e.g., "MIMX9352DVVXM" for the i.MX93 EVK).
  if(CONFIG_SOC_PART_NUMBER)
    string(REPLACE "-" "_" CPU_DEFINE_PART "${CONFIG_SOC_PART_NUMBER}")
    zephyr_compile_definitions(${CMAKE_CURRENT_SOURCE_DIR}/m33/soc.c CPU_${CPU_DEFINE_PART}_cm33)
    zephyr_compile_definitions(${CMAKE_CURRENT_SOURCE_DIR}/common_clock_set.c CPU_${CPU_DEFINE_PART}_cm33)
    zephyr_compile_definitions(${CMAKE_CURRENT_SOURCE_DIR}/m33/imx93_m33_startup.S CPU_${CPU_DEFINE_PART}_cm33)
  endif()
endif()
