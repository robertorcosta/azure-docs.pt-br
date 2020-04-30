---
title: Criar um certificado de Azure Key Vault | Azure Marketplace
description: Explica como registrar uma VM de um VHD do Azure implantado.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 9981f8eda174bbe04b54933528d20d270d360824
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82148251"
---
# <a name="create-certificates-for-azure-key-vault"></a>Criar certificados para o Azure Key Vault

> [!IMPORTANT]
> A partir de 13 de abril de 2020, começaremos a mover o gerenciamento de suas ofertas de máquina virtual do Azure para o Partner Center. Após a migração, você criará e gerenciará suas ofertas no Partner Center. Siga as instruções em [certificação de imagem de VM do Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-image-certification) para gerenciar suas ofertas migradas.

Este artigo explica como provisionar os certificados autoassinados necessários para estabelecer uma conectividade do WinRM (Gerenciamento Remoto do Windows) com uma VM (máquina virtual) hospedada no Azure. Esse processo consiste em três etapas:

1.    Criar o certificado de segurança. 
2.    Criar o Azure Key Vault para armazenar esse certificado. 
3.    Armazenar os certificados neste cofre de chaves. 

Você pode usar um grupo de recursos do Azure novo ou existente para este trabalho.  A primeira abordagem é usada na explicação a seguir.



[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="create-the-certificate"></a>Criar o certificado

Edite e execute o seguinte script do Azure Powershell para criar o arquivo de certificado (.pfx) em uma pasta local.  Você precisará substituir os valores dos seguintes parâmetros:

|  **Parâmetro**        |   **Descrição**                                                               |
|  -------------        |   ---------------                                                               |
| `$certroopath` | Pasta local para salvar o arquivo .pfx  |
| `$location`    | Um dos locais geográficos padrão do Azure  |
| `$vmName`      | Nome da máquina virtual planejada do Azure   |
| `$certname`    | Nome do certificado, que precisa corresponder ao nome de domínio totalmente qualificado da VM planejada  |
| `$certpassword` | Senha dos certificados, precisa corresponder à senha usada para a VM planejada  |
|  |  |

```powershell
    # Certification creation script 
    
    # pfx certification stored path
    $certroopath = "C:\certLocation"
    
    #location of the resource group
    $location = "westus"
    
    # Azure virtual machine name that we are going to create
    $vmName = "testvm000000906"
    
    # Certification name - should match with FQDN of Windows Azure creating VM 
    $certname = "$vmName.$location.cloudapp.azure.com"
    
    # Certification password - should be match with password of Windows Azure creating VM 
    $certpassword = "SecretPassword@123"
    
    $cert=New-SelfSignedCertificate -DnsName "$certname" -CertStoreLocation cert:\LocalMachine\My
    $pwd = ConvertTo-SecureString -String $certpassword -Force -AsPlainText
    $certwithThumb="cert:\localMachine\my\"+$cert.Thumbprint
    $filepath="$certroopath\$certname.pfx"
    Export-PfxCertificate -cert $certwithThumb -FilePath $filepath -Password $pwd
    Remove-Item -Path $certwithThumb 

```
> [!TIP]
> Mantenha a mesma sessão de console do PowerShell ativa durante essas etapas para que os valores dos diversos parâmetros sejam retidos.

> [!WARNING]
> Se você salvar esse script, armazene-o somente em um local seguro porque ele contém informações de segurança (uma senha).


## <a name="create-the-key-vault"></a>Criar o cofre de chaves

Copie o conteúdo do [modelo de implantação do cofre de chaves](./cpp-key-vault-deploy-template.md) em um arquivo em seu computador local. (no script de exemplo abaixo, esse recurso é `C:\certLocation\keyvault.json`.)  Edite e execute o seguinte script do Azure PowerShell para criar uma instância de Azure Key Vault e o grupo de recursos associado.  Você precisará substituir os valores dos seguintes parâmetros:

|  **Parâmetro**        |   **Descrição**                                                               |
|  -------------        |   ---------------                                                               |
| `$postfix`            | A cadeia de caracteres numérica arbitrária acrescentada aos identificadores de implantação                     |
| `$rgName`             | Nome do RG (grupo de recursos) do Azure a ser criado                                        |
|  `$location`          | Um dos locais geográficos padrão do Azure                                  |
| `$kvTemplateJson`     | Caminho do arquivo (keyvault.json) que contém o modelo do Resource Manager para o cofre de chaves |
| `$kvname`             | Nome do novo cofre de chaves                                                       |
|  |  |

```powershell
    # Creating Key vault in resource group
    
    # "Random" number for deployment identifiers
    $postfix = "0101048"
    
    # Resource group name
    $rgName = "TestRG$postfix"
    
    # Location of Resource Group
    $location = "westus"
    
    # Key vault template location
    $kvTemplateJson = "C:\certLocation\keyvault.json"
    
    # Key vault name
    $kvname = "isvkv$postfix"
    
    # code snippet to get the Azure user object ID
    try
       {
        $accounts = Get-AzureAccount
        $accountNum = 0
        $accounts.Id | %{ ++$accountNum; Write-Host $accountNum $_}
        Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
        [Int] $accountChoice = Read-Host 
        
        While($accountChoice -lt 1 -or $accountChoice -gt $accounts.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red 
            Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
            [Int] $accountChoice = Read-Host
        }
        
        $accountSelected = $accounts[$accountChoice-1]
        echo $accountSelected
        $id = $accountSelected.Id
                              
        Write-Host "User $id Selected"
        $myobjectId=(Get-AzADUser -Mail $id)[0].Id
      }
      catch
      {
      Write-Host $_.Exception.Message    
      Break
      } 

    # code snippet to get Azure User Tenant Id
      # SELECT Subscriptions
        #**************************************
        try
        {
        $subslist=Get-AzureSubscription
        for($i=1; $i -le $subslist.Length;$i++)
        {
           Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
        }
        Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
        [int] $selectedsub=Read-Host
    
        While($selectedsub -lt 1 -or $selectedsub -gt $subslist.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red 
            for($i=1; $i -le $subslist.Length;$i++)
             {
              Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
             }
            Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
           [int] $selectedsub=Read-Host
        }
        if($selectedsub -ge 1 -and $selectedsub -le $subslist.Length)
        {
        $mysubid=$subslist[$selectedsub-1].SubscriptionId
        $mysubName=$subslist[$selectedsub-1].SubscriptionName
        $mytenantId=$subslist[$selectedsub-1].TenantId
        Write-Host "$mysubName selected"
        }
        }
        catch
        {
        Write-Host $_.Exception.Message    
        Break
        }
    
    # Create a resource group
     Write-Host "Creating Resource Group $rgName"
     Create-ResourceGroup -rgName $rgName -location $location
     Write-Host "-----------------------------------" 
    
    # Create key vault and configure access
    New-AzResourceGroupDeployment -Name "kvdeploy$postfix" -ResourceGroupName $rgName -TemplateFile $kvTemplateJson -keyVaultName $kvname -tenantId $mytenantId -objectId $myobjectId
    
    Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $myobjectId -PermissionsToKeys all -PermissionsToSecrets all 
        
```

## <a name="store-the-certificate"></a>Armazenar o certificado

Agora você pode armazenar os certificados contidos no arquivo .pfx no novo cofre de chaves executando o script a seguir. 

```powershell
    #push certificate to key vault secret

     $fileName =$certroopath+"\$certname"+".pfx" 
      
     $fileContentBytes = get-content $fileName -Encoding Byte
     $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    
            $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certpassword"
    }
"@
            echo $certpassword
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $objAzureKeyVaultSecret=Set-AzureKeyVaultSecret -VaultName $kvname -Name "ISVSecret$postfix" -SecretValue $secret
            echo $objAzureKeyVaultSecret.Id 
    
```


## <a name="next-steps"></a>Próximas etapas

Em seguida, você [implantará uma VM da imagem de VM do usuário](./cpp-deploy-vm-user-image.md).
