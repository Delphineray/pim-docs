General information on rule format
==================================

**This cookbook is about a feature only provided in the Enterprise Edition.**

File Structure
--------------

Enrichment rules are defined in YAML. The file extension has to be ".yml". Indentation is mandatory within the
file and has to follow the YAML format strictly. To use a rule it has to be imported in the PIM.

This file starts with "rules" root element, which contains the list of enrichment rules. This document is about this
list. Each rule is referred to by a code and can contain a list of conditions or actions. Actions are executed if all
conditions are verified or if no action is defined.

.. code-block:: yaml

    # Example of a file with 2 rules
    rules:
        camera_set_canon_brand:
            priority: 0
            conditions:
                - field: family.code
                operator: IN
                value:
                    - camcorders
                - field: name
                operator: CONTAINS
                value: Canon
            actions:
                - type: set_value
                field: camera_brand
                value: canon_brand
        camera_copy_name_to_model:
            priority: 0
            conditions:
                - field: family.code
                operator: IN
                value:
                    - camcorders
            actions:
                - type: copy_value
                from_field: name
                to_field: camera_model_name

Enrichment Rule Structure
-------------------------

Structure’s elements which define a rule are :
 - rule code (dynamic)
 - priority*
 - conditions
 - field
 - locale*
 - scope*
 - operator
 - value
 - actions
 - type

An enrichment rule is structured as follows :

.. code-block:: yaml

    [free rule code]:
        priority​*:
        conditions:
            - field:
            locale​*:
            scope​*:
            operator:
            value:
        actions:
            - type:
            [Diverse elements according to the action]

Elements with * are optionals.

**Dashes** - ​before element field and after each element contained in value part are mandatory.

**Colon** : ​mandatory after each structure element.

.. warning::

    Rules code choice is up to you, thus it has to contain only alphanumeric characters, underscore and dashes only
    accepted and 255 characters maximum.

A priority can be given to a rule. Priority will be considered for rules execution order. Without any given
priority, rule has a zero-priority. The higher the priority, the sooner the rule will be executed.
Therefore, a rule with 90 priority will be executed before rules with a 0 priority. If no rule has defined priority,
they will be executed in a "technical" order. (database reading order)

Action’s conditions can be applied on localizable and scopable values. In this case, it has
to be specified using locale and scope elements.

Enrichment Rule Definition
--------------------------

Available Actions List
++++++++++++++++++++++

Copy
____

This action enables to copy an attribute value into another.

.. warning::

    Source and target attributes have to be of the same type. If source attribute is empty, the value "empty" will also
    be copied.

Two parameters are required and four other are optional:
 - from_field: code of the attribute to be copied
 - from_locale : locale code of the value to be copied (optional).
 - from_scope : channel code of the value to be copied (optional).
 - to_field: attribute code the value will be copied into
 - to_locale: locale code the value will be copied into (optional)
 - to_scope: channel code the value will be copied into (optional).

.. tip::

    For instance, to copy description from en_US print channel to the en_US description e-commerce channel, action will
    be defined as follows:

        .. code-block:: yaml

            actions:
                - type:      copy
                from_field:  description
                from_locale: en_US
                from_scope:  print
                to_field:    description
                to_locale:   en_US
                to_scope:    ecommerce

Set
___

This action enables assigning values to an attribute.

Two parameters are required, two other are optional.
 - field : attribute code.
 - locale : local code for which value is assigned (optional).
 - scope : channel code for which value is assigned (optional).
 - value : attribute value

.. tip::

    For instance, to set the value "My very new description for purple tshirt" to description attribute in en_US locale,
    for ecommerce channel, the action will be as follows:

    .. code-block:: yaml

        actions:
            ­ type: set
            field:  description
            locale: en_US
            scope:  ecommerce
            value:  My very new description for purple tshirt

Add
___

This action allows to add values to a multiselect, a category or a collection.

Two parameters are required, two others are optional.
 - field : attribute code.
 - locale : local code for which value is assigned (optional).
 - scope : channel code for which value is assigned (optional).
 - value : attribute value

.. tip::

    For instance, adding category "t-shirts" action will be as follows:

    .. code-block:: yaml

        actions:
            - type: add
            field:  category
            value:
                - t-shirts

Fields
++++++

Created
_______
+--------------+----------------------+
| Operator     | - =                  |
|              | - >                  |
|              | - <                  |
|              | - BETWEEN            |
|              | - NOT BETWEEN        |
|              | - EMPTY              |
+--------------+----------------------+
| Value        | dates format :       |
|              | mm/dd/yyyy. If       |
|              | operator is EMPTY,   |
|              | values information   |
|              | are ignored          |
+--------------+----------------------+
| Example      | .. code-block:: yaml |
|              |                      |
|              |   field: created     |
|              |   operator: =        |
|              |   value:             |
|              |    ­ - 01/23/2015    |
+--------------+----------------------+

Updated
_______
+--------------+----------------------+
| Operator     | - =                  |
|              | - >                  |
|              | - <                  |
|              | - BETWEEN            |
|              | - NOT BETWEEN        |
|              | - EMPTY              |
+--------------+----------------------+
| Value        | dates format :       |
|              | mm/dd/yyyy. If       |
|              | operator is EMPTY,   |
|              | values information   |
|              | are ignored          |
|              |                      |
|              |                      |
+--------------+----------------------+
| Example      | .. code-block:: yaml |
|              |                      |
|              |   field: updated     |
|              |   operator: =        |
|              |   value:             |
|              |    - ­ 01/23/2015    |
+--------------+----------------------+

Enabled
_______
+--------------+----------------------+
| Operator     | - =                  |
+--------------+----------------------+
| Value        | activated=> 1,       |
|              | deactived => 0.      |
+--------------+----------------------+
| Example      | .. code-block:: yaml |
|              |                      |
|              |   field: enabled     |
|              |   operator: =        |
|              |   value:             |
|              |    - ­ 0             |
+--------------+----------------------+

Completeness
____________
+--------------+-----------------------+
| Operator     | - =                   |
|              | - >                   |
|              | - <                   |
+--------------+-----------------------+
| Value        | Percentage.           |
|              | /!\ locale and scope  |
|              | are mandatory         |
+--------------+-----------------------+
| Example      | .. code-block:: yaml  |
|              |                       |
|              |   field: completeness |
|              |   locale: fr_FR       |
|              |   scope: print        |
|              |   operator: =         |
|              |   value:              |
|              |     - ­ 100%          |
+--------------+-----------------------+

Family
______
+--------------+------------------------+
| Operator     | - IN                   |
|              | - NOT IN               |
|              | - EMPTY                |
+--------------+------------------------+
| Value        | Family codes or ids.   |
|              | If operator is         |
|              | EMPTY, value           |
|              | information are        |
|              | ignored.               |
+--------------+------------------------+
| Example      | .. code-block:: yaml   |
|              |                        |
|              |   field: family.code   |
|              |   operator: IN         |
|              |   value:               |
|              |    -­ camcorders       |
|              |    - digital_cameras   |
+--------------+------------------------+


Groups
______
+--------------+-----------------------+
| Operator     | - IN                  |
|              | - NOT IN              |
|              | - EMPTY               |
+--------------+-----------------------+
| Value        | Groups codes or Ids.  |
|              | If operator is        |
|              | EMPTY, value          |
|              | information are       |
|              | ignored.              |
+--------------+-----------------------+
| Example      | .. code-block:: yaml  |
|              |                       |
|              |   field: groups.code  |
|              |   operator: IN        |
|              |   value:              |
|              |    -­ oro_tshirts     |
|              |    - akeneo_tshirts   |
+--------------+-----------------------+

Categories
__________
+--------------+--------------------------+
| Operator     | - IN                     |
|              | - NOT IN                 |
|              | - UNCLASSIFIED           |
|              | - IN OR UNCLASSIFIED     |
|              | - IN CHILDREN            |
|              | - NOT IN CHILDREN        |
+--------------+--------------------------+
| Value        | Categories codes or      |
|              | ids.                     |
+--------------+--------------------------+
| Example      | .. code-block:: yaml     |
|              |                          |
|              |   field: categories.code |
|              |   operator: IN           |
|              |   value:                 |
|              |    -­ C0056              |
|              |    - F677                |
+--------------+--------------------------+

Attribute Types
+++++++++++++++

Text / Textarea
_______________
+--------------+-------------------------+
| Operator     | - STARTS WITH           |
|              | - ENDS WITH             |
|              | - CONTAINS              |
|              | - DOES NOT CONTAINS     |
|              | - =                     |
|              | - EMPTY                 |
+--------------+-------------------------+
| Value        | Text, with or without   |
|              | quotation marks. if     |
|              | operator is empty,      |
|              | Values information      |
|              | are ignored.            |
+--------------+-------------------------+
| Example      | .. code-block:: yaml    |
|              |                         |
|              |   field: description    |
|              |   operator: CONTAIN     |
|              |   value:                |
|              |    -­ "Awesome product" |
+--------------+-------------------------+

Metric
______
+--------------+------------------------+
| Operator     | - <                    |
|              | - <=                   |
|              | - =                    |
|              | - >                    |
|              | - >=                   |
|              | - EMPTY                |
+--------------+------------------------+
| Value        | Numeric value and      |
|              | measure unity code.    |
|              | Dot "." is the decimal |
|              | separator No space     |
|              | between thousands. If  |
|              | operators is empty,    |
|              | values information     |
|              | are ignored.           |
+--------------+------------------------+
| Example      | .. code-block:: yaml   |
|              |                        |
|              |   field: weight        |
|              |   operator: =          |
|              |   value:               |
|              |    -­ data: 0.5        |
|              |    - unit: KILOGRAM    |
+--------------+------------------------+


Boolean
_______
+--------------+------------------------+
| Operator     | - =                    |
+--------------+------------------------+
| Value        | Yes => 1, No => 0      |
+--------------+------------------------+
| Example      | .. code-block:: yaml   |
|              |                        |
|              |   field: shippable_us  |
|              |   operator: =          |
|              |   value:               |
|              |    -­ 0                |
+--------------+------------------------+

Dropdown List
_____________
+--------------+------------------------+
| Operator     | - IN                   |
|              | - EMPTY                |
+--------------+------------------------+
| Value        | Option code. If        |
|              | operator is empty,     |
|              | values information     |
|              | are ignored.           |
+--------------+------------------------+
| Example      | .. code-block:: yaml   |
|              |                        |
|              |   field: size          |
|              |   operator: IN         |
|              |   value:               |
|              |    -­ xxl              |
+--------------+------------------------+


Multiselect List
________________
+--------------+------------------------+
| Operator     | - IN                   |
|              | - EMPTY                |
+--------------+------------------------+
| Value        | Option code. If        |
|              | operator is empty,     |
|              | values information     |
|              | are ignored.           |
+--------------+------------------------+
| Example      | .. code-block:: yaml   |
|              |                        |
|              |   field: material      |
|              |   operator: IN         |
|              |   value:               |
|              |    -­ GOLD             |
|              |    - LEATHER           |
+--------------+------------------------+

Number
______
+--------------+------------------------+
| Operator     | - <                    |
|              | - <=                   |
|              | - =                    |
|              | - >                    |
|              | - >=                   |
|              | - EMPTY                |
+--------------+------------------------+
| Value        | Number. If operator    |
|              | is empty, values       |
|              | information are        |
|              | ignored.               |
+--------------+------------------------+
| Example      | .. code-block:: yaml   |
|              |                        |
|              |   field: min_age       |
|              |   operator: =          |
|              |   value:               |
|              |    -­ 12               |
+--------------+------------------------+

Date
____
+--------------+------------------------+
| Operator     | - <                    |
|              | - =                    |
|              | - >                    |
|              | - BETWEEN              |
|              | - NOT BETWEEN          |
|              | - EMPTY                |
+--------------+------------------------+
| Value        | Format date :          |
|              | MM/DD/YYYY. If         |
|              | operator is empty,     |
|              | Values information     |
|              | are ignored.           |
+--------------+------------------------+
| Example      | .. code-block:: yaml   |
|              |                        |
|              |   field: fix_date      |
|              |   operator: >          |
|              |   value:               |
|              |    -­ 12/05/2016       |
+--------------+------------------------+

Price
_____
+--------------+------------------------+
| Operator     | - <                    |
|              | - <=                   |
|              | - =                    |
|              | - >                    |
|              | - >=                   |
|              | - EMPTY                |
+--------------+------------------------+
| Value        | Numeric value and      |
|              | currency code.         |
|              | Dot "." is the decimal |
|              | separator. No space    |
|              | between thousands.     |
|              | If operator is empty,  |
|              | values information     |
|              | are ignored.           |
+--------------+------------------------+
| Example      | .. code-block:: yaml   |
|              |                        |
|              |   field: basic_price   |
|              |   operator: <=         |
|              |   value:               |
|              |    -­ 12 EUR           |
+--------------+------------------------+

Picture or file
_______________
+--------------+---------------------------------+
| Operator     | - STARTS WITH                   |
|              | - ENDS WITH                     |
|              | - CONTAINS                      |
|              | - DOES NOT                      |
|              | - CONTAIN                       |
|              | - =                             |
|              | - EMPTY                         |
+--------------+---------------------------------+
| Value        | Text. If operator is            |
|              | empty, values                   |
|              | information are                 |
|              | ignored.                        |
+--------------+---------------------------------+
| Example      | .. code-block:: yaml            |
|              |                                 |
|              |   field: small_image            |
|              |   operator: CONTAIN             |
|              |   value:                        |
|              |    - filePath : ../../../       |
|              |    src/PimEnterprise/Bundle/    |
|              |    InstallerBundle/Resources/   |
|              |    fixtures/icecat_demo/images/ |
|              |    AKNTS_PB.jpg                 |
|              |    - originalFilename: akeneo   |
+--------------+---------------------------------+
