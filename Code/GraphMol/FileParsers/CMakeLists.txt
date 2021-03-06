
remove_definitions(-DRDKIT_GRAPHMOL_BUILD)
add_definitions(-DRDKIT_FILEPARSERS_BUILD)

if(RDK_USE_BOOST_IOSTREAMS)
if(WIN32)
  find_package(Boost 1.56.0 COMPONENTS system iostreams REQUIRED)
  find_package(Boost 1.56.0 COMPONENTS zlib)
  set (link_iostreams ${Boost_LIBRARIES})
  if (NOT Boost_USE_STATIC_LIBS)
     add_definitions("-DBOOST_IOSTREAMS_DYN_LINK")
  endif()
else()
  find_package(Boost 1.56.0 COMPONENTS system iostreams REQUIRED)
  set (link_iostreams ${Boost_LIBRARIES})
  if (NOT Boost_USE_STATIC_LIBS)
     add_definitions("-DBOOST_IOSTREAMS_DYN_LINK")
  endif()
endif()
ADD_DEFINITIONS("-DRDK_USE_BOOST_IOSTREAMS")
endif(RDK_USE_BOOST_IOSTREAMS)

if(RDK_BUILD_COORDGEN_SUPPORT)
    include_directories(${maeparser_INCLUDE_DIRS})
    set (maesupplier MaeMolSupplier.cpp)
endif()

if(RDK_USE_BOOST_REGEX)
  add_definitions(-DRDKIT_USE_BOOST_REGEX)
  find_package(Boost 1.58.0 COMPONENTS regex REQUIRED)
  set(regex_lib Boost::regex)
endif()

rdkit_library(FileParsers
              Mol2FileParser.cpp MolFileParser.cpp
              MolSGroupParsing.cpp MolSGroupWriting.cpp
              MolFileStereochem.cpp MolFileWriter.cpp
              ForwardSDMolSupplier.cpp SDMolSupplier.cpp SDWriter.cpp
              SmilesMolSupplier.cpp SmilesWriter.cpp
              TDTMolSupplier.cpp TDTWriter.cpp
              TplFileParser.cpp TplFileWriter.cpp
              PDBParser.cpp PDBWriter.cpp PDBSupplier.cpp
              XYZFileWriter.cpp
              ${maesupplier}
              ProximityBonds.cpp
              SequenceParsers.cpp SequenceWriters.cpp
              SVGParser.cpp
              LINK_LIBRARIES Depictor SmilesParse GraphMol ${RDK_COORDGEN_LIBS} ${regex_lib} ${link_iostreams})

rdkit_headers(FileParsers.h
              FileParserUtils.h
              MolFileStereochem.h
              MolSupplier.h
              MolWriters.h
              SequenceParsers.h SequenceWriters.h
              DEST GraphMol/FileParsers)

rdkit_test(fileParsersTest1 test1.cpp
           LINK_LIBRARIES FileParsers SubstructMatch )



rdkit_test(testMolSupplier testMolSupplier.cpp
           LINK_LIBRARIES  FileParsers RDStreams)

rdkit_test(testMolWriter testMolWriter.cpp LINK_LIBRARIES FileParsers )

rdkit_test(testTplParser testTpls.cpp LINK_LIBRARIES FileParsers )

rdkit_test(testMol2ToMol testMol2ToMol.cpp LINK_LIBRARIES FileParsers )

rdkit_test(testSequence testSequence.cpp LINK_LIBRARIES FileParsers )

rdkit_test(testExtendedStereoParsing testExtendedStereoParsing.cpp
           LINK_LIBRARIES FileParsers )

rdkit_catch_test(fileParsersCatchTest file_parsers_catch.cpp catch_main.cpp 
           LINK_LIBRARIES FileParsers )

rdkit_catch_test(testPropertyLists testPropertyLists.cpp catch_main.cpp 
           LINK_LIBRARIES FileParsers )
