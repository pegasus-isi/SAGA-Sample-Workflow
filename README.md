# Using the Pegasus Workflow Management System

This is an example on how to run Pegasus workflows on the ISI SAGA cluster. The sample workflow can be found on [GitHub](https://github.com/pegasus-isi/SAGA-Sample-Workflow)

## Introduction

[The Pegasus project](https://pegasus.isi.edu) encompasses a set of technologies that help workflow-based applications execute in a number of different environments including desktops, campus clusters, grids, and clouds. Pegasus bridges the scientific domain and the execution environment by automatically mapping high-level workflow descriptions onto distributed resources. It automatically locates the necessary input data and computational resources necessary for workflow execution. Pegasus enables scientists to construct workflows in abstract terms without worrying about the details of the underlying execution environment or the particulars of the low-level specifications required by the middleware. Some of the advantages of using Pegasus include:

   * **Portability / Reuse** - User-created workflows can easily be run in different environments without alteration. Pegasus currently runs workflows on top of Condor, Grid infrastrucutures such as Open Science Grid and TeraGrid, Amazon EC2, Nimbus, and many campus clusters. The same workflow can run on a single system or across a heterogeneous set of resources.

   * **Performance** - The Pegasus mapper can reorder, group, and prioritize tasks in order to increase the overall workflow performance.

   * **Scalability** - Pegasus can easily scale both the size of the workflow, and the resources that the workflow is distributed over. Pegasus runs workflows ranging from just a few computational tasks up to 1 million tasks. The number of resources involved in executing a workflow can scale as needed without any impediments to performance.

   * **Provenance** - By default, all jobs in Pegasus are launched via the kickstart process that captures runtime provenance of the job and helps in debugging. The provenance data is collected in a database, and the data can be summaries with tools such as pegasus-statistics, pegasus-plots, or directly with SQL queries.

   * **Data Management** - Pegasus handles replica selection, data transfers and output registrations in data catalogs. These tasks are added to a workflow as auxiliary jobs by the Pegasus planner.

   * **Reliability** - Jobs and data transfers are automatically retried in case of failures. Debugging tools such as pegasus-analyzer help the user to debug the workflow in case of non-recoverable failures.

   * **Error Recovery** - When errors occur, Pegasus tries to recover when possible by retrying tasks, retrying the entire workflow, providing workflow-level checkpointing, re-mapping portions of the workflow, trying alternative data sources for staging data, and, when all else fails, providing a rescue workflow containing a description of only the work that remains to be done.

Pegasus workflows have 4 components:

   * **DAX** - Abstract workflow description containing compute steps and dependencies between the steps. This is called abstract because it does not contain data locations and available software. The DAX format is XML, but it is most commonly generated via the provided APIS ([documentation](https://pegasus.isi.edu/documentation)). [Python](https://pegasus.isi.edu/documentation/python/), [Java](https://pegasus.isi.edu/documentation/javadoc/edu/isi/pegasus/planner/dax/ADAG.html) and [Perl](https://pegasus.isi.edu/documentation/perl/) APIs are available. 
     
   * **Transformation Catalog** - Specifies locations of software used by the workflow
     
   * **Replica Catalog** - Specifies locations of input data
     
   * **Site Catalog** - Describes the execution environment

However, for simple workflows, the transformation and replica catalog can be contained inside the DAX, and to further simplify the setup, the following examples generate the site catalog on the fly. This means that the user really only has to be concerned about creating the DAX.  

For details, please refer to the [Pegasus documentation](https://pegasus.isi.edu/documentation/).


## Description of Files and Directories

Pegasus is very flexible and how you layout your workflow is your preference. This example has the following layout:

   * **bin/** - A set of binaries run by the jobs

   * **daxgen.py** - The workflow definition. This is the file to start editing for convert the workflow to your needs.

   * **pegasus.conf** - Configuration file for Pegasus.

   * **rc.txt** - Replica catalog which specifies where to find input files. Note that Pegasus does not assume files are local. You can specify input files as URLs if the data is remote.

   * **sites.xml** - Site catalog, a description of the execution environment. Edit this file if you want to change the queue or file system locations.

   * **submit.sh** - Helper script which generates the DAX with `daxgen.py` and then submit the workflow.

   * **tc.txt** - Transformation catalog, which is a list of executables used by the workflow.


## Running the Sample Workflow

**Exercise 1:** Submit the workflow by executing the submit command.

	$ ./submit.sh

Note that when Pegasus plans/submits a workflow, a work directory is created and presented in the output. This directory is the handle to the workflow instance and used by Pegasus command line tools. Some useful tools to know about:

   * `pegasus-status -v [wfdir]`
        Provides status on a currently running workflow. ([more](https://pegasus.isi.edu/documentation/cli-pegasus-status.php))
   * `pegasus-analyzer [wfdir]`
        Provides debugging clues why a workflow failed. Run this after a workflow has failed. ([more](https://pegasus.isi.edu/documentation/cli-pegasus-analyzer.php))
   * `pegasus-statistics [wfdir]`
        Provides statistics, such as walltimes, on a workflow after it has completed. ([more](https://pegasus.isi.edu/documentation/cli-pegasus-statistics.php))
   * `pegasus-remove [wfdir]`
        Removes a workflow from the system. ([more](https://pegasus.isi.edu/documentation/cli-pegasus-remove.php))

During the workflow planning, Pegasus transforms the workflow to make it work well in the target execution environment.

The executable workflow has a set of additional tasks added by Pegasus: create scratch dir, data staging in and out, and data cleanup jobs.

**Exercise 2:** Check the status of the workflow:

	$ pegasus-status [wfdir]

You can keep checking the status periodically to see that the workflow is making progress.

**Exercise 3:** Keep checking progress with `pegasus-status`. Once the workflow is done, display statistics with `pegasus-statistics`:

	$ pegasus-status [wfdir]
	$ pegasus-statistics [wfdir]
	...
 
