Modules
=======

This section contains detailed documentation for all custom Odoo modules.

.. toctree::
   :maxdepth: 2
   :caption: Available Modules:

   sample_module/index
   custom_sales/index

Module Overview
---------------

Below is a quick reference for all available modules:

Custom Sales Module
~~~~~~~~~~~~~~~~~~~

:Module Name: ``custom_sales``
:Category: Sales
:Version: 17.0.1.0.0
:Dependencies: sale, stock
:Description: Extends sales functionality with custom workflows and automation

See :doc:`custom_sales/index` for detailed documentation.

Sample Module
~~~~~~~~~~~~~

:Module Name: ``sample_module``
:Category: Custom
:Version: 17.0.1.0.0
:Dependencies: base
:Description: A sample module demonstrating documentation structure

See :doc:`sample_module/index` for detailed documentation.

Module Installation
-------------------

General installation steps for custom modules:

1. Copy the module to your Odoo addons directory
2. Restart the Odoo service
3. Update the apps list (Apps > Update Apps List)
4. Search for the module name
5. Click Install

For module-specific installation instructions, refer to each module's documentation.

Module Dependencies
-------------------

.. graphviz::

   digraph module_dependencies {
      rankdir=LR;
      node [shape=box, style=rounded];
      
      "base" -> "sample_module";
      "sale" -> "custom_sales";
      "stock" -> "custom_sales";
   }

.. note::
   Always ensure all dependencies are installed before installing a custom module.
