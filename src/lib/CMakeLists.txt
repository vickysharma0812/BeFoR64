SET(SRC_PATH "${CMAKE_CURRENT_LIST_DIR}/")
TARGET_SOURCES(
    ${PROJECT_NAME} PRIVATE
    ${SRC_PATH}/befor64.F90
    ${SRC_PATH}/befor64_pack_data_m.F90
)

# set variables used for compile definitions of targets after support check
include(CheckFortranSourceRuns)
check_fortran_source_runs(
    "program r16p_support;
         integer, parameter :: r16p = selected_real_kind(33, 4931);
         if(r16p < 0) stop 1;
     end program r16p_support"
     R16P_SUPPORTED
     SRC_EXT f90)
if(R16P_SUPPORTED)
    set(r16p_supported "-D_R16P")
endif()

check_fortran_source_runs(
    "program ascii_support;
         integer, parameter :: ascii = selected_char_kind('ascii');
         if(ascii < 0) stop 1;
     end program ascii_support"
    ASCII_SUPPORTED
    SRC_EXT f90)
if(ASCII_SUPPORTED)
    set(ascii_supported "-D_ASCII_SUPPORTED")
endif()

check_fortran_source_runs(
    "program ascii_neq_default;
         integer, parameter :: ascii = selected_char_kind('ascii');
         integer, parameter :: default = selected_char_kind('default');
         if(ascii == default) stop 1;
     end program ascii_neq_default"
    ASCII_NEQ_DEFAULT
    SRC_EXT f90
)
if(ASCII_NEQ_DEFAULT)
    set(ascii_neq_default "-D_ASCII_NEQ_DEFAULT")
endif()

check_fortran_source_runs(
    "program ucs4_support;
         integer, parameter :: ucs4 = selected_char_kind('iso_10646');
         if(ucs4 < 0) stop 1;
     end program ucs4_support"
    UCS4_SUPPORTED
    SRC_EXT f90)
if(UCS4_SUPPORTED)
    set(ucs4_supported "-D_UCS4_SUPPORTED")
endif()

LIST( APPEND TARGET_COMPILE_DEF ${r16p_supported} )
LIST( APPEND TARGET_COMPILE_DEF ${ascii_supported} )
LIST( APPEND TARGET_COMPILE_DEF ${ascii_neq_default} )
LIST( APPEND TARGET_COMPILE_DEF ${ucs4_supported} )