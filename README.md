OCDS Extension Template Repository
==================================

This repository has two purposes:

 *  To give a template of an extension that people can base their own on.
 *  To give a place for comment and discussion on how extensions should work.

The structure of the extension repository should look like:

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

It copies its layout from the core [standards repository](https://github.com/open-contracting/standard/tree/1.0/standard/schema) and that can be a useful reference when developing an extension.


extension.json
--------------

This file is required and it gives any automated tools information about the extension.

[An example of this file can be found in this repository](https://github.com/open-contracting/standard_extension_template/blob/master/extension.json) 

### required fields

* name (Name of extension)
* description (Description of extension)

### optional fields

* compatibility (A semver description of what version of the core standard the extension in compatible with e.g >=1.0)
* dependencies (A list of other extensions that this extension depends on)


How the extensions work
-----------------------

In the core repository as of OCDS version 1.0.1 there are the following 4 schema files.

* release-schema.json
* record-package-schema.json
* release-package-schema.json
* versioned-release-validation-schema.json

In the extension some or all of these files can be used to do a JSON Merge Patch of the corresponding file.

A JSON merge patch is described by this [rfc](https://tools.ietf.org/html/rfc7386).

The patches are very simple. They just copy the same structure from the core schema and allow you add your extra fields or update existing fields just in the places the extension wants to change the schema. 

Here are some simple examples of how this works. They are illistrative, any changes to existing fields should respect the [Conformance documentation](http://standard.open-contracting.org/latest/en/schema/conformance_and_extensions/).

They all are examples of what could go in release-schema.json files in an extension.

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

It is possible to copy the whole of the schemas from the core, change them and as long as you have only added or changed properties the extension will work as expected. However doing it this way will mean that it will be hard to see what changes are made and will be much harder to track changes of the core schema (as every change in the core schema will need to be copied).  

So it is best practice to just put in an extension the minimal changes that are required. However, it can be useful to work with the whole schema when developing an extension. In this case use the [json merge patch library](https://github.com/open-contracting/json-merge-patch) and it will generate the minimal patch for you by using the command line tool.

```
json-merge-patch create-patch core_schema.json new_modified_schema.json -o minimal_patch.json
```

You can also test your extension by:

```
json-merge-patch merge core_schema.json your_extension.json -o expected_output.json

```



