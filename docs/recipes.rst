.. _recipes:

Recipes
=======


Converting App Argument to `Integer`
------------------------------------

All arguments passed to the application in the ApplicationCall transaction are converted to bytes.
If you passed an integer and want to use it as integer in the contract, you have to convert it to integer using
`btoi <https://developer.algorand.org/docs/get-details/dapps/avm/teal/opcodes/#btoi>`_ opcode.

.. literalinclude:: ../examples/recipes/btoi.tl
   :language: tealish
   :emphasize-lines: 2
   :linenos:

Opting in the application to an asset
-------------------------------------

.. literalinclude:: ../examples/recipes/application_asset_optin.tl
   :language: tealish
   :emphasize-lines: 7-14
   :linenos:


Calling another application with an inner transaction
-----------------------------------------------------

.. literalinclude:: ../examples/recipes/inner_app_call.tl
   :language: tealish
   :linenos:


Handling Integer Overflow
-------------------------

Converting `integers` to `big-endian unsigned integers` and using
`byte arithmetics <https://developer.algorand.org/docs/get-details/dapps/avm/teal/specification/#byte-array-manipulation>`_
allows handling integers up to 512 bits without overflow. Note that these ``b*`` and others have a higher `opcode cost <https://developer.algorand.org/docs/get-details/dapps/avm/teal/opcodes/#b>`_ than the normal integer operators.


.. literalinclude:: ../examples/recipes/overflow.tl
   :language: tealish
   :emphasize-lines: 7-9
   :linenos:

Optimizing the generated TEAL code partially
--------------------------------------------

Teal allows using raw TEAL directly, see :ref:`inline_teal`.
Decreasing the size and computational cost using TEAL and custom stack management is possible.

Reformatting the Tealish code may result a `small` gain. Reducing the variable usage help you ascetically if you are on the limit of budget cost.
Variable assignment generates `store,` and usages generates `load` opcodes. So, you can remove the variables used one time.

.. literalinclude:: ../examples/recipes/optimization_a.tl
   :language: tealish
   :emphasize-lines: 1-5
   :linenos:

.. literalinclude:: ../examples/recipes/optimization_b.tl
   :language: tealish
   :emphasize-lines: 1
   :linenos:


Increasing the budget
---------------------

The opcode budget of an application call is 700. Most TEAL Opcodes have a cost of ``1`` but some are much higher. \
For example  `b+ <https://developer.algorand.org/docs/get-details/dapps/avm/teal/opcodes/#b>`_ is 20 times more costly than ``b``
If the transaction group has multiple application calls, the budget is pooled. The details are explained in the
`Algorand Developer Documentation
<https://developer.algorand.org/docs/get-details/dapps/avm/teal/#dynamic-operational-cost-of-teal-opcodes>`_.

Similar to `OpUp utility of PyTeal <https://pyteal.readthedocs.io/en/latest/opup.html>`_,
you can increase the cost budget by adding another app call using inner transactions. Each app call adds an extra 700 to the budget.

`increase_cost_budget` creates and deletes an app with single transactions. This is the most portable and efficient way to achieve the goal.

.. literalinclude:: ../examples/recipes/increase_cost_budget.tl
   :language: tealish
   :emphasize-lines: 2-8
   :linenos:

Testing & Debugging
-------------------

After compiling the Tealish code into TEAL, you can use all the testing, and
`debugging <https://developer.algorand.org/docs/get-details/dapps/smart-contracts/debugging/#create-publication-overlay>`_
tools that are compatible with TEAL.
Using `Sandbox <https://developer.algorand.org/docs/get-started/devenv/sandbox/#create-publication-overlay>`_ node
for `testing and developing <https://developer.algorand.org/docs/sdks/python/#create-publication-overlay>`_ as explained
on Algorand Developer Documentation is also an option.

Algojig
^^^^^^^

    " `AlgoJig <https://github.com/Hipo/algojig>`_ is a development and testing jig for Algorand.
    It allows developers to run transactions and write tests starting from a known Algorand ledger state.
    It is suitable for testing all kinds of transactions, including application calls and logic signatures."


`Counter Prize and Auction example contracts <https://github.com/tinymanorg/tealish/tree/main/examples>`_
implemented by Tealish contains unit-tests and they use `AlgoJig <https://github.com/Hipo/algojig>`_.

Example:

.. literalinclude:: ../examples/counter_prize/tests/tests.py
   :language: python
   :lines: 37-62
   :linenos:
