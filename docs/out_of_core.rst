Out of Core in Modin (experimental)
===================================

If you are working with very large files or would like to exceed your memory, you may
change the primary location of the DataFrame. If you would like to exceed memory, you
can use your disk as an overflow for the memory. This API is experimental in the context
of Modin. Please let us know what you think!

Install Modin out of core
-------------------------

As we mentioned in the `installation page`_, we have set up a select dependency set for
users who want to use Modin out of core. It can be installed with pip:

.. code-block:: bash

  pip install "modin[out_of_core]"

This will ensure that you have all of the required dependencies for Modin to run out of
core.

Starting Modin with out of core enabled
---------------------------------------

Out of core is detected from an environment variable set in bash.

.. code-block:: bash

   export MODIN_OUT_OF_CORE=true

We also set up a way to tell Modin how much memory you'd like to use. Currently, this
only accepts the number of bytes. This can only exceed your memory if you have enabled
``MODIN_OUT_OF_CORE``.

**Warning: Make sure you have enough space in your disk for however many bytes you**
**request for your DataFrame**

Here is how you set ``MODIN_MEMORY``:

.. code-block:: bash

  export MODIN_MEMORY=200000000000 # Set the number of bytes to 200GB

This limits the amount of memory that Modin can use.

Running an example with out of core
-----------------------------------

Before you run this, please make sure you follow the instructions listed above.

.. code-block:: python

  import modin.pandas as pd
  import numpy as np
  frame_data = np.random.randint(0, 100, size=(2**20, 2**8)) # 2GB each
  df = pd.DataFrame(frame_data).add_prefix("col")
  big_df = pd.concat([df for _ in range(20)]) # 20x2GB frames
  print(big_df)
  nan_big_df = big_df.isna() # The performance here represents a simple map
  print(big_df.apply(lambda col: col.sum())) # apply along an entire axis (columns in this case)

This example creates a 40GB DataFrame from 20 identical 2GB DataFrames and performs
various operations on them. Feel free to play around with this code and let us know what
you think!

.. _`installation page`: installation.html