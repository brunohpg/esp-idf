if(BOOTLOADER_BUILD)
    return()
endif()

set(srcs)

if(CONFIG_PM_POWER_DOWN_CPU_IN_LIGHT_SLEEP)
    list(APPEND srcs "cpu_retention/port/${target}/sleep_cpu.c")
    if(CONFIG_SOC_PM_CPU_RETENTION_BY_SW)
        list(APPEND srcs "cpu_retention/port/${target}/sleep_cpu_asm.S")
        set_property(TARGET ${COMPONENT_LIB}
                    APPEND PROPERTY INTERFACE_LINK_LIBRARIES "-u rv_core_critical_regs_save")
        set_property(TARGET ${COMPONENT_LIB}
                    APPEND PROPERTY INTERFACE_LINK_LIBRARIES "-u rv_core_critical_regs_restore")
    endif()
endif()

if(CONFIG_IDF_TARGET_ESP32C5)
    list(REMOVE_ITEM srcs "cpu_retention/port/${target}/sleep_cpu.c"         # TODO: [ESP32C5] IDF-8638, IDF-8640
        )
endif()

add_prefix(srcs "${CMAKE_CURRENT_LIST_DIR}/" "${srcs}")

target_sources(${COMPONENT_LIB} PRIVATE "${srcs}")
