cmake_minimum_required(VERSION 3.8)
project(CudaArchitectureSelectorExample LANGUAGES CUDA)

add_subdirectory(.. modules)
include(CudaArchitectureSelector)


# regular usage: just describe the architectures for which you want to compile
# the CUDA sources of the target:
file(WRITE "${PROJECT_BINARY_DIR}/test.cu"
    "#include <cstdio>\n"
    "__global__ void hello() { printf(\"Hello World\"); }"
    "int main() { hello<<<1,1>>>(); }")
add_executable(test "${PROJECT_BINARY_DIR}/test.cu")
# Generate CUBIN for GPUs detected on the system and all Kepler-generation GPUs.
# Also generate PTX using the 3.0 instruction set.
# Do not generate anything for 2.0 and 2.1, even if such a GPU is detected on
# the system.
cas_target_cuda_architectures(test
    ARCHITECTURES "Auto" "Kepler" "(30)"
    UNSUPPORTED "20" "21")

# advanced usage (you probably don't need this!): 

# get list of architectures supported by the compiler
cas_get_supported_architectures(supported)
message("Architectures supported by the compiler: ${supported}")

# get list of architectures detected on the system
cas_get_onboard_architectures(onboard)
message("Architectures present on the system: ${onboard}")

# get list of architectures of a certain generation supported by the compiler
foreach(gen IN ITEMS Tesla Fermi Kepler Maxwell Pascal Volta)
    cas_get_architectures_by_name(${gen} arch_list)
    message("Supported ${gen} generation architectures: ${arch_list}")
endforeach()

# check if an architecture specification is valid and supported by the compiler
# agregates (Auto, All, generation names) are not supported
cas_is_supported_architecture_spec("52" out)
message("52    : ${out}")

cas_is_supported_architecture_spec("(52)" out)
message("  (52): ${out}")

cas_is_supported_architecture_spec("50(50)" out)
message("50(50): ${out}")

cas_is_supported_architecture_spec("39" out)
message("39    : ${out}")

cas_is_supported_architecture_spec("39" out)
message("  (39): ${out}")

cas_is_supported_architecture_spec("50(39)" out)
message("50(39): ${out}")

cas_is_supported_architecture_spec("39(50)" out)
message("39(50): ${out}")

# update list of flags with an architecture specification, agregates are not
# supported
set(flags "")
set(spec "30(32);30;50;(52)")
cas_update_flag_list(flags ARCHITECTURES ${spec})
message("Flags for ${spec}: ${flags}")

cas_update_flag_list(flags UNSUPPORTED "32;50")
message("Flags without 32;50: ${flags}")

# get a list of compiler flags for specification 
# (same syntax as cas_taget_cuda_architectures)
set(spec "Maxwell;Kepler;(30)")
cas_get_compiler_flags(flags
    ARCHITECTURES ${spec}
    UNSUPPORTED 20 21 35)
message("Flags for ${spec}: ${flags}")
