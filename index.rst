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

This is an "evolving strawman design" document based on prototyping activities undertaken by SQuaRE to support Science Platform development.

Glossary
--------

-  JupyterLab is an extension of the Jypyter notebook platform that provides an extensible workspace in which users build custom interactive science workflow environments.
-  JupyterHub is a server for spawning Jupyter notebooks and now JupyterLab environments
-  Kubernetes is a container orchestration platform.
-  GKE (Google Container Engine) is a cloud-hosted kubernetes service.
-  Story Point: A DM unit of work; there are 7.5 SPs in a typical developer week.


SQuaRE Technical Status
-----------------------

At the most recent point in the timeline above, we have:

-  An experimental sandbox at jupyterlabdemo.lsst.codes (*not* a stable service; frequently redeployed with no notice; functionality subject to change).
-  A Dockerfile for installing JupyterHub and JupyterLab components onto our LSST stack container and exposing the stack kernel to Jupyter.
-  A Kubernetes configuration to deploy this service on the GKE cluster.
-  Our deployment involves JupyterHub spawning Jupyterlabs in individual pods based on a SQuaRE’s CI-built Docker container dinstribution of the stack.
-  Authentication to this service via SQuaRE’s usual “shim” authentication mechanism that uses Github organization membership for OAuth authentication
- Consistent Unix uid/gid mapping for users using Github as a shim (your user id is your github id, your groups are the orgs you belong to). 
- We have a modest amount of persistent storage via an NFS pod to allow alpha-testers to keep notebooks around in between redeployments.
  

.. figure:: /_static/jupyterlab.png
	:name: fig-arch


Timeline Overview
-----------------
2017-03:
  In the S17-B cycle (March-May 2017) I allocated a timebox of 30 story points (4 person-weeks) to get SQuaRE familiar with the JupyterLab environment. JupyterLab is still in alpha but after talking extensively to one of the leaders of the project (Brian Granger) in February we felt the platform was mature enough to try prototyping with and the seemingly best techological target for the Science Platform. This work is covered under the SQuaRE epic `DM-9984 <https://jira.lsstcorp.org/browse/DM-9984>`__.

2017-04-13:
  At this point (2017-04-13) Adam Thornton, who is SQuaRE’s point engineer for Jupyter activities, has spent 19 SPs on this epic. Our technical target was to produce a JupyterLab environment provisioned by the automatic weekly build of a Docker container distribution of the LSST stack (lsst\_distrib). Allowances were made regarding JupyterLab’s alpha status. Our overall goal was to give ourselves a JupyterLab environment where we could prototype Science Platform user services (e.g. Notebooks as documentation) and verification activities (Notebook verification reports).

2017-04-27:
  We are closing the first DM-9984 after 30 SPs and opening DM-10387 to continue. We now have a workable deployment for alpha-testers (only lacking persistant storage) for (relative) stability and an architecture that includes a kubernetes deployment of Hub spinning off Labs in individual pods (for scaling), shim OAuth, shim Unix uid/gid, and a kernel based on a Docker container automatically produced by CI. Oh and this technote. KSK out of sheer enthusiasm makes a set of killer demo videos based on a stack notebook and DS9 visualisation.

2017-04-28:
  We are now producing Docker Python 3 containers from CI and deploying them to our Lab. The KSK notebook ran great. Investigating a bug in the auth context for spun pods.

2017-05-02:
  The "missing icons" and "missing Hub menu" issue turned out to be an issue mixing-and-matching node.js modules across packages (which is a known issue upstream and the plugin system is being revisited as a result). 
  
2017-05-09:
  We have added a modest amount of persistent storage (NSF pod) to avoid inconveniencing alpha-testers with perennially re-uploading their notebooks and data sets in between redeploys. We have also added a log out button and improved server shutdown.

2017-05-09:
  The demo instance is up to date with the features in the sandbox instance. 
		   
Coming Soon-ish
-------------------

- We're looking into how we can provide a multiple choice of
  containers to users (eg. with different versions of the stack). This
  does mean shifting the kernel selection step up into Hub (right now
  it's at the Lab pod level).

- Investigate addressing usability concerns (github-based workflows)


Repositories
------------

Code repos for system:



https://github.com/lsst-sqre/jupyterlabdemo :
	(JupyterLab container provisioning and Kubernetes cofig)
https://github.com/lsst-sqre/k8s-jupyterlabdemo-nginx :
	(proxy and Kubernetes config)
https://github.com/lsst-sqre/ghowlauth :
	(authenticator)

 
