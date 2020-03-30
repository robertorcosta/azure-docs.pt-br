---
title: Implantar uma VM Azure de um Usuário VHD | Mercado Azure
description: Explica como implantar uma imagem de VHD do usuário para criar uma instância de VM do Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 957bc187b0123338947f62333c913cf82a6c66a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277949"
---
# <a name="deploy-an-azure-vm-from-a-user-vhd"></a>Implantar uma VM do Azure de um VHD do usuário

Esse artigo explica como implantar uma imagem de VHD generalizada para criar um novo recurso de VM do Azure, usando o modelo do Azure Resource Manager fornecido e o script do Azure PowerShell.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vhd-deployment-template"></a>Modelo de implantação do VHD

Copie o modelo do Azure Resource Manager para [implantação de VHD](cpp-deploy-json-template.md) para um arquivo local chamado `VHDtoImage.json`.  Edite esse arquivo para fornecer valores para os parâmetros a seguir. 

|  **Parâmetro**             |   **Descrição**                                                              |
|  -------------             |   ---------------                                                              |
| ResourceGroupName          | O nome do grupo de recursos do Azure existente.  Normalmente usam o mesmo RG associado ao cofre de chaves  |
| TemplateFile               | Nome do caminho completo para o arquivo `VHDtoImage.json`                                    |
| userStorageAccountName     | Nome da conta de armazenamento                                                    |
| sNameForPublicIP           | Nome DNS do IP público. Precisa estar em letras minúsculas                                  |
| subscriptionId             | O identificador da assinatura                                                  |
| Location                   | Local geográfico padrão do Azure para o grupo de recursos                       |
| vmName                     | Nome da Máquina Virtual                                                    |
| vaultName                  | Nome do cofre de chaves                                                          |
| vaultResourceGroup         | Grupo de recursos do cofre de chaves
| certificateUrl             | URL do certificado, incluindo a versão armazenada no cofre de chaves, por exemplo:  `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
| vhdUrl                     | URL do disco rígido virtual                                                   |
| vmSize                     | Tamanho da instância de máquina virtual                                           |
| publicIPAddressName        | Nome do endereço IP público                                                  |
| virtualNetworkName         | Nome da rede virtual                                                    |
| nicName                    | Nome da placa de interface de rede para a rede virtual                     |
| adminUserName              | Nome de usuário da conta de administrador                                          |
| adminPassword              | Senha do administrador                                                          |
|  |  |


## <a name="powershell-script"></a>Script do PowerShell

Copie e edite o script a seguir para fornecer valores para as variáveis `$storageaccount` e `$vhdUrl`.  Executá-lo para criar um recurso de VM do Azure a partir de seu VHD generalizado existente.

```powershell
# storage account of existing generalized VHD 
$storageaccount = "testwinrm11815"

# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

#deploying VM with existing VHD
New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd 

```

## <a name="next-steps"></a>Próximas etapas

Depois que a VM for implantada, você estará pronto para [certificar a imagem de VM](./cpp-certify-vm.md).
