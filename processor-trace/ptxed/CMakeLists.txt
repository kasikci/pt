# Copyright (c) 2013-2014, Intel Corporation
#
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions are met:
#
#  * Redistributions of source code must retain the above copyright notice,
#    this list of conditions and the following disclaimer.
#  * Redistributions in binary form must reproduce the above copyright notice,
#    this list of conditions and the following disclaimer in the documentation
#    and/or other materials provided with the distribution.
#  * Neither the name of Intel Corporation nor the names of its contributors
#    may be used to endorse or promote products derived from this software
#    without specific prior written permission.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
# AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
# IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
# ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT OWNER OR CONTRIBUTORS BE
# LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR
# CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF
# SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS
# INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN
# CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE)
# ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE
# POSSIBILITY OF SUCH DAMAGE.

include_directories(
  include
  ../libipt/internal/include
)

include_directories(SYSTEM
  ${XED_INCLUDE}
)

link_directories(
  ${XED_LIBDIR}
)

set(PTXED_FILES
  src/ptxed.c
  ../libipt/src/pt_cpu.c
)

if (FEATURE_ELF)
  set(PTXED_FILES ${PTXED_FILES} src/load_elf.c)
endif (FEATURE_ELF)

add_executable(ptxed
  ${PTXED_FILES}
)
target_link_libraries(ptxed libipt)

if (CMAKE_HOST_UNIX)
  target_link_libraries(ptxed
    # XED is spelled libxed on linux
    #
    xed
  )
endif (CMAKE_HOST_UNIX)

if (CMAKE_HOST_WIN32)
  target_link_libraries(ptxed
    # XED is spelled libxed on windows
    #
    libxed
  )

  # suppress warnings from XED header files
  #
  #   w4127: conditional expression is constant
  #
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} /wd4127")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /wd4127")

  #   w4244: conversion: possible loss of data
  #
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} /wd4244")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /wd4244")
endif (CMAKE_HOST_WIN32)
