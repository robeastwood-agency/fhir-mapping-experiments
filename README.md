### Goal

Transform FHIR Coverage instances - from version [1.4](https://hl7.org/fhir/2016May/coverage.html) to [R4](https://hl7.org/fhir/R4/coverage.html) using:

* instance of v1.4 Coverage
* FHIR mapping language - see [coveragev14toR4.map](./inputs/maps/coveragev14toR4.map)
* transform operation on [Matchbox server](https://test.ahdis.ch/matchbox/fhir/) - see [test http file](transform-coverage.http)

### Assumptions

* no 'out of the box' support for FHIR 1.4, therefore in an attempt to circumvent this, the respective StructureDefinitions were obtained and used (note- not sure if this approach is valid/workable, but we are giving it a try)

### What we have done

1. Coverage **v1.4** StructureDefinition
   * downloaded from the FHIR spec here https://hl7.org/fhir/2016May/coverage.profile.xml.html
   * updated the following elements to identify and differentiate as v1.4
      * `StructureDefinition.id` to `Coveragev14`
      * `StructureDefinition.url` to `http://example.org/fhir/StructureDefinition/Coveragev14`
      * `StructureDefinition.name` to `Coveragev14`
   * saved here: [StructureDefinition-coverage-v14.xml](inputs/StructureDefinition/StructureDefinition-coverage-v14.xml)
   * POSTed to matchbox using [this REST request](transform-coverage.http#L6-L10)
      * sfdsdf

1. Coverage **R4** StructureDefinition
   * downloaded from the FHIR spec here https://hl7.org/fhir/R4/coverage.profile.xml.html
   * updated the following elements to identify and differentiate as R4
      * `StructureDefinition.id` to `CoverageR4`
      * `StructureDefinition.url` to `http://example.org/fhir/StructureDefinition/CoverageR4`
      * `StructureDefinition.name` to `CoverageR4`   
   * saved here: [StructureDefinition-coverage-R4.xml](inputs/StructureDefinition/StructureDefinition-coverage-R4.xml)
   * POSTed to matchbox using [this REST request](https://github.com/robeastwood-agency/fhir-mapping-experiments/blob/main/transform-coverage.http#L15-L19)
      * sfdsdf

1. FML map file
   * created FML file: [coveragev14toR4.map](inputs/maps/coveragev14toR4.map)
   * Added the above 2 URLs into the map file as *source* and *target* (see [here](coveragev14toR4.map#L3-L4))
   * added FML rules to transform various v1.4 elements to respective R4 elements

1. v1.4 example Coverage instance
   * an [example from the v1.4 FHIR specification](https://hl7.org/fhir/2016May/coverage-example.xml.html) was downloaded to use as the input to the transform
   * saved [here](inputs/Coverage/example1.xml)
   * a couple of optional elements not present were added in order to invoke the respective rule (ie `network` and `contract`) 

1. Transform operation
   * issued the transform operation with [this REST request](transform-coverage.http#L33-L38)

1. Transform result
   * an instance of Coverage is successfully generated, however there are some gaps - see below

### Transform gaps

1. `Coverage.issuerReference` -> `Coverage.payor`
   * v1.4 Coverage.issuerReference is member of choice group `Coverage.issuer` and is of datatype `Reference`
   * R4 `Coverage.payor` is of datatype `Reference`
   * a [number of rule variations](inputs/maps/coveragev14toR4.map#L10-L14) have not yet yielded a successful transform
   * note that a transform of `Coverage.contract` -> `Coverage.contract` (also datatype `Reference`) does yield success

1. `Coverage.beneficiaryReference` -> `Coverage.beneficiary`
   * as datatype `Reference` - same issue as above

1. Datatype transform `Coding` to `CodeableConcept`
   * cannot figure out how to transform these elements:
      * `Coverage.type` (Coding) -> `Coverage.type` (CodeableConcept)
      * `Coverage.relationship` (Coding) -> `Coverage.relationship` (CodeableConcept)
   * see attempt [here](inputs/maps/coveragev14toR4.map#L32C5-L39)
