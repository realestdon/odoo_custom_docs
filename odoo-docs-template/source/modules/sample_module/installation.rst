Installation
============

This guide covers the installation process for the Sample Module.

Prerequisites
-------------

Before installing the Sample Module, ensure you have:

* Odoo 17.0 installed and running
* Administrator access to the Odoo instance
* Database backup (recommended before installing new modules)

Required Dependencies
~~~~~~~~~~~~~~~~~~~~~

The following modules must be installed:

* ``base`` - Automatically installed with Odoo

Optional Dependencies
~~~~~~~~~~~~~~~~~~~~~

For full functionality, these modules are recommended:

* ``sale`` - Sales management
* ``stock`` - Inventory management

Installation Methods
--------------------

Method 1: Through Odoo Apps (Recommended)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

1. **Copy Module Files**

   Copy the module directory to your Odoo addons path:

   .. code-block:: bash

      # Default addons path
      cp -r sample_module /path/to/odoo/addons/

      # Or custom addons path
      cp -r sample_module /path/to/custom/addons/

2. **Update Apps List**

   * Log in to Odoo as Administrator
   * Go to **Apps** menu
   * Click **Update Apps List** (may need to activate Developer Mode)
   * In the confirmation dialog, click **Update**

3. **Install the Module**

   * In the Apps menu, remove the "Apps" filter
   * Search for "Sample Module"
   * Click **Install**

   .. image:: /_static/images/sample_module/install_screen.png
      :alt: Installation Screen
      :align: center
      :width: 70%

4. **Verify Installation**

   After installation, verify the module is active:

   * Go to **Settings > Technical > Installed Modules**
   * Search for "sample_module"
   * Status should show "Installed"

Method 2: Command Line Installation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

For development or automated deployments:

.. code-block:: bash

   # Using odoo-bin
   /path/to/odoo/odoo-bin -d your_database -i sample_module

   # Or using docker
   docker exec -it odoo odoo -d your_database -i sample_module

Method 3: Through Configuration File
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Add to your ``odoo.conf``:

.. code-block:: ini

   [options]
   addons_path = /path/to/odoo/addons,/path/to/custom/addons
   init = sample_module
   db_name = your_database

Then restart Odoo:

.. code-block:: bash

   sudo systemctl restart odoo

Installation in Different Environments
---------------------------------------

Development Environment
~~~~~~~~~~~~~~~~~~~~~~~

1. Clone or copy the module to your development addons directory
2. Add the path to ``--addons-path`` parameter
3. Start Odoo with ``-i sample_module``

.. code-block:: bash

   ./odoo-bin --addons-path=/path/to/addons -d dev_db -i sample_module

Production Environment
~~~~~~~~~~~~~~~~~~~~~~

.. warning::
   Always test modules in a staging environment before production installation.

1. **Backup Database**

   .. code-block:: bash

      pg_dump production_db > backup_$(date +%Y%m%d).sql

2. **Stop Odoo Service**

   .. code-block:: bash

      sudo systemctl stop odoo

3. **Deploy Module**

   .. code-block:: bash

      cp -r sample_module /opt/odoo/custom/addons/

4. **Update and Install**

   .. code-block:: bash

      sudo -u odoo /opt/odoo/odoo-bin -c /etc/odoo/odoo.conf \
           -d production_db -i sample_module --stop-after-init

5. **Start Odoo Service**

   .. code-block:: bash

      sudo systemctl start odoo

Docker Environment
~~~~~~~~~~~~~~~~~~

**Using Docker Compose:**

.. code-block:: yaml

   version: '3'
   services:
     web:
       image: odoo:17.0
       volumes:
         - ./custom-addons:/mnt/extra-addons
       environment:
         - ADDONS_PATH=/mnt/extra-addons

**Install module:**

.. code-block:: bash

   docker-compose exec web odoo -d odoo -i sample_module

Post-Installation Steps
-----------------------

1. **Configure Module Settings**

   Navigate to **Settings > Sample Module** and configure required settings.
   See :doc:`configuration` for details.

2. **Set User Permissions**

   Grant appropriate access rights to users:

   * Go to **Settings > Users & Companies > Users**
   * Select a user
   * Under **Access Rights**, enable Sample Module permissions

3. **Import Initial Data (Optional)**

   If you have initial data to import:

   * Go to **Sample Module > Configuration > Import**
   * Upload your CSV or Excel file
   * Follow the import wizard

4. **Run Initial Setup Wizard**

   Some modules include a setup wizard:

   * A popup may appear on first access
   * Follow the guided setup steps
   * Or access via **Settings > Sample Module > Setup Wizard**

Upgrading from Previous Versions
---------------------------------

If upgrading from an earlier version:

1. **Backup Database**

   .. code-block:: bash

      pg_dump your_database > backup_before_upgrade.sql

2. **Update Module Files**

   Replace old module files with new version:

   .. code-block:: bash

      rm -rf /path/to/addons/sample_module
      cp -r sample_module /path/to/addons/

3. **Upgrade Module**

   * Go to **Apps** menu
   * Search for "Sample Module"
   * Click **Upgrade**

   Or via command line:

   .. code-block:: bash

      odoo-bin -d your_database -u sample_module

4. **Run Migration Scripts**

   Some upgrades require data migration. Check the release notes.

Troubleshooting
---------------

Module Not Appearing in Apps List
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Cause:** Module path not included in addons path.

**Solution:**

1. Verify module is in correct directory
2. Check ``addons_path`` in config file
3. Restart Odoo service
4. Update Apps List

Installation Fails with Dependency Error
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Cause:** Required dependencies not installed.

**Solution:**

1. Check error message for missing dependencies
2. Install missing modules first
3. Retry installation

Permission Denied Error
~~~~~~~~~~~~~~~~~~~~~~~

**Cause:** File permissions incorrect.

**Solution:**

.. code-block:: bash

   sudo chown -R odoo:odoo /path/to/addons/sample_module
   sudo chmod -R 755 /path/to/addons/sample_module

Python Module Import Error
~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Cause:** Missing Python dependencies.

**Solution:**

.. code-block:: bash

   pip install -r /path/to/sample_module/requirements.txt

Verification
------------

After installation, verify the module works correctly:

.. code-block:: python

   # In Odoo shell
   self.env['ir.module.module'].search([
       ('name', '=', 'sample_module'),
       ('state', '=', 'installed')
   ])

Expected result: Should return one record.

Uninstallation
--------------

To uninstall the module:

1. **Uninstall via Apps**

   * Go to **Apps**
   * Search for "Sample Module"
   * Click **Uninstall**

2. **Command Line Uninstall**

   .. code-block:: bash

      odoo-bin -d your_database --uninstall sample_module

.. warning::
   Uninstalling will remove all module data. Backup before uninstalling!

Next Steps
----------

* :doc:`configuration` - Configure the module
* :doc:`user_guide` - Learn how to use the module
* :doc:`technical` - Technical documentation for customization
