Developer Documentation
=======================

Technical documentation for developers working with custom Odoo modules.

.. toctree::
   :maxdepth: 2
   :caption: Developer Guides:

   api_reference
   coding_guidelines

Overview
--------

This section provides technical documentation for:

* Module developers
* System administrators
* Integration developers
* DevOps engineers

What You'll Find Here
---------------------

**API Documentation**
   Complete API reference for all custom modules

**Coding Guidelines**
   Standards and best practices for development

**Integration Guides**
   How to integrate with custom modules

**Deployment Documentation**
   Deployment and configuration instructions

Prerequisites
-------------

Before developing custom modules, you should be familiar with:

* Python programming
* Odoo framework basics
* PostgreSQL database
* XML and XPath
* Git version control

Development Environment
-----------------------

Setting Up
~~~~~~~~~~

1. Install Odoo 17.0 development environment
2. Clone the custom modules repository
3. Configure your IDE (VS Code recommended)
4. Set up debugging tools

Recommended Tools
~~~~~~~~~~~~~~~~~

* **IDE**: VS Code with Python extension
* **Version Control**: Git
* **Database**: PostgreSQL 12+
* **Debugging**: pdb or VS Code debugger
* **Testing**: pytest-odoo

Quick Start for Developers
---------------------------

1. **Clone Repository**

   .. code-block:: bash

      git clone https://github.com/yourcompany/custom-modules.git
      cd custom-modules

2. **Set Up Environment**

   .. code-block:: bash

      python3 -m venv venv
      source venv/bin/activate
      pip install -r requirements.txt

3. **Configure Odoo**

   .. code-block:: bash

      ./odoo-bin --addons-path=./addons,./custom-modules \
                  -d dev_db --dev=all

4. **Install Module**

   .. code-block:: bash

      ./odoo-bin -d dev_db -i sample_module

Module Development Workflow
----------------------------

1. **Create Feature Branch**

   .. code-block:: bash

      git checkout -b feature/my-new-feature

2. **Develop**

   * Create models, views, controllers
   * Write tests
   * Update documentation

3. **Test**

   .. code-block:: bash

      ./odoo-bin -d test_db -i sample_module --test-enable

4. **Commit and Push**

   .. code-block:: bash

      git add .
      git commit -m "feat: add new feature"
      git push origin feature/my-new-feature

5. **Create Pull Request**

   Submit PR for code review

Contributing
------------

We welcome contributions! Please read our :doc:`coding_guidelines` before submitting code.

**Contribution Process:**

1. Fork the repository
2. Create a feature branch
3. Write code and tests
4. Update documentation
5. Submit pull request
6. Address review comments

Architecture Overview
---------------------

Module Structure
~~~~~~~~~~~~~~~~

All custom modules follow the standard Odoo module structure:

.. code-block:: text

   module_name/
   ├── __init__.py
   ├── __manifest__.py
   ├── models/
   ├── views/
   ├── security/
   ├── data/
   ├── static/
   └── tests/

Common Patterns
~~~~~~~~~~~~~~~

* **Model-View-Controller**: Standard Odoo architecture
* **Inheritance**: Extending existing models
* **Mixins**: Reusable model features
* **Computed Fields**: Dynamic field values
* **Automated Actions**: Scheduled tasks and triggers

Resources
---------

Official Documentation
~~~~~~~~~~~~~~~~~~~~~~

* `Odoo Documentation <https://www.odoo.com/documentation/17.0/>`_
* `Odoo Developer Documentation <https://www.odoo.com/documentation/17.0/developer.html>`_

Internal Resources
~~~~~~~~~~~~~~~~~~

* Code Repository: https://github.com/yourcompany/custom-modules
* Issue Tracker: https://github.com/yourcompany/custom-modules/issues
* Wiki: https://wiki.yourcompany.com/odoo

Community
~~~~~~~~~

* Odoo Community: https://www.odoo.com/forum
* Stack Overflow: Tag [odoo]
* GitHub Discussions

Support
-------

For technical support:

* **Development Team**: dev@yourcompany.com
* **Chat**: #odoo-dev channel
* **Office Hours**: Tuesdays 2-4 PM

Next Steps
----------

* Review :doc:`api_reference` for available APIs
* Read :doc:`coding_guidelines` for development standards
* Explore module documentation in :doc:`/modules/index`
