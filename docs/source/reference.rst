.. _reference:

=========
Reference
=========

about
=====

Syntax
------

        ..  code-block:: none

                about [OPTIONS] [COMMANDS]

Options
-------

        ..  code-block:: none

                --version        Show the version and exit.
                -h, --help       Show this message and exit.

Commands
--------

        ..  code-block:: none

                attrib              Generate an attribution document from .ABOUT files.
                check               Validate that the format of .ABOUT files is correct and
                                    report errors and warnings.
                collect_redist_src  Collect redistributable sources.
                gen                 Generate .ABOUT files from an inventory as CSV or JSON.
                inventory           Collect the inventory of .ABOUT files to a CSV or JSON
                                    file.
                transform           Transform a CSV/JSON by applying renamings, filters and checks.

attrib
======

Syntax
------

        ..  code-block:: none

                about attrib [OPTIONS] LOCATION OUTPUT

                LOCATION: Path to a file, directory or .zip archive containing .ABOUT
                files.
                
                OUTPUT: Path where to write the attribution document.

Options
-------

        ..  code-block:: none

                --template FILE          Path to an optional custom attribution template to
                                         generate the attribution document. If not provided
                                         the default built-in template is used.
                --vartext <key>=<value>  Add variable text as key=value for use in a custom
                                         attribution template.
                -q, --quiet              Do not print error or warning messages.
                --verbose                Show all error and warning messages.
                -h, --help               Show this message and exit.

Purpose
-------

Generate an attribution file which contains the all license information from the LOCATION along with the license text.

Assume the following:

        ..  code-block:: none

            '/home/about_files/'** contains all the ABOUT files [LOCATION]
            '/home/attribution/attribution.html' is the user's output path [OUTPUT]

            $ about attrib /home/about_files/ /home/attribution/attribution.html

Details
^^^^^^^

        ..  code-block:: none

                --template
                
                    This option allows you to use your own template for attribution generation.
                    For instance, if you have a custom template located at:
                    /home/custom_template/template.html
                
                $ about attrib --template /home/custom_template/template.html LOCATION OUTPUT
                
                --vartext
                
                    This option allow you to pass variable texts to the attribution template
                
                $ about attrib --vartext "title=Attribution Notice" --vartext "header=Product 101" LOCATION OUTPUT
                
                    Users can use the following in the template to get the vartext:
                    {{ variables['title'] }}
                    {{ variables['header'] }}
                
                --verbose
                
                    This option tells the tool to show all errors found.
                    The default behavior will only show 'CRITICAL', 'ERROR', and 'WARNING'

The following data are passed to jinja2 and, therefore, can be used for a custom template:
 * about object: the about objects
 * common_licenses: a common license keys list in licenses.py
 * license_file_key_and_context: a dictionary with license_file_key (It's basically a license_key if it's not a custom license or license file name otherwise) as a key and license text as the value
 * license_file_key_and_license_key: a dictionary with license file key as a key and license key as the value
 * license_file_name_and_license_file_key: a dictionary with license file name as a key and license file key as the value
 * license_key_and_license_file_name: a dictionary with license key as a key and license file name as the value
 * license_key_and_license_name: a dictionary with license key as a key and license name as the value
 * license_name_and_license_key: a dictionary with license name as a key and license key as the value

check
=====

Syntax
------

        ..  code-block:: none

                about check [OPTIONS] LOCATION

                LOCATION: Path to an ABOUT file or a directory with ABOUT files.

Options
-------

        ..  code-block:: none

                --verbose                Show all the errors and warning
                -h, --help               Show this message and exit.

Purpose
-------

Validating ABOUT files at LOCATION.

Details
^^^^^^^

        ..  code-block:: none

                --verbose
                
                    This option tells the tool to show all errors found.
                    The default behavior will only show 'CRITICAL', 'ERROR', and 'WARNING'
                
                $ about check --verbose /home/project/about_files/

collect_redist_src
==================

Syntax
------

        ..  code-block:: none

                about collect_redist_src [OPTIONS] LOCATION OUTPUT
                
                LOCATION: Path to a directory containing sources that need to be copied
                (and containing ABOUT files if `inventory` is not provided)
                
                OUTPUT: Path to a directory or a zip file where sources will be copied to.

Options
-------

        ..  code-block:: none

                --from-inventory FILE  Path to an inventory CSV/JSON file as the base list
                                       for files/directories that need to be copied which
                                       have the 'redistribute' flagged.
                --with-structures      Copy sources with directory structure.
                --zip                  Zip the copied sources to the output location.
                -q, --quiet            Do not print error or warning messages.
                --verbose              Show all error and warning messages.
                -h, --help             Show this message and exit.

Purpose
-------

Collect sources that have 'redistribute' flagged as 'True' in .ABOUT files or inventory to the output location.

Details
^^^^^^^

        ..  code-block:: none

                --from-inventory
                
                    Provide an inventory CSV/JSON file with the 'redistribute' field filled as
                    the indication of which files/sources need to be copied.
                
                $ about collect_redist_src --from-inventory 'path to the inventory' LOCATION OUTPUT
                
                --with-structures
                
                    Copy the file(s) along with its parent directories
                
                    For instance, assuming we want to copy the following file:
                    /project/work/hello/foo.c
                
                    OUTPUT: /output/
                
                $ about collect_redist_src --with-structure /project/ /output/
                
                    OUTPUT: /output/work/hello/foo.c
                
                $ about collect_redist_src /project/ /output/
                
                    OUTPUT: /output/foo.c
                
                --zip
                
                    Zip the copied sources to the output location
                
                $ about collect_redist_src --zip /project/ /output/output.zip
                
                --verbose
                
                    This option tells the tool to show all errors found.
                    The default behavior will only show 'CRITICAL', 'ERROR', and 'WARNING'

gen
===

Syntax
------

        ..  code-block:: none

                about gen [OPTIONS] LOCATION OUTPUT
                
                LOCATION: Path to a JSON or CSV inventory file.
                OUTPUT: Path to a directory where ABOUT files are generated.

Options
-------

        ..  code-block:: none

                --android                           Generate MODULE_LICENSE_XXX (XXX will be
                                                    replaced by license key) and NOTICE as the same
                                                    design as from Android.
                
                --fetch-license api_url api_key     Fetch licenses data from DejaCode License
                                                    Library and create <license>.LICENSE
                                                    side-by-side with the generated .ABOUT file.
                                                    The following additional options are required:
                
                                                    api_url - URL to the DejaCode License Library
                                                    API endpoint
                
                                                    api_key - DejaCode API key
                                                    Example syntax:
                
                                                    about gen --fetch-license 'api_url' 'api_key'
                --reference PATH                    Path to a directory with reference license
                                                    data and text files.
                -q, --quiet                         Do not print any error/warning.
                --verbose                           Show all the errors and warning.
                -h, --help                          Show this message and exit.

Purpose
-------

Given a CSV/JSON inventory, generate ABOUT files in the output location.

Details
^^^^^^^

        ..  code-block:: none

                --android
                
                    Create an empty file named `MODULE_LICENSE_XXX` where `XXX` is the license
                    key and create a NOTICE file which these two files follow the design from
                    Android Open Source Project.
                
                    The input **must** have the license key information as this is needed to
                    create the empty MODULE_LICENSE_XXX
                
                $ about gen --android LOCATION OUTPUT
                
                --fetch-license
                
                    Fetch licenses text from a DejaCode API, and create <license>.LICENSE side-by-side
                    with the generated .ABOUT file using the data fetched from the DejaCode License Library.
                
                    This option requires 2 parameters:
                        api_url - URL to the DJE License Library.
                        api_key - Hash key to authenticate yourself in the API.
                
                    In addition, the input needs to have the 'license_expression' field.
                    (Please contact nexB to get the api_* value for this feature)
                
                $ about gen --fetch-license 'api_url' 'api_key' LOCATION OUTPUT
                
                --reference
                
                    Copy the reference files such as 'license_files' and 'notice_files' to the
                    generated location from the specified directory.
                
                    For instance,
                    the specified directory, /home/licenses_notices/, contains all the licenses and notices:
                    /home/licenses_notices/apache2.LICENSE
                    /home/licenses_notices/jquery.js.NOTICE
                
                $ about gen --reference /home/licenses_notices/ LOCATION OUTPUT
                
                --verbose
                
                    This option tells the tool to show all errors found.
                    The default behavior will only show 'CRITICAL', 'ERROR', and 'WARNING'

inventory
=========

Syntax
------

        ..  code-block:: none

                about inventory [OPTIONS] LOCATION OUTPUT
                
                LOCATION: Path to an ABOUT file or a directory with ABOUT files.
                OUTPUT: Path to the JSON or CSV inventory file to create.

Options
-------

        ..  code-block:: none

                -f, --format [json|csv]     Set OUTPUT file format.  [default: csv]
                -q, --quiet                 Do not print any error/warning.
                --verbose                   Show all the errors and warning.
                -h, --help                  Show this message and exit.

Purpose
-------

Create a JSON or CSV inventory of components from ABOUT files.

Details
^^^^^^^

        ..  code-block:: none

                -f, --format [json|csv]
                
                    Set OUTPUT file format.  [default: csv]
                
                $ about inventory -f json LOCATION OUTPUT
                
                --verbose
                
                    This option tells the tool to show all errors found.
                    The default behavior will only show 'CRITICAL', 'ERROR', and 'WARNING'

Special Notes
-------------

Multiple licenses support format
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The multiple licenses support format for CSV files are separated by line break

+----------------+------+--------------+---------------+---------------------+
| about_resource | name | license_key  | license_name  | license_file        |
+================+======+==============+===============+=====================+
| test.tar.xz    | test | | apache-2.0 | | Apache 2.0  | | apache-2.0.LICENSE|
|                |      | | mit        | | MIT License | | mit.LICENSE       |
+----------------+------+--------------+---------------+---------------------+

The multiple licenses support format for ABOUT files are by "grouping" with the keyword "licenses"

        ..  code-block:: none

                about_resource: test.tar.xz
                name: test
                licenses:
                    -   key: apache 2.0
                        name: Apache 2.0
                        file: apache-2.0.LICENSE
                    -   key: mit
                        name: MIT License
                        file: mit.LICENSE

Multiple license_file support
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To support multiple license file for a license, the correct format is to separate by comma

+----------------+------+--------------+---------------+---------------------+
| about_resource | name | license_key  | license_name  | license_file        |
+================+======+==============+===============+=====================+
| test.tar.xz    | test | | gpl-2.0    | | GPL 2.0     | | COPYING, COPYINGv2|
|                |      | | mit        | | MIT License | | mit.LICENSE       |
+----------------+------+--------------+---------------+---------------------+

        ..  code-block:: none

                about_resource: test.tar.xz
                name: test
                licenses:
                    -   key: gpl-2.0
                        name: gpl-2.0
                        file: COPYING, COPYING.v2
                    -   key: mit
                        name: mit
                        file: mit.LICENSE

Note that if license_name is not provided, the license key will be used as the license name.

transform
=========

Syntax
------

        ..  code-block:: none

                about transform [OPTIONS] LOCATION OUTPUT
                
                LOCATION: Path to a CSV/JSON file.
                OUTPUT: Path to CSV/JSON inventory file to create.

Options
-------

        ..  code-block:: none

                -c, --configuration FILE  Path to an optional YAML configuration file. See
                                          --help-format for format help.
                --help-format             Show configuration file format help and exit.
                -q, --quiet               Do not print error or warning messages.
                --verbose                 Show all error and warning messages.
                -h, --help                Show this message and exit.

Purpose
-------

Transform the CSV/JSON file at LOCATION by applying renamings, filters and checks and then write a new CSV/JSON to OUTPUT (Format for input and output need to be the same).

Details
^^^^^^^

        ..  code-block:: none

                -c, --configuration
                
                    Path to an optional YAML configuration file. See--help-format for format help.
                
                $ about transform -c 'path to the YAML configuration file' LOCATION OUTPUT
                
                --help-format
                
                    Show configuration file format help and exit.
                    This option will print out examples of the the YAML configuration file.
                
                    Keys configuration are: `field_renamings`, `required_fields` and `field_filters`
                
                $ about transform --help-format
                
                --verbose
                
                    This option tells the tool to show all errors found.
                    The default behavior will only show 'CRITICAL', 'ERROR', and 'WARNING'

Special Notes
-------------
When using the field_filters configuration, all the standard required columns (about_resource and name) and the user defined required_fields need to be included.