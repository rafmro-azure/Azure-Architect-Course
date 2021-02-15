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

3.3 skipped due unavailibility of extending freetrial for Premium AD

3.4 
    a. create Resource Group
        $RGname = "vanl-p-neu-rg-main-7003"
        az group create --name $RGname --location northeurope
    b. create keyvault with username/password pair
            $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        $adUserId = (Get-AzADUser -mail $upn).Id
        $tenantId=(Get-AzSubscription).TenantId
        
    
        $templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json"
        $username = Read-Host -Prompt "Enter the virtual machine administrator name"
        $password = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString
        $sourcekeyVaultName = Read-Host -Prompt "Enter the name of existing keyvault to copy access list"
        $keyVaultName = ($RGname+"kvlt03").Replace('-' , '')

        $keyVaultAccessPolicies = (Get-AzureRMKeyVault -VaultName $sourcekeyVaultName).accessPolicies
        $armAccessPolicies = @()
        if($keyVaultAccessPolicies)
        {
           foreach($keyVaultAccessPolicy in $keyVaultAccessPolicies)
           {
              $armAccessPolicy = [pscustomobject]@{
                 tenantId = $keyVaultAccessPolicy.TenantId
                 objectId = $keyVaultAccessPolicy.ObjectId
              }
              $armAccessPolicyPermissions = [pscustomobject]@{
                 keys = $keyVaultAccessPolicy.PermissionsToKeys
                 secrets = $keyVaultAccessPolicy.PermissionsToSecrets
                 certificates = $keyVaultAccessPolicy.PermissionsToCertificates
                 storage = $keyVaultAccessPolicy.PermissionsToStorage
             }
             $armAccessPolicy | Add-Member -MemberType NoteProperty -Name permissions -Value $armAccessPolicyPermissions
             $armAccessPolicies += $armAccessPolicy
           }
        }
        $armAccessPoliciesParameter = [pscustomobject]@{
           list = $armAccessPolicies
        }
        $armAccessPoliciesParameter = $armAccessPoliciesParameter | ConvertTo-Json -Depth 5 -Compress
        Write-Host ("##vso[task.setvariable variable=Infra.KeyVault.AccessPolicies;]$armAccessPoliciesParameter")
        
        az deployment group create --resource-group $RGname --template-uri $templateUri --parameters keyVaultName=$keyVaultName secretName=$username secretValue=$password location=australiacentral accessPolicies=$armAccessPoliciesParameter
        



vanlpneurgmain7111kv01