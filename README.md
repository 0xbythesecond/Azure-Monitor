# Azure-Monitor

## Collect data from an Azure virtual machine with Azure Monitor

Duration: 20 minutes

Objective:
In this exercise, I will collect data from an Azure virtual machine using Azure Monitor. I will deploy a virtual machine, create a Log Analytics workspace, enable the Log Analytics virtual machine extension, configure data collection, and view/query the collected data.

Prerequisites:

- An Azure subscription with Owner or Contributor role access.
- Basic knowledge of Azure virtual machines and Azure portal.

<details> 
  
<summary> 
  
### Task 1: Deploy an Azure virtual machine
  
  </summary>   

Sign in to the Azure portal at https://portal.azure.com/ using an account with Owner or Contributor role access to the Azure subscription.

Open the Cloud Shell by clicking the first icon in the top right of the Azure Portal. If prompted, select PowerShell and Create storage.

Ensure PowerShell is selected in the drop-down menu in the upper-left corner of the Cloud Shell pane.

Run the following command in the PowerShell session within the Cloud Shell pane to create a resource group that will be used in this lab:

```powershell
New-AzResourceGroup -Name AZ500LAB131415 -Location 'South Central US'
````

Wait for the resource group creation to complete.

Run the following command in the PowerShell session within the Cloud Shell pane to create a new Azure virtual machine:

```powershell
New-AzVm -ResourceGroupName "AZ500LAB131415" -Name "myVM" -Location 'EastUS' -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName   "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -PublicIpSku Standard -OpenPorts 80,3389 -Size Standard_DS1_v2
```

When prompted for credentials, use the following:

|User: | localadmin|
|------|------|
| Password: | Use a password that can be remembered easily|

Wait for the virtual machine deployment to complete.

Run the following command to confirm that the virtual machine named "myVM" was created and its ProvisioningState is "Succeeded":

```powershell
Get-AzVM -Name 'myVM' -ResourceGroupName 'AZ500LAB131415' | Format-Table
```
  
Close the Cloud Shell pane.
  
</details>

#

<details> 
  
<summary> 

### Task 2: Create a Log Analytics workspace
  
  </summary>   

In the Azure portal, use the search box at the top to search for "Log Analytics workspaces" and press Enter.

On the Log Analytics workspaces blade, click "+ Create."

On the Basics tab of the Create Log Analytics workspace blade, specify the following settings:

| Subscription: | Select the name of the Azure subscription you are using in this lab.|
|--------|-------|
| Resource group: | AZ500LAB131415|
| Name: | Enter any valid, globally unique name for the Log Analytics workspace.|
| Region: | South Central US|

Select "Review + create."

On the Review + create tab of the Create Log Analytics workspace blade, click "Create" to start the workspace creation.

Wait for the Log Analytics workspace creation to complete.

</details> 

#

<details> 

<summary> 
  
### Task 3: Enable the Log Analytics virtual machine extension
  
</summary>  

In the Azure portal, navigate back to the Log Analytics workspaces blade.

In the list of workspaces, click the entry representing the workspace you created in the previous task.

On the Log Analytics workspace blade, on the Overview page, in the "Connect a Data Source" section, click the "Azure Virtual machines (VMs)" entry.

  >**Note**: For the agent to be successfully installed, the virtual machine must be running.

In the list of virtual machines, locate the entry representing the Azure VM "myVM" you deployed in Task 1 and note that it is listed as "Not connected."

Click the "myVM" entry and then, on the "myVM" blade, click "Connect."

Wait for the virtual machine to connect to the Log Analytics workspace. The status displayed on the "myVM" blade will change from "Connecting" to "This workspace."
  
</details> 

#

<details> 
  
<summary>   

### Task 4: Collect virtual machine event and performance data
  
  </summary>   

In the Azure portal, navigate back to the Log Analytics workspace you created earlier in this exercise.

On the Log Analytics workspace blade, in the "Settings" section, click "Legacy agents management."

On the Agents configuration blade, review the configurable settings including Windows Event Logs, Windows Performance Counters, Linux Performance Counters, IIS Logs, and Syslog.

Ensure that "Windows Event Logs" is selected, click "+ Add windows event log," and in the listing of event log types, select "System."

Deselect the "Information" checkbox, leaving the "Error" and "Warning" checkboxes selected.

Click "Windows Performance Counters," click "+ Add performance counter," review the listing of available performance counters, and add the following ones:

Memory(*)\Available Memory Mbytes
Process(*)% Processor Time
Event Tracing for Windows\Total Memory Usage — Non-Paged Pool
Event Tracing for Windows\Total Memory Usage — Paged Pool

  >**Note**: The counters are added and configured with a 60-second collection sample interval.

On the Agents configuration blade, click "Apply."
  
</details>  

#

<details> 

<summary> 

### Task 5: View and query collected data
  
</summary>   

In the Azure portal, navigate back to the Log Analytics workspace you created earlier in this exercise.

On the Log Analytics workspace blade, in the "General" section, click "Logs."

If needed, close the "Welcome to Log Analysis" window.

On the Queries pane, in the "All Queries" column, scroll down to the bottom of the list of resource types, and click "Virtual machines."

Review the list of predefined queries, select "Memory and CPU usage," and click the corresponding "Run" button.

  >**Note**: You can start with the query "Virtual machine available memory." If you don't get any results, check that the scope is set to "virtual machine."

The query will automatically open in a new query tab. Note: Log Analytics uses the Kusto query language. You can customize the existing queries or create your own.

  >**Note**: The results of the query you selected are automatically displayed below the query pane. To re-run the query, click "Run."

  >**Note**: Since this virtual machine was just created, there may not be any data yet.

  >**Note**: You have the option of displaying data in different formats. You also have the option of creating an alert rule based on the results of the query.

Optional: Generate additional load on the Azure VM to collect more data:

Navigate to the Azure VM blade.
In the Operations section, select "Run command."
On the "RunPowerShellScript" blade, run the following script:

```elm
cmd
:loop
dir c:\ /s > SWAP
goto loop
```  
  
Switch back to the Log Analytics blade and re-run the query. You might need to wait a few minutes for data to be collected before re-running the query.
Results:
You have used a Log Analytics workspace to configure data sources and query logs from the Azure virtual machine.
  
</details>   





