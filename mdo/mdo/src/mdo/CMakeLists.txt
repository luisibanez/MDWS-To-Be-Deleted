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
  Address
  AdHocHealthSummary
  Adt
  Allergy
  AnatomicPathologyReport
  Appointment
  Author
  BasicMdo
  ChemHemReport
  City
  Claim
  ClinicalReminder
  ClosestFacility
  CollectionSample
  Consult
  CytologyReport
  DataSource
  DemographicSet
  Diagnosis
  DictionaryHashList
  DischargeDiagnoses
  Drg
  ElectronMicroscopyReport
  EmailAddress
  Encounter
  FileHeader
  GeographicLocation
  HealthSummary
  HospitalLocation
  IcdReport
  ImagingExam
  ImagingWorklist
  IndexedHashtable
  InpatientStay
  ISpringer
  IvSolution
  LabObservation
  LabObservationType
  LabReport
  LabResult
  LabSpecimen
  LabTest
  MdoConstants
  MdoDocument
  MdoQuery
  MdoTimeInterval
  Medication
  MentalHealthInstrumentAdministration
  MentalHealthInstrument
  MentalHealthInstrumentResultSet
  MicrobiologyReport
  Note
  NoteDefinition
  NoteResult
  Observation
  ObservationType
  OEF_OIF
  OracleQuery
  Order
  OrderDialogColumn
  OrderDialog
  OrderDialogItem
  OrderDialogName
  OrderDialogRow
  OrderResponse
  OrderType
  PathologyReport
  PatientAssociate
  Patient
  PatientListEntry
  PatientRecordFlag
  PatientRecordFlagNote
  Person
  PersonName
  PhoneNum
  Problem
  ProstheticClaim
  Questionnaire
  QuestionnairePage
  QuestionnaireQuestion
  QuestionnaireSection
  QuestionnaireSet
  QuestionnaireSubsection
  RadiologyReport
  RatedDisability
  Region
  ReminderReportPatientList
  Report
  Service
  Site
  SiteId
  SiteTable
  SnoMedAttribute
  SnoMedConcept
  SnoMedDescription
  SocSecNum
  Specialty
  SpringVitalSign
  SqlQuery
  State
  SurgeryReport
  SurgicalPathologyReport
  Symptom
  Team
  TextReport
  TreatingSpecialty
  User
  UserOption
  UserSecurityKey
  Visit
  VitalSign
  VitalSignSet
  Ward
  Zipcode
  )

SET(DEP)

# http://www.cmake.org/Wiki/CMake_FAQ#How_can_I_add_a_dependency_to_a_source_file_which_is_generated_in_a_subdirectory.3F
# Tell CMake the source won't be available until build time.
SET_SOURCE_FILES_PROPERTIES(${MDWS_LIBRARY_DIR}/mdws-mdo-sharp.dll PROPERTIES GENERATED 1)

# Make sure the source is generated before the executable builds.
FOREACH(sourcefile ${CSHARP_SOURCES})
  file(TO_NATIVE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/${sourcefile}.cs result)
# WORKING_DIRECTORY is set to the src dir because of a strange issue with CSC compiler on Win32 system:
# http://groups.google.com/group/microsoft.public.dotnet.languages.csharp/browse_thread/thread/9d3ac7eb9f7f56be
  ADD_CUSTOM_COMMAND(
    OUTPUT ${MDWS_EXECUTABLE_DIR}/${sourcefile}.exe
    COMMAND ${CMAKE_CSHARP_COMPILER} ARGS "/r:${MDWS_LIBRARY_DIR}/mdws-mdo-sharp.dll" "/out:${MDWS_EXECUTABLE_DIR}/${sourcefile}.exe" ${result}
    #WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
    DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/${sourcefile}.cs
    COMMENT "Create ${sourcefile}.exe"
  )
  ADD_CUSTOM_TARGET(mdws_sharp_${sourcefile} DEPENDS ${MDWS_EXECUTABLE_DIR}/${sourcefile}.exe)
  ADD_DEPENDENCIES(mdws_sharp_${sourcefile} MDWSCSharp)

  SET(DEP ${DEP} ${MDWS_EXECUTABLE_DIR}/${sourcefile}.exe)
ENDFOREACH(sourcefile)

ADD_CUSTOM_TARGET(MDWSExampleCSharp ALL
  DEPENDS ${DEP}
  COMMENT "building examples"
)
ADD_DEPENDENCIES(MDWSExampleCSharp  MDWSCSharp)

