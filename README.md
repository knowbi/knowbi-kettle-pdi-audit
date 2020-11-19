# Kettle/PDI Code Audit

Kettle (a.k.a Pentaho Data Integration) is an open source data integration platform. 
This repository contains a set of tools to perform checks on code quality and working practices. 

## Using this repository 

The checks are built in Kettle. You'll need 
- Java 8 
- Kettle/PDI download 8.2 [here](https://sourceforge.net/projects/pentaho/files/Pentaho%208.2/client-tools/pdi-ce-8.2.0.0-342.zip/download).


The main job is `jb_pentaho_code_audit.kjb`. This job takes two parameters: 
- PRM_DO_AUDIT (default: 'Y'): perform a code audit 
- PRM_DO_WPS (default: 'Y'): perform a working practices check 

## Configuration 

The `config` folder contains a number of files. These are stored in this code repository as `filename.properties.template`. Create your copy of each file without the `.template` extension to configure this framework.  

### General 

- `kettle-pdi-general.properties`: general properties about your Kettle/PDI version, code paths etc 
<details>
  <summary>click for details</summary>
  - `kettle.pdi.version`: your Kett/PDI version, e.g. 8.2
  - `kettle.pdi.locale`: your locale, eg. en_US
  - `kettle.pdi.code.tmp.dir`: temporary directory to write the audit and working practices checks to. 
  - `kettle.pdi.code.path`: path to the jobs and transformations you'd like to check 
  - `kettle.pdi.code.path.exclude.dir`: directory that needs to be excluded from `kettle.pdi.code.path`
  - `know.bi.error.handling.framework`: shared error handling framework. Processes all incoming errors based on a provided error code. Error handling and actions to be taken are handled from a set of business rules, as defined in [this](https://github.com/knowbi/knowbi-pentaho-error-handling-framework) example repository (private repository, will be public soon (2020-11-17)).     
</details>

### Audit 

- `kettle-pdi-audit.properties`: a set of audit-specific properties. 
<details>
  <summary>click for details</summary>
  - `impacted.tables`: tables to specifically include for table impact analysis
</details>

### Working Practices

- `kettle-pdi-code-wps.properties`: configuration for the working practice checks to be performed. 
<details>
  <summary>click for details</summary>
  - `kettle.pdi.conventions.naming.transformation.step.default.avoid` (default: 'Y'): avoid the default step name, e.g. avoid 'Select Values'. Default step names don't offer any context and make a transformations unnecessarily hard to read. 
  - `kettle.pdi.conventions.naming.job.entry.default.avoid` (default: 'Y'): avoid the default job entry name, e.g. avoid 'Simple Evaluation' as a job entry name. Default job entry names don't offer any context and make a job unnecessarily hard to read. 
  - `pentaho.code.wps.pdi.trans.prefix` (default: 'tr_'): filename prefix for transformations
  - `kettle.pdi.code.wps.pdi.job.prefix` (default: 'jb_'): filename prefix for jobs
  - `kettle.pdi.code.wps.pdi.trans.casing` (default: 'lower'): filename casing for transformations (lower, upper, initcap)
  - `kettle.pdi.code.wps.pdi.job.casing` (default: 'lower'): filename casing for jobs (lower, upper, initcap)
  - `kettle.pdi.code.wps.pdi.trans.hasnote` (default: 'Y'): are notes required for transformations?
  - `kettle.pdi.code.wps.pdi.job.hasnote` (default: 'Y'): are notes required for jobs? 
  - `kettle.pdi.code.wps.pdi.trans.hasdescription` (default: 'Y'): are descriptions required for transformations?
  - `kettle.pdi.code.wps.pdi.job.hasdescription` (default: 'Y'): are descriptions required for jobs? 
  - `kettle.pdi.code.wps.pdi.trans.ignoredefaults` (default: ''): comma separated list of step types to ignore for default step name checks.
  - `kettle.pdi.code.wps.pdi.job.ignoredefaults` default: "Abort job,Success"): comma separated list of job entry types to ignore for default job entry name checks (there is no added value in providing context for a 'Start' or 'Success' job entry). 
</details>

## Code Audit 

The checks performed in the code audit job are listed below. A csv file for the results per check is written to `${kettle.pdi.code.tmp.dir}/output/audit` 

- **Connections**: generates a list of connections and their configuration. Use the results from this check to identify inconsistent, unused, incorrect, hard coded etc connections.  
- **Step Types**: generates a number of views on the steps used in your transformations. Use this list to identify transformations that use spefic step types, e.g. blockers
- **Table Impact**: lists all tables that are used in jobs and transformations that read from or write to tables. 
- **File Impact**: lists all files (excluding job and transformation files) that are used in jobs and transformations to read from or write to files. Use this overview to find out which files are read/created in your jobs and transformations, and to make sure no hard coded files exist in your code base. 
-**Logging**: lists all logging settings defined through variables or in job and/or transformations files. Use this information to avoid logging settings littered through your jobs and transformations.  
- **Carte**: lists all carte definitions in your jobs and transformations. Use this list to clean any incorrect, unneeded, hard coded carte definitions etc. 
- **Repository**: lists all job entries and step types that reference repository objects. 

## Working Practices

The working practices downloads the xml and properties files for steps and job entries to compare the actual step and entry names to the default names, and uses those files to perform the working practices checks. 

- **default step entry names**: a list of steps and job entries that use default names, or contain the default step/entry name
- **naming conventions**: checks the naming conventions as defined in the configuration 
- **disabled hops**: checks all jobs and transformations for disabled hops. Disabled Hops are equivalent of code that is commented out. Jobs and transformations that have large commented sections shouldn't be deployed to production. 



