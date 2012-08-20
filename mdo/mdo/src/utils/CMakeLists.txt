#---------------------------------------------------------------------------
# Copyright 2011 The Open Source Electronic Health Record Agent
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#---------------------------------------------------------------------------

SET(CSHARP_SOURCES
  AssertionGenerator
  Compression
  DateUtils
  FileIOUtils
  ResourceUtils
  SSTCryptographer
  StringUtils
  Utils
  XmlUtils
  )

SET(DEP)

# http://www.cmake.org/Wiki/CMake_FAQ#How_can_I_add_a_dependency_to_a_source_file_which_is_generated_in_a_subdirectory.3F
# Tell CMake the source won't be available until build time.
SET_SOURCE_FILES_PROPERTIES(${MDWS_LIBRARY_DIR}/mdo-utils-sharp.dll PROPERTIES GENERATED 1)

# Make sure the source is generated before the executable builds.
FOREACH(sourcefile ${CSHARP_SOURCES})
  file(TO_NATIVE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/${sourcefile}.cs result)
# WORKING_DIRECTORY is set to the src dir because of a strange issue with CSC compiler on Win32 system:
# http://groups.google.com/group/microsoft.public.dotnet.languages.csharp/browse_thread/thread/9d3ac7eb9f7f56be
  ADD_CUSTOM_COMMAND(
    OUTPUT ${MDWS_EXECUTABLE_DIR}/${sourcefile}.exe
    COMMAND ${CMAKE_CSHARP_COMPILER} ARGS "/r:${MDWS_LIBRARY_DIR}/mdo-utils-sharp.dll" "/out:${MDWS_EXECUTABLE_DIR}/${sourcefile}.exe" ${result}
    #WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
    DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/${sourcefile}.cs
    COMMENT "Create ${sourcefile}.exe"
  )
  ADD_CUSTOM_TARGET(mdo_utils_sharp_${sourcefile} DEPENDS ${MDWS_EXECUTABLE_DIR}/${sourcefile}.exe)
  ADD_DEPENDENCIES(mdo_utils_sharp_${sourcefile} MDWSMDOUTILSCSharp)

  SET(DEP ${DEP} ${MDWS_EXECUTABLE_DIR}/${sourcefile}.exe)
ENDFOREACH(sourcefile)

ADD_CUSTOM_TARGET(MDWS_MDO_UTILS_CSharp ALL
  DEPENDS ${DEP}
  COMMENT "building examples"
)
ADD_DEPENDENCIES(MDWS_MDO_UTILS_CSharp  MDWSMDOUTILSCSharp)

