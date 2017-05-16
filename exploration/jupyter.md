# Using Jupyter

The [Jupyter Notebook](http://jupyter.org) is a web application that allows you to create and share documents that contain live code, equations, visualizations and explanatory text. A notebook document is associated with a "computation engine", called notebook kernel. Kernels for many other languages exist (see [official kernels](http://jupyter.readthedocs.io/en/latest/#kernels)).

Jupyter is integrated with PNDA platform as a standard service with three pre-configured kernels by default, primarily python kernels:

* Python2 kernel
* Python3 kernel
* PySpark (Python2) kernel

These kernels are installed along with 3rd-party libraries such as [numpy](http://www.numpy.org/) and [matplotlib](http://matplotlib.org/), which allow you to run scientific computing and visualization against your data. In addition to those fundamental scientific computing facilities, the PySpark (Python2) kernel also integrates PNDA [platform libaries](https://github.com/pndaproject/platform-libraries) that provides easy-to-use utilities for large-scale big data analytics using Spark. Such utilities include loading and decoding [PNDA Avro datasets](../producer/data-preparation.md) as an RDD, and submit MapR jobs to Hadoop cluster via YARN for data pre-processing and data transformation, and data query.

For those who would like to try Jupyter without pre-existed data, a data-generation tool is also provided that allows you to generate test datasets. Follow the instructions from [the example Jupyter notebook](https://github.com/pndaproject/example-applications/tree/master/jupyter-notebooks) to connect to PNDA jupyter node and start using Jupyter. Detailed explanations of the example Jupyter notebook cells can be found in the [Lab](lab.md).
