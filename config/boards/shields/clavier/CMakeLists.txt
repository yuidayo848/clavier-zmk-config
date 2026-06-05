# config/boards/shields/clavier/CMakeLists.txt
zephyr_library()
zephyr_library_sources(boards/shields/clavier/clavier_left.overlay)

# ドライバーのKconfigとソースを読み込ませるためのパスを通す
zephyr_library_include_directories(
    ${ZEPHYR_BASE}/../zmk-pmw3610-driver/include
)
