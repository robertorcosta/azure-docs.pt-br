---
title: Validação de imagem de máquina virtual do Azure – Azure Marketplace
description: Saiba como testar e enviar uma oferta de máquina virtual no Marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 08/14/2020
ms.openlocfilehash: 2d19098ec82fe9361154d798b981341a86decf97
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89647827"
---
# <a name="azure-virtual-machine-image-validation"></a>Validação de imagem de máquina virtual do Azure

Este artigo descreve como testar e enviar uma imagem de VM (máquina virtual) no Marketplace comercial para garantir que ela atenda aos requisitos mais recentes de publicação do Azure Marketplace.

Conclua estas etapas antes de enviar sua oferta de VM:

- Implante uma VM do Azure usando sua imagem generalizada.
- Execute as validações.

## <a name="deploy-an-azure-vm-using-your-generalized-image"></a>Implante uma VM do Azure usando sua imagem generalizada

Esta seção descreve como implantar uma imagem de VHD (disco rígido virtual) generalizado para criar um novo recurso de VM do Azure. Para esse processo, usaremos o modelo de Azure Resource Manager fornecido e Azure PowerShell script.

### <a name="prepare-an-azure-resource-manager-template"></a>Preparar um modelo do Azure Resource Manager

Esta seção descreve como criar e implantar uma imagem de VM (máquina virtual) fornecida pelo usuário. Você pode fazer isso fornecendo imagens VHD do sistema operacional e do disco de dados de um disco rígido virtual implantado pelo Azure. Essas etapas implantam a VM usando o VHD generalizado.

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Carregue o VHD do sistema operacional generalizado e os VHDs do disco de dados em sua conta de armazenamento do Azure.
3. Na home page, selecione **criar um recurso**, pesquise "implantação de modelo" e selecione **criar**.
4. Escolha **Criar seu modelo no editor**.

    :::image type="content" source="media/vm/template-deployment.png" alt-text="Mostra a seleção de um modelo.":::

5. Cole o modelo JSON a seguir no editor e selecione **salvar**.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "userStorageAccountName": {
            "type": "String"
        },
        "userStorageContainerName": {
            "defaultValue": "vhds",
            "type": "String"
        },
        "dnsNameForPublicIP": {
            "type": "String"
        },
        "adminUserName": {
            "defaultValue": "isv",
            "type": "String"
        },
        "adminPassword": {
            "defaultValue": "",
            "type": "SecureString"
        },
        "osType": {
            "defaultValue": "windows",
            "allowedValues": [
                "windows",
                "linux"
            ],
            "type": "String"
        },
        "subscriptionId": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "vmSize": {
            "type": "String"
        },
        "publicIPAddressName": {
            "type": "String"
        },
        "vmName": {
            "type": "String"
        },
        "virtualNetworkName": {
            "type": "String"
        },
        "nicName": {
            "type": "String"
        },
        "vhdUrl": {
            "type": "String",
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
        "hostDNSNameScriptArgument": "[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]",
        "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
    },
    "resources": [
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2015-06-15",
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
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2015-06-15",
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
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2015-06-15",
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
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2015-06-15",
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
                    "adminPassword": "[parameters('adminPassword')]"
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
                }
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "apiVersion": "2015-06-15",
            "name": "[concat(parameters('vmName'),'/WinRMCustomScriptExtension')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "CustomScriptExtension",
                "typeHandlerVersion": "1.4",
                "settings": {
                    "fileUris": [
                        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1",
                        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe",
                        "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd"
                    ],
                    "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -file ConfigureWinRM.ps1 ',variables('hostDNSNameScriptArgument'))]"
                }
            }
        }
    ]
}
```

<br>

6. Forneça os valores de parâmetro para as páginas de propriedades Implantação Personalizada.

| ResourceGroupName | O nome do grupo de recursos do Azure existente. Normalmente, usa o mesmo RG associado ao cofre de chaves. |
| --- | --- |
| TemplateFile | Nome do caminho completo para o arquivo VHDtoImage.json. |
| userStorageAccountName | Nome da conta de armazenamento. |
| dnsNameForPublicIP | Nome DNS para o IP público; deve estar em minúsculas. |
| subscriptionId | O identificador da assinatura do Azure. |
| Location | Local geográfico padrão do Azure para o grupo de recursos. |
| vmName | Nome da máquina virtual. |
| vhdUrl | Endereço Web do disco rígido virtual. |
| vmSize | Tamanho da instância de máquina virtual. |
| publicIPAddressName | Nome do endereço IP público. |
| virtualNetworkName | Nome da rede virtual. |
| nicName | Nome da placa de interface de rede para a rede virtual. |
| adminUserName | Nome de usuário da conta de administrador. |
| adminPassword | Senha do administrador. |
|

7. Depois de fornecer esses valores, selecione **Comprar**.

O Azure iniciará a implantação. Ele cria uma VM com o VHD não gerenciado especificado, no caminho de conta de armazenamento especificada. Você pode acompanhar o progresso no portal do Azure selecionando **Máquinas Virtuais** no lado esquerdo do portal. Quando a VM tiver sido criada, o status será alterado de Iniciando para Em execução.

#### <a name="for-generation-2-vm-deployment-use-this-template"></a>Para a implantação de VM de geração 2, use este modelo:

```JSON
{
   "$schema":"https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "parameters":{
      "userStorageAccountName":{
         "type":"String"
      },
      "userStorageContainerName":{
         "type":"String",
         "defaultValue":"vhds-86350-720-f4efbbb2-611b-4cd7-ad1e-afdgfuadfluad"
      },
      "dnsNameForPublicIP":{
         "type":"String"
      },
      "adminUserName":{
         "defaultValue":"isv",
         "type":"String"
      },
      "adminPassword":{
         "type":"securestring",
         "defaultValue":"Certcare@86350"
      },
      "osType":{
         "type":"string",
         "defaultValue":"linux",
         "allowedValues":[
            "windows",
            "linux"
         ]
      },
      "subscriptionId":{
         "type":"string"
      },
      "location":{
         "type":"string"
      },
      "vmSize":{
         "type":"string"
      },
      "publicIPAddressName":{
         "type":"string"
      },
      "vmName":{
         "type":"string"
      },
      "vNetNewOrExisting":{
         "defaultValue":"existing",
         "allowedValues":[
            "new",
            "existing"
         ],
         "type":"String",
         "metadata":{
            "description":"Specify whether to create a new or existing virtual network for the VM."
         }
      },
      "virtualNetworkName":{
         "type":"String",
         "defaultValue":""
      },
      "SubnetName":{
         "defaultValue":"subnet-5",
         "type":"String"
      },
      "SubnetPrefix":{
         "defaultValue":"10.0.5.0/24",
         "type":"String"
      },
      "nicName":{
         "type":"string"
      },
      "vhdUrl":{
         "type":"string",
         "metadata":{
            "description":"VHD Url..."
         }
      },
      "Datadisk1":{
         "type":"string",
         "metadata":{
            "description":"datadisk1 Url..."
         }
      },
      "Datadisk2":{
         "type":"string",
         "metadata":{
            "description":"datadisk2 Url..."
         }
      },
      "Datadisk3":{
         "type":"string",
         "metadata":{
            "description":"datadisk2 Url..."
         }
      }
   },
   "variables":{
      "addressPrefix":"10.0.0.0/16",
      "subnet1Name":"[parameters('SubnetName')]",
      "subnet1Prefix":"[parameters('SubnetPrefix')]",
      "publicIPAddressType":"Static",
      "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
      "subnet1Ref":"[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
      "osDiskVhdName":"[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]",
      "dataDiskVhdName":"[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'datadisk')]",
      "imageName":"[concat(parameters('vmName'), '-image')]"
   },
   "resources":[
      {
         "apiVersion":"2015-05-01-preview",
         "type":"Microsoft.Network/publicIPAddresses",
         "name":"[parameters('publicIPAddressName')]",
         "location":"[parameters('location')]",
         "properties":{
            "publicIPAllocationMethod":"[variables('publicIPAddressType')]",
            "dnsSettings":{
               "domainNameLabel":"[parameters('dnsNameForPublicIP')]"
            }
         }
      },
      {
         "type":"Microsoft.Compute/images",
         "apiVersion":"2019-12-01",
         "name":"[variables('imageName')]",
         "location":"[parameters('location')]",
         "properties":{
            "storageProfile":{
               "osDisk":{
                  "osType":"[parameters('osType')]",
                  "osState":"Generalized",
                  "blobUri":"[parameters('vhdUrl')]",
                  "storageAccountType":"Standard_LRS"
               },
               "dataDisks":[
                  {
                     "lun":0,
                     "blobUri":"[parameters('Datadisk1')]",
                     "storageAccountType":"Standard_LRS"
                  },
                  {
                     "lun":1,
                     "blobUri":"[parameters('Datadisk2')]",
                     "storageAccountType":"Standard_LRS"
                  },
                  {
                     "lun":2,
                     "blobUri":"[parameters('Datadisk3')]",
                     "storageAccountType":"Standard_LRS"
                  }
               ]
            },
            "hyperVGeneration":"V2"
         }
      },
      {
         "apiVersion":"2015-05-01-preview",
         "type":"Microsoft.Network/virtualNetworks",
         "name":"[parameters('virtualNetworkName')]",
         "location":"[parameters('location')]",
         "properties":{
            "addressSpace":{
               "addressPrefixes":[
                  "[variables('addressPrefix')]"
               ]
            },
            "subnets":[
               {
                  "name":"[variables('subnet1Name')]",
                  "properties":{
                     "addressPrefix":"[variables('subnet1Prefix')]"
                  }
               }
            ]
         },
         "condition":"[equals(parameters('vNetNewOrExisting'), 'new')]"
      },
      {
         "apiVersion":"2016-09-01",
         "type":"Microsoft.Network/networkInterfaces",
         "name":"[parameters('nicName')]",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
            "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
         ],
         "properties":{
            "ipConfigurations":[
               {
                  "name":"ipconfig1",
                  "properties":{
                     "privateIPAllocationMethod":"Dynamic",
                     "publicIPAddress":{
                        "id":"[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                     },
                     "subnet":{
                        "id":"[variables('subnet1Ref')]"
                     }
                  }
               }
            ]
         }
      },
      {
         "apiVersion":"2019-12-01",
         "type":"Microsoft.Compute/virtualMachines",
         "name":"[parameters('vmName')]",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]",
            "[variables('imageName')]"
         ],
         "properties":{
            "hardwareProfile":{
               "vmSize":"[parameters('vmSize')]"
            },
            "osProfile":{
               "computername":"[parameters('vmName')]",
               "adminUsername":"[parameters('adminUsername')]",
               "adminPassword":"[parameters('adminPassword')]"
            },
            "storageProfile":{
               "imageReference":{
                  "id":"[resourceId('Microsoft.Compute/images', variables('imageName'))]"
               },
               "dataDisks":[
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk0')]",
                     "lun":0,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  },
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk1')]",
                     "lun":1,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  },
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk2')]",
                     "lun":2,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  }
               ],
               "osDisk":{
                  "name":"[concat(parameters('vmName'),'-OSDisk')]",
                  "createOption":"FromImage",
                  "managedDisk":{
                     "storageAccountType":"Standard_LRS"
                  }
               }
            },
            "networkProfile":{
               "networkInterfaces":[
                  {
                     "id":"[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                  }
               ]
            }
         }
      }
   ]
}
```

### <a name="deploy-an-azure-vm-using-powershell"></a>Implantar uma VM do Azure usando o PowerShell

Copie e edite o script a seguir para fornecer valores para as variáveis `$storageaccount` e `$vhdUrl`. Executá-lo para criar um recurso de VM do Azure a partir de seu VHD generalizado existente.

```PowerShell
# storage account of existing generalized VHD

$storageaccount = "testwinrm11815" # generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl
$objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD

New-AzResourceGroupDeployment -Name"dplisvvm$postfix" -ResourceGroupName"$rgName" -TemplateFile"C:\certLocation\VHDtoImage.json" - userStorageAccountName"$storageaccount" -dnsNameForPublicIP"$vmName" -subscriptionId"$mysubid" -location"$location" - vmName"$vmName" -vaultName"$kvname" -vaultResourceGroup"$rgName" -certificateUrl$objAzureKeyVaultSecret.Id -vhdUrl"$vhdUrl" - vmSize"Standard\_A2" -publicIPAddressName"myPublicIP1" -virtualNetworkName"myVNET1" -nicName"myNIC1" -adminUserName"isv" - adminPassword$pwd
```

## <a name="run-validations"></a>Executar as validações

Há duas maneiras de executar validações na imagem implantada.

### <a name="use-certification-test-tool-for-azure-certified"></a>Use a Ferramenta do teste de certificação para o Certificado Azure

#### <a name="download-and-run-the-certification-test-tool"></a>Baixar e executar a ferramenta de teste de certificação

A Ferramenta de Teste de Certificação para Certificado do Azure executa em uma máquina Windows local, mas testa uma VM do Linux ou do Windows baseado no Azure. Ela verifica se a imagem da VM do usuário pode ser usada com o Microsoft Azure, ou seja, se as diretrizes e os requisitos de preparação do VHD foram cumpridos.

1. Baixe e instale a [Ferramenta de Teste de Certificação para Certificado do Azure](https://www.microsoft.com/download/details.aspx?id=44299) mais recente.
2. Abra a ferramenta de certificação e, em seguida, clique em **Iniciar Novo Teste**.
3. Na tela Informações do Teste, insira um **Nome de Teste** para a execução de teste.
4. Selecione a plataforma para sua VM, **Windows Server** ou **Linux**. A escolha da plataforma impactará nas opções restantes.
5. Se a VM estiver usando esse serviço de banco de dados, marque a caixa de seleção **Testar para Banco de Dados SQL do Azure**.

#### <a name="connect-the-certification-tool-to-a-vm-image"></a>Conectar a ferramenta de certificação a uma imagem da VM

1. Selecione o modo Autenticação SSH: Autenticação de senha ou de arquivo de chave.
2. Se estiver usando a autenticação baseada em senha, insira valores para o **nome DNS da VM**, o **nome de usuário**e a **senha**. É possível alterar o número da Porta SSH padrão.

    :::image type="content" source="media/vm/azure-vm-cert-2.png" alt-text="Mostra a seleção de informações de teste da VM.":::

3. Se estiver usando a autenticação baseada em arquivo, insira valores para o Nome DNS da VM, Nome de usuário e local da Chave Privada. Você também pode incluir uma frase secreta ou alterar o número da Porta SSH.
4. Insira o nome DNS totalmente qualificado da VM (por exemplo, MyVMName.Cloudapp.net).
5. Insira o **nome de usuário** e a **senha**.

    :::image type="content" source="media/vm/azure-vm-cert-4.png" alt-text="Mostra a seleção de nome de usuário e senha da VM.":::

6. Selecione **Avançar**.

#### <a name="run-a-certification-test"></a>Executar um teste de certificação

Depois de obter os valores de parâmetro para sua imagem de VM na ferramenta de certificação, selecione testar conexão para criar uma conexão válida com sua VM. Após verificar a conexão, selecione **Avançar** para iniciar o teste. Quando o teste é concluído, os resultados são exibidos em uma tabela. A coluna status mostra (aprovação/falha/aviso) para cada teste. Se algum dos testes falhar, a imagem não será certificada. Nesse caso, revise os requisitos e as mensagens de falha, faça as alterações sugeridas e execute novamente o teste.

Após a conclusão do teste automatizado, forneça informações adicionais sobre a imagem da VM nas duas guias da tela Questionário, Avaliação geral e Personalização do kernel e, em seguida, selecione Próximo.

A última tela permite que você forneça mais informações, como informações de acesso SSH para uma imagem de VM do Linux e uma explicação para quaisquer avaliações com falha se você estiver procurando por exceções.

Por fim, selecione Gerar Relatório para baixar os resultados do teste e os arquivos de log dos casos de teste juntamente com as respostas ao questionário. Salve os resultados no mesmo contêiner como seus VHDs.

## <a name="how-to-use-powershell-to-consume-the-self-test-api"></a>Como usar o PowerShell para consumir a API de teste automático

### <a name="on-linux-os"></a>No SO Linux

Chame a API no PowerShell:

1. Use o comando Invoke-WebRequest para chamar a API.
2. O método é Post e o tipo de conteúdo é JSON, conforme mostrado no exemplo de código e na captura de tela a seguir.
3. Especifique parâmetros de corpo no formato JSON.

Este exemplo a seguir mostra uma chamada do PowerShell para a API:

```POWERSHELL
$accesstoken = “token”
$headers = New-Object “System.Collections.Generic.Dictionary[[String],[String]]”
$headers.Add(“Authorization”, “Bearer $accesstoken”)
$DNSName = “\&lt;\&lt;Machine DNS Name\&gt;\&gt;”
$UserName = “\&lt;\&lt;User ID\&gt;\&gt;”
$Password = “\&lt;\&lt;Password\&gt;\&gt;”
$OS = “Linux”
$PortNo = “22”
$CompanyName = “ABCD”
$AppID = “\&lt;\&lt;Application ID\&gt;\&gt;”
$TenantId = “\&lt;\&lt;Tenant ID\&gt;\&gt;”

$body =
@{
DNSName = $DNSName
UserName = $UserName
Password = $Password
OS = $OS
PortNo = $PortNo
CompanyName = $CompanyName
AppID = $AppID
TenantId = $TenantId
}| ConvertTo-Json

$body

$uri = “URL”

$res = (Invoke-WebRequest -Method “Post” -Uri $uri -Body $body -ContentType “application/json” -Headers $headers).Content
```

<br>Aqui está um exemplo de como chamar a API no PowerShell:

[![Exemplo de tela para chamar a API no PowerShell.](media/vm/call-api-in-powershell.png)](media/vm/call-api-in-powershell.png#lightbox)

<br>Usando o exemplo anterior, você poderá recuperar o JSON e analisá-lo para obter os seguintes detalhes:

```PowerShell
$resVar=$res|ConvertFrom-Json

$actualresult =$resVar.Response |ConvertFrom-Json

Write-Host”OSName: $($actualresult.OSName)”Write-Host”OSVersion: $($actualresult.OSVersion)”Write-Host”Overall Test Result: $($actualresult.TestResult)”For ($i=0; $i -lt$actualresult.Tests.Length; $i++){ Write-Host”TestID: $($actualresult.Tests[$i].TestID)”Write-Host”TestCaseName: $($actualresult.Tests[$i].TestCaseName)”Write-Host”Description: $($actualresult.Tests[$i].Description)”Write-Host”Result: $($actualresult.Tests[$i].Result)”Write-Host”ActualValue: $($actualresult.Tests[$i].ActualValue)”}
```

<br>Esta tela de exemplo, que mostra `$res.Content` , mostra detalhes dos resultados do teste no formato JSON:

[![Exemplo de tela para chamar a API no PowerShell com detalhes de resultados de teste.](media/vm/call-api-in-powershell-details.png)](media/vm/call-api-in-powershell-details.png#lightbox)

<br>Aqui está um exemplo de resultados de teste JSON exibidos em um visualizador JSON online (como [código Beautify](https://codebeautify.org/jsonviewer) ou [Visualizador JSON](https://jsonformatter.org/json-viewer)).

![Mais resultados de teste em um visualizador JSON online.](media/vm/test-results-json-viewer-1.png)

### <a name="on-windows-os"></a>No sistema operacional Windows

Chame a API no PowerShell:

1. Use o comando Invoke-WebRequest para chamar a API.
2. O método é post e o tipo de conteúdo é JSON, conforme mostrado no exemplo de código e na tela de exemplo a seguir.
3. Crie os parâmetros de corpo no formato JSON.

Este exemplo de código mostra uma chamada do PowerShell para a API:

```PowerShell
$accesstoken = “Get token for your Client AAD App”$headers = New-Object”System.Collections.Generic.Dictionary[[String],[String]]”$headers.Add(“Authorization”, “Bearer $accesstoken”)$Body = @{ “DNSName” = “XXXX.westus.cloudapp.azure.com”“UserName” = “XXX”“Password” = “XXX@123456”“OS” = “Windows”“PortNo” = “5986”“CompanyName” = “ABCD” “AppID” = “XXXX-XXXX-XXXX” “TenantId” = “XXXX-XXXX-XXXX” } | ConvertTo-Json$res = Invoke-WebRequest -Method”Post” -Uri$uri -Body$Body -ContentType”application/json” –Headers $headers;$Content = $res | ConvertFrom-Json
```

Essas telas de exemplo mostram o exemplo para chamar a API no PowerShell:

**Com chave SSH**:

 :::image type="content" source="media/vm/call-api-with-ssh-key.png" alt-text="Chamar a API no PowerShell com uma chave SSH.":::

**Com a senha**:

 :::image type="content" source="media/vm/call-api-with-password.png" alt-text="Chamar a API no PowerShell com uma senha.":::

<br>Usando o exemplo anterior, você poderá recuperar o JSON e analisá-lo para obter os seguintes detalhes:

```PowerShell
$resVar=$res|ConvertFrom-Json

$actualresult =$resVar.Response |ConvertFrom-Json

Write-Host”OSName: $($actualresult.OSName)”Write-Host”OSVersion: $($actualresult.OSVersion)”Write-Host”Overall Test Result: $($actualresult.TestResult)”For ($i=0; $i -lt$actualresult.Tests.Length; $i++){ Write-Host”TestID: $($actualresult.Tests[$i].TestID)”Write-Host”TestCaseName: $($actualresult.Tests[$i].TestCaseName)”Write-Host”Description: $($actualresult.Tests[$i].Description)”Write-Host”Result: $($actualresult.Tests[$i].Result)”Write-Host”ActualValue: $($actualresult.Tests[$i].ActualValue)”}
```

<br>Essa tela, que mostra `$res.Content` , mostra os detalhes dos resultados do teste no formato JSON:

 :::image type="content" source="media/vm/test-results-json-format.png" alt-text="Detalhes dos resultados de teste no formato JSON.":::

<br>Aqui está um exemplo de resultados de teste exibidos em um visualizador JSON online (como [código Beautify](https://codebeautify.org/jsonviewer) ou [Visualizador JSON](https://jsonformatter.org/json-viewer)).

![Resultados de teste em um visualizador JSON online.](media/vm/test-results-json-viewer-2.png)

## <a name="how-to-use-curl-to-consume-the-self-test-api-on-linux-os"></a>Como usar a rotação para consumir a API de teste automático no sistema operacional Linux

Chame a API em ONDULAção:

1. Use o comando curl para chamar a API.
2. O método é Post e o tipo de conteúdo é JSON, conforme mostrado no snippet de código a seguir.

```JSON
CURL POST -H “Content-Type:application/json”

-H “Authorization: Bearer XXXXXX-Token-XXXXXXXX”

[https://isvapp.azure-api.net/selftest-vm](https://isvapp.azure-api.net/selftest-vm)

-d ‘{ “DNSName”:”XXXX.westus.cloudapp.azure.com”, “UserName”:”XXX”, “Password”:”XXXX@123456”, “OS”:”Linux”, “PortNo”:”22”, “CompanyName”:”ABCD”, “AppId”:”XXXX-XXXX-XXXX”, “TenantId “XXXX-XXXX-XXXX”}’
```

<br>Aqui está um exemplo de uso de ONDULAção para chamar a API:

![Exemplo de uso de ONDULAção para chamar a API.](media/vm/use-curl-call-api.png)

<br>Aqui estão os resultados JSON da chamada de ONDULAção:

![Resultados JSON da chamada de ONDULAção.](media/vm/test-results-json-viewer-3.png)

## <a name="next-step"></a>Próxima etapa

- Leia [problemas e correções do URI de SAS comuns](common-sas-uri-issues.md).
