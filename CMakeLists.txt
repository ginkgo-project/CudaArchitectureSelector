cmake_minimum_required(VERSION 3.8)
project(CudaArchitectureSelector VERSION 1.0)

set(CAS_SOURCE "${PROJECT_SOURCE_DIR}/CudaArchitectureSelector.cmake"
    CACHE INTERNAL "Full path to CudaArchitectureSelector source")
mark_as_advanced(FORCE CAS_SOURCE)
