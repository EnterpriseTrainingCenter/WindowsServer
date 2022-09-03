# Lab: Monitor performance

## Required VMs

* VN1-DC1
* VN1-FS1
* CL1

## Exercises

1. [Performance Monitoring](#exercise-1-performance-monitoring)

## Exercise 1: Performance Monitoring

### Introduction

In this exercise, you will create a data collector set on WS2019 with most relevant counters for memory, CPU, storage and network. Then, you will simulate some workload. Finally, you will analyze the collected performance data.

#### Tasks

1. [Create Data Collector Sets](#task-1-create-data-collector-sets)
1. [Analyze performance data](#task-2-analyze-performance-data)

### Task 1: Create Data Collector Sets

Perform these steps on WS2019.

1. From start menu, open the **Performance Monitor** console.
1. Expand **Data collector sets**.
1. From the context menu of **User Defined**, select **New**, **Data collector set** to create a data collector set.
   * **Name:** Performance Baseline
   * **Create manually (Advanced)**
   * **Data logs:** **Performance counter**
1. On the page **Which performance counters would you like to log**, click on **Add…**.
1. In the section **Available counters**, select and expand the **Processor** category ([figure 1]).
1. Select the counter **% Processor Time** ([figure 2]).
1. In the section **Instances**, select **_Total** and click on **Add** ([figure 3]).
1. Repeat the previous steps to add the following counters.
   * Logical Disk, Avg. Disk Queue Length, _Total
   * Memory, % Committed Bytes In Use
   * Network Interface, Bytes Total/Sec, Microsoft Hyper-V Network Adapter_2
1. Click on **Next** twice.
1. Select **Start the data collector set now** and click on **Finish**.
1. Add a second data collector set.
   * **Name:** CPU Alert
   * **Create manually**
   * **Type:** **Performance Counter Alert**
1. Add the performance counter **% Processor Time** and set the **Alert when** to **Above** with the **Limit** of **80** ([figure 4]).
1. Select the **CPU Alert** data collector set.
1. Double click **DataCollector01**.
1. Select the tab **Alert Actions**.
1. Activate **Log an entry in the application event log** ([figure 5]).
1. From the context menu of the **CPU Alert** data collector set, select **Start**.
1. Run a **Command Prompt** as Administrator.
1. Change directory to **L:\Performance**.

   ````shell
   L:
   Cd \Performance
   ````

1. Start a tool to simulate a CPU load near 100% for 30 seconds.

   ````shell
   consume -cpu-time -time 30
   ````

1. Start the tool again to consume all of the physical memory for 30 seconds. If your session with the VM gets disconnected, reconnect after 30 seconds.

   ````shell
   consume -physical-memory -time 30
   ````

1. Stop both data collector sets.

### Task 2: Analyze performance data

Perform these steps on WS2019.

1. Switch to **Performance Monitor**.
1. Fully expand **Reports** and open the report for **Performance Baselining** ([figure 6]).

   CPU load has been measured every 15 Seconds. You also should see the increase in memory usage and subsequent memory paging on disk. The memory percentage is the ratio between the size of the memory used and the size of the pagefile. That’s the reason why it will not raise up to 100%.

1. Open **Event Viewer**.
1. Navigate to the logfile **Microsoft**, **Windows**, **Diagnosis-PLA**, **Operational**.
1. You’ll find messages regarding exceeding the set CPU performance limit of 80% ([figure 7]).

[figure 1]: images/Performance-counters-available-selected.png
[figure 2]: images/Performance-counters-available.png
[figure 3]: images/Performance-counter-add.png
[figure 4]: images/Performance-counter-alert.png
[figure 5]: images/Performance-DataCollector-properties.png
[figure 6]: images/Performance-graph.png
[figure 7]: images/Performance-event-treshold.png