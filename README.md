# OCDS Extension Template Repository

[OCDS extensions](https://standard.open-contracting.org/latest/en/extensions/) can be used to declare additional fields, objects and sections that will be included in an OCDS file, but which are not in the core schema, and to add or extend codelists. When any OCDS publication cites an extension in its' package metadata, it should be validated against the schema with that extension applied. 

This repository provides a template of an extension that people can base their own on.

## Getting started

To create an extension based on this template, you should:

1. Download [a zip file version of the template](https://github.com/open-contracting/standard_extension_template/archive/master.zip)
1. Extract it, and initialise it as a git repository (`git init`) †
1. Update the README.md and extension.json files, and prepare any schema, codelist, documentation and test files
1. Commit the files you have changed
1. Delete files you have not changed
1. Push to a new public git repository
1. (For core or community extensions) Register this extension with the [extensions registry](https://github.com/open-contracting/extension_registry)

† Use of git for version control is recommended, but optional. Extensions can be hosted on any public HTTP server, providing the file structure matches that in this template repository. 

## Naming extension repositories

Names for extension repositories should conform to the following pattern:

`ocds_[name]_extension`

For example, `ocds_additionalContactPoints_extension`.

`[name]` should indicate the purpose of the extension, and/or the field or object being extended to aid self-documentation. This could be the JSON Pointer fragment for the name of the primary field or object that the extension introduces.

## Extension repository structure

The structure of an extension repository should look like:

```text
├── README.md (a full description of the extension in markdown - required)
├── extension.json (a json file giving metadata about the extension - required)
├── release-schema.json (JSON Merge Patch of release-schema.json)
├── record-package-schema.json (JSON Merge Patch of record-package-schema.json)
├── release-package-schema.json (JSON Merge Patch of release-package-schema.json)
└── codelists
    ├── emptyCodelist.csv (A new codelist)
    |__ +milestoneType.csv (Add values to the milestoneType codelist)
    └── milestoneType.csv (Overwrite the existing codelist - not recommended)
```

This copies the layout of the core [standards repository](https://github.com/open-contracting/standard/tree/HEAD/schema).

### Schema files

The extension template includes the following schema files:

* release-schema.json
* release-package-schema.json
* record-package-schema.json

In most cases, the extension will have a _release-schema.json_ with the minimal changes required to patch the schema, although there may be more marginal user cases requiring metadata patches for _release-package-schema.json_ and/or _record-package-schema.json_. Empty schema files should not be included in the extension.

### extension.json

This file is required. It provides information about the extension to be used by any OCDS automated tool.

This repository contains an [example extension.json](https://github.com/open-contracting/standard_extension_template/blob/master/extension.json), whose format is described by [extension-schema.json](https://github.com/open-contracting/standard-maintenance-scripts/blob/main/schema/extension-schema.json).

#### Required fields

* `name`: An object mapping language codes to the title case name of the extension in the language
* `description`: An object mapping language codes to the description of the extension in the language
* `documentationUrl`: An object mapping language codes to the URL for documentation in the language, e.g. the extension's GitHub page
* `compatibility`: An array of minor versions of the core standard that the extension is compatible with, e.g. `[ "1.1" ]`
* `contactPoint/name`: The name of the contact point to communicate with the extension's authors or maintainers. It can be an individual or an organization.
* `contactPoint/email`: The email address of the contact point.

#### Optional fields

* `schemas`: An array of the filenames of the JSON Schema files in the extension's root directory, e.g. `[ "release-schema.json" ]`
* `codelists`: An array of the filenames of the CSV files in the extension's `codelists` directory, e.g. `[ "codelistName.csv", "+milestoneType.csv" ]`
* `dependencies`: An array of the URLs of other extensions whose definitions this extension $ref'erences, e.g. `[ "http://path/to/extension/extension.json" ]`

#### Description

Here are some guidelines for writing the text for the mandatory `description` field in _extension.json_ :

* There is no maximum length for the description, but you should try to keep it concise. In any case, do not sacrifice clarity for the sake of brevity.
* Refer to the part(s) of the schema the extension is modifying.
* Do not include in the description the development status of the extension (e.g. _draft_). If you need to add current status, do so in a _README_ file, it will be much more visible and therefore less prone to be forgotten and not updated.
* Avoid descriptions that simply duplicate or paraphrase the extension name.

For example, for [ocds_performance_failures_extension](https://github.com/open-contracting-extensions/ocds_performance_failures_extension), this wouldn't be a good description:

> An extension covering performance failures in OCDS.

The actual description in the extension is much better:

> Adds fields to the implementation section to allow disclosure of an array of contracting performance failures. Based on the performance failures reporting table defined in the World Bank Framework for Disclosure in PPPs.

#### Translation

To translate the `name` and `description` fields, and to provide `documentationUrl` in multiple languages, add additional key-value pairs for each language, for example:

```json
{
  "description": {
    "es": "Descripción",
    "en": "Description"
  }
}
```

## Codelists

As in the [core standard repository](https://github.com/open-contracting/standard), the extension template also includes a codelists folder to store extension-specific codelists.

Codelists are CSV files with camel case names, e.g. _implementationStatus.csv_. Be aware that a codelist in your extension using the same name as an existing codelist in the standard repository will override the existing codelist.

Prefixing a codelist name with _+_ (e.g. _+milestoneStatus.csv_) indicates that contents of the codelist should be appended to an existing codelist of the same name. 

## How the extensions work

As of OCDS 1.1, the schema files from the core standard that extensions can extend are:

* release-schema.json
* record-package-schema.json
* release-package-schema.json

In the extension some or all of these files can be used to do a JSON Merge Patch of the corresponding file.

A JSON Merge Patch is described by this [RFC](https://tools.ietf.org/html/rfc7386).

The patches are very simple. They just copy the same structure from the core schema and allow you add your extra fields or update existing fields just in the places the extension wants to change the schema.

Here are some simple examples of how this works. They are illustrative; any changes to existing fields should respect the [Conformance documentation](https://standard.open-contracting.org/latest/en/schema/conformance_and_extensions/).

They all are examples of what could go in `release-schema.json` files in an extension.

Add a new field `newField` to the release.

```json
{
  "properties": {
    "newField": {
      "title": "New field",
      "description": "A new field",
      "type": "string"
    }
  }
}
```

Add a new field `newOrgField` to the organization definition.

```json
{
  "definitions": {
    "Organization": {
      "newOrgField": {
        "title": "New Org field",
        "description": "A new field",
        "type": "string"
      }
    }
  }
}
```

## Best practice

It is possible to copy the whole of the schemas from the core, change them and as long as you have only added or changed properties the extension will work as expected. However doing it this way will mean that it will be hard to see what changes are made and will be much harder to track changes of the core schema (as every change in the core schema will need to be copied).

So it is best practice to just put in an extension the minimal changes that are required. However, it can be useful to work with the whole schema when developing an extension. In this case use the [json-merge-patch library](https://github.com/open-contracting/json-merge-patch) and it will generate the minimal patch for you by using the command line tool.

```shell
json-merge-patch create-patch core_schema.json new_modified_schema.json -o minimal_patch.json
```

You can also test your extension by:

```shell
json-merge-patch merge core_schema.json your_extension.json -o expected_output.json
```
