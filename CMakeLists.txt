cmake_minimum_required(VERSION 3.2)
project(voip_patrol VERSION 1.0.0 LANGUAGES C CXX)

set(CMAKE_VERBOSE_MAKEFILE ON)

message("CMAKE_SYSTEM_PROCESSOR:${CMAKE_SYSTEM_PROCESSOR} CMAKE_SYSTEM:${CMAKE_SYSTEM}  CMAKE_BUILD_TYPE:${CMAKE_BUILD_TYPE} OV:${OV}")

set(ROOT_DIR ".")
set(SRC_DIR "${ROOT_DIR}/src")
set(PESQ_SRC_DIR "${SRC_DIR}/PESQ")
set(CURL_SRC_DIR "${SRC_DIR}/curl")
set(EZXML_SRC_DIR "${SRC_DIR}/ezxml")
set(HYPPO_SRC_DIR "${SRC_DIR}/voip_patrol")

set(PESQ_SRCS
	${PESQ_SRC_DIR}/dsp.c
	${PESQ_SRC_DIR}/pesqdsp.c
	${PESQ_SRC_DIR}/pesqio.c
	${PESQ_SRC_DIR}/pesqmod.c
)

set(CURL_SRCS
	${CURL_SRC_DIR}/email.c
)

set(EZXML_SRCS
	${EZXML_SRC_DIR}/ezxml.c
)
include_directories("${SRC_DIR}")

set(HYPPO_SRCS_CPP
	${HYPPO_SRC_DIR}/voip_patrol.cpp
)

set(HYPPO_SRCS_C
	${HYPPO_SRC_DIR}/patrol_pesq.c
)

include_directories(${ROOT_DIR}/pjsua/pjmedia/include)
include_directories(${ROOT_DIR}/pjsua/pjsip/include)
include_directories(${ROOT_DIR}/pjsua/pjlib/include)
include_directories(${ROOT_DIR}/pjsua/pjnath/include)
include_directories(${ROOT_DIR}/pjsua/pjlib-util/include)

include_directories(${ROOT_DIR}/include)

set(SOURCE_FILES ${HYPPO_SRCS_CPP} ${HYPPO_SRCS_C} ${CURL_SRCS} ${EZXML_SRCS} ${PESQ_SRCS})

add_executable(voip_patrol ${SOURCE_FILES})

set(CMAKE_LIBRARY_PATH
	"${ROOT_DIR}/pjsua/pjsip/lib"
	"${ROOT_DIR}/pjsua/pjnath/lib"
	"${ROOT_DIR}/pjsua/pjlib/lib"
	"${ROOT_DIR}/pjsua/media/lib"
	"${ROOT_DIR}/pjsua/pjlib-util/lib"
)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -g")

execute_process(COMMAND "./pjsua/config.guess" OUTPUT_VARIABLE AC_SYSTEM)
string(STRIP ${AC_SYSTEM} AC_SYSTEM)
target_link_libraries(voip_patrol
	pjsua2-${AC_SYSTEM}
	stdc++
	pjsua-${AC_SYSTEM}
	pjsip-ua-${AC_SYSTEM}
	pjsip-simple-${AC_SYSTEM}
	pjsip-${AC_SYSTEM}
	pjmedia-codec-${AC_SYSTEM}
	pjmedia-${AC_SYSTEM}
	pjmedia-videodev-${AC_SYSTEM}
	pjmedia-audiodev-${AC_SYSTEM}
	pjmedia-${AC_SYSTEM}
	pjnath-${AC_SYSTEM}
	pjlib-util-${AC_SYSTEM}
	srtp-${AC_SYSTEM}
	resample-${AC_SYSTEM}
	gsmcodec-${AC_SYSTEM}
	speex-${AC_SYSTEM}
	ilbccodec-${AC_SYSTEM}
	g7221codec-${AC_SYSTEM}
	pj-${AC_SYSTEM}
	pthread
	curl
	m
	asound
	-std=c++11
)

find_package(PkgConfig REQUIRED)
pkg_search_module(OPENSSL openssl)
if( OPENSSL_FOUND )
	message("openssl found")
	target_link_libraries(voip_patrol ssl crypto)
else()
	message("openssl not found")
endif()
