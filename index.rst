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

   A frequently updated note on SQuaRE’s JupyterLab prototyping

SQuaRE’s JupyterLab Environment
===============================

This is an "evolving strawman design" document based on prototyping
activities undertaken by SQuaRE to support Science Platform development. It is not yet baselined.


Glossary
--------

- JupyterLab is an extension of the Jupyter notebook platform that provides an extensible workspace in which users build custom interactive science workflow environments.
- JupyterHub is a server for spawning Jupyter notebooks and now JupyterLab environments.
- Kubernetes is a container orchestration platform.
- GKE (Google Container Engine) is a cloud-hosted kubernetes service.
- Story Point: A DM unit of work; there are 7.5 SPs in a typical developer week. 


SQuaRE Technical Status
-----------------------

At the most recent point in the timeline above, we have:

-  An experimental sandbox at nublado.lsst.codes (*not* a stable service; frequently redeployed with no notice; functionality subject to change).
-  An experimental instance at lsst-lsp-int.ncsa.illinois.edu (*not* a stable service; frequently redeployed with no notice; functionality subject to change).
-  A marginally-less-experimental instance at lsst-lsp-stable.ncsa.illinois.edu (best-effort for stability; some warning given prior to redeployment; functionality subject to change).
-  A Dockerfile for installing JupyterHub and JupyterLab components onto our LSST stack container and exposing the stack kernel to Jupyter.
-  A Kubernetes configuration to deploy this service on the GKE cluster.
-  An environment-driven or interactive process for deployment of a JupyterLab demo cluster.
-  Our deployment involves JupyterHub spawning JupyterLabs in individual pods based on a SQuaRE’s CI-built Docker container distribution of the stack.
-  Authentication to this service via SQuaRE’s usual “shim” authentication mechanism that uses GitHub organization membership for OAuth authentication
-  Authentication via OAuth2 to NCSA via the CILogon service.
-  Consistent Unix uid/gid mapping for users using GitHub as a shim (your user id is your github id, your groups are the orgs you belong to).
-  We have a modest amount of persistent storage via an NFS pod to allow alpha-testers to keep notebooks around in between redeployments.
-  A tool to do one-command deployment of a cluster; this tool allows customization either through environment variables, command-line flags, or by generating a set of Kubernetes configurations which are then available for manual editing before deployment.


.. figure:: /_static/jupyterlab_sp.png
	:name: fig-arch


Timeline Overview
-----------------

2017-03:
  In the S17-B cycle (March-May 2017) We allocated a timebox of 30 story points (4 person-weeks) to get SQuaRE familiar with the JupyterLab environment. JupyterLab is still in alpha but after talking extensively to one of the leaders of the project (Brian Granger) in February we felt the platform was mature enough to try prototyping with and the seemingly best techological target for the Science Platform. This work is covered under the SQuaRE epic `DM-9984 <https://jira.lsstcorp.org/browse/DM-9984>`__.

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

2017-06-15:
  We can provide a multiple choice of containers to users (eg. with different versions of the stack). This meant shifting the kernel selection step up into Hub (before it was at the Lab pod level).

2017-06-30:
  We turned on autoscaling as a test. Everything looks good. As threatened we removed the Python 2 containers now that we have multiple stack versions to test the selector with.

2017-07-07:
  We have upstreamed our first patch into JupyterLab :-) It's great to
  be giving back. Keeping up with the alpha updates is a bit of work
  right now, but well worth it.

2017-08-16:
  Another pull request, this time for OAuth2 and GitHub, to allow token caching, is approved and waiting for merge.  We now no longer need any repositories other than lsst-sqre/jupyterlabdemo and jupyterlab-savequit--our authenticator, spawner, and login handler subclasses are handled in JupyterHub configuration.

2017-12-13:
  Much work has happened since the last update.  A prepuller DaemonSet has been added to keep downloaded container images up-to-date, and the list presented at login time is automatically updated from the repository container set.  We have integration with NCSA OAuth2 via CILogon (although there is coordination required for UID/GID determination).  There is now an automated deployment process that can use either interactive prompts, a YAML document, or environment variables to drive the deployment.  A basic directory layout for LSST-provided notebooks has been agreed to.  A repository containing standard notebooks has been defined, and it is populated/refreshed for each container at login time.  Workshops and similar things will, for the time being, use GitHub organizations to access control.  All currently appropriate patches have been accepted upstream.

2018-02-26:
  The prepuller has been rewritten to not require mapping the Docker socket, which makes it able to run without privilege and to be independent of the underlying host OS.  Systems Engineering and EPO are now running minor variants of the Demo environment for their own needs.  JupyterLab Beta has been released and we have adopted it.  Bokeh accepted our PR to jupyterlab_bokeh, so that's another upstream project we are now contributing to.  We are on track to close about another 40 story points on this epic at the end of the month.

2019-03-20:
  Wow, where to start?  In no particular order, a non-exhaustive list:

  - "Nublado" is the internal name for the Hub/Lab/Kubernetes environment that makes up the Notebook Aspect of the LSST Science Platform.
  - The increasingly-inaccurately-named "jupyterlabdemo" has been retired in favor of "nublado."
  - User containers spawn in their own namespaces.
  - The user namespaces have individual resource quotas.
  - Support for authentication with pre-provided JWT headers has been added.
  - Experimental Dask support has been added for workload parallelization.
  - The user proxy and the Hub have been split into separate deployments, so a Hub restart has no effect at all on the user experience of someone with an active notebook container.
  - We gave a well-received presentation at JupyterCon 2018.
  - Proxy support inside the user notebook has been added, so that we can do things like display Dask worker dashboards to the user.
  - Mounted filesystems have been externalized to a ConfigMap document, so that mounted volumes can be updated as a very minor configuration change.
  - CILogon provides correct group information with the NCSA identity provider, and is therefore a fully-supported authentication source.
  - Federated authentication is available via CILogon (if additional identities are registered with the NCSA Identity Provider).
  - A Terraform deployment structure has been added.  When it reaches feature parity with our custom deployment tool, it will become the recommended method of deployment.
  - We now use RBAC for fine-grained permissions within the Nublado environment.
  - The Lab container does not start as root; it has a "provisionator" user that is allowed to run a few privileged commands to set up the actual user as whom to run, and that's all.
  - Various components can be pinned to particular node labels, so we can restrict infrastructure, firefly, Lab, or Dask pods to particular nodes.
  - Firefly JupyterLab widget has been added to the environment.
  - Many more visualization and analysis tools, such as Vaex, bqplot/ipyvolume, datashader, etc., have been added to the environment.
  - Better LaTeX support.
  - The user experience for shell users in the Terminal has been improved.
  - Much upstreaming of our work and integration with the Jupyter, Dask, and Bokeh communities.

Coming Eventually
=================

- End-to-end automated deployment testing.

- Better automated notebook testing.

- Better integration with Portal and DAX components of the Science Platform.

- Split repository into build and deployment pieces.

- Full-featured Terraform deployment.

- Chain together OAuth providers, so that we can use NCSA for go/no-go decisions, but still consult GitHub to get a token for magic HTTPS pushes and git configuration.

- Investigate addressing usability concerns (github-based workflows).

Repositories
------------

Code repos for system:

https://github.com/lsst-sqre/nublado :
	(JupyterLab container provisioning and Kubernetes cofig)

https://github.com/lsst-sqre/jupyterlab-savequit :
        (JupyterLab Save-and-Exit menu)

https://github.com/lsst-sqre/namespacedkubespawner :
        (K8s spawner that can put user pods into individual namespaces)

https://github.com/lsst-sqre/jupyterhubutils :
        (Hub utilities, mostly around scanning Docker repositories for images)

https://github.com/lsst-sqre/jupyterlabutils :
        (Lab utilities: mostly around proxying cluster resources to the user)

https://github.com/lsst-sqre/jupyterlab-lsstquery :
        (Lab extension to create a templated notebook from a query)

Related
-------

https://github.com/lsst-dm/sciplat-examples :
	(Some example demos contributed by DM science staff)

https://github.com/lsst-dm/tutorial-lsst2017 :
        (Large tutorial example from LSST All-Hands 2017)

https://github.com/lsst-sqre/notebook-demo :
        (Automatically updated notebooks for Lab environment)
 
Science Platform Design Discussion and Forward Look
===================================================

The JupyterLab service is an integral part of the Science Platform which, among other things, is expected to be the primary interface to the DM SubSystem for the LSST System Scientist and their commissioning team. As such, it goes beyond a standalone notebook service and has a number of critical interfaces to other components of the DM (and LSST) system.

The main interfaces are:

- Interfaces to data such as the Butler and DAX
  
- Interface to the "batch" system

- Interface to the Engineering Facilities Database (EFD)

- Interface to the authentication/authorisation system

- Interface to persistent storage

Additionally, we expect the following services to need to become "notebook aware":

- Documentation (LtD): Notebooks to be offered as a first class form for reports and playbook type documentation.

- CI/release engineering: As notebooks are a critical part of software infrastructure through their role in commissioning, they need to have a workflow similar to normal code, such as continuous integration, versioned deployment, etc.

Additionally, as the notebook platform is now a primary user interface into the DM system, its scaling requirements need to be revisited compared to the optional "Level 3" service it replaced.

This section captures SQuaRE's current thinking on these issues pending a formal architectural baseline being achieved. A milestone table summarising the target dates for meeting the various interfaces is given at the end.

Major Issues
------------

- Interface to the EFD

  It is a primary requirement of the commissioning team to cross-correlate image data with readouts stored in the EFD. These are later ingested in the main database and can be accessible via DAX. However we strongly feel that for usability reasons (and managing the limited resources of the commissioning cluster) the same notebook should be able to run at the datacenter Jupyter deployment that can run on the commissioning cluster and vice versa. The two obvious ways of achieving this are:

  - request a generalised python interface to both;

  - have the EFD data be availabe through the DAX in timescales short enough to satisfy the commissioning team and only support one way of accessing EFD data.

  We strongly favour the latter approach but it has implications on other parts of the DM Subsystem that need to be discussed.

  
- Interface to the batch system: Right now the baseline is that some kind of user intervention will have to happen to go from a satisfactory notebook to running the same code over large datasets through the batch system. This presents significant usability challenges. If the workflow system would present an interface that allows optimized idempotent execution of notebooks ("you just asked me to do a job with this configuration and these inputs; I recognise that I have already executed such a job so I will return the results to you right away as a no-op") the usability will be vastly improved. We do not know whether such an interface can be provided at this stage.


Deployment and Scaling
----------------------

- We need to settle on a system for managing our kubernetes applications. This is likely to be Terraform. 

- Integration with datacenter-side persistent storage (GPFS?).  This currently seems to be more likely to be GPFS-exported-as-NFS.

- Integration with datacenter-side auth

    - We would like to be able to chain OAuth2 providers and pass GitHub token
      information along with the CILogon-provided UID/GID data.

Infrastructure Resources
========================

In this section we specify the resources required to support a deployment of our current JupyterLab system as a function of users, with the expectation that the current design scales well to about 10^2 users; we believe we understand how we can evolve the design to scale to 10^3 users but it's premature optimization at this point.

We will refine our recommendations for infrastructure resources as we study how our deployments hold up to real-world usage; right now these are estimates based on our pre-alpha prototype experience.

Permissions: admin
  A Kubernetes cluster **to which we have admin access**.  The cluster administrator will need to be able to create all types of Kubernetes resources: persistent volumes and claims, deployments, configmaps, and daemonsets in particular.  During normal operation, it will frequently be required to replace environment variables and perhaps configmaps in order to expose new Lab builds.  The Hub pod must be able to dynamically create and destroy Lab pods.

CPU capacity: 0.5 < x < 4 cores per concurrent user
  CPU capacity scales **per concurrent user**.  As a rule of thumb, a half CPU core guaranteed per pod (which would imply a minimum of 50 CPUs for the JupyterLab portion of the cluster if we have 100 concurrent users) with an upper limit of four cores is our current best estimate.  For computation that requires more than four cores, we expect to eventually require use of the batch system rather than the interactive notebook.

Memory: 8 GB per user
  Memory scales **per concurrent user**. A lower bound of 512MB and an upper bound of perhaps 8GB per user Lab container seems appropriate, although this may be bumped up as we see what stack workflows people tend to engage in. Again, for much larger jobs, we will eventually use batch rather than notebook.

Overall VM size: 6 cores / 16GB RAM per node (guide)
  Those two previous constraints taken together seem to indicate that an appropriate VM size for a node is something like 6 cores and 16GB. From the Lab perspective, we really don't care: as long as the resources are available, lots of small machines versus a few enormous ones is fairly immaterial, since Kubernetes abstracts the resources away.

Node-local storage: 200GB / node
  GKE currently provides 200GB of local storage per node.  Each container image takes about 10GB, but once running, a container has very modest storage needs (excluding user data).  200GB seems entirely adequate if we expect to have fewer than ten container images at any time, assuming that images are stored on node-local storage. We highly recommend SSD backing of the nodes for performance.
  
Persistent storage: 50 GB / user (beta phase estimate)
  Storage scales *per user*. Each user needs some amount of persistent storage for notebooks and workspace.  50-100GB per user is probably adequate for this phase of service, although it is a fair guess that a few users will use much more and most users will use almost nothing. We recommend that a fairly large shared filesystem is provisioned for home directories, and usage is monitored to establish actual data usage patterns. For short demos or limited time deployments (eg. to support a workshop) it may be possible to aggressively downsize that estimate depending on the notebooks and data that are expected to be used.

Storage for container cache: 250GB SSD total
  A local-to-the-cluster mirror of the container images makes first startup time for a given image significantly better. Making that pull happen over an internal-to-the-data-center network rather than from Docker Hub will reduce the data transfer time, if not the unpacking time.  After an image has been pulled and is resident in local storage, startup times are quite fast.

Shared storage: 10TB
  We anticipate the need for a shared group-writeable filesystem for collaboration, download of large artifacts, or production of large result sets.  On the order of 10 TB, writeable by all users of the cluster, is our initial estimate.  Again, this may change depending on observed needs.  Once again, though, we would reiterate that the JupyterLab platform is intended for rapid prototyping, hypothesis testing, and quick iteration; for large-scale bulk computation or catalogue production, the batch system is probably more appropriate.

User Management: Map UIDs and GIDs from OAuth2 system
  The current prototype system provides a persistent UID mapping shim from the user's GitHub account for this stage of development. A user's UID is simply that user's GitHub numeric ID, and their GIDs are the IDs of their GitHub Organizations.  It may be necessary to construct some other UID/GID mapping, but at any given cluster, or any set of clusters that share a filesystem, it will be necessary for the same user to always resolve to the same UID and set of GIDs.  This is not a difficult problem with a network filesystem, but the filesystem chosen must allow effectively POSIX permission semantics.  The current prototype is using NFSv4; we suspect that Ceph may make more sense as a production filesystem, but our actual position is that the choice of filesystem is an implementation detail of the cluster, and anything that allows users with persistent UIDs and GIDs to behave as if they were using a traditional Unix filesystem will be fine.

  The authentication system must also, of course, provide consistent UIDs and GIDs at least within the scope of a shared filesystem.  While we re using GitHub as a source of authentication truth (which make sense for developers as long as it is our source code control system of record, as it currently is) then we get *globally* consistent UIDs/GIDs without the need for a seperate user management system. Ultimately and for data center deployments we will work with the production auth system.  NCSA is aware of our requirement to return Unix UID and group-to-GID-mappings as part of their OAuth2 implementation. 

  
The JupyterLab Platform and Verification
----------------------------------------

- "New face of SQuaSH" interface: Following the adoption of the JupyterLab Platform and the involvement of SQuaRE's WBS, we need to consolidate the functionality of the front end that is currently being served by the Django portal into the JupyterLab platform as much as possible as we don't have sufficient effort to maintain two different user interfaces, and the JupyterLab one is likely to be superior in functionality. However we have not yet investigated dashboarding under JupyterLab and might revise this plan.

  We have demonstrated that SQuaSH can run successfully in a kubernetes cluster, just as the JupyterLab platform can.  Some work has been done to make Bokeh widgets compatible with JupyterLab, but much remains.

- Telemetry Gateway: while this is not currently an interface to JupyterLab (but rather to SQuaSH), in the event that notebook execution is used to compute metrics that are needed at the summit, the same mechanism that is used for SQuaSH may be required here. Potentially this uncovers an interface to the Telemetry Gateway but we are not certain at this point.


Integration with Developer/User Services
----------------------------------------

- Verification report generation/publication harness - (LtD support for notebooks)

- Production hardening: During commissioning rapid partial or whole re-deployment of assets is likely to be needed frequently and/or at short notice. While we are designing with this in mind, we have a target date for demonstrating this capability and improving on any bottlenecks (which may be in other components, in particular the CI chain).

Milestones
----------


+-----------+----------------+-----------------------------------------------------------------+-----------+
| Planned   | ETA            | Milestone                                                       | Met       |
+===========+================+=================================================================+===========+
| 2017-07   |                | Alpha deployment of JupyterHub/JupyterLab                       | 2017-05   |
+-----------+----------------+-----------------------------------------------------------------+-----------+
| 2017-08   |                | Continuous provisioning of stack containers from CI             | 2017-09   |
+-----------+----------------+-----------------------------------------------------------------+-----------+
| 2017-08   | \* all         | Hardware/Resource specification estimate                        |           |
+-----------+----------------+-----------------------------------------------------------------+-----------+
| 2017-09   |                | Commissioning 2-3 banner usecases selected                      |           |
+-----------+----------------+-----------------------------------------------------------------+-----------+
| 2017-09   |                | automated k8s provisioning                                      | 2017-12   |
+-----------+----------------+-----------------------------------------------------------------+-----------+
| 2017-10   | \* IPAC        | Understand interaction with SUI Portal and/or Firefly           |           |
+-----------+----------------+-----------------------------------------------------------------+-----------+
| 2017-10   |                | Informal Design Review of JupyterLab architecture               |           |
+-----------+----------------+-----------------------------------------------------------------+-----------+
| 2017-10   |                | LTD support                                                     |           |
+-----------+----------------+-----------------------------------------------------------------+-----------+
| 2017-11   | \* NCSA        | Integration with data center resources                          |           |
+-----------+----------------+-----------------------------------------------------------------+-----------+
| 2017-12   | \* NCSA        | Beta service deployed scaled up for DM in-project use           |           |
+-----------+----------------+-----------------------------------------------------------------+-----------+
| 2018-01   | \* NCSA        | EFD interface design baselined                                  |           |
+-----------+----------------+-----------------------------------------------------------------+-----------+
| 2018-02   | \* SysEng      | Full set of comissioning usecases fully defined                 |           |
+-----------+----------------+-----------------------------------------------------------------+-----------+
| 2018-06   |                | "Son of SQuaSH" verification dashboards deployed                |           |
+-----------+----------------+-----------------------------------------------------------------+-----------+
| 2018-08   | \* NCSA        | Batch interface design baselined                                |           |
+-----------+----------------+-----------------------------------------------------------------+-----------+
| 2018-10   | \* all         | Production hardening (inc. rapid deployment)                    |           |
+-----------+----------------+-----------------------------------------------------------------+-----------+
| 2019-06   |                | Notebook-as-software (inc CI and deployment) critical review    |           |
+-----------+----------------+-----------------------------------------------------------------+-----------+
| 2019-08   | \* Pipelines   | Science Verification/Validation usecases fully defined          |           |
+-----------+----------------+-----------------------------------------------------------------+-----------+
| 2020-08   | \* Science     | General User usecases fully defined                             |           |
+-----------+----------------+-----------------------------------------------------------------+-----------+
