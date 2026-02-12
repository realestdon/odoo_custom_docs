# Odoo Documentation Quick Reference

## Essential reStructuredText Syntax

### Headers
```rst
Main Title
==========

Chapter
-------

Section
~~~~~~~

Subsection
""""""""""
```

### Text Formatting
```rst
**Bold text**
*Italic text*
``Code or literal``
```

### Lists
```rst
Bullet list:
* Item 1
* Item 2

Numbered list:
1. First
2. Second

Definition list:
Term
    Definition of the term
```

### Code Blocks
```rst
.. code-block:: python
   :linenos:
   :emphasize-lines: 2,3

   def my_function():
       print("Hello")
       return True
```

### Links
```rst
`External link <https://example.com>`_
:doc:`other_page`
:ref:`section-label`
:doc:`/modules/sample_module/index`
```

### Images
```rst
.. image:: /_static/images/screenshot.png
   :alt: Alternative text
   :align: center
   :width: 600px
```

### Admonitions
```rst
.. note::
   This is a note

.. warning::
   This is a warning

.. tip::
   This is a helpful tip

.. important::
   This is important

.. danger::
   This is dangerous
```

### Tables
```rst
.. list-table::
   :header-rows: 1
   :widths: 30 70

   * - Header 1
     - Header 2
   * - Row 1, Col 1
     - Row 1, Col 2
```

### Cross-References
```rst
.. _my-label:

Section Title
-------------

Reference: :ref:`my-label`
```

## Common Build Commands

```bash
# Build HTML
make html

# Build and watch for changes
sphinx-autobuild source build/html

# Clean build
make clean

# Build PDF
make latexpdf

# Test build
make linkcheck
```

## Module Documentation Template

### Minimum Files Required

```
module_name/
├── index.rst           # Module overview
├── installation.rst    # How to install
├── configuration.rst   # Settings and config
├── user_guide.rst      # How to use
└── technical.rst       # Developer docs
```

### index.rst Template

```rst
Module Name
===========

Brief description.

.. toctree::
   :maxdepth: 2

   overview
   installation
   configuration
   user_guide
   technical

Quick Info
----------

:Module Name: module_name
:Version: 17.0.1.0.0
:Category: Custom
:Dependencies: base, sale
```

## Field Documentation Pattern

```python
field_name = fields.Type(
    string='Display Name',
    required=True,
    help='Help text shown to users',
    tracking=True,  # Track in chatter
)
```

## Method Documentation Pattern

```python
def my_method(self, param1, param2=None):
    """Brief description.
    
    Longer description with details about what
    the method does and when to use it.
    
    Args:
        param1 (str): Description of param1
        param2 (int, optional): Description of param2
    
    Returns:
        bool: True if successful, False otherwise
    
    Raises:
        ValidationError: When validation fails
    
    Example:
        >>> record.my_method('test', param2=5)
        True
    """
    pass
```

## Odoo-Specific Markup

### Model Reference
```rst
.. py:class:: sample.module

   Brief class description.

   .. py:method:: action_submit()
   
      Submit record for approval.
      
      :raises ValidationError: If not in draft state
      :return: True
      :rtype: bool
```

### Workflow Diagrams
```rst
.. graphviz::

   digraph workflow {
      rankdir=LR;
      node [shape=box];
      
      "Draft" -> "Submitted";
      "Submitted" -> "Approved";
      "Approved" -> "Done";
   }
```

## File Locations

### User-Facing Content
- `/source/_static/images/` - Screenshots and images
- `/source/user/` - User guides
- `/source/modules/` - Module documentation

### Developer Content
- `/source/developer/` - Technical documentation
- `/source/_static/css/` - Custom styles

### Configuration
- `/source/conf.py` - Sphinx configuration
- `/requirements.txt` - Python dependencies
- `/Makefile` - Build commands

## Common Patterns

### Version Notice
```rst
.. versionadded:: 17.0.2.0.0
   This feature was added in version 17.0.2.0.0

.. versionchanged:: 17.0.3.0.0
   Behavior changed to...

.. deprecated:: 17.0.4.0.0
   Use :func:`new_function` instead
```

### TODO Items
```rst
.. todo::
   Add more examples here
```

### Including Files
```rst
.. literalinclude:: /../models/sample_model.py
   :language: python
   :lines: 10-30
   :emphasize-lines: 5,6
```

## Troubleshooting

### Build Warnings
```
WARNING: document isn't included in any toctree
```
**Fix:** Add document to a toctree or add `:orphan:` at top

```
WARNING: undefined label: 'my-label'
```
**Fix:** Define label with `.. _my-label:` or check spelling

### Missing Images
**Fix:** Use absolute paths: `/_static/images/file.png`

### Broken Links
**Fix:** Run `make linkcheck` to find broken links

## Quick Tips

1. **Preview as you write**: `sphinx-autobuild source build/html`
2. **Use includes**: Don't repeat content, use `.. include::`
3. **External links**: Use `_blank` with `.. _link: https://...`
4. **Cross-reference**: Use `:doc:` and `:ref:` instead of URLs
5. **Semantic markup**: Use proper heading levels
6. **Keep it DRY**: Use substitutions for repeated content

## Keyboard Shortcuts

In VSCode with reStructuredText extension:
- `Ctrl+Shift+R` - Preview
- `Ctrl+K Ctrl+R` - Open preview to side

---

Keep this reference handy while writing documentation!
