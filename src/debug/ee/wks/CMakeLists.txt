
if (WIN32)

if (IS_64BIT_BUILD EQUAL 1)
  FIND_PROGRAM(ASM_COMPILER ml64.exe)
else ()
  FIND_PROGRAM(ASM_COMPILER ml.exe)
endif (IS_64BIT_BUILD EQUAL 1)

get_include_directories(ASM_INCLUDE_DIRECTORIES)
get_compile_definitions(ASM_DEFINITIONS)

# Need to compile asm file using custom command as include directories are not provided to asm compiler
add_custom_command(OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/dbghelpers.obj
                   COMMAND ${ASM_COMPILER} ${ASM_INCLUDE_DIRECTORIES} ${ASM_DEFINITIONS} /c /Zi /Fo${CMAKE_CURRENT_BINARY_DIR}/dbghelpers.obj /W3 /errorReport:prompt /Ta${CORDBEE_DIR}/${ARCH_SOURCES_DIR}/dbghelpers.asm
                   DEPENDS ${CORDBEE_DIR}/${ARCH_SOURCES_DIR}/dbghelpers.asm
                   COMMENT "Compiling dbghelpers.asm")

#mark obj as source that does not require compile
set_source_files_properties(${CMAKE_CURRENT_BINARY_DIR}/dbghelpers.obj PROPERTIES EXTERNAL_OBJECT TRUE)

add_library(cordbee_wks ${CORDBEE_SOURCES_WKS} ${CMAKE_CURRENT_BINARY_DIR}/dbghelpers.obj)

else ()

add_compile_options(-fPIC)
add_library(cordbee_wks ${CORDBEE_SOURCES_WKS} ../amd64/dbghelpers.S)

endif (WIN32)
