3.1 - Naming Convention definition in 3.1-NamingConvention.txt
3.2 - sample linked template in two files 3.2_host_template.json and 3.2-master_template.json
    a. create Resource Group
        az group create --name vanl-p-neu-rg-main-7002 --location northeurope
    b. validate deployment
        az deployment group validate --resource-group vanl-p-neu-rg-main-7002 --template-file ./3.2-master_template.json
    c. deploy
        az deployment group create --resource-group vanl-p-neu-rg-main-7002 --template-file ./3.2-master_template.json
    d. clean-up
        az group delete --name vanl-p-neu-rg-main-7002 -y