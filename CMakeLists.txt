# Workaround for prospector-zmk-module (status_advertisement.c).
#
# The module picks its advertising layout with
#   #if defined(BT_LE_ADV_OPT_FORCE_NAME_IN_AD)
# but in Zephyr that name is an enum constant, not a macro, so the test is
# always false and the "older Zephyr" layout is used: a full 31-byte AD
# (flags + manufacturer data). ZMK advertises with the device name forced
# into the AD, so while no BLE host is connected every update fails with
# "Too big advertising data" (-EINVAL) and the scanner never sees the board.
#
# Defining the name as a macro that expands to itself makes the test true
# without changing the value, selecting the layout the module intended for
# this Zephyr: name in AD, manufacturer data in the scan response.
# Remove once the module tests the Zephyr version instead.
if(CONFIG_ZMK_STATUS_ADVERTISEMENT)
  zephyr_compile_definitions(BT_LE_ADV_OPT_FORCE_NAME_IN_AD=BT_LE_ADV_OPT_FORCE_NAME_IN_AD)
endif()
