include (GenerateExportHeader)
include_directories(../deps/marisa-0.2.5/include)
include_directories(../deps/rapidjson-1.1.0)
include_directories(../deps/tclap-1.2.2)

# Library

set(
  LIBOPENCC_HEADERS
  Common.hpp
  Config.hpp
  Conversion.hpp
  ConversionChain.hpp
  Converter.hpp
  Dict.hpp
  DictConverter.hpp
  DictEntry.hpp
  DictGroup.hpp
  Exception.hpp
  Export.hpp
  Lexicon.hpp
  MarisaDict.hpp
  MaxMatchSegmentation.hpp
  Optional.hpp
  PhraseExtract.hpp
  Segmentation.hpp
  Segments.hpp
  SerializableDict.hpp
  SerializedValues.hpp
  SimpleConverter.hpp
  TextDict.hpp
  UTF8StringSlice.hpp
  UTF8Util.hpp
  opencc.h
)

set(
  LIBOPENCC_SOURCES
  Config.cpp
  Conversion.cpp
  ConversionChain.cpp
  Converter.cpp
  Dict.cpp
  DictConverter.cpp
  DictEntry.cpp
  DictGroup.cpp
  MarisaDict.cpp
  MaxMatchSegmentation.cpp
  PhraseExtract.cpp
  SerializedValues.cpp
  SimpleConverter.cpp
  Segmentation.cpp
  TextDict.cpp
  UTF8StringSlice.cpp
  UTF8Util.cpp
)

set(UNITTESTS
  ConfigTest
  ConversionChainTest
  ConversionTest
  DictGroupTest
  MarisaDictTest
  MaxMatchSegmentationTest
  PhraseExtractTest
  SerializedValuesTest
  SimpleConverterTest
  TextDictTest
  UTF8StringSliceTest
  UTF8UtilTest
)

if (ENABLE_DARTS)
  include_directories(../deps/darts-clone)
  set(
    LIBOPENCC_HEADERS
    ${LIBOPENCC_HEADERS}
    BinaryDict.hpp
    DartsDict.hpp
  )
  set(
    LIBOPENCC_SOURCES
    ${LIBOPENCC_SOURCES}
    BinaryDict.cpp
    DartsDict.cpp
  )
  set(
    UNITTESTS
    ${UNITTESTS}
    BinaryDictTest
    DartsDictTest
  )
endif()

add_library(libopencc ${LIBOPENCC_SOURCES} ${LIBOPENCC_HEADERS})
set_target_properties(libopencc PROPERTIES POSITION_INDEPENDENT_CODE ON)
source_group(libopencc FILES ${LIBOPENCC_SOURCES} ${LIBOPENCC_HEADERS})
target_link_libraries(libopencc marisa)

GENERATE_EXPORT_HEADER(
  libopencc
  BASE_NAME OPENCC
  EXPORT_MACRO_NAME OPENCC_EXPORT
  EXPORT_FILE_NAME Opencc_Export.h
  STATIC_DEFINE Opencc_BUILT_AS_STATIC
)

set_target_properties(
  libopencc
  PROPERTIES
    LINKER_LANGUAGE
      CXX
    OUTPUT_NAME
      opencc
    VERSION
      1.0.0
    SOVERSION
      2
)

# Installation

install(
  TARGETS libopencc
  LIBRARY DESTINATION ${DIR_LIBRARY}
  ARCHIVE DESTINATION ${DIR_LIBRARY}
  RUNTIME DESTINATION bin
)

install(
  FILES ${LIBOPENCC_HEADERS}
  DESTINATION ${DIR_INCLUDE}/opencc
)

# Gtest

if (ENABLE_GTEST)
  foreach(TESTCASE ${UNITTESTS})
    add_executable(${TESTCASE} ${TESTCASE}.cpp)
    target_link_libraries(${TESTCASE} gtest gtest_main libopencc)
    add_test(${TESTCASE} ${TESTCASE})
  endforeach()
endif()

# Subdir

add_subdirectory(tools)
