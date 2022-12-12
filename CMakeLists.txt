cmake_minimum_required(VERSION 3.2)
project(binapi)

set(CMAKE_CXX_STANDARD 14)

#set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra -fsanitize=address")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra")

add_definitions(
        -UNDEBUG
        -DDTF_HEADER_ONLY
)

include_directories(
        ./include
)

if (DEFINED ${BOOST_INCLUDE_DIR})
    include_directories(
            ${BOOST_INCLUDE_DIR}
    )
endif()


find_package(OpenSSL REQUIRED)

if (${OPENSSL_FOUND})
    include_directories(
            ${OPENSSL_INCLUDE_DIR}
    )
else()
    message(FATAL_ERROR "OpenSSL not found please install it!")
endif()

find_package(Threads REQUIRED)
if (NOT ${Threads_FOUND})
    message(FATAL_ERROR "Threads not found please install it!")
endif()

find_package(Boost REQUIRED)
if (${Boost_FOUND})
    include_directories(
            ${Boost_INCLUDE_DIRS}
    )
else()
    message(FATAL_ERROR "Boost not found please install it!")
endif()

find_package(ZLIB REQUIRED)

if (${ZLIB_FOUND})
    include_directories(
            ${ZLIB_INCLUDE_DIRS}
    )
else()
    message(FATAL_ERROR "ZLIB not found please install it!")
endif()

set(BINAPI_HEADERS
        binapi/api.hpp
        binapi/flatjson.hpp
        binapi/dtf.hpp
        binapi/double_type.hpp
        binapi/enums.hpp
        binapi/errors.hpp
        binapi/invoker.hpp
        binapi/message.hpp
        binapi/pairslist.hpp
        binapi/reports.hpp
        binapi/tools.hpp
        binapi/types.hpp
        binapi/websocket.hpp
        )

set(BINAPI_SOURCES
        src/api.cpp
        src/enums.cpp
        src/errors.cpp
        src/pairslist.cpp
        src/reports.cpp
        src/tools.cpp
        src/types.cpp
        src/websocket.cpp
        )

add_library(${PROJECT_NAME} STATIC
        ${BINAPI_SOURCES})

target_link_libraries(
        ${PROJECT_NAME}
        PRIVATE ${OPENSSL_LIBRARIES}
        PRIVATE ${ZLIB_LIBRARIES}
        PRIVATE ${CMAKE_THREAD_LIBS_INIT}
        #this is herer because of this: https://stackoverflow.com/questions/50163828/c-addresssanitizer-with-cmakelists-txt-results-in-asan-errors
        #        -fsanitize=address
)
