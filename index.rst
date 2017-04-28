..
  Technote content.

  See https://developer.lsst.io/docs/rst_styleguide.html
  for a guide to reStructuredText writing.

  Do not put the title, authors or other metadata in this document;
  those are automatically added.

  Use the following syntax for sections:

  Sections
  ========

  and

  Subsections
  -----------

  and

  Subsubsections
  ^^^^^^^^^^^^^^

  To add images, add the image file (png, svg or jpeg preferred) to the
  _static/ directory. The reST syntax for adding the image is

  .. figure:: /_static/filename.ext
     :name: fig-label
     :target: http://target.link/url

     Caption text.

   Run: ``make html`` and ``open _build/html/index.html`` to preview your work.
   See the README at https://github.com/lsst-sqre/lsst-technote-bootstrap or
   this repo's README for more info.

   Feel free to delete this instructional comment.

:tocdepth: 1

.. Please do not modify tocdepth; will be fixed when a new Sphinx theme is shipped.

.. sectnum::

.. Add content below. Do not include the document title.

.. note::

   **This technote is not yet published.**

   A frequently updated note on SQuaRE’s JupyterLab prototyping

SQuaRE’s JupyterLab Demo
========================

Glossary
--------

-  JupyterLab is an extension of the Jypyter notebook platform that provides an extensible workspace in which users build custom interactive science workflow environments.
-  JupyterHub is a server for spawning Jupyter notebooks and now JupyterLab environments
-  Kubernetes is a container orchestration platform.
-  GKE (Google Container Engine) is a cloud-hosted kubernetes service.
-  SP: A DM unit of work; there are 7.5 SPs in a typical developer week.


Timeline Overview
-----------------
2017-03:
  In the S17-B cycle (March-May 2017) I allocated a timebox of 30 story points (4 person-weeks) to get SQuaRE familiar with the JupyterLab environment. JupyterLab is still in alpha but after talking extensively to one of the leaders of the project (Brian Granger) in February we felt the platform was mature enough to try prototyping with and the seemingly best techological target for the Science Platform. This work is covered under the SQuaRE epic `DM-9984 <https://jira.lsstcorp.org/browse/DM-9984>`__.

2017-04-13:
  At this point (2017-04-13) Adam Thornton, who is SQuaRE’s point engineer for Jupyter activities, has spent 19 SPs on this epic. Our technical target was to produce a JupyterLab environment provisioned by the automatic weekly build of a Docker container distribution of the LSST stack (lsst\_distrib). Allowances were made regarding JupyterLab’s alpha status. Our overall goal was to give ourselves a JupyterLab environment where we could prototype Science Platform user services (e.g. Notebooks as documentation) and verification activities (Notebook verification reports).

SQuaRE Technical Status
-----------------------

In fact, we had a very positive experience. At this point we have:

-  An experimental sandbox at jupyterlabdemo.lsst.codes (*not* a stable service; frequently redeployed with no notice; functionality subject to change).
-  A Dockerfile for installing JupyterHub and JupyterLab components onto our LSST stack container and exposing the stack kernel to Jupyter.
-  A Kubernetes configuration to deploy this service on the GKE cluster.
-  Our deployment involves JupyterHub spawning a single JupyterLab process per user in SQuaRE’s container.
-  Authentication to this service via SQuaRE’s usual “shim” authentication mechanism that uses Github organization membership for OAuth authentication (in operations this shim will be replaced by the NCSA authentication service which is not currently available to us).
-  We do not currently have persistent storage configured for this service, which we would have to provide if we were to use the GKE deployment as a developer service.


.. figure:: /_static/jupyterlab.png
	:name: fig-arch



SQuaRE’s next step
------------------

Since we (unexpectedly) have SPs still available in this epic, we intend to investigate scaling of this service. Our strawman approach is to accomplish this by having JupyterHub spawning multiple Kubernetes pods. Each pod would have a JupyterLab process running inside its own container. I regard scaling as an important area of technical risk to be addressed before committing to this technology (unlike, say, persistent storage, which is a solved problem that just needs implementation).

