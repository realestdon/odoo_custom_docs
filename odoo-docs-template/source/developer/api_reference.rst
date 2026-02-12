API Reference
=============

Complete API reference for custom Odoo modules.

Overview
--------

This document provides API documentation for programmatic access to custom modules.

.. note::
   All APIs follow Odoo's standard RPC patterns and can be accessed via
   XML-RPC, JSON-RPC, or the Python ORM.

Authentication
--------------

XML-RPC Authentication
~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: python

   import xmlrpc.client
   
   url = 'http://localhost:8069'
   db = 'your_database'
   username = 'admin'
   password = 'admin'
   
   common = xmlrpc.client.ServerProxy(f'{url}/xmlrpc/2/common')
   uid = common.authenticate(db, username, password, {})
   
   models = xmlrpc.client.ServerProxy(f'{url}/xmlrpc/2/object')

JSON-RPC Authentication
~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: python

   import requests
   import json
   
   url = 'http://localhost:8069'
   db = 'your_database'
   username = 'admin'
   password = 'admin'
   
   headers = {'Content-Type': 'application/json'}
   auth_data = {
       'jsonrpc': '2.0',
       'params': {
           'db': db,
           'login': username,
           'password': password
       }
   }
   
   response = requests.post(
       f'{url}/web/session/authenticate',
       data=json.dumps(auth_data),
       headers=headers
   )
   session_id = response.cookies.get('session_id')

Sample Module API
-----------------

Create Record
~~~~~~~~~~~~~

**Endpoint**: ``execute_kw``

**Method**: ``create``

.. code-block:: python

   # XML-RPC
   record_id = models.execute_kw(
       db, uid, password,
       'sample.module', 'create',
       [{
           'name': 'New Record',
           'partner_id': 1,
           'date': '2024-01-01',
       }]
   )

**Parameters:**

:name: (required) Record name
:partner_id: (required) Related partner ID
:date: (optional) Record date
:description: (optional) Description
:amount: (optional) Amount value

**Returns:** Integer - Created record ID

Read Records
~~~~~~~~~~~~

**Endpoint**: ``execute_kw``

**Method**: ``search_read``

.. code-block:: python

   # XML-RPC
   records = models.execute_kw(
       db, uid, password,
       'sample.module', 'search_read',
       [[['state', '=', 'draft']]],  # domain
       {'fields': ['name', 'state', 'date'], 'limit': 10}
   )

**Parameters:**

:domain: Search criteria (list of tuples)
:fields: List of fields to retrieve
:limit: Maximum number of records
:offset: Number of records to skip
:order: Sort order

**Returns:** List of dictionaries with record data

Update Record
~~~~~~~~~~~~~

**Endpoint**: ``execute_kw``

**Method**: ``write``

.. code-block:: python

   # XML-RPC
   success = models.execute_kw(
       db, uid, password,
       'sample.module', 'write',
       [[record_id], {'state': 'approved'}]
   )

**Parameters:**

:ids: List of record IDs to update
:values: Dictionary of field values

**Returns:** Boolean - Success status

Delete Record
~~~~~~~~~~~~~

**Endpoint**: ``execute_kw``

**Method**: ``unlink``

.. code-block:: python

   # XML-RPC
   success = models.execute_kw(
       db, uid, password,
       'sample.module', 'unlink',
       [[record_id]]
   )

**Parameters:**

:ids: List of record IDs to delete

**Returns:** Boolean - Success status

Custom Methods
~~~~~~~~~~~~~~

Submit for Approval
"""""""""""""""""""

.. code-block:: python

   # XML-RPC
   result = models.execute_kw(
       db, uid, password,
       'sample.module', 'action_submit',
       [[record_id]]
   )

**Parameters:**

:record_id: ID of record to submit

**Returns:** Boolean - Success status

**Raises:** ValidationError if record is not in draft state

Approve Record
""""""""""""""

.. code-block:: python

   result = models.execute_kw(
       db, uid, password,
       'sample.module', 'action_approve',
       [[record_id]]
   )

**Parameters:**

:record_id: ID of record to approve

**Returns:** Boolean - Success status

**Raises:** ValidationError if record is not in submitted state

Field Reference
---------------

Sample Module Fields
~~~~~~~~~~~~~~~~~~~~

.. list-table::
   :header-rows: 1
   :widths: 20 20 15 45

   * - Field Name
     - Type
     - Required
     - Description
   * - name
     - Char
     - Yes
     - Record name
   * - description
     - Text
     - No
     - Detailed description
   * - state
     - Selection
     - No
     - Current status
   * - partner_id
     - Many2one
     - Yes
     - Related partner
   * - user_id
     - Many2one
     - No
     - Responsible user
   * - date
     - Date
     - Yes
     - Record date
   * - deadline
     - Datetime
     - No
     - Deadline
   * - amount
     - Float
     - No
     - Amount value
   * - line_ids
     - One2many
     - No
     - Related lines
   * - tag_ids
     - Many2many
     - No
     - Tags
   * - total_amount
     - Float
     - No
     - Computed total (read-only)
   * - active
     - Boolean
     - No
     - Active flag

State Values
""""""""""""

.. list-table::
   :header-rows: 1
   :widths: 30 70

   * - Value
     - Description
   * - draft
     - Initial state, editable
   * - submitted
     - Submitted for approval
   * - approved
     - Approved and processing
   * - done
     - Completed
   * - rejected
     - Rejected, can be reset to draft

Python ORM Examples
-------------------

Direct ORM Access
~~~~~~~~~~~~~~~~~

When writing Odoo modules or server actions:

.. code-block:: python

   # Get the model
   SampleModel = env['sample.module']
   
   # Create record
   record = SampleModel.create({
       'name': 'New Record',
       'partner_id': partner.id,
   })
   
   # Search records
   records = SampleModel.search([
       ('state', '=', 'draft'),
       ('date', '>=', '2024-01-01'),
   ])
   
   # Update records
   records.write({'state': 'submitted'})
   
   # Call methods
   record.action_approve()
   
   # Access fields
   total = record.total_amount
   lines = record.line_ids

Domain Filters
~~~~~~~~~~~~~~

Common domain filter examples:

.. code-block:: python

   # Exact match
   [('name', '=', 'Test')]
   
   # Not equal
   [('state', '!=', 'draft')]
   
   # Greater than
   [('amount', '>', 100)]
   
   # Contains
   [('name', 'ilike', 'test')]
   
   # In list
   [('state', 'in', ['draft', 'submitted'])]
   
   # Multiple conditions (AND)
   ['&', ('state', '=', 'draft'), ('amount', '>', 100)]
   
   # OR condition
   ['|', ('state', '=', 'draft'), ('state', '=', 'submitted')]

Computed Fields
~~~~~~~~~~~~~~~

Accessing computed fields:

.. code-block:: python

   # Read directly
   total = record.total_amount
   
   # Force recomputation
   record._compute_total_amount()
   
   # Access via read
   data = record.read(['total_amount'])[0]

REST API Endpoints
------------------

If REST API module is installed:

List Records
~~~~~~~~~~~~

**GET** ``/api/sample-module``

**Query Parameters:**

* ``limit``: Number of records (default: 80)
* ``offset``: Offset for pagination
* ``state``: Filter by state
* ``partner_id``: Filter by partner

**Response:**

.. code-block:: json

   {
       "count": 150,
       "records": [
           {
               "id": 1,
               "name": "Record 1",
               "state": "draft",
               "date": "2024-01-01"
           }
       ]
   }

Get Single Record
~~~~~~~~~~~~~~~~~

**GET** ``/api/sample-module/{id}``

**Response:**

.. code-block:: json

   {
       "id": 1,
       "name": "Record 1",
       "description": "Description here",
       "state": "draft",
       "partner_id": {
           "id": 5,
           "name": "Partner Name"
       }
   }

Create Record
~~~~~~~~~~~~~

**POST** ``/api/sample-module``

**Request Body:**

.. code-block:: json

   {
       "name": "New Record",
       "partner_id": 5,
       "date": "2024-01-01"
   }

**Response:**

.. code-block:: json

   {
       "id": 123,
       "message": "Record created successfully"
   }

Update Record
~~~~~~~~~~~~~

**PUT** ``/api/sample-module/{id}``

**Request Body:**

.. code-block:: json

   {
       "state": "approved",
       "description": "Updated description"
   }

Delete Record
~~~~~~~~~~~~~

**DELETE** ``/api/sample-module/{id}``

**Response:**

.. code-block:: json

   {
       "message": "Record deleted successfully"
   }

Webhooks
--------

Event Triggers
~~~~~~~~~~~~~~

Configure webhooks for events:

* ``record.created``
* ``record.updated``
* ``record.deleted``
* ``state.changed``

Webhook Payload
~~~~~~~~~~~~~~~

.. code-block:: json

   {
       "event": "record.created",
       "model": "sample.module",
       "record_id": 123,
       "timestamp": "2024-01-01T10:00:00Z",
       "data": {
           "name": "New Record",
           "state": "draft"
       }
   }

Error Handling
--------------

Common Error Codes
~~~~~~~~~~~~~~~~~~

.. list-table::
   :header-rows: 1
   :widths: 20 30 50

   * - Code
     - Error Type
     - Description
   * - 400
     - Bad Request
     - Invalid parameters
   * - 401
     - Unauthorized
     - Authentication failed
   * - 403
     - Forbidden
     - Insufficient permissions
   * - 404
     - Not Found
     - Record not found
   * - 500
     - Server Error
     - Internal server error

Error Response Format
~~~~~~~~~~~~~~~~~~~~~

.. code-block:: json

   {
       "jsonrpc": "2.0",
       "error": {
           "code": 200,
           "message": "Odoo Server Error",
           "data": {
               "name": "ValidationError",
               "message": "Only draft records can be submitted!"
           }
       }
   }

Rate Limiting
-------------

API requests are limited to:

* **Anonymous**: 60 requests/minute
* **Authenticated**: 300 requests/minute
* **Premium**: 1000 requests/minute

Best Practices
--------------

1. **Use Batch Operations**

   .. code-block:: python

      # Good: Single call
      records.write({'state': 'approved'})
      
      # Avoid: Multiple calls
      for record in records:
          record.write({'state': 'approved'})

2. **Limit Field Retrieval**

   .. code-block:: python

      # Good: Only needed fields
      records.read(['name', 'state'])
      
      # Avoid: All fields
      records.read()

3. **Use Proper Domains**

   .. code-block:: python

      # Good: Efficient filtering
      records.search([('state', '=', 'draft')], limit=100)
      
      # Avoid: No limits
      all_records = records.search([])

4. **Handle Exceptions**

   .. code-block:: python

      try:
          record.action_approve()
      except ValidationError as e:
          # Handle validation error
          print(f"Validation failed: {e}")

See Also
--------

* :doc:`coding_guidelines` - Development standards
* :doc:`/modules/sample_module/technical` - Module technical docs
* Official Odoo API Documentation
