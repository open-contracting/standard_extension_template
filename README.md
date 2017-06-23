OCDS Extension Template Repository
==================================

This repository has two purposes:

 *  To give a template of an extension that people can base their own on.
 *  To give a place for comment and discussion on how extensions should work.

The structure of the extension repository should look like this:

```
├── README.md (a full description of the extension in markdown - required)
├── extension.json (a json file giving metadata about the extension - required)
├── release-schema.json (json merge patch of release-schema.json)
├── record-package-schema.json (json merge patch of record-package-schema.json)
├── release-package-schema.json (json merge patch of release-package-schema.json)
├── versioned-release-validation-schema.json (json merge patch of versioned-release-validation-schema.json)
├── codelists 
│   ├── emptyCodelist.csv (A new codelist)
│   └── awardCriteria.csv (This will overwrite the existing codelist)
├── docs (more in depth documentation if required)
│   └── index.md
├── tests (tests to run against the schema)
│   └──
└── tools (tools to help)
    └── 
```

This layout is copied from the core [standards repository](https://github.com/open-contracting/standard/tree/1.0/standard/schema) and is intended to be a useful reference when developing an extension.


extension.json
--------------

This file is required. It provides information about the extension to be used by any OCDS automated tool.

This repository contains an [example extension.json](https://github.com/open-contracting/standard_extension_template/blob/master/extension.json) and also an [extension-schema.json](https://github.com/open-contracting/standard_extension_template/blob/master/schema/extension-schema.json) describing extension.json data format.

### required fields

* name: name of the extension, an object mapping language codes to the name of the extension in the language.
* description: description of the extension, an object mapping language codes to the description of the extension in the language.
* documentationUrl: Documentation URL for the extension, an object mapping language codes to an URL with documentation in the language.

### optional fields

* codelist: an array containing the names of the CVS files included in the extension codelists directory.
* compatibility: a semver description of what version of the core standard the extension in compatible with, e.g >=1.0.
* dependencies: an array containing the URLs of other extensions this extension depends on.


How the extensions work
-----------------------

In the core repository there are the following 4 schema files (as of OCDS version 1.1.0):

* release-schema.json
* record-package-schema.json
* release-package-schema.json
* versioned-release-validation-schema.json

An extension to the OCDS just copies the same files from the core schema, adding extra fields or updating existing ones in those places where additions or modifications are required. Empty schema files (`{}`) in the extension are ignored, whilst files containing data are used to do a JSON Merge Patch with the corresponding file in the core schema. JSON Merge Patch is described in this [rfc](https://tools.ietf.org/html/rfc7386).

Any changes to existing fields should respect the [Conformance documentation](http://standard.open-contracting.org/latest/en/schema/conformance_and_extensions/).

Here are some simple examples illustrating how this works. All of them modify release-schema.json using the extension mechanism.

Add a new field "newField" to the release.
```
{"properties": 
  {"newField": {
     "title": "New field",
     "description": "A new field",
     "type": "string"}
  }
}
```

Add a new field "newOrgField" to the organization definition.
```
{"definitions":
  {"Oranization": 
    {"newOrgField": {
       "title": "New Org field",
       "description": "A new field",
       "type": "string"}
    }
  }
}
```

To update the description of the planning phase. 
```
{"properties": 
    {"planning": 
        {"description": "Information from the planning phase of the contracting process. This may include detailed budgets broken down by year (supported by the budgets extension).”}
    }
}
```

Remove whole planning section. Putting in null will remove the key associated with it.
```
{
 "properties":
   {"planning": null}
 "definitions":
   {"Planning": null}
}
```


Leave the core release-schema.json untouched and not do any update to it.
```
{}
```


Best practice
-------------

It is possible to copy all files from the core schema and modify them as needed: as long as you only add or change properties the extension will work as expected. However, the problem with replicating whole schema files is that it makes very hard to see what the extension does and how the core schema gets extended. To avoid that it is best practice to only include in the extension the minimal changes required.

Although it is recommended that extensions contain minimal changes, when developing an extension it can be useful to work with the whole schema. In that case you can use the [json merge patch library](https://github.com/open-contracting/json-merge-patch), which will generate the minimal patch for you by using the command line tool.

```
json-merge-patch create-patch core_schema.json new_modified_schema.json -o minimal_patch.json
```

You can also test your extension:

```
json-merge-patch merge core_schema.json your_extension.json -o expected_output.json

```



