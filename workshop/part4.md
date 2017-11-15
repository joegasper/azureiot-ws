# Azure IoT WS - Part 4

> In part four we are further completing the scenario: implement an event processor to analyze data coming in. Through Stream Analytics we calculate usage of a device and take action based on values surpassing a certain threshold. We leverage a Service Bus Queue which is picked up by an Azure Function to act upon the trigger and send a command to the device to shut something off. Power BI is also used to build a dashboard.

![picture alt](media/part4-architecture.png "Azure Architecture for part 4")

### Create new resources - Service Bus, Azure Function and new consumer group in IoT Hub

1. Create a new Service Bus account in your resource group.
1. Create a Queue - if you prefer you can leverage Topics but for this exercise a Queue is fine.
1. Create an Azure Function in your Resource Group.
1. In your existing IoT Hub account, go to your built-in Events endpoint.
    1. Add a new Consumer group. Name it `asaconsumergroup`.

### Azure Stream Analytics to process hot data

> In this section, we'll leverage Azure Stream Analytics as the stream processor. Azure Stream Analytics will get data from the IoT Hub and deliver 2 outputs: near real-time data directly to Power Bi, and a message into a Service Bus based on a calculation of energy usage by a device.

1. Create a new Azure Stream Analytics account. You can use S1 as the SKU. Note that because we are using several queries and outputs, you should scale out to 2 instances. You can scale down right after trying out the exercise to save costs.
1. Create an Input:
    1. Choose IoT Hub as the type of input.
    1. Connect to your IoT Hub account, and use the consumer group name `asaconsumergroup`.
1. Create an Output:
    1. Service Bus Queue as type of output.
    1. Select the Service bus created earlier.


### Azure Function to take action towards the device

1. Create a new Azure Function App in your resource group.
1. Add a Function named `shutdownusage`. You can use the C# Service Bus Queue Trigger as template, or use an empty one.
1. Leverage the code under ../src/functions/ as the body of the function. 
1. Under the Function Apps's **App Settings**, add a connection string to IoT Hub. You can copy the connection string from the IoT Hub's Shared access policies screen.
    1. Name the connection string `Azure_IoT_ConnectionString`.
1. Add a binding connecting to the Service Bus Queue.
![picture alt](media/functionbindings.png "Azure Function bindings screen")
1. Add the project.json file as found in ../src/functions/.
1. To test your function:
    1. Make sure you have the client simulator running and a registered event handler for the method named `shutdownUsage`. 
    1. You can use the following input to test your function:
    `{"deviceid":"[sampleDevice]","averageconsumption":30.0,"maxconsumption":35.0,"overtimeinseconds":60.0,"howmanytimes":3}
`


## Optional: add a Power BI report

1. Configure your Power BI account if you don't already have one.
1. Leverage the existing Stream Analytics job to add a query that serves primarily as a pass-through to take all messages as push them to Power BI.
1. You can now create a report leveraging the Streaming Data from the Stream Analytics job.


