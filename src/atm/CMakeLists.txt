if(CMAKE_Fortran_COMPILER_ID MATCHES "^(Intel)$")
  set(CMAKE_Fortran_FLAGS
      "-g -traceback -convert big_endian -free -assume byterecl -fp-model strict"
  )
  set(CMAKE_Fortran_FLAGS_RELEASE "-O2")
elseif(CMAKE_Fortran_COMPILER_ID MATCHES "^(GNU|Clang|AppleClang)$")
  set(CMAKE_Fortran_FLAGS "-g -fconvert=big-endian -ffree-form")
  set(CMAKE_Fortran_FLAGS_RELEASE "-O2")
  set(CMAKE_Fortran_FLAGS_DEBUG "-ggdb -Wall")
endif()

if(CMAKE_C_COMPILER_ID MATCHES "^(Intel)$")
  set(CMAKE_C_FLAGS "-g -traceback")
  set(CMAKE_C_FLAGS_RELEASE "-O2")
elseif(CMAKE_C_COMPILER_ID MATCHES "^(GNU|Clang|AppleClang)$")
  set(CMAKE_C_FLAGS "-g -Wc++11-extensions")
  set(CMAKE_C_FLAGS_DEBUG "-ggdb -Wall")
endif()

if(${CMAKE_Fortran_COMPILER_ID} MATCHES "^(GNU)$" AND ${CMAKE_Fortran_COMPILER_VERSION} VERSION_GREATER_EQUAL 10)
  set(CMAKE_Fortran_FLAGS "${CMAKE_Fortran_FLAGS} -w -fallow-argument-mismatch -fallow-invalid-boz")
endif()

list(APPEND nemsiocc_srcs
            nems2ncvars.h
            nems2nc.h
            nems2nc.cc
            gfsnc.h
            gfsnc.cc
            nemsio.h
            nemsio.cc
            nemsio_interface.h
            nemsio_interface.f90)

set(module_dir ${CMAKE_CURRENT_BINARY_DIR}/include)

add_library(nemsiocc ${nemsiocc_srcs})
set_target_properties(nemsiocc PROPERTIES Fortran_MODULE_DIRECTORY ${module_dir})
target_include_directories(nemsiocc INTERFACE $<BUILD_INTERFACE:${module_dir}>
                                              $<INSTALL_INTERFACE:include>)

target_link_libraries(nemsiocc PRIVATE nemsio::nemsio bacio::bacio_4 w3nco::w3nco_d)
target_link_libraries(nemsiocc PRIVATE NetCDF::NetCDF_C)

add_executable(nemsioatm2nc nemsio2nc.cc)
add_dependencies(nemsioatm2nc nemsiocc)
target_link_libraries(nemsioatm2nc PRIVATE nemsiocc)
target_link_libraries(nemsioatm2nc PRIVATE nemsio::nemsio bacio::bacio_4 w3nco::w3nco_d)
target_link_libraries(nemsiocc PRIVATE NetCDF::NetCDF_C)

install(TARGETS nemsioatm2nc
        RUNTIME DESTINATION bin
        COMPONENT utilities)
