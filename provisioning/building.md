# Building PNDA

PNDA has a number of components and these need to be built prior to provisioning a cluster in your target environment.

There are two types of component - those built from PNDA source code and those built from upstream projects.

Once the PNDA components have been built, they must be staged in such a way that they are accessible via HTTP. Copying them to a the document root of a simple HTTP server is one way of accomplishing this.

For full instructions on building PNDA and some advice on staging the built components, please refer the [repository notes](https://github.com/pndaproject/pnda/blob/master/build/README.md).
