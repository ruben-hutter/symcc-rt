# This file is part of SymCC.
#
# SymCC is free software: you can redistribute it and/or modify it under the
# terms of the GNU General Public License as published by the Free Software
# Foundation, either version 3 of the License, or (at your option) any later
# version.
#
# SymCC is distributed in the hope that it will be useful, but WITHOUT ANY
# WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR
# A PARTICULAR PURPOSE. See the GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License along with
# SymCC. If not, see <https://www.gnu.org/licenses/>.

# Build the C++ wrapper that bridges _sym_* calls to _rsym_* (Rust runtime).
# The _rsym_* symbols are provided by LibAFL's symcc_runtime crate at final
# link time, so they remain unresolved in these libraries.

add_library(SymCCRtStatic STATIC
  ${SHARED_RUNTIME_SOURCES}
  Runtime.cpp)

target_include_directories(SymCCRtStatic PRIVATE
  ${CMAKE_CURRENT_SOURCE_DIR}
  ${CMAKE_CURRENT_SOURCE_DIR}/..)

set_target_properties(SymCCRtStatic PROPERTIES COMPILE_FLAGS "-Werror")

# Shared variant (optional, for non-static linking)
add_library(SymCCRtShared SHARED
  ${SHARED_RUNTIME_SOURCES}
  Runtime.cpp)

target_include_directories(SymCCRtShared PRIVATE
  ${CMAKE_CURRENT_SOURCE_DIR}
  ${CMAKE_CURRENT_SOURCE_DIR}/..)

# Allow unresolved _rsym_* symbols in the shared library
target_link_options(SymCCRtShared PRIVATE
  "LINKER:--unresolved-symbols=ignore-in-object-files")

# Backward-compatible alias
add_library(SymRuntime ALIAS SymCCRtStatic)

INSTALL(TARGETS SymCCRtStatic SymCCRtShared)
