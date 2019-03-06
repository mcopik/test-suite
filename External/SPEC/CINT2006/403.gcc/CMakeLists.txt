include_directories(${BENCHMARK_DIR}/src)

include(TestBigEndian)
if(IS_BIGENDIAN)
  list(APPEND CPPFLAGS -DHOST_WORDS_BIG_ENDIAN)
endif()
if(TARGET_OS STREQUAL "Darwin")
  # Necessary for iOS
  list(APPEND LDFLAGS -Xlinker -stack_size -Xlinker 0x8000000)
endif()
list(APPEND CPPFLAGS -DPOSIX -DSPEC_CPU_HAVE_BOOL)

macro(test_input run_type ifile sfile)
  llvm_test_run(RUN_TYPE ${run_type} WORKDIR ${CMAKE_CURRENT_BINARY_DIR}
    data/${run_type}/input/${ifile} -o ${sfile}
  )
  llvm_test_verify(RUN_TYPE ${run_type} WORKDIR ${CMAKE_CURRENT_BINARY_DIR}
    ${FPCMP} data/${run_type}/output/${sfile} ${sfile}
  )
endmacro()

test_input(test cccp.i cccp.s)
test_input(train integrate.i integrate.s)
test_input(ref 166.i 166.s)
test_input(ref 200.i 200.s)
test_input(ref c-typeck.i c-typeck.s)
test_input(ref cp-decl.i cp-decl.s)
test_input(ref expr.i expr.s)
test_input(ref expr2.i expr2.s)
test_input(ref g23.i g23.s)
test_input(ref s04.i s04.s)
test_input(ref scilab.i scilab.s)

llvm_test_executable(403.gcc ${Source})
llvm_test_data_spec_default(403.gcc)
