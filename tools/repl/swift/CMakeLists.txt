# Set the correct rpath to locate libswiftCore.
if (LLDB_BUILD_FRAMEWORK)
  set(CMAKE_EXE_LINKER_FLAGS "-Wl,-rpath \
      -Wl,${CMAKE_BINARY_DIR}/${LLDB_FRAMEWORK_INSTALL_DIR}/${LLDB_FRAMEWORK_RESOURCE_DIR}/Swift/macosx")
elseif( CMAKE_SYSTEM_NAME MATCHES "Linux" )
  # Set the correct rpath to locate libswiftCore
  set(CMAKE_EXE_LINKER_FLAGS "-Wl,-rpath,${CMAKE_BINARY_DIR}/../swift-linux-x86_64/lib${LLVM_LIBDIR_SUFFIX}/swift/linux -Wl,-ldl")
endif()

add_lldb_tool(repl_swift
  main.c
  )

# The dummy repl executable is a C program, but we always look for a mangled
# swift symbol (corresponding to main). If we build the repl with debug info,
# the debugger looks at the frame language (looking up the compile unit) and gets
# confused.
set_target_properties(repl_swift PROPERTIES
    COMPILE_FLAGS "-g0")

if (LLDB_BUILD_FRAMEWORK)
  # lldb --repl expects the dummy executable to be located in the framework resource dir,
  # so we copy it.
  add_custom_command(TARGET repl_swift POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy
            ${CMAKE_BINARY_DIR}/bin/repl_swift
            ${CMAKE_BINARY_DIR}/${LLDB_FRAMEWORK_INSTALL_DIR}/${LLDB_FRAMEWORK_RESOURCE_DIR}/repl_swift
    )
endif()
