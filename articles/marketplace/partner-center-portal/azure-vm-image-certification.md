---
title: Certificação de máquinavirtual Azure - Azure Marketplace
description: Aprenda a testar e enviar uma oferta de máquina virtual no mercado comercial.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 9bd7e40855f30612b90cf28365c0b1410cd3e3d8
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731118"
---
# <a name="azure-virtual-machine-vm-image-certification"></a>Certificação de imagem da máquina virtual Azure (VM)

> [!NOTE]
> Estamos mudando o gerenciamento de suas ofertas de VM do Portal de Parceiros na Nuvem para o Partner Center. Até que suas ofertas sejam migradas, continue a seguir as instruções em [Criar certificados para o Azure Key Vault](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-key-vault-cert) no Cloud Partner Portal para gerenciar suas ofertas.

Este artigo descreve como testar e enviar uma imagem de máquina virtual (VM) no mercado comercial para garantir que ela atenda aos mais recentes requisitos de publicação do Azure Marketplace.

Complete estas etapas antes de enviar sua oferta de VM:

1. Crie e implante certificados.
2. Implante uma VM Azure usando sua imagem generalizada.
3. Executar validações.

## <a name="create-and-deploy-certificates-for-azure-key-vault"></a>Crie e implante certificados para o Azure Key Vault

Esta seção descreve como criar e implantar os certificados auto-assinados necessários para configurar a conectividade WinRM (Windows Remote Management, gerenciamento remoto) do Windows para uma máquina virtual hospedada no Azure.

### <a name="create-certificates-for-azure-key-vault"></a>Criar certificados para o Azure Key Vault

Esse processo consiste em três etapas:

1. Criar o certificado de segurança.
2. Crie o Cofre de Chaves Do Azure para armazenar o certificado.
3. Guarde os certificados para o cofre principal.

Você pode usar um grupo de recursos do Azure novo ou existente para este trabalho.

#### <a name="create-the-security-certificate"></a>Crie o certificado de segurança

Editar e executar o script Azure PowerShell a seguir para criar o arquivo de certificado (.pfx) em uma pasta local. Substitua os valores pelos parâmetros mostrados na tabela a seguir.

| **Parâmetro** | **Descrição** |
| --- | --- |
| $certroopath | Pasta local para salvar o arquivo .pfx para. |
| $location | Uma das localizações geográficas padrão do Azure. |
| $vmName | Nome da máquina virtual planejada do Azure. |
| $certname | Nome do certificado; deve corresponder ao nome de domínio totalmente qualificado da VM planejada. |
| $certpassword | Senha para os certificados, deve coincidir com a senha usada para a VM planejada. |
| | |

```PowerShell
   # Certification creation script

    # pfx certification stored path
    $certroopath = "C:\certLocation"

    # location of the resource group
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
> Mantenha aberta e aberta a mesma sessão de console Azure PowerShell durante essas etapas para manter os valores dos vários parâmetros.

> [!WARNING]
> Se você salvar esse script, só salve-o em um local seguro porque ele contém informações de segurança (uma senha).

#### <a name="create-the-azure-key-vault-to-store-the-certificate"></a>Crie o cofre-chave do Azure para armazenar o certificado

Copie o conteúdo do modelo abaixo para um arquivo em sua máquina local. No exemplo do script abaixo, este recurso é `C:\certLocation\keyvault.json`).

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "defaultValue":"isvkv0001",
      "metadata": {
        "description": "Name of the Vault"
      }
    },
    "tenantId": {
      "type": "string",
      "defaultValue":"72f988bf-86f1-41af-91ab-2d7cd011db47",
      "metadata": {
        "description": "Tenant Id of the subscription. Get using Get-AzureSubscription cmdlet or Get Subscription API"
      }
    },
    "objectId": {
      "type": "string",
      "defaultValue":"d55739bf-d5d6-4ce0-be1c-49ade53c4315",
      "metadata": {
        "description": "Object Id of the AD user. Get using Get-AzureADUser or Get-AzureADServicePrincipal cmdlets"
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": ["all"],
      "metadata": {
        "description": "Permissions to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": ["all"],
      "metadata": {
        "description": "Permissions to secrets in the vault. Valid values are: all, get, set, list, and delete."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "SKU for the vault"
      }
    },
    "enableVaultForDeployment": {
      "type": "bool",
      "defaultValue": true,
      "allowedValues": [
        true,
        false
      ],
      "metadata": {
        "description": "Specifies if the vault is enabled for a VM deployment"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "[parameters('keyVaultName')]",
      "apiVersion": "2015-06-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "[parameters('enableVaultForDeployment')]",
        "tenantId": "[parameters('tenantId')]",
        "accessPolicies": [
          {
            "tenantId": "[parameters('tenantId')]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]"
            }
          }
        ],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        }
      }
    }
  ]
}

```

Editar e executar o seguinte script Azure PowerShell para criar um Azure Key Vault e o grupo de recursos associado. Substitua os valores pelos parâmetros mostrados na tabela a seguir

| **Parâmetro** | **Descrição** |
| --- | --- |
| $postfix | String numérica aleatória anexada a identificadores de implantação. |
| $rgName | Nome do Grupo de Recursos Do Azul (RG) para criar. |
| $location | Uma das localizações geográficas padrão do Azure. |
| $kvTemplateJson | Caminho do arquivo (keyvault.json) contendo modelo de Gerenciador de recursos para cofre de chaves. |
| $kvname | Nome do novo cofre de chaves.|
|   |   |

```PowerShell
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

#### <a name="store-the-certificates-to-the-key-vault"></a>Armazene os certificados no cofre principal

Armazene os certificados contidos no arquivo .pfx para o novo cofre de chaves usando este script:

```PowerShell
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

## <a name="deploy-an-azure-vm-using-your-generalized-image"></a>Implante uma VM Azure usando sua imagem generalizada

Esta seção descreve como implantar uma imagem VHD generalizada para criar um novo recurso Azure VM. Para este processo, usaremos o modelo azure Resource Manager fornecido e o script Azure PowerShell.

### <a name="prepare-an-azure-resource-manager-template"></a>Prepare um modelo de Gerenciador de recursos do Azure

Copie o seguinte modelo do Azure Resource Manager para implantação de VHD em um arquivo local chamado VHDtoImage.json. O próximo script solicitará a localização na máquina local para usar este JSON.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "userStorageAccountName": {
            "type": "string"
        },
        "userStorageContainerName": {
            "type": "string",
            "defaultValue": "vhds"
        },
        "dnsNameForPublicIP": {
            "type": "string"
        },
        "adminUserName": {
            "defaultValue": "isv",
            "type": "string"
        },
        "adminPassword": {
            "type": "securestring",
            "defaultValue": "Password@123"
        },
        "osType": {
            "type": "string",
            "defaultValue": "windows",
            "allowedValues": [
                "windows",
                "linux"
            ]
        },
        "subscriptionId": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "vmSize": {
            "type": "string"
        },
        "publicIPAddressName": {
            "type": "string"
        },
        "vmName": {
            "type": "string"
        },
        "virtualNetworkName": {
            "type": "string"
        },
        "nicName": {
            "type": "string"
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the KeyVault"
            }
        },
        "vaultResourceGroup": {
            "type": "string",
            "metadata": {
                "description": "Resource Group of the KeyVault"
            }
        },
        "certificateUrl": {
            "type": "string",
            "metadata": {
                "description": "Url of the certificate with version in KeyVault e.g. https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7"
            }
        },
        "vhdUrl": {
            "type": "string",
            "metadata": {
                "description": "VHD Url..."
            }
        }
    },
        "variables": {
            "addressPrefix": "10.0.0.0/16",
            "subnet1Name": "Subnet-1",
            "subnet2Name": "Subnet-2",
            "subnet1Prefix": "10.0.0.0/24",
            "subnet2Prefix": "10.0.1.0/24",
            "publicIPAddressType": "Dynamic",
            "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
            "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
        },
        "resources": [
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/publicIPAddresses",
                "name": "[parameters('publicIPAddressName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                    "dnsSettings": {
                        "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                    }
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/virtualNetworks",
                "name": "[parameters('virtualNetworkName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "addressSpace": {
                        "addressPrefixes": [
                            "[variables('addressPrefix')]"
                        ]
                    },
                    "subnets": [
                        {
                            "name": "[variables('subnet1Name')]",
                            "properties": {
                                "addressPrefix": "[variables('subnet1Prefix')]"
                            }
                        },
                        {
                            "name": "[variables('subnet2Name')]",
                            "properties": {
                                "addressPrefix": "[variables('subnet2Prefix')]"
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/networkInterfaces",
                "name": "[parameters('nicName')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                    "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "ipConfigurations": [
                        {
                            "name": "ipconfig1",
                            "properties": {
                                "privateIPAllocationMethod": "Dynamic",
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                                },
                                "subnet": {
                                    "id": "[variables('subnet1Ref')]"
                                }
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-06-15",
                "type": "Microsoft.Compute/virtualMachines",
                "name": "[parameters('vmName')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
                ],
                "properties": {
                    "hardwareProfile": {
                        "vmSize": "[parameters('vmSize')]"
                    },
                    "osProfile": {
                        "computername": "[parameters('vmName')]",
                        "adminUsername": "[parameters('adminUsername')]",
                        "adminPassword": "[parameters('adminPassword')]",
                        "secrets": [
                            {
                                "sourceVault": {
                                    "id": "[resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                                },
                                "vaultCertificates": [
                                    {
                                        "certificateUrl": "[parameters('certificateUrl')]",
                                        "certificateStore": "My"
                                    }
                                ]
                            }
                        ],
                        "windowsConfiguration": {
                            "provisionVMAgent": "true",
                            "winRM": {
                                "listeners": [
                                    {
                                        "protocol": "http"
                                    },
                                    {
                                        "protocol": "https",
                                        "certificateUrl": "[parameters('certificateUrl')]"
                                    }
                                ]
                            },
                            "enableAutomaticUpdates": "true"
                        }
                    },
                    "storageProfile": {
                        "osDisk": {
                            "name": "[concat(parameters('vmName'),'-osDisk')]",
                            "osType": "[parameters('osType')]",
                            "caching": "ReadWrite",
                            "image": {
                                "uri": "[parameters('vhdUrl')]"
                            },
                            "vhd": {
                                "uri": "[variables('osDiskVhdName')]"
                            },
                            "createOption": "FromImage"
                        }
                    },
                    "networkProfile": {
                        "networkInterfaces": [
                            {
                                "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                            }
                        ]
                    },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[concat('http://', parameters('userStorageAccountName'), '.blob.core.windows.net')]"
                    }
                }
                }
            }
        ]
    }

```

Edite este arquivo para fornecer valores para esses parâmetros:

| **Parâmetro** | **Descrição** |
| --- | --- |
| ResourceGroupName | O nome do grupo de recursos do Azure existente. Normalmente, use o mesmo RG do seu cofre de chaves. |
| TemplateFile | Nome completo do caminho para o arquivo VHDtoImage.json. |
| userStorageAccountName | Nome da conta de armazenamento. |
| sNameForPublicIP | Nome DNS para o IP público; deve ser minúsculo. |
| subscriptionId | Identificador de assinatura do Azure. |
| Location | Localização geográfica padrão do grupo de recursos do Azure. |
| vmName | Nome da máquina virtual. |
| vaultName | Nome do cofre da chave. |
| vaultResourceGroup | Grupo de recursos do cofre de chaves. |
| certificateUrl | Endereço web (URL) do certificado, incluindo a versão armazenada `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7`no cofre de chaves, por exemplo: . |
| vhdUrl | Endereço web do disco rígido virtual. |
| vmSize | Tamanho da instância da máquina virtual. |
| publicIPAddressName | Nome do endereço IP público. |
| virtualNetworkName | Nome da rede virtual. |
| nicName | Nome da placa de interface de rede para a rede virtual. |
| adminUserName | Nome de usuário da conta do administrador. |
| adminPassword | Senha de administrador. |
|   |   |

### <a name="deploy-an-azure-vm"></a>Implantar uma VM Azure

Copie e edite o seguinte `$storageaccount` script `$vhdUrl` para fornecer valores para as variáveis. Executá-lo para criar um recurso de VM do Azure a partir de seu VHD generalizado existente.

```PowerShell

# storage account of existing generalized VHD

$storageaccount = "testwinrm11815"

# generalized VHD URL

$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD

New-AzResourceGroupDeployment -Name"dplisvvm$postfix" -ResourceGroupName"$rgName" -TemplateFile"C:\certLocation\VHDtoImage.json" -userStorageAccountName"$storageaccount" -dnsNameForPublicIP"$vmName" -subscriptionId"$mysubid" -location"$location" -vmName"$vmName" -vaultName"$kvname" -vaultResourceGroup"$rgName" -certificateUrl$objAzureKeyVaultSecret.Id  -vhdUrl"$vhdUrl" -vmSize"Standard\_A2" -publicIPAddressName"myPublicIP1" -virtualNetworkName"myVNET1" -nicName"myNIC1" -adminUserName"isv" -adminPassword$pwd

```

## <a name="run-validations"></a>Executar validações

Existem duas maneiras de executar validações na imagem implantada:

- Use a ferramenta de teste de certificação para certificado azure
- Use a API de autoteste

### <a name="download-and-run-the-certification-test-tool"></a>Baixar e executar a ferramenta de teste de certificação

A Ferramenta de Teste de Certificação para Certificado Azure é executada em uma máquina Windows local, mas testa um Windows ou Linux VM baseado em Azure. Ele certifica que sua imagem VM do usuário pode ser usada com o Microsoft Azure e que as orientações e requisitos em torno da preparação do seu VHD foram cumpridos. A saída da ferramenta é um relatório de compatibilidade que você enviará ao portal do Partner Center para solicitar a certificação VM.

1. Baixe e instale a [Ferramenta de Teste de Certificação para Certificado do Azure](https://www.microsoft.com/download/details.aspx?id=44299) mais recente.
2. Abra a ferramenta de certificação e selecione **Start New Test**.
3. Na tela **Informações do Teste**, insira um **Nome de Teste** para a execução de teste.
4. Selecione a **plataforma** para sua VM, seja o Windows Server ou o Linux. A escolha da plataforma impactará nas opções restantes.
5. Se a VM estiver usando esse serviço de banco de dados, selecione a caixa de seleção **Teste para Banco de Dados SQL do Azure.**

### <a name="connect-the-certification-tool-to-a-vm-image"></a>Conectar a ferramenta de certificação a uma imagem da VM

A ferramenta se conecta a VMs baseados no Windows com [o Azure PowerShell](https://docs.microsoft.com/powershell/) e se conecta a VMs Linux através [de SSH.Net](https://www.ssh.com/ssh/protocol/).

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>Conectar a ferramenta de certificação a uma imagem da VM do Linux

1. Selecione o modo **de autenticação SSH:** Autenticação de senha ou autenticação de arquivo de chave.
2. Se usar autenticação baseada em senha, digite valores para o nome do **DNS vm,** **nome de usuário**e **senha**. Você também pode alterar o número padrão **da porta SSH.**

    ![Ferramenta de teste certificada do Azure, autenticação de senha da imagem Linux VM](media/avm-cert2.png)

3. Se estiver usando a autenticação baseada em arquivo, insira valores para o **Nome DNS da VM**, **Nome de usuário** e local da **Chave Privada**. Você também pode incluir uma **senha** ou alterar o número padrão **da porta SSH.**

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Conectar a ferramenta de certificação a uma Imagem VM baseada em Windows**

1. Digite o **nome DNS VM** totalmente qualificado (por exemplo, MyVMName.Cloudapp.net).
2. Insira os valores para o **Nome de Usuário** e **Senha**.

    ![Ferramenta de teste certificada do Azure, autenticação por senha da imagem VM baseada no Windows](media/avm-cert4.png)

### <a name="run-a-certification-test"></a>Executar um teste de certificação

Depois de dar os valores de parâmetro para sua imagem VM na ferramenta de certificação, selecione **Conexão de teste** para criar uma conexão válida com sua VM. Após verificar a conexão, selecione **Avançar** para iniciar o teste. Quando o teste é concluído, os resultados do teste são mostrados em uma tabela. A coluna Status mostra (Passe/Falha/Aviso) para cada teste. Se algum dos testes falhar, a imagem _não_ será certificada. Neste caso, revise os requisitos e mensagens de falha, faça as alterações sugeridas e execute o teste novamente.

Após a conclusão do teste automatizado, forneça informações adicionais sobre sua imagem vm nas duas guias da tela do **Questionário,** **Avaliação Geral** e **Personalização do Kernel**e selecione **Next**.

A última tela permite que você forneça mais informações, como informações de acesso ssh para uma imagem Linux VM, e uma explicação para quaisquer avaliações fracassadas se você estiver procurando exceções.

Por fim, selecione **Gerar relatório** para baixar os resultados do teste e registrar arquivos para os casos de teste executados, juntamente com suas respostas ao questionário. Salve os resultados no mesmo contêiner como seus VHDs.

## <a name="next-step"></a>Próxima etapa

- [Gerar um uri de identificadores de recursos uniformes (URI) para cada VHD](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-get-sas-uri)
