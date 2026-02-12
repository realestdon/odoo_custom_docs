Coding Guidelines
=================

Development standards and best practices for custom Odoo modules.

General Principles
------------------

1. **Code Quality**: Write clean, maintainable code
2. **Documentation**: Document all public APIs
3. **Testing**: Write tests for new features
4. **Security**: Follow security best practices
5. **Performance**: Optimize for performance

Python Coding Standards
------------------------

PEP 8 Compliance
~~~~~~~~~~~~~~~~

Follow PEP 8 with these specific rules:

**Indentation**

* Use 4 spaces (never tabs)
* Maximum line length: 120 characters
* Break long lines appropriately

.. code-block:: python

   # Good
   result = some_function(
       argument1, argument2,
       argument3, argument4
   )
   
   # Avoid
   result = some_function(argument1, argument2, argument3, argument4)

**Naming Conventions**

.. list-table::
   :header-rows: 1
   :widths: 30 30 40

   * - Type
     - Convention
     - Example
   * - Module
     - snake_case
     - ``sample_module``
   * - Class
     - PascalCase
     - ``SampleModel``
   * - Function/Method
     - snake_case
     - ``compute_total()``
   * - Variable
     - snake_case
     - ``total_amount``
   * - Constant
     - UPPER_CASE
     - ``MAX_VALUE``
   * - Private
     - _prefix
     - ``_compute_value()``

**Imports**

.. code-block:: python

   # Good: Grouped and sorted
   import json
   import logging
   from datetime import datetime
   
   from odoo import models, fields, api
   from odoo.exceptions import ValidationError
   
   _logger = logging.getLogger(__name__)

Odoo-Specific Standards
------------------------

Model Definition
~~~~~~~~~~~~~~~~

.. code-block:: python

   class SampleModel(models.Model):
       """Model description.
       
       Longer description if needed.
       Multiple lines are fine.
       """
       
       _name = 'sample.module'
       _description = 'Sample Module'
       _inherit = ['mail.thread', 'mail.activity.mixin']
       _order = 'create_date desc'
       
       # Fields grouped by type
       # Basic fields
       name = fields.Char(
           string='Name',
           required=True,
           tracking=True,
       )
       
       # Relational fields
       partner_id = fields.Many2one(
           'res.partner',
           string='Partner',
           required=True,
       )
       
       # Computed fields
       total = fields.Float(
           compute='_compute_total',
           store=True,
       )

Field Ordering
~~~~~~~~~~~~~~

Order fields logically:

1. Private attributes (_name, _inherit, etc.)
2. Basic fields (Char, Text, Integer, Float, Boolean)
3. Date/Datetime fields
4. Selection fields  
5. Relational fields (Many2one, One2many, Many2many)
6. Computed fields
7. Constraints and validations

Method Organization
~~~~~~~~~~~~~~~~~~~

Order methods logically:

.. code-block:: python

   class SampleModel(models.Model):
       # 1. Private attributes
       _name = 'sample.module'
       
       # 2. Fields
       name = fields.Char()
       
       # 3. Default methods
       @api.model
       def _default_user(self):
           return self.env.user
       
       # 4. Compute methods
       @api.depends('field')
       def _compute_value(self):
           pass
       
       # 5. Onchange methods
       @api.onchange('partner_id')
       def _onchange_partner(self):
           pass
       
       # 6. Constraint methods
       @api.constrains('amount')
       def _check_amount(self):
           pass
       
       # 7. CRUD methods
       @api.model
       def create(self, vals):
           pass
       
       def write(self, vals):
           pass
       
       # 8. Action methods
       def action_submit(self):
           pass
       
       # 9. Business methods
       def process_record(self):
           pass
       
       # 10. Private helper methods
       def _send_notification(self):
           pass

Documentation Standards
-----------------------

Docstrings
~~~~~~~~~~

Use Google-style docstrings:

.. code-block:: python

   def compute_total(self, include_tax=False):
       """Compute the total amount.
       
       This method calculates the total amount from all lines,
       optionally including tax.
       
       Args:
           include_tax (bool): Whether to include tax in calculation.
               Defaults to False.
       
       Returns:
           float: The computed total amount.
       
       Raises:
           ValidationError: If no lines are present.
       
       Example:
           >>> record.compute_total(include_tax=True)
           1500.00
       """
       if not self.line_ids:
           raise ValidationError("No lines to compute")
       
       total = sum(self.line_ids.mapped('amount'))
       if include_tax:
           total *= 1.15
       return total

Field Documentation
~~~~~~~~~~~~~~~~~~~

.. code-block:: python

   amount = fields.Float(
       string='Amount',
       required=True,
       help='The base amount before tax',
       digits=(16, 2),
   )

View Standards
--------------

XML Formatting
~~~~~~~~~~~~~~

.. code-block:: xml

   <?xml version="1.0" encoding="utf-8"?>
   <odoo>
       <record id="view_sample_form" model="ir.ui.view">
           <field name="name">sample.module.form</field>
           <field name="model">sample.module</field>
           <field name="arch" type="xml">
               <form string="Sample Module">
                   <header>
                       <!-- Buttons -->
                   </header>
                   <sheet>
                       <!-- Form content -->
                   </sheet>
                   <div class="oe_chatter">
                       <!-- Chatter -->
                   </div>
               </form>
           </field>
       </record>
   </odoo>

View Organization
~~~~~~~~~~~~~~~~~

1. Form views
2. Tree views
3. Search views
4. Kanban views
5. Calendar/Graph/Pivot views
6. Actions
7. Menu items

Security Best Practices
------------------------

Access Rights
~~~~~~~~~~~~~

Always define access rights:

.. code-block:: text

   id,name,model_id:id,group_id:id,perm_read,perm_write,perm_create,perm_unlink
   access_sample_user,sample.user,model_sample,group_user,1,1,1,0

Record Rules
~~~~~~~~~~~~

.. code-block:: xml

   <record id="sample_personal_rule" model="ir.rule">
       <field name="name">Personal Records</field>
       <field name="model_id" ref="model_sample_module"/>
       <field name="domain_force">
           [('user_id', '=', user.id)]
       </field>
       <field name="groups" eval="[(4, ref('group_user'))]"/>
   </record>

SQL Injection Prevention
~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: python

   # Good: Parameterized query
   self.env.cr.execute(
       "SELECT * FROM table WHERE id = %s",
       (record_id,)
   )
   
   # Bad: String concatenation
   self.env.cr.execute(
       f"SELECT * FROM table WHERE id = {record_id}"
   )

XSS Prevention
~~~~~~~~~~~~~~

.. code-block:: python

   # Always escape user input in templates
   from markupsafe import escape
   
   safe_text = escape(user_input)

Performance Guidelines
----------------------

Database Queries
~~~~~~~~~~~~~~~~

.. code-block:: python

   # Good: Batch operations
   records.write({'state': 'done'})
   
   # Avoid: Individual operations
   for record in records:
       record.state = 'done'

Computed Fields
~~~~~~~~~~~~~~~

.. code-block:: python

   # Good: Store frequently accessed
   total = fields.Float(
       compute='_compute_total',
       store=True,  # Cache in database
   )
   
   # Good: Use depends
   @api.depends('line_ids.amount')
   def _compute_total(self):
       for record in self:
           record.total = sum(record.line_ids.mapped('amount'))

Search Optimization
~~~~~~~~~~~~~~~~~~~

.. code-block:: python

   # Good: Use limits
   records = model.search([domain], limit=100)
   
   # Good: Only fetch needed fields
   records.read(['name', 'state'])
   
   # Avoid: Search without limits
   all_records = model.search([])

Testing Standards
-----------------

Test Structure
~~~~~~~~~~~~~~

.. code-block:: python

   from odoo.tests import tagged
   from odoo.tests.common import TransactionCase
   
   @tagged('post_install', '-at_install')
   class TestSampleModule(TransactionCase):
       
       def setUp(self):
           super().setUp()
           self.Model = self.env['sample.module']
       
       def test_create(self):
           """Test record creation."""
           record = self.Model.create({'name': 'Test'})
           self.assertTrue(record)
           self.assertEqual(record.state, 'draft')

Test Coverage
~~~~~~~~~~~~~

Aim for:

* **Critical paths**: 100% coverage
* **Business logic**: 90%+ coverage
* **Overall**: 80%+ coverage

Git Workflow
------------

Commit Messages
~~~~~~~~~~~~~~~

Use conventional commits:

.. code-block:: text

   type(scope): subject
   
   body (optional)
   
   footer (optional)

Types:

* ``feat``: New feature
* ``fix``: Bug fix
* ``docs``: Documentation
* ``style``: Formatting
* ``refactor``: Code restructuring
* ``test``: Adding tests
* ``chore``: Maintenance

Example:

.. code-block:: text

   feat(sample): add approval workflow
   
   - Add state field with workflow
   - Add approval buttons
   - Add email notifications
   
   Closes #123

Branch Naming
~~~~~~~~~~~~~

* ``feature/description`` - New features
* ``bugfix/description`` - Bug fixes
* ``hotfix/description`` - Urgent fixes
* ``release/version`` - Release branches

Code Review Checklist
---------------------

Before submitting code for review:

☐ Code follows PEP 8 and Odoo standards
☐ All methods have docstrings
☐ Tests written and passing
☐ No debugging code left (print, pdb)
☐ No commented-out code
☐ Proper error handling
☐ Security considerations addressed
☐ Performance optimized
☐ Documentation updated
☐ Commit messages follow conventions

Common Pitfalls
---------------

Avoid these common mistakes:

**1. SQL Queries**

.. code-block:: python

   # Avoid: Raw SQL when ORM works
   self.env.cr.execute("SELECT * FROM table")
   
   # Prefer: Use ORM
   self.env['model.name'].search([])

**2. Hardcoded Values**

.. code-block:: python

   # Avoid: Hardcoded
   if record.state == 'done':
       ...
   
   # Prefer: Use constants or parameters
   STATE_DONE = 'done'
   if record.state == STATE_DONE:
       ...

**3. Not Using API Decorators**

.. code-block:: python

   # Wrong: Missing @api.depends
   def _compute_total(self):
       self.total = sum(self.line_ids.mapped('amount'))
   
   # Correct:
   @api.depends('line_ids.amount')
   def _compute_total(self):
       for record in self:
           record.total = sum(record.line_ids.mapped('amount'))

**4. Modifying cr Directly**

.. code-block:: python

   # Avoid: Direct cr modification
   self.env.cr.commit()
   
   # Prefer: Let Odoo handle transactions

Resources
---------

* `PEP 8 <https://pep8.org/>`_
* `Odoo Guidelines <https://www.odoo.com/documentation/17.0/developer/reference/backend/guidelines.html>`_
* `Python Documentation <https://docs.python.org/3/>`_

See Also
--------

* :doc:`api_reference` - API documentation
* :doc:`/modules/sample_module/technical` - Module examples
