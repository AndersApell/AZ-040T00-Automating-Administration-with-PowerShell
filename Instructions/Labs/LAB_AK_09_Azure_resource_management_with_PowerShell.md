---
lab:
    title: 'Lab: Azure resource management with PowerShell'
    type: 'Answer Key'
    module: 'Module 9: Managing Azure resources with PowerShell'
---

# Lab answer key: Azure resource management with PowerShell

## Exercise 1: Activating the Azure subscription and installing the PowerShell Az module

### Task 1: Activate your Azure subscription by using Azure pass voucher

1. On **LON-CL1**, open the Microsoft Edge browser and navigate to **https://www.microsoftazurepass.com/**.
1. On the **Try Microsoft Azure Pass** webpage, select **Start**.
1. On the **Sign in** page, enter the Microsoft account that you want to use for your Azure pass. It can be any Microsoft account of your choice, or you can create a new one. Be sure to note the account you use to sign in here, because you'll need it later. Select **Next**.
1. On the **Enter Password** page, enter the password of the Microsoft account you want to use in this lab. Select **Sign in**.
1. On the confirmation page, review the data you provided, and then select **Confirm Microsoft Account**.
1. In the **Enter Promo code** box, enter the Azure pass code provided by your instructor or lab hosting provider. Select **Claim Promo Code**. Wait until your request is processed.
1. On the **Your profile** page, verify your data, select **I agree to the subscription agreement, offer details, and privacy statement**, and then select **Sign up**.
1. On the **Setting up your account** page, provide your feedback for the sign-up experience, and then select **Submit**.
1. The Azure portal will open. On the **Welcome to Microsoft Azure** page, select **Maybe later** to skip the tour.
1. On the **Microsoft Azure** page, select **Subscriptions**.
1. Ensure that on the **Subscriptions** page, the **Azure Pass - Sponsorship** has the status **Active**.
1. Open a new tab in the same Microsoft Edge window and navigate to **https://www.microsoftazuresponsorships.com/**.
1. On the **Azure Sponsorship** page, select **Check Your Balance**.
1. On the **Azure Sponsorship** page, verify that you have 50 USD of credit available. Close the **Azure Sponsorship** tab. Leave the Microsoft Azure portal tab open.

### Task 2: Install the Azure Az module for PowerShell

1. On **LON-CL1**, select **Start**. Then, run PowerShell 7.1 as administrator.
1. In the **Administrator: Windows PowerShell** window, enter the following command, and then select Enter to check your PowerShell version:

   ```powershell
   $PSVersionTable.PSVersion
   ```

1. To set your execution policy to the proper value so you can install the Az module, enter the following command, and then select Enter. Enter **Y** to confirm your choice:

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
   ```

1. To install the Az module, enter the following command:

   ```powershell
   Install-Module -Name Az -Scope CurrentUser -Repository PSGallery -Force
   ```

1. Wait until the module is installed and the command prompt displays.
1. After the Az module is installed, enter the following command:

   ```powershell
   Connect-AzAccount
   ```

1. When prompted, sign in with the account that you used in the previous task to provision your Azure subscription.
1. Verify that after sign-in, your account name and **Azure Pass - Sponsorship** subscription are listed.

## Exercise 2: Using Azure Cloud Shell

### Task 1: Use Azure Cloud Shell to create a resource group

1. On the **LON-CL1** computer, restore the browser window where you have the Azure portal open.
1. On the Microsoft Azure portal homepage, select **Virtual Machines**. Ensure that no virtual machines (VMs) are created. Select **Home**.
1. On the Microsoft Azure portal homepage, select **Storage accounts**. Ensure that no storage accounts are created. Select **Home**.
1. On the Microsoft Azure portal homepage, select the **Cloud Shell** icon.
1. In the **Welcome to Azure Cloud Shell** window, select **PowerShell**.
1. On the **You have no storage mounted** page, review the note about the missing storage account that's needed for Cloud Shell to run. Verify that in the **Subscription** field, your **Azure Pass - Sponsorship** subscription is selected, and then select **Create storage**. Wait until the storage account is created.
1. When your storage account is created, the Cloud Shell console should open, and you should get a prompt in the format **PS /home/yourname>**.
1. At the PowerShell prompt, enter **Get-AzSubscription**, and then select Enter to review your subscriptions.
1. Enter **Get-AzResourceGroup** to review the resource group information.
1. Use the drop-down list to switch from PowerShell to the **Bash** shell and confirm your choice.
1. At the Bash shell prompt, enter **az account list**, and then select Enter to review your subscriptions. Also, try tab completion.
1. Enter **az resource list** to review the resource group information.
1. Switch back to the PowerShell interface.
1. In the PowerShell console, enter the following command, and then select Enter to create a new resource group:

    ```powershell
    New-AzResourceGroup -Name YournameM9 -Location westeurope
    ```

1. Verify that your new resource group is created. Remember the name of your resource group.

## Exercise 3: Managing Azure resources with Azure PowerShell

### Task 1: Create an Azure VM by using PowerShell

1. In the PowerShell 7.1 window, enter the following command to define the admin credentials for the VM you want to create in Azure:

   ```powershell
   $cred = Get-Credential -Message "Enter a username and password for the virtual machine."
   ```

1. When prompted, choose the username and password that you want to use as admin credentials for the new VM. Do not use name Admin for administrator.
1. In the PowerShell 7.1 window, enter the following command to define the VM parameters, and then select Enter. Replace *yourname* with your real name:

   ```powershell
   $vmParams = @{
     ResourceGroupName = 'yournameM9'
     Name = 'TestVM1'
     Location = 'westeurope'
     ImageName = 'Win2016Datacenter'
     PublicIpAddressName = 'TestPublicIp'
     Credential = $cred
     OpenPorts = 3389
   }
   ```

1. To create a new VM based on these parameters and store it in the `newVM1` variable, enter the following command, and then select Enter:

   ```powershell
   $newVM1 = New-AzVM @vmParams
   ```

   >**Note:** Wait until the Azure VM is created.

1. To check the parameters on the new VM, enter the following commands, and then select Enter:

   ```powershell
   $NewVM1
   $newVM1.OSProfile | Select-Object ComputerName,AdminUserName
   $newVM1 | Get-AzNetworkInterface | Select-Object -ExpandProperty IpConfigurations | Select-Object Name,PrivateIpAddress
   ```

1. To find a public IP address on the Azure VM you created so you can connect to it, enter the following commands, and then select Enter:

   ```powershell
   $publicIp = Get-AzPublicIpAddress -Name TestPublicIp -ResourceGroupName yournameM9
   
   $publicIp | Select-Object Name,IpAddress,@{label='FQDN';expression={$_.DnsSettings.Fqdn}}
   ```

1. Note the value of **IPAddress** in the table output.
1. Enter the following command, and then select Enter to connect to the VM:

   ```powershell
   mstsc.exe /v <PUBLIC_IP_ADDRESS>
   ```

1. When prompted, sign in with the credentials you provided for this VM. Ensure that you're connected to the Windows Server 2016 VM. Check the VM functionality and then shut it down.

### Task 2: Add a disk to the Azure VM by using PowerShell

1. Switch to the Microsoft Edge window, where you have the Azure portal open. Select **Virtual Machines**.
1. Ensure that **TestVM1** is listed. Select it.
1. On the **Overview** page, check the parameters of the VM you created. Select **Disk**. Ensure that only one disk is created (Os disk).
1. To create a data disk for the existing VM, in the PowerShell 7.1 window, enter the following commands, and select Enter after each:

   ```powershell
   $VirtualMachine = Get-AzVM -ResourceGroupName "yournameM9" -Name "TestVM1"
   Add-AzVMDataDisk -VM $VirtualMachine -Name "disk1" -LUN 0 -Caching ReadOnly -DiskSizeinGB 1 -CreateOption Empty
   Update-AzVM -ResourceGroupName "yournameM9" -VM $VirtualMachine
   ```

1. Switch to the Azure portal and refresh the **Disks** page. You should be able to notice a new disk called **disk1** in the **Data disks** section.
