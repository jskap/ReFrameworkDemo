# ReFrameworkDemo
We will build 2 processes - one to feed a queue in Orchestrator (Dispatcher) and one to process transactions from the queue (built using the REFramework).

The Activities and Methods used:

1. Dispatcher - get entries from an Excel file and add them as queue items in Orchestrator

We started the project as a sequence and added a 'Try Catch' activity. In the 'Try' block:

  --  We used a 'Read Range' to read the first 10 lines (A1:C11) from an Excel file and store them in a newly created DataTable variable;
  
  --  We used a 'For Each Row' to loop through the rows of the DataTable variable and add each of them in the queue using the 'Add Queue Item' activity.
  
  --  We used a 'Log Message' activity to log the fact that the dispatching process was successful.

2. Performer - get items (transactions) from the queue and process them

a. In the Init state:

   -- InitAllSettings workflow invoked to initialize all settings and data for the applications and processes used. In this part, the settings, 
      constants and assets in the Config.xlsx file from the Data folder are read;
      
   -- InitAllApps workflow invoked to open apps and check their status, credentials and so on;
   
   -- KillAllProcesses workflow invoked to leave a single instance of the applications used.

Any system error sends the process to the End Process state.

b. In the Get Transaction Data state:

   -- In a Sequence, use a 'Should Stop' activity to verify if the Orchestrator sends a stop signal.
   
   -- If not, invoke the 'GetTransactionData' workflow that uses a 'Get Queue Item' activity to get the transaction information from the Orchestrator.

When there's no data to be read, it moves to the End Process state.

c. In the Process Transaction state:

   -- The process is a Sequence with a 'Try Catch' activity set to catch both system exceptions and business exceptions.
   
   -- Inside the 'Try' block, the ProcessTransaction workflowis invoked that has a simple process to:
      --  Attach to UiDemo application
      
      --  3 'Type into' activities to fill in the Cash in, On Us Check, Not On Us Check fields;
      
      --  'Click' "Accept" button.

d. In the End Process state, the applications closed are used.
