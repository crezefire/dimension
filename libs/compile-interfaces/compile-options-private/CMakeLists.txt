project(dimension-compile-options-private
    VERSION 0.0
    LANGUAGES CXX
)

option(DIMENSION_USE_LIBCXX "Use clangs libcxx. Only works when using clang." OFF)

add_library(dimension-compile-options-private
    INTERFACE
)

add_library(dime::compile-options-private
    ALIAS
        dimension-compile-options-private
)

if (MSVC)
    target_compile_options(dimension-compile-options-private
        INTERFACE
            /GR-    # Disable RTTI
            #/EH-    # Disable exception handling
            /MP     # Multi-process compilation
            /Za     # Disable language extensions
            /WX     # Warnings as errors
            /FA     # Generate asm
            /permissive-               # Strict conformance                 
            $<$<CONFIG:Release>:/Fa${PROJECT_BINARY_DIR}/asm-output/>        # Assembly location
            $<$<CONFIG:Debug>:/ZI>     # Edit and continue
            $<$<CONFIG:Release>:/GS->  # No Buffer Security Checks
    )
elseif(UNIX)
    target_compile_options(dimension-compile-options-private
        INTERFACE
            -fno-rtti
            -flto
            -Wall
            -Werror
            -march=native
            -fno-exceptions
            -pedantic
            $<$<CONFIG:Release>:-save-temps>
            $<$<CONFIG:RelWithDebInfo>:-fno-omit-frame-pointer>
    )

endif()

if (("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang") AND DIMENSION_USE_LIBCXX)
        target_compile_options(dimension-compile-options-private
            INTERFACE
                -stdlib=libc++
        )
endif()

install(
    TARGETS
        dimension-compile-options-private
    EXPORT
        DimensionCompileOptionsPrivateTargets
)

install(
    EXPORT
        DimensionCompileOptionsPrivateTargets
    FILE
        DimensionCompileOptionsPrivateTargets.cmake
    NAMESPACE
        dime::
    DESTINATION
        lib/cmake/DimensionCompileOptionsPrivateTargets
)
