# For file(GLOB_RECOURSE CONFIGURE_DEPENDS ...)
cmake_minimum_required(VERSION 3.12)

project(fheroes2 VERSION 0.9.4 LANGUAGES C CXX)
set (CMAKE_CXX_STANDARD 11)

include(GNUInstallDirs)
list(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake)

# Compile-time options
set(SDL_VERSIONS SDL SDL2)
set(USE_SDL_VERSION SDL2 CACHE STRING "SDL version")
set_property(CACHE USE_SDL_VERSION PROPERTY STRINGS ${SDL_VERSIONS})

option(ENABLE_IMAGE    "Enable SDL/SDL2 Image support (requires libpng)" ON)
option(ENABLE_UNICODE  "Enable Unicode support (requires SDL/SDL2 TTF and iconv libraries)" ON)
option(ENABLE_AUDIOCD  "Enable AudioCD support" OFF)
option(ENABLE_TOOLS    "Enable additional tools" OFF)
option(ENABLE_RELEASE  "Release mode (disables debug options)" ON)

option(USE_SYSTEM_LIBSMACKER     "Use system libsmacker instead bundled version" OFF)

option(FHEROES2_STRICT_COMPILATION "Enable -Werror strict compilation" OFF)

set(CONFIGURE_FHEROES2_LOCALEDIR "${CMAKE_INSTALL_LOCALEDIR}/${PROJECT_NAME}" CACHE STRING "System Locale directory")
set(CONFIGURE_FHEROES2_DATA      "${CMAKE_INSTALL_DATADIR}/${PROJECT_NAME}" CACHE STRING "System fheroes2 game directory")

# Find libraries and enable/disable features
find_package(${USE_SDL_VERSION} REQUIRED)
find_package(${USE_SDL_VERSION}_mixer REQUIRED)
find_package(ZLIB REQUIRED)
find_package(Threads)

if(ENABLE_IMAGE)
	find_package(${USE_SDL_VERSION}_image REQUIRED)
	find_package(PNG REQUIRED)
endif(ENABLE_IMAGE)
if(ENABLE_UNICODE)
	find_package(${USE_SDL_VERSION}_ttf REQUIRED)
	find_package(Iconv REQUIRED)
endif(ENABLE_UNICODE)

add_subdirectory(src)

install(
	FILES doc/README.txt LICENSE changelog.txt
	DESTINATION ${CMAKE_INSTALL_DOCDIR}
	)
if(WIN32)
	install(
		FILES script/demo/demo_windows.bat script/demo/demo_windows.ps1
		DESTINATION ${CMAKE_INSTALL_DOCDIR}/demo
		)
else(WIN32)
	install(
		FILES script/demo/demo_unix.sh
		PERMISSIONS OWNER_EXECUTE OWNER_WRITE OWNER_READ GROUP_EXECUTE GROUP_READ WORLD_EXECUTE WORLD_READ
		DESTINATION ${CMAKE_INSTALL_DOCDIR}/demo
		)
endif(WIN32)
