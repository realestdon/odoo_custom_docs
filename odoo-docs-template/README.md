# Odoo Custom Modules Documentation

Professional documentation template for Odoo custom modules, based on the official Odoo documentation structure.

## Quick Start

### Prerequisites

- Python 3.10 or higher
- pip (Python package manager)

### Installation

1. **Clone or download this template**

```bash
git clone https://github.com/yourcompany/odoo-docs-template.git
cd odoo-docs-template
```

2. **Create a virtual environment**

```bash
# Linux/Mac
python3 -m venv .venv
source .venv/bin/activate

# Windows (PowerShell)
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

3. **Install dependencies**

```bash
pip install -r requirements.txt
```

4. **Build the documentation**

```bash
make html
```

5. **View the documentation**

Open `build/html/index.html` in your web browser.

## Project Structure

```
odoo-docs-template/
├── source/                    # Documentation source files
│   ├── _static/              # Static assets (CSS, images)
│   ├── _templates/           # Custom templates
│   ├── modules/              # Module documentation
│   │   └── sample_module/    # Example module docs
│   ├── developer/            # Developer documentation
│   ├── user/                 # User guides
│   ├── conf.py               # Sphinx configuration
│   └── index.rst             # Main documentation page
├── build/                    # Generated documentation (created on build)
├── requirements.txt          # Python dependencies
├── Makefile                  # Build commands
└── README.md                 # This file
```

## Adding Your Modules

### Option 1: Use the Sample Module Template

1. Copy the `source/modules/sample_module/` directory
2. Rename it to your module name
3. Update the content in each file
4. Add it to `source/modules/index.rst`

### Option 2: Create from Scratch

1. Create a new directory in `source/modules/`
2. Create these files:
   - `index.rst` - Module overview
   - `overview.rst` - Detailed description
   - `installation.rst` - Installation guide
   - `configuration.rst` - Configuration options
   - `user_guide.rst` - User instructions
   - `technical.rst` - Technical documentation

3. Add your module to `source/modules/index.rst`:

```rst
.. toctree::
   :maxdepth: 2

   sample_module/index
   your_module/index  # Add this line
```

## Customization

### Change Theme Colors

Edit `source/_static/css/custom.css`:

```css
:root {
    --primary-color: #714B67;    /* Your primary color */
    --secondary-color: #875A7B;  /* Your secondary color */
    --accent-color: #00A09D;     /* Your accent color */
}
```

### Update Project Information

Edit `source/conf.py`:

```python
project = 'Your Project Name'
copyright = '2024, Your Company'
author = 'Your Name'
```

### Add Your Logo

1. Add your logo to `source/_static/images/logo.png`
2. Update `source/conf.py`:

```python
html_logo = '_static/images/logo.png'
```

## Build Commands

```bash
# Build HTML documentation
make html

# Build PDF (requires LaTeX)
make latexpdf

# Clean build files
make clean

# Live preview with auto-rebuild
sphinx-autobuild source build/html
```

## Writing Documentation

### reStructuredText Basics

```rst
Title
=====

Section
-------

Subsection
~~~~~~~~~~

**Bold text**
*Italic text*
``Code or command``

* Bullet point
* Another point

1. Numbered list
2. Second item

.. note::
   This is a note

.. warning::
   This is a warning

.. code-block:: python

   def hello():
       print("Hello, World!")
```

### Cross-References

```rst
:doc:`other_page`                    # Link to other page
:ref:`section-label`                 # Link to section
:doc:`/modules/sample_module/index`  # Absolute path
```

### Including Code

```rst
.. code-block:: python
   :linenos:
   :emphasize-lines: 3,4

   class MyModel(models.Model):
       _name = 'my.model'
       
       name = fields.Char('Name')
```

### Including Images

```rst
.. image:: /_static/images/screenshot.png
   :alt: Alternative text
   :align: center
   :width: 600px
```

## Deployment

### GitHub Pages

```bash
pip install ghp-import
make html
ghp-import -n -p -f build/html
```

Your documentation will be available at: `https://yourusername.github.io/your-repo/`

### Read the Docs

1. Push your documentation to GitHub
2. Go to [Read the Docs](https://readthedocs.org)
3. Import your repository
4. Configure build settings

### Self-Hosted

Copy the `build/html` directory to your web server:

```bash
scp -r build/html/* user@server:/var/www/html/docs/
```

## Tips and Best Practices

1. **Keep it Updated**: Update docs when you update code
2. **Use Examples**: Include code examples and screenshots
3. **Be Consistent**: Follow the same structure for all modules
4. **Test Links**: Verify all internal links work
5. **Version Control**: Commit documentation with code changes

## Troubleshooting

### Build Errors

**"Command not found: sphinx-build"**
- Ensure virtual environment is activated
- Run `pip install -r requirements.txt`

**"WARNING: document isn't included in any toctree"**
- Add the document to a toctree directive
- Or add `:orphan:` at the top of the file

### Rendering Issues

**Images not showing**
- Check image path is correct
- Ensure image file exists
- Use absolute paths: `/_static/images/...`

**Styles not applying**
- Clear browser cache
- Rebuild with `make clean html`
- Check CSS file is referenced in conf.py

## Resources

- [Sphinx Documentation](https://www.sphinx-doc.org/)
- [reStructuredText Primer](https://www.sphinx-doc.org/en/master/usage/restructuredtext/basics.html)
- [Read the Docs](https://docs.readthedocs.io/)
- [Odoo Documentation](https://www.odoo.com/documentation/)

## Support

For questions or issues:
- Create an issue on GitHub
- Contact: dev@yourcompany.com

## License

This template is provided under the MIT License. Documentation content should be licensed according to your organization's policies.

---

**Happy Documenting!** 📚
