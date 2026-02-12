Technical Documentation
=======================

This document provides technical details for developers working with the Sample Module.

Module Structure
----------------

Directory Layout
~~~~~~~~~~~~~~~~

.. code-block:: text

   sample_module/
   ├── __init__.py              # Module initialization
   ├── __manifest__.py          # Module manifest/descriptor
   ├── models/                  # Python models
   │   ├── __init__.py
   │   ├── sample_model.py      # Main model
   │   └── res_config_settings.py
   ├── views/                   # XML views
   │   ├── sample_views.xml
   │   ├── sample_menus.xml
   │   └── sample_templates.xml
   ├── security/                # Access control
   │   ├── ir.model.access.csv
   │   └── sample_security.xml
   ├── data/                    # Data files
   │   ├── sample_data.xml
   │   └── email_templates.xml
   ├── wizard/                  # Wizard models
   │   ├── __init__.py
   │   └── sample_wizard.py
   ├── report/                  # Reports
   │   ├── __init__.py
   │   ├── sample_report.py
   │   └── sample_report_template.xml
   ├── static/                  # Static assets
   │   ├── description/
   │   │   ├── icon.png
   │   │   └── index.html
   │   └── src/
   │       ├── js/
   │       ├── css/
   │       └── xml/
   ├── tests/                   # Unit tests
   │   ├── __init__.py
   │   └── test_sample.py
   ├── i18n/                    # Translations
   │   ├── fr.po
   │   └── es.po
   └── README.md

Manifest File
~~~~~~~~~~~~~

**__manifest__.py:**

.. code-block:: python

   {
       'name': 'Sample Module',
       'version': '17.0.1.0.0',
       'category': 'Custom',
       'summary': 'Sample module for documentation',
       'description': """
           Sample Module
           =============
           
           This module demonstrates:
           * Model creation
           * View definitions
           * Security rules
           * Automated actions
       """,
       'author': 'Your Company',
       'website': 'https://www.yourcompany.com',
       'license': 'LGPL-3',
       'depends': [
           'base',
           'mail',
       ],
       'data': [
           # Security
           'security/sample_security.xml',
           'security/ir.model.access.csv',
           
           # Data
           'data/sample_data.xml',
           'data/email_templates.xml',
           
           # Views
           'views/sample_views.xml',
           'views/sample_menus.xml',
           
           # Wizards
           'wizard/sample_wizard_views.xml',
           
           # Reports
           'report/sample_report_template.xml',
       ],
       'demo': [
           'demo/sample_demo.xml',
       ],
       'assets': {
           'web.assets_backend': [
               'sample_module/static/src/js/sample.js',
               'sample_module/static/src/css/sample.css',
           ],
       },
       'installable': True,
       'application': True,
       'auto_install': False,
   }

Models
------

Main Model
~~~~~~~~~~

**models/sample_model.py:**

.. code-block:: python

   from odoo import models, fields, api
   from odoo.exceptions import ValidationError
   
   class SampleModel(models.Model):
       """Main model for Sample Module.
       
       This model stores and manages sample records with
       various fields and automated workflows.
       """
       
       _name = 'sample.module'
       _description = 'Sample Module Record'
       _inherit = ['mail.thread', 'mail.activity.mixin']
       _order = 'create_date desc'
       
       # Basic fields
       name = fields.Char(
           string='Name',
           required=True,
           tracking=True,
           help='Name of the record'
       )
       
       description = fields.Text(
           string='Description',
           help='Detailed description'
       )
       
       # Selection field
       state = fields.Selection([
           ('draft', 'Draft'),
           ('submitted', 'Submitted'),
           ('approved', 'Approved'),
           ('done', 'Done'),
           ('rejected', 'Rejected'),
       ], string='Status', default='draft', tracking=True)
       
       # Relational fields
       user_id = fields.Many2one(
           'res.users',
           string='Responsible User',
           default=lambda self: self.env.user,
           tracking=True
       )
       
       partner_id = fields.Many2one(
           'res.partner',
           string='Related Partner',
           required=True
       )
       
       line_ids = fields.One2many(
           'sample.module.line',
           'sample_id',
           string='Lines'
       )
       
       tag_ids = fields.Many2many(
           'sample.module.tag',
           string='Tags'
       )
       
       # Date fields
       date = fields.Date(
           string='Date',
           default=fields.Date.today,
           required=True
       )
       
       deadline = fields.Datetime(
           string='Deadline'
       )
       
       # Numeric fields
       amount = fields.Float(
           string='Amount',
           digits=(16, 2)
       )
       
       quantity = fields.Integer(
           string='Quantity',
           default=1
       )
       
       # Computed fields
       total_amount = fields.Float(
           string='Total Amount',
           compute='_compute_total_amount',
           store=True
       )
       
       line_count = fields.Integer(
           string='Number of Lines',
           compute='_compute_line_count'
       )
       
       # Boolean fields
       active = fields.Boolean(
           string='Active',
           default=True
       )
       
       is_urgent = fields.Boolean(
           string='Urgent'
       )
       
       # Computed fields
       @api.depends('line_ids', 'line_ids.subtotal')
       def _compute_total_amount(self):
           """Calculate total amount from lines."""
           for record in self:
               record.total_amount = sum(record.line_ids.mapped('subtotal'))
       
       @api.depends('line_ids')
       def _compute_line_count(self):
           """Count the number of lines."""
           for record in self:
               record.line_count = len(record.line_ids)
       
       # Constraints
       @api.constrains('amount')
       def _check_amount(self):
           """Validate amount is positive."""
           for record in self:
               if record.amount < 0:
                   raise ValidationError('Amount must be positive!')
       
       @api.constrains('date', 'deadline')
       def _check_dates(self):
           """Validate deadline is after date."""
           for record in self:
               if record.deadline and record.date:
                   if fields.Datetime.from_string(record.deadline).date() < record.date:
                       raise ValidationError('Deadline must be after date!')
       
       # CRUD methods
       @api.model
       def create(self, vals):
           """Override create to add custom logic."""
           # Custom logic before create
           if 'name' not in vals:
               vals['name'] = self.env['ir.sequence'].next_by_code('sample.module')
           
           # Create record
           record = super().create(vals)
           
           # Custom logic after create
           record._send_notification('created')
           
           return record
       
       def write(self, vals):
           """Override write to add custom logic."""
           # Custom logic before write
           old_state = self.state
           
           # Write values
           result = super().write(vals)
           
           # Custom logic after write
           if 'state' in vals and old_state != self.state:
               self._send_notification('state_changed')
           
           return result
       
       def unlink(self):
           """Override unlink to add validations."""
           for record in self:
               if record.state not in ['draft', 'rejected']:
                   raise ValidationError('Only draft or rejected records can be deleted!')
           return super().unlink()
       
       # Action methods
       def action_submit(self):
           """Submit record for approval."""
           self.ensure_one()
           if self.state != 'draft':
               raise ValidationError('Only draft records can be submitted!')
           
           self.write({'state': 'submitted'})
           self._create_approval_activity()
       
       def action_approve(self):
           """Approve the record."""
           self.ensure_one()
           if self.state != 'submitted':
               raise ValidationError('Only submitted records can be approved!')
           
           self.write({'state': 'approved'})
           self._send_notification('approved')
       
       def action_reject(self):
           """Reject the record."""
           self.ensure_one()
           if self.state != 'submitted':
               raise ValidationError('Only submitted records can be rejected!')
           
           self.write({'state': 'rejected'})
           self._send_notification('rejected')
       
       def action_set_to_done(self):
           """Mark as done."""
           self.ensure_one()
           if self.state != 'approved':
               raise ValidationError('Only approved records can be done!')
           
           self.write({'state': 'done'})
       
       def action_reset_to_draft(self):
           """Reset to draft."""
           self.write({'state': 'draft'})
       
       # Helper methods
       def _send_notification(self, notification_type):
           """Send email notification."""
           self.ensure_one()
           template_ref = f'sample_module.email_template_{notification_type}'
           template = self.env.ref(template_ref, raise_if_not_found=False)
           if template:
               template.send_mail(self.id)
       
       def _create_approval_activity(self):
           """Create activity for approval."""
           self.ensure_one()
           self.activity_schedule(
               'sample_module.mail_activity_type_approval',
               user_id=self.user_id.id,
               summary='Approval Required',
               note=f'Please review and approve {self.name}'
           )

Line Model
~~~~~~~~~~

**models/sample_module_line.py:**

.. code-block:: python

   class SampleModuleLine(models.Model):
       """Line items for Sample Module."""
       
       _name = 'sample.module.line'
       _description = 'Sample Module Line'
       _order = 'sequence, id'
       
       sequence = fields.Integer(string='Sequence', default=10)
       sample_id = fields.Many2one('sample.module', string='Sample', required=True, ondelete='cascade')
       product_id = fields.Many2one('product.product', string='Product', required=True)
       quantity = fields.Float(string='Quantity', default=1.0)
       price_unit = fields.Float(string='Unit Price')
       subtotal = fields.Float(string='Subtotal', compute='_compute_subtotal', store=True)
       
       @api.depends('quantity', 'price_unit')
       def _compute_subtotal(self):
           for line in self:
               line.subtotal = line.quantity * line.price_unit
       
       @api.onchange('product_id')
       def _onchange_product_id(self):
           if self.product_id:
               self.price_unit = self.product_id.list_price

Views
-----

Form View
~~~~~~~~~

**views/sample_views.xml:**

.. code-block:: xml

   <?xml version="1.0" encoding="utf-8"?>
   <odoo>
       <!-- Form View -->
       <record id="view_sample_module_form" model="ir.ui.view">
           <field name="name">sample.module.form</field>
           <field name="model">sample.module</field>
           <field name="arch" type="xml">
               <form string="Sample Module">
                   <header>
                       <button name="action_submit" string="Submit" 
                               type="object" class="oe_highlight"
                               states="draft"/>
                       <button name="action_approve" string="Approve" 
                               type="object" class="oe_highlight"
                               states="submitted"
                               groups="sample_module.group_sample_module_manager"/>
                       <button name="action_reject" string="Reject" 
                               type="object"
                               states="submitted"
                               groups="sample_module.group_sample_module_manager"/>
                       <button name="action_set_to_done" string="Done" 
                               type="object"
                               states="approved"/>
                       <button name="action_reset_to_draft" string="Reset to Draft" 
                               type="object"
                               states="rejected"/>
                       <field name="state" widget="statusbar" 
                              statusbar_visible="draft,submitted,approved,done"/>
                   </header>
                   <sheet>
                       <div class="oe_button_box" name="button_box">
                           <button name="action_view_lines" type="object"
                                   class="oe_stat_button" icon="fa-list">
                               <field name="line_count" widget="statinfo" 
                                      string="Lines"/>
                           </button>
                       </div>
                       <div class="oe_title">
                           <h1>
                               <field name="name" placeholder="Name"/>
                           </h1>
                       </div>
                       <group>
                           <group>
                               <field name="partner_id"/>
                               <field name="user_id"/>
                               <field name="date"/>
                           </group>
                           <group>
                               <field name="deadline"/>
                               <field name="is_urgent"/>
                               <field name="tag_ids" widget="many2many_tags"/>
                           </group>
                       </group>
                       <notebook>
                           <page string="Details">
                               <field name="description"/>
                           </page>
                           <page string="Lines">
                               <field name="line_ids">
                                   <tree editable="bottom">
                                       <field name="sequence" widget="handle"/>
                                       <field name="product_id"/>
                                       <field name="quantity"/>
                                       <field name="price_unit"/>
                                       <field name="subtotal"/>
                                   </tree>
                               </field>
                               <group class="oe_subtotal_footer">
                                   <field name="total_amount"/>
                               </group>
                           </page>
                       </notebook>
                   </sheet>
                   <div class="oe_chatter">
                       <field name="message_follower_ids"/>
                       <field name="activity_ids"/>
                       <field name="message_ids"/>
                   </div>
               </form>
           </field>
       </record>
       
       <!-- Tree View -->
       <record id="view_sample_module_tree" model="ir.ui.view">
           <field name="name">sample.module.tree</field>
           <field name="model">sample.module</field>
           <field name="arch" type="xml">
               <tree string="Sample Modules" 
                     decoration-info="state=='draft'" 
                     decoration-warning="state=='submitted'"
                     decoration-success="state=='approved'">
                   <field name="name"/>
                   <field name="partner_id"/>
                   <field name="date"/>
                   <field name="user_id"/>
                   <field name="total_amount"/>
                   <field name="state" widget="badge"/>
               </tree>
           </field>
       </record>
       
       <!-- Search View -->
       <record id="view_sample_module_search" model="ir.ui.view">
           <field name="name">sample.module.search</field>
           <field name="model">sample.module</field>
           <field name="arch" type="xml">
               <search string="Sample Modules">
                   <field name="name"/>
                   <field name="partner_id"/>
                   <field name="user_id"/>
                   <filter name="my_records" string="My Records"
                           domain="[('user_id', '=', uid)]"/>
                   <filter name="urgent" string="Urgent"
                           domain="[('is_urgent', '=', True)]"/>
                   <separator/>
                   <filter name="draft" string="Draft"
                           domain="[('state', '=', 'draft')]"/>
                   <filter name="submitted" string="Submitted"
                           domain="[('state', '=', 'submitted')]"/>
                   <filter name="approved" string="Approved"
                           domain="[('state', '=', 'approved')]"/>
                   <group expand="0" string="Group By">
                       <filter name="group_state" string="Status"
                               context="{'group_by': 'state'}"/>
                       <filter name="group_user" string="User"
                               context="{'group_by': 'user_id'}"/>
                       <filter name="group_date" string="Date"
                               context="{'group_by': 'date'}"/>
                   </group>
               </search>
           </field>
       </record>
       
       <!-- Kanban View -->
       <record id="view_sample_module_kanban" model="ir.ui.view">
           <field name="name">sample.module.kanban</field>
           <field name="model">sample.module</field>
           <field name="arch" type="xml">
               <kanban default_group_by="state">
                   <field name="name"/>
                   <field name="partner_id"/>
                   <field name="total_amount"/>
                   <field name="state"/>
                   <templates>
                       <t t-name="kanban-box">
                           <div class="oe_kanban_global_click">
                               <div class="oe_kanban_details">
                                   <strong><field name="name"/></strong>
                                   <div><field name="partner_id"/></div>
                                   <div><field name="total_amount"/></div>
                               </div>
                           </div>
                       </t>
                   </templates>
               </kanban>
           </field>
       </record>
       
       <!-- Actions -->
       <record id="action_sample_module" model="ir.actions.act_window">
           <field name="name">Sample Modules</field>
           <field name="res_model">sample.module</field>
           <field name="view_mode">tree,kanban,form</field>
           <field name="context">{'search_default_my_records': 1}</field>
           <field name="help" type="html">
               <p class="o_view_nocontent_smiling_face">
                   Create your first record
               </p>
           </field>
       </record>
   </odoo>

Security
--------

Access Rights
~~~~~~~~~~~~~

**security/ir.model.access.csv:**

.. code-block:: text

   id,name,model_id:id,group_id:id,perm_read,perm_write,perm_create,perm_unlink
   access_sample_module_user,sample.module.user,model_sample_module,group_sample_module_user,1,1,1,0
   access_sample_module_manager,sample.module.manager,model_sample_module,group_sample_module_manager,1,1,1,1
   access_sample_module_line_user,sample.module.line.user,model_sample_module_line,group_sample_module_user,1,1,1,1

Security Groups
~~~~~~~~~~~~~~~

**security/sample_security.xml:**

.. code-block:: xml

   <?xml version="1.0" encoding="utf-8"?>
   <odoo>
       <record id="module_category_sample" model="ir.module.category">
           <field name="name">Sample Module</field>
       </record>
       
       <record id="group_sample_module_user" model="res.groups">
           <field name="name">User</field>
           <field name="category_id" ref="module_category_sample"/>
       </record>
       
       <record id="group_sample_module_manager" model="res.groups">
           <field name="name">Manager</field>
           <field name="category_id" ref="module_category_sample"/>
           <field name="implied_ids" eval="[(4, ref('group_sample_module_user'))]"/>
       </record>
       
       <!-- Record Rules -->
       <record id="sample_module_personal_rule" model="ir.rule">
           <field name="name">Personal Records</field>
           <field name="model_id" ref="model_sample_module"/>
           <field name="domain_force">
               [('user_id', '=', user.id)]
           </field>
           <field name="groups" eval="[(4, ref('group_sample_module_user'))]"/>
       </record>
   </odoo>

API Reference
-------------

Public Methods
~~~~~~~~~~~~~~

.. py:class:: SampleModel

   .. py:method:: action_submit()
   
      Submit the record for approval.
      
      :raises ValidationError: If record is not in draft state
      :return: True
      :rtype: bool

   .. py:method:: action_approve()
   
      Approve the record.
      
      :raises ValidationError: If record is not in submitted state
      :return: True
      :rtype: bool

   .. py:method:: compute_total()
   
      Compute total amount from lines.
      
      :return: Total amount
      :rtype: float

Testing
-------

Unit Tests
~~~~~~~~~~

**tests/test_sample.py:**

.. code-block:: python

   from odoo.tests import tagged
   from odoo.tests.common import TransactionCase
   from odoo.exceptions import ValidationError
   
   @tagged('post_install', '-at_install')
   class TestSampleModule(TransactionCase):
       
       def setUp(self):
           super().setUp()
           self.SampleModel = self.env['sample.module']
           self.partner = self.env['res.partner'].create({
               'name': 'Test Partner'
           })
       
       def test_create_record(self):
           """Test record creation."""
           record = self.SampleModel.create({
               'name': 'Test Record',
               'partner_id': self.partner.id,
           })
           self.assertTrue(record)
           self.assertEqual(record.state, 'draft')
       
       def test_workflow(self):
           """Test state workflow."""
           record = self.SampleModel.create({
               'name': 'Test Workflow',
               'partner_id': self.partner.id,
           })
           
           # Submit
           record.action_submit()
           self.assertEqual(record.state, 'submitted')
           
           # Approve
           record.action_approve()
           self.assertEqual(record.state, 'approved')
           
           # Done
           record.action_set_to_done()
           self.assertEqual(record.state, 'done')
       
       def test_validation(self):
           """Test field validation."""
           with self.assertRaises(ValidationError):
               self.SampleModel.create({
                   'name': 'Test',
                   'partner_id': self.partner.id,
                   'amount': -100,  # Should fail
               })

Development Guidelines
----------------------

Coding Standards
~~~~~~~~~~~~~~~~

Follow PEP 8 and Odoo guidelines:

* Use 4 spaces for indentation
* Maximum line length: 120 characters
* Add docstrings to all methods
* Use meaningful variable names

Performance Best Practices
~~~~~~~~~~~~~~~~~~~~~~~~~~~

* Use ``_sql_constraints`` for database-level validation
* Batch operations when possible
* Use ``@api.depends`` efficiently
* Avoid unnecessary database queries

Version Control
~~~~~~~~~~~~~~~

* Use meaningful commit messages
* Follow git flow branching model
* Tag releases properly

Deployment
----------

Production Deployment Checklist
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

☐ Run all tests
☐ Update version in manifest
☐ Update changelog
☐ Backup production database
☐ Deploy to staging first
☐ Verify in staging
☐ Deploy to production
☐ Monitor logs

Migration Guide
~~~~~~~~~~~~~~~

When upgrading between versions, follow the migration scripts in the ``migrations/`` directory.

Related Documentation
---------------------

* :doc:`overview` - Module overview
* :doc:`configuration` - Configuration guide
* :doc:`user_guide` - User documentation
* Back to :doc:`index` - Module home
