# Source files
set(tufao_SRC
    httpserver.cpp
    httpserverrequest.cpp
    httpserverresponse.cpp
    httpsserver.cpp
    priv/tcpserverwrapper.cpp
    priv/reasonphrase.cpp
    priv/http_parser.c
    websocket.cpp
    abstractmessagesocket.cpp
    httpfileserver.cpp
    httpserverrequestrouter.cpp
    headers.cpp
    priv/rfc1123.cpp
    priv/rfc1036.cpp
    priv/asctime.cpp
    sessionstore.cpp
    simplesessionstore.cpp
    notfoundhandler.cpp
    urlrewriterhandler.cpp
    httpupgraderouter.cpp
    priv/workerthreadpool.cpp
    priv/workerthread.cpp
    priv/workerrunnable.cpp
    abstractconnectionhandler.cpp
    httpconnectionhandler.cpp
    httpsconnectionhandler.cpp
    threadedhttpserver.cpp
)

add_definitions(-DTUFAO_LIBRARY
    -Wall
    -Wextra
#    -Wpedantic
#    -Weffc++
)

add_library("${TUFAO_LIBRARY}" SHARED ${tufao_SRC})

qt5_use_modules("${TUFAO_LIBRARY}" Core Network)

set_target_properties(
    "${TUFAO_LIBRARY}"
    PROPERTIES
    SOVERSION ${TUFAO_VERSION_MAJOR}
    VERSION ${TUFAO_VERSION}
    VS_KEYWORD Qt4VSv1.0
    AUTOMOC ON
    CXX_STANDARD 11
    CXX_STANDARD_REQUIRED ON
)

if("${CMAKE_VERSION}" VERSION_LESS "2.8.12")
  target_include_directories("${TUFAO_LIBRARY}" INTERFACE "${CMAKE_INSTALL_PREFIX}/${includedir}")
  install(TARGETS "${TUFAO_LIBRARY}" EXPORT "${TUFAO_LIBRARY}-export"
          LIBRARY DESTINATION "${libdir}"
          PUBLIC_HEADER DESTINATION "${includedir}"
  )
else()
  # Support relocatable packages. It can be usefull on Windows
  install(TARGETS "${TUFAO_LIBRARY}" EXPORT "${TUFAO_LIBRARY}-export"
          LIBRARY DESTINATION "${libdir}"
          PUBLIC_HEADER DESTINATION "${includedir}" INCLUDES DESTINATION "${includedir}"
  )
endif()

# Tests
if(ENABLE_TESTS)
    enable_testing()
    add_subdirectory(tests)
endif()

# Install info
install(TARGETS "${TUFAO_LIBRARY}"
    DESTINATION "${libdir}"
    COMPONENT runtime
)
install(DIRECTORY .
    DESTINATION "${includedir}/Tufao"
    COMPONENT development
    FILES_MATCHING PATTERN *.h
    PATTERN doc EXCLUDE
    PATTERN priv EXCLUDE
    PATTERN tests EXCLUDE
)
