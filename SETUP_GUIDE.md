# Custom Odoo Module Documentation Setup Guide

This guide will help you set up a documentation system for your custom Odoo modules based on the official Odoo documentation structure.

## Quick Start

### 1. Initial Setup

```bash
# Create your documentation directory
mkdir my-odoo-docs
cd my-odoo-docs

# Create a virtual environment
python3 -m venv .venv
source .venv/bin/activate  # On Windows: .\.venv\Scripts\Activate.ps1

# Install Sphinx and dependencies
pip install sphinx sphinx-rtd-theme sphinx-autobuild
```

### 2. Initialize Sphinx

```bash
# Run Sphinx quickstart
sphinx-quickstart

# Answer the prompts:
# - Separate source and build directories: yes
# - Project name: Your Project Name
# - Author name: Your Name
# - Project version: 1.0
# - Language: en
```

## Directory Structure

Here's the recommended structure for your documentation:

```
my-odoo-docs/
├── source/                      # Documentation source files
│   ├── _static/                 # Static files (CSS, images, JS)
│   │   ├── css/
│   │   │   └── custom.css
│   │   └── images/
│   ├── _templates/              # Custom HTML templates
│   ├── modules/                 # Module documentation
│   │   ├── index.rst
│   │   ├── module_name_1/
│   │   │   ├── index.rst
│   │   │   ├── overview.rst
│   │   │   ├── installation.rst
│   │   │   ├── configuration.rst
│   │   │   ├── user_guide.rst
│   │   │   └── technical.rst
│   │   └── module_name_2/
│   │       └── ...
│   ├── developer/               # Developer documentation
│   │   ├── index.rst
│   │   ├── api_reference.rst
│   │   └── coding_guidelines.rst
│   ├── user/                    # User guides
│   │   ├── index.rst
│   │   └── getting_started.rst
│   ├── conf.py                  # Sphinx configuration
│   └── index.rst                # Main documentation index
├── build/                       # Generated documentation
├── requirements.txt             # Python dependencies
├── Makefile                     # Build commands (Linux/Mac)
├── make.bat                     # Build commands (Windows)
└── README.md                    # Repository documentation
```

## Step-by-Step Implementation

### Step 1: Create the Directory Structure

```bash
# Create main directories
mkdir -p source/_static/css
mkdir -p source/_static/images
mkdir -p source/_templates
mkdir -p source/modules
mkdir -p source/developer
mkdir -p source/user
```

### Step 2: Configure Sphinx (conf.py)

The configuration file is automatically created, but you'll need to customize it. Key sections to modify:

```python
# Project information
project = 'My Odoo Modules Documentation'
copyright = '2024, Your Company'
author = 'Your Name'
version = '17.0'  # Match your Odoo version
release = '17.0'

# Theme
html_theme = 'sphinx_rtd_theme'

# Extensions
extensions = [
    'sphinx.ext.autodoc',      # Auto-generate documentation from docstrings
    'sphinx.ext.napoleon',     # Support for Google/NumPy docstrings
    'sphinx.ext.viewcode',     # Add links to source code
    'sphinx.ext.intersphinx',  # Link to other documentation
    'sphinx.ext.todo',         # Support for TODO items
]

# Intersphinx mapping (link to Odoo official docs)
intersphinx_mapping = {
    'odoo': ('https://www.odoo.com/documentation/17.0', None),
    'python': ('https://docs.python.org/3', None),
}

# HTML theme options
html_theme_options = {
    'navigation_depth': 4,
    'collapse_navigation': False,
    'sticky_navigation': True,
}

# Static files
html_static_path = ['_static']
html_css_files = ['css/custom.css']

# Master document
master_doc = 'index'
```

### Step 3: Create Main Index (source/index.rst)

```rst
Welcome to My Odoo Modules Documentation
=========================================

This documentation covers custom Odoo modules developed for [Your Company].

.. toctree::
   :maxdepth: 2
   :caption: Contents:

   user/index
   modules/index
   developer/index

About This Documentation
------------------------

This documentation is organized into three main sections:

* **User Guide**: End-user documentation for using the modules
* **Modules**: Detailed documentation for each custom module
* **Developer Guide**: Technical documentation for developers

Getting Started
---------------

If you're new to our custom modules, start with the :doc:`user/getting_started` guide.

Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
```

### Step 4: Create Module Documentation Template

Create a template structure for each module:

**source/modules/index.rst:**
```rst
Modules
=======

This section contains documentation for all custom Odoo modules.

.. toctree::
   :maxdepth: 2
   :caption: Available Modules:

   module_name_1/index
   module_name_2/index

Module List
-----------

* :doc:`module_name_1/index` - Brief description
* :doc:`module_name_2/index` - Brief description
```

**source/modules/module_name_1/index.rst:**
```rst
Module Name 1
=============

Brief description of what this module does.

.. toctree::
   :maxdepth: 2

   overview
   installation
   configuration
   user_guide
   technical

Quick Info
----------

:Module Name: module_name_1
:Version: 17.0.1.0.0
:Category: Custom/Specific Category
:License: LGPL-3
:Dependencies: base, sale, stock

Overview
--------

This module extends Odoo's functionality to provide...

See :doc:`overview` for detailed information.
```

### Step 5: Create Requirements File

**requirements.txt:**
```txt
Sphinx>=7.0.0
sphinx-rtd-theme>=2.0.0
sphinx-autobuild>=2024.0.0
```

### Step 6: Build the Documentation

```bash
# Build HTML documentation
make html

# Or use sphinx-autobuild for live preview
sphinx-autobuild source build/html

# View the documentation
# Open build/html/index.html in your browser
```

## Documentation Writing Tips

### 1. Use reStructuredText (RST) Syntax

```rst
Section Header
==============

Subsection
----------

**Bold text**
*Italic text*
``Code or command``

Bullet list:
* Item 1
* Item 2

Numbered list:
1. First
2. Second

Code block:
.. code-block:: python

   def my_function():
       return "Hello"

Note boxes:
.. note::
   This is a note

.. warning::
   This is a warning

Links:
`Link text <http://example.com>`_

Internal references:
:doc:`other_page`
:ref:`section-label`
```

### 2. Document Each Module Consistently

Create these files for each module:

**overview.rst** - What the module does, key features
**installation.rst** - Installation instructions, dependencies
**configuration.rst** - Settings, configuration options
**user_guide.rst** - How to use the module (step-by-step)
**technical.rst** - Technical details, models, fields, methods

### 3. Include Screenshots

```rst
.. image:: /_static/images/module_name/screenshot.png
   :alt: Alternative text
   :align: center
   :width: 600px
```

### 4. Document Code with Docstrings

If you want to auto-generate API documentation:

```python
class MyModel(models.Model):
    """Brief description of the model.
    
    Longer description with more details about what this model does
    and how it's used in the system.
    """
    _name = 'my.model'
    _description = 'My Model Description'
    
    name = fields.Char(
        string='Name',
        required=True,
        help='The name of the record'
    )
    
    def my_method(self):
        """Brief description of the method.
        
        Detailed explanation of what the method does.
        
        :return: Description of return value
        :rtype: type
        :raises ValueError: When invalid input is provided
        """
        pass
```

Then in your RST file:

```rst
API Reference
=============

.. automodule:: your_module_name.models.my_model
   :members:
   :undoc-members:
   :show-inheritance:
```

## Advanced Features

### 1. Version Switcher

To support multiple Odoo versions:

```python
# In conf.py
html_context = {
    'versions': [
        ('17.0', '/17.0/'),
        ('16.0', '/16.0/'),
        ('15.0', '/15.0/'),
    ],
    'current_version': version,
}
```

### 2. Custom CSS

**source/_static/css/custom.css:**
```css
/* Company branding */
.wy-side-nav-search {
    background-color: #714B67;
}

/* Custom colors */
.wy-nav-content {
    max-width: 1200px;
}
```

### 3. PDF Generation

```bash
# Install LaTeX (required for PDF)
# Ubuntu/Debian: sudo apt-get install texlive-full
# macOS: brew install mactex

# Build PDF
make latexpdf
```

### 4. Auto-rebuild on Changes

```bash
# Install sphinx-autobuild
pip install sphinx-autobuild

# Run live server (auto-rebuilds on file changes)
sphinx-autobuild source build/html --open-browser
```

## Hosting Your Documentation

### Option 1: GitHub Pages

```bash
# Install ghp-import
pip install ghp-import

# Build and push to GitHub Pages
make html
ghp-import -n -p -f build/html
```

### Option 2: Read the Docs

1. Push your documentation to GitHub
2. Go to https://readthedocs.org
3. Import your repository
4. Configure build settings

### Option 3: Self-hosted

Deploy the `build/html` directory to any web server:

```bash
# Example with nginx
sudo cp -r build/html/* /var/www/html/docs/
```

## Git Integration

**.gitignore:**
```
# Sphinx build
build/
_build/

# Python
.venv/
venv/
*.pyc
__pycache__/

# IDE
.vscode/
.idea/
```

**README.md:**
```markdown
# My Odoo Modules Documentation

Documentation for our custom Odoo modules.

## Building Locally

1. Install dependencies: `pip install -r requirements.txt`
2. Build: `make html`
3. View: Open `build/html/index.html`

## Contributing

Please follow the documentation guidelines when adding new content.
```

## Maintenance Tips

1. **Keep it updated**: Update docs when you update modules
2. **Use version control**: Commit documentation changes with code
3. **Review regularly**: Check for outdated information
4. **Get feedback**: Have users review documentation
5. **Automate**: Set up CI/CD to build docs automatically

## Next Steps

1. Create your directory structure
2. Configure Sphinx with your settings
3. Write documentation for your first module
4. Build and review locally
5. Set up version control
6. Deploy to hosting platform

## Resources

- Sphinx Documentation: https://www.sphinx-doc.org/
- reStructuredText Primer: https://www.sphinx-doc.org/en/master/usage/restructuredtext/basics.html
- Read the Docs: https://docs.readthedocs.io/
- Odoo Documentation Style: https://github.com/odoo/documentation

---

**Need help?** This guide covers the basics. For advanced customization, refer to the Sphinx documentation or the official Odoo documentation repository structure.
