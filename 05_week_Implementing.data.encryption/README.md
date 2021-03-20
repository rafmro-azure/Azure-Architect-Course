    $RGName = 'vanl-e-neu-7006'
    $Region = "northeurope"


1. Create KV 

        $KVName = ($RGName + 'neukv001').Replace('-','')
        az keyvault create --name $KVName --resource-group $RGName --location $Region --enabled-for-disk-encryption

2. Create simple VM (with Ubuntu)

    Full list of linux distributions which are comaptible with Azure Disk Encryption is under this link:
    https://docs.microsoft.com/pl-pl/azure/virtual-machines/linux/disk-encryption-overview#supported-operating-systems
    
        $VMName = $RGName + '-neu-ubuntu001'
        $VMusername = Read-Host -Prompt "Enter the virtual machine administrator name"
        $VMpassword = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString
    
    Ubuntu:
        
        az vm create --resource-group $RGName --name $VMName --image 'Canonical:UbuntuServer:18.04-LTS:latest' --admin-username $VMusername --admin-password ($VMpassword | ConvertFrom-SecureString -AsPlainText) --location $Region --size 'Standard_B2ms'
    
    Note about Linux size : 8GB RAM is needed for Azure Disk Encryption!
    
    Windows 2016:
        
        $VMNameW = ($RGName + '-neu-win2016-001').Replace('-','')
        $VMNameW = $VMNameW.Substring($VMNameW.Length - 15)
        az vm create --resource-group $RGName --name $VMNameW --image 'MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest' --admin-username $VMusername --admin-password ($VMpassword | ConvertFrom-SecureString -AsPlainText) --location $Region --size 'Standard_B2ms'
        
3. Encrypt VMs volume 

    a. Create KEK for VM volumes encryption (Key Encryption Key)
    
        $KEKName = $KVName + '-kek001'
        az keyvault key create --name $KEKName --vault-name $KVName --kty RSA
    
    b. Enable custom encryption using KEK:
        
        az vm encryption enable -g $RGName --name $VMName --disk-encryption-keyvault $KVName --key-encryption-key $KEKName

    c. Wait till encryption finish
        
        az vm encryption show -g $RGName --name $VMname
        az vm encryption show -g $RGName --name $VMnameW
        
4. VMs volume custom encryption is enabled.
    
    Get-AzVMDiskEncryptionStatus -ResourceGroupName $RGName -VMName $VMname
    
    Ubuntu VM's response:
    
        [
            {
                "code": "ComponentStatus/Microsoft.Azure.Security.AzureDiskEncryptionForLinux/succeeded",
                "level": "Info",
                "displayStatus": "Provisioning succeeded",
                "message": "{\"os\": \"Encrypted\", \"data\": \"NotMounted\"}"
            }
        ]
    
    Windows 2016's response:
        "

            OsVolumeEncrypted          : Encrypted
            DataVolumesEncrypted       : NoDiskFound
            OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
            ProgressMessage            : [2.2.0.37]
        "
    Note: After logging in to the Windows' console the Bitlocker's app shows status : "encrypting". It took about 30 minutes to fully encrypt OS drive.
          Meanwhile, on VM with linux the system is not accessible until OS' drive is not fully encrypted.
            
5. Deploy VM with Windows Server 2016 
    
        $VMNameW1 = ($RGName + '-neu-win2016-001').Replace('-','')
        $VMNameW1 = $VMNameW.Substring($VMNameW.Length - 15)
        $VMusername = Read-Host -Prompt "Enter the virtual machine administrator name"
        $VMpassword = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

        az vm create --resource-group $RGName --name $VMNameW1 --image 'MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest' --admin-username $VMusername --admin-password ($VMpassword | ConvertFrom-SecureString -AsPlainText) --location $Region --size 'Standard_D2s_v3'
    
    Note: only v3 (and above) series of Azure VMs support nested virtualization.
    
6. Install Hyper-V
    
       Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart

7. Prepare disk for export 
        On the left menu, select Virtual Machines, and select the VM from the list. Then, on the overview page for the VM, select Stop.
        When VM fully stops, select Disks. Then pick encrypted OS disk and when new window pop-ups select Disk Export. 
        Hit Generate URL and copy URL to clipboard.
    
        Download VHD to Windows Server:
        
            $ProgressPreference = 'SilentlyContinue' #otherwise the download process will be slow like a snail :) It's so called "progress bar's issue" and it is not solved since 2010 ;)
            $source = 'put_there_a_generated_earlier_link_for_VHD_disk_image'
            $destination = 'c:\temp\1.vhd'
            Invoke-WebRequest -Uri $source -OutFile $destination
            
8. Create VM in Hyper-V using downloaded VHD image
   
        New-VM -Name 'NEWone' -VHDPath C:\temp\abcd.vhd -Generation 1 -MemoryStartupBytes 2GB
        Start-VM 'NEWOne'
    
    VM cannot boot properly.
    As a result we should get effect simialr to captured on this picture:
    https://github.com/rafmro-azure/Azure-Architect-Course/blob/main/05_week_Implementing.data.encryption/VM_on_hyperV_from_ADE_VHD.JPG
