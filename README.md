Introduction
============

This extension provides a system based on the XWiki scheduler that is able to run execute-once long-running tasks in the background. It also provide reporting and logging facilities so that the user can have a clue about the progress status. The main use case for this extension is large batch import of data into the wiki.

Architecture
------------

This extension defines two types of entities: tasks, ask executors and a task executor manager.

* A task is a document containing information about the task to be executed. The page, for example could contain, as attachments, the files to be processed or the object defining the parameters for the process that will execute this task. A task is identified by an object of type *TaskClass* which is used to define the following properties: the executor that will handle the task, and the status of the task: *SUSPENDED*, *READY*, *RUNNING*, *DONE* or *ERROR*

* A task executor is document containing a Groovy script that implements the logic for processing a given task. A task executor defines a function *run* that takes three parameters: a logger, the XWiki API object and the task document.

* A task executor manager is a scheduler job that takes care of executing tasks. What it does is quite straightforward: when called, it looks for a task that is *READY* to run and it executes it in a well defined context. It also takes care of handling the task status, logging and error management. By default the task executor manager executes one task per invocation. Subsequent invocations will execute new tasks depending on the *maxConcurrentTasks* settings. At any given moment there will always be at most *maxConcurrentTasks* in the *RUNNING* state. This setting is configurable in the document *TaskExecutorCode.TaskExecutorManagerJobConfiguration* and by default it is set to 1.

* Every task has a private log where it can log its progress. Task logs can be accessed from the *TaskExecutorCode.WebHome* page, or by using the *TaskExecutorCode.ShowLog* page, passing the query parameter *log* with the log name, or also using the *{{showlog log="..."/}}* macro in wiki pages. The task log name is its document name with the *.log* suffix (e.g., if a task document is defined in the *Tasks* space and has the *SimpleTask* name, then its log name will be *Tasks.SimpleTask.log*)


