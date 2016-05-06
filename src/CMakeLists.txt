
set(SRCS main.c
         xs_kernel.c
         io.c
         init.c
         material.c
         utils.c)

if (PAPI)
    set(PAPI_SRCS papi.c)
endif (PAPI)

add_executable(rsbench ${SRCS} ${PAPI_SRCS})

target_link_libraries(rsbench m)

if (PAPI)
    target_link_libraries(rsbench ${PAPI_LIBRARIES})
endif (PAPI)
