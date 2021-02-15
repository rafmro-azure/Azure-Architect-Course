3.1 - Naming Convention definition in 3.1-NamingConvention.txt
3.2 - sample linked template in two files 3.2_host_template.json and 3.2-master_template.json
    a. create Resource Group
        az group create --name vanl-p-neu-rg-main-7002 --location northeurope
        
    b. validate deployment
        az deployment group validate --resource-group vanl-p-neu-rg-main-7002 --template-file ./3.2-master_template.json
        
    c. deploy
        az deployment group create --resource-group vanl-p-neu-rg-main-7002 --template-file ./3.2-master_template.json  --parameters secretkey=PUT_HERE_A_PASSWORD
        
    d. clean-up
        az group delete --name vanl-p-neu-rg-main-7002 -y

3.3 skipped due the unavailibility of extending free-trial for Premium AD

3.4 
    a. create Resource Group
        $RGname = "vanl-p-neu-rg-main-7003"
        az group create --name $RGname --location northeurope
    b. create keyvault with username/password pair (due well-known problem with setting accesslist in ARM template there is no other way than CLI/Powershell/GUI)
        
        $keyVaultIndex = "kvlt05"
        $keyVaultName = ($RGname+$keyVaultIndex).Replace('-' , '')
        $keyvaultlocation="australiacentral"
        
        az keyvault create --name $keyVaultName --resource-group $RGname --location $keyvaultlocation --enabled-for-deployment

        $username = Read-Host -Prompt "Enter the virtual machine administrator name"
        $password = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString
        
        az keyvault secret set --vault-name $keyVaultName --name $username --value ($password | ConvertFrom-SecureString -AsPlainText)
        az keyvault secret show --name $username --vault-name $keyVaultName --output table
        
    c. validate deployment
        az deployment group validate --resource-group $RGname --template-file ./3.4-master_template.json --parameters vaultName=$keyVaultName secretName=$username
        
    d. deploy
        az deployment group create --resource-group $RGname --template-file ./3.4-master_template.json --parameters vaultName=$keyVaultName secretName=$username
        
    e. clean-up
        az group delete --name $RGname -y
        