Configuration
=============

This guide covers all configuration options for the Sample Module.

Initial Configuration
---------------------

After installation, configure the module through the Settings menu.

Accessing Configuration
~~~~~~~~~~~~~~~~~~~~~~~~

1. Navigate to **Settings**
2. Scroll to **Sample Module** section
3. Click **Configure**

Or directly:

* Go to **Sample Module > Configuration > Settings**

General Settings
----------------

Basic Configuration
~~~~~~~~~~~~~~~~~~~

.. image:: /_static/images/sample_module/settings_general.png
   :alt: General Settings
   :align: center
   :width: 80%

Enable Module Features
""""""""""""""""""""""

:Feature 1: Enable advanced processing
   
   * **Default:** Disabled
   * **Effect:** Enables additional processing options in records
   * **Recommendation:** Enable for production use

:Feature 2: Automatic notifications

   * **Default:** Enabled  
   * **Effect:** Sends email notifications on status changes
   * **Recommendation:** Keep enabled for better communication

:Feature 3: Integration with Sales

   * **Default:** Disabled
   * **Effect:** Adds Sample Module fields to Sales Orders
   * **Dependencies:** Requires ``sale`` module
   * **Recommendation:** Enable if using Sales module

Company Settings
""""""""""""""""

:Default Workflow:
   * **Options:** Standard, Express, Custom
   * **Default:** Standard
   * **Description:** Defines the default workflow for new records

:Automatic Numbering:
   * **Default:** Enabled
   * **Format:** SM/YYYY/####
   * **Description:** Auto-generates sequence numbers for records

:Email Template:
   * **Default:** Default Template
   * **Description:** Template used for automated emails

Advanced Settings
-----------------

Developer Mode Required
~~~~~~~~~~~~~~~~~~~~~~~

.. note::
   Enable Developer Mode to access advanced settings:
   Settings > Activate Developer Mode

Performance Settings
""""""""""""""""""""

:Batch Size:
   * **Default:** 100
   * **Range:** 1-1000
   * **Description:** Number of records processed in each batch
   * **Impact:** Higher values = faster processing but more memory

:Cache Duration:
   * **Default:** 3600 seconds (1 hour)
   * **Range:** 0-86400 seconds
   * **Description:** How long to cache computed values
   * **Impact:** Longer cache = less database queries

:Enable Logging:
   * **Default:** Disabled
   * **Description:** Detailed logging for debugging
   * **Warning:** May impact performance

.. code-block:: python

   # Example configuration via code
   config = self.env['ir.config_parameter'].sudo()
   config.set_param('sample_module.batch_size', '200')
   config.set_param('sample_module.cache_duration', '7200')

Security Configuration
""""""""""""""""""""""

:Restrict Access:
   * **Default:** Disabled
   * **Description:** Limit module access to specific user groups

:Enable Audit Trail:
   * **Default:** Disabled
   * **Description:** Track all changes to records

:Data Encryption:
   * **Default:** Disabled
   * **Description:** Encrypt sensitive fields
   * **Requirement:** Additional setup required

Access Rights Configuration
---------------------------

User Groups
~~~~~~~~~~~

The module provides the following access groups:

Sample Module User
""""""""""""""""""

* View and create records
* Edit own records
* No delete permissions

**Assign to:** Regular users who need to use the module

Sample Module Manager
"""""""""""""""""""""

* All User permissions
* Edit all records
* Delete records
* Access to configuration

**Assign to:** Team leaders and supervisors

Sample Module Administrator
"""""""""""""""""""""""""""

* All Manager permissions
* System configuration
* User management
* Import/Export capabilities

**Assign to:** System administrators only

Assigning Access Rights
~~~~~~~~~~~~~~~~~~~~~~~~

1. Go to **Settings > Users & Companies > Users**
2. Select a user
3. Scroll to **Access Rights** section
4. Find **Sample Module** and select appropriate group:

   .. image:: /_static/images/sample_module/user_rights.png
      :alt: User Rights Configuration
      :align: center
      :width: 70%

Record Rules Configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~

Configure record-level security:

1. **Settings > Technical > Security > Record Rules**
2. Create new rule:

   .. code-block:: xml

      <record id="sample_module_personal_records" model="ir.rule">
          <field name="name">Personal Records</field>
          <field name="model_id" ref="model_sample_module"/>
          <field name="domain_force">
              [('user_id', '=', user.id)]
          </field>
          <field name="groups" eval="[(4, ref('group_sample_module_user'))]"/>
      </record>

Integration Configuration
-------------------------

Email Configuration
~~~~~~~~~~~~~~~~~~~

Configure email settings for notifications:

1. **Settings > Technical > Email > Templates**
2. Find **Sample Module: Notification**
3. Customize subject and body:

   .. code-block:: html

      <p>Dear ${object.partner_id.name},</p>
      <p>Your request ${object.name} has been processed.</p>
      <p>Status: ${object.state}</p>

External API Configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~

For external system integration:

:API Endpoint:
   * Configure URL of external system
   * Example: ``https://api.example.com/v1``

:API Key:
   * Enter authentication key
   * Obtain from external system administrator

:Sync Frequency:
   * **Default:** Every hour
   * **Options:** Real-time, Hourly, Daily, Manual

Test connection:

.. code-block:: python

   # Via Python console
   self.env['sample.module.config'].test_api_connection()

Database Configuration
~~~~~~~~~~~~~~~~~~~~~~

For multi-database deployments:

.. code-block:: ini

   # In odoo.conf
   [sample_module]
   db_host = localhost
   db_port = 5432
   db_user = odoo_sample
   db_password = your_password

Automation Rules
----------------

Configure automated actions:

Workflow Automation
~~~~~~~~~~~~~~~~~~~

1. **Settings > Technical > Automation > Automated Actions**
2. Create new action for Sample Module:

   :Trigger: On creation or update
   :Model: Sample Module
   :Condition: ``record.state == 'draft'``
   :Action: Execute Python Code

   .. code-block:: python

      # Example automation
      if record.total_amount > 1000:
          record.state = 'pending_approval'
      else:
          record.state = 'approved'

Scheduled Actions
~~~~~~~~~~~~~~~~~

Setup recurring tasks:

1. **Settings > Technical > Automation > Scheduled Actions**
2. Configure action:

   :Name: Daily Sample Module Sync
   :Model: Sample Module
   :Interval: Daily at 02:00 AM
   :Number of Calls: -1 (unlimited)
   :Code:

   .. code-block:: python

      records = env['sample.module'].search([
          ('state', '=', 'pending')
      ])
      records.process_pending()

Notification Configuration
--------------------------

Email Notifications
~~~~~~~~~~~~~~~~~~~

:Send on Creation: Yes/No
:Send on Status Change: Yes/No
:Send to: Record owner, Manager, Custom recipients
:Email Template: Select from available templates

In-App Notifications
~~~~~~~~~~~~~~~~~~~~

:Enable Activities: Create activity on status change
:Activity Type: Task, Meeting, Call, etc.
:Assign to: Record owner, Manager, Specific user

SMS Notifications (Optional)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Requires SMS module:

:Enable SMS: Yes/No
:SMS Template: Select template
:Send to: Partner mobile number

Data Import/Export Configuration
---------------------------------

Import Settings
~~~~~~~~~~~~~~~

:Default Import Format: CSV, Excel
:Date Format: DD/MM/YYYY or MM/DD/YYYY
:Decimal Separator: Dot (.) or Comma (,)
:Encoding: UTF-8, Latin-1

Export Settings
~~~~~~~~~~~~~~~

:Export Format: CSV, Excel, PDF
:Include Headers: Yes/No
:Export All Fields: Yes/No (select specific fields if No)

Multi-Company Configuration
----------------------------

For multi-company installations:

Enable Multi-Company
~~~~~~~~~~~~~~~~~~~~

1. Activate multi-company mode in Settings
2. Configure company-specific settings:

   :Company: Select company
   :Specific Workflow: Define per-company workflow
   :Numbering Sequence: Separate sequence per company

Company-Specific Rules
~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: python

   # Example: Restrict to current company
   domain = [('company_id', '=', company_id)]

Configuration Best Practices
-----------------------------

1. **Start with Defaults**
   
   Use default settings initially, adjust based on usage patterns

2. **Test in Staging**
   
   Always test configuration changes in staging environment

3. **Document Custom Settings**
   
   Keep track of any non-default configurations

4. **Regular Reviews**
   
   Review settings quarterly to ensure they still meet needs

5. **Backup Before Changes**
   
   Always backup before making significant configuration changes

Configuration Examples
----------------------

Example 1: Simple Setup
~~~~~~~~~~~~~~~~~~~~~~~

For small teams:

* Enable Feature 1 and 2
* Standard workflow
* Email notifications enabled
* Basic user access for all

Example 2: Advanced Setup
~~~~~~~~~~~~~~~~~~~~~~~~~

For large organizations:

* All features enabled
* Custom workflow with approvals
* Automated processing with scheduled actions
* Role-based access control
* Integration with external systems

Example 3: Development Setup
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

For testing and development:

* Enable logging
* Lower batch sizes
* Test mode for external APIs
* Administrator access for developers

Troubleshooting Configuration
------------------------------

Settings Not Saving
~~~~~~~~~~~~~~~~~~~

**Cause:** Insufficient permissions

**Solution:** Ensure user has Administrator rights

Features Not Working
~~~~~~~~~~~~~~~~~~~~

**Cause:** Dependencies not installed

**Solution:** Install required modules and restart

Performance Issues
~~~~~~~~~~~~~~~~~~

**Cause:** Inefficient settings

**Solution:** 
* Increase batch size
* Enable caching
* Disable detailed logging

Configuration Checklist
-----------------------

Before going live, verify:

☐ All required features enabled
☐ User access rights configured
☐ Email templates customized
☐ Automation rules tested
☐ Integration connections verified
☐ Backup procedures in place
☐ Documentation updated

Next Steps
----------

* :doc:`user_guide` - Learn how to use the configured module
* :doc:`technical` - Technical details and customization
* Back to :doc:`index` - Module overview
