{
"title": "YAML Configuration loading",
"linkTitle": "YAML Configuration loading",
"weight":"70",
"date": "2020-09-25",
"description": "Learn how the YAML configuration is loaded."
}

Entities are read the following way: from the Root to its children in descending order

Starting from the root directory:

1. Read the current directory.
2. Load `_parent.yaml`.
3. List all other files in the directory (if any)
    * Parse all YAML files (other extensions are ignored)
    * Go back to first step with every sub-directories found in this location

This means that:

* Every folder should contain a `_parent.yaml`
    * Except for top level folder, that are just "sorting" folders
* The folder (containing a `_parent.yaml`) represent the parent of entities contained in this folder.

For instance, this is valid:

* `Folder A/`
    * `_parent.yaml` (name=A)
        * `Folder X/`
            * `policy X-123.yaml`
            * `policy X-456.yaml`
        * `Folder Y/`
            * `policy Y-123.yaml`
            * `policy Y-456.yaml`

The in-memory hierarchy will be:

* A
    * policy X-123
    * policy X-456
    * policy Y-123
    * policy Y-456

This way you can sort policies (and other entities) in various directories without having to create containers.

Beware that your policy must be uniquely named for a common ancestor ("A" in this case), as the directory is just a directory, not a container. Containers do contain a `_parent.yaml` file.