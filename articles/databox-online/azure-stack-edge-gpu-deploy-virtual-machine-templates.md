---
title: Implantar VMs em seu dispositivo de Azure Stack Edge por meio de modelos
description: Descreve como criar e gerenciar máquinas virtuais (VMs) em um dispositivo Azure Stack Edge usando modelos.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/04/2020
ms.author: alkohli
ms.openlocfilehash: 5b69d10bc2f3c5ec737e026059c82c3efac681b5
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268152"
---
# <a name="deploy-vms-on-your-azure-stack-edge-gpu-device-via-templates"></a>Implantar VMs em seu Azure Stack dispositivo de GPU de borda por meio de modelos

Este tutorial descreve como criar e gerenciar uma VM em seu dispositivo Azure Stack Edge usando modelos. Esses modelos são arquivos JavaScript Object Notation (JSON) que definem a infraestrutura e a configuração da sua VM. Nesses modelos, você especifica os recursos a serem implantados e as propriedades desses recursos.

Os modelos são flexíveis em ambientes diferentes, pois eles podem usar parâmetros como entrada no tempo de execução de um arquivo. A estrutura de nomenclatura padrão é `TemplateName.json` para o modelo e `TemplateName.parameters.json` para o arquivo de parâmetros. Para obter mais informações sobre modelos do ARM, acesse [o que são Azure Resource Manager modelos?](../azure-resource-manager/templates/overview.md).

Neste tutorial, usaremos modelos de exemplo pré-gravados para a criação de recursos. Você não precisará editar o arquivo de modelo e poderá modificar apenas os `.parameters.json` arquivos para personalizar a implantação em seu computador. 

## <a name="vm-deployment-workflow"></a>Fluxo de trabalho de implantação de VM

Para implantar Azure Stack VMs de borda em vários dispositivos, você pode usar um único VHD Sysprep para sua frota completa, o mesmo modelo para implantação e apenas fazer pequenas alterações nos parâmetros para esse modelo para cada local de implantação (essas alterações podem ser feitas manualmente, como estamos fazendo aqui ou programaticamente.) 

O resumo de alto nível do fluxo de trabalho de implantação usando modelos é o seguinte:

1. **Configurar os pré-requisitos** – há três tipos de pré-requisitos; dispositivo, cliente e para a VM.

    1. **Pré-requisitos do dispositivo**

        1. Conecte-se a Azure Resource Manager no dispositivo.
        2. Habilite a computação por meio da interface do usuário local.

    2. **Pré-requisitos do cliente**

        1. Baixe os modelos de VM e os arquivos associados no cliente.
        1. Opcionalmente, configure o TLS 1,2 no cliente.
        1. [Baixe e instale Gerenciador de armazenamento do Microsoft Azure](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409) em seu cliente.

    3. **Pré-requisitos de VM**

        1. Crie um grupo de recursos no local do dispositivo que conterá todos os recursos da VM.
        1. Crie uma conta de armazenamento para carregar o VHD usado para criar a imagem de VM.
        1. Adicione o URI da conta de armazenamento local ao arquivo DNS ou hosts no cliente que está acessando seu dispositivo.
        1. Instale o certificado de armazenamento de BLOBs no dispositivo, bem como no cliente local que está acessando seu dispositivo. Opcionalmente, instale o certificado de armazenamento de BLOBs no Gerenciador de Armazenamento.
        1. Crie e carregue um VHD para a conta de armazenamento criada anteriormente.

2. **Criar VM com base em modelos**

    1. Crie uma imagem de VM e uma VNet usando o `CreateImageAndVnet.parameters.json` modelo de arquivo e implantação de parâmetros `CreateImageAndVnet.json` .
    1. Crie uma VM com recursos criados anteriormente usando o `CreateVM.parameters.json` modelo de arquivo e implantação de parâmetros  `CreateVM.json` .

## <a name="device-prerequisites"></a>Pré-requisitos do dispositivo

Configure esses pré-requisitos em seu dispositivo Azure Stack Edge.

[!INCLUDE [azure-stack-edge-gateway-deploy-virtual-machine-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]

## <a name="client-prerequisites"></a>Pré-requisitos do cliente

Configure esses pré-requisitos em seu cliente que serão usados para acessar o dispositivo do Azure Stack Edge.

1. [Baixe Gerenciador de armazenamento](https://azure.microsoft.com/features/storage-explorer/) se você estiver usando-o para carregar um VHD. Como alternativa, você pode baixar o AzCopy para carregar um VHD. Talvez seja necessário configurar o TLS 1,2 no computador cliente se estiver executando versões mais antigas do AzCopy. 
1. [Baixe os modelos de VM e os arquivos de parâmetros](https://aka.ms/ase-vm-templates) em seu computador cliente. Descompacte-o em um diretório que você usará como um diretório de trabalho.


## <a name="vm-prerequisites"></a>Pré-requisitos de VM

Configure esses pré-requisitos para criar recursos que serão necessários para a criação da VM. 

    
### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure, como a conta de armazenamento, disco, disco gerenciado são implantados e gerenciados.

> [!IMPORTANT]
> Todos os recursos são criados no mesmo local que o do dispositivo e o local é definido como **DBELocal**.

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

Um exemplo de saída é mostrado abaixo.

```powershell
PS C:\windows\system32> New-AzureRmResourceGroup -Name myasegpurgvm -Location DBELocal

ResourceGroupName : myasegpurgvm
Location          : dbelocal
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/DDF9FC44-E990-42F8-9A91-5A6A5CC472DB/resourceGroups/myasegpurgvm

PS C:\windows\system32>
```

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Crie uma nova conta de armazenamento usando o grupo de recursos criado na etapa anterior. Esta é uma **conta de armazenamento local** que será usada para carregar a imagem de disco virtual para a VM.

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Somente as contas de armazenamento local, como o armazenamento com redundância local (Standard_LRS ou Premium_LRS), podem ser criadas via Azure Resource Manager. Para criar contas de armazenamento em camadas, consulte as etapas em [Adicionar, conectar-se a contas de armazenamento em seu Azure Stack Edge](azure-stack-edge-j-series-deploy-add-storage-accounts.md).

Um exemplo de saída é mostrado abaixo.

```powershell
PS C:\windows\system32> New-AzureRmStorageAccount -Name myasegpusavm -ResourceGroupName myasegpurgvm -Location DBELocal -SkuName Standard_LRS

StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime
------------------ ----------------- -------- -------     ----    ---------- ------------
myasegpusavm       myasegpurgvm      DBELocal StandardLRS Storage            7/29/2020 10:11:16 PM

PS C:\windows\system32>
```

Para obter a chave da conta de armazenamento, execute o `Get-AzureRmStorageAccountKey` comando. Uma saída de exemplo desse comando é mostrada aqui.

```powershell
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: myasegpurgvm
Name: myasegpusavm

KeyName    Value                                                  Permissions   
-------     -----                                                   --
key1 GsCm7QriXurqfqx211oKdfQ1C9Hyu5ZutP6Xl0dqlNNhxLxDesDej591M8y7ykSPN4fY9vmVpgc4ftkwAO7KQ== 11 
key2 7vnVMJUwJXlxkXXOyVO4NfqbW5e/5hZ+VOs+C/h/ReeoszeV+qoyuBitgnWjiDPNdH4+lSm1/ZjvoBWsQ1klqQ== ll
```

### <a name="add-blob-uri-to-hosts-file"></a>Adicionar URI de blob ao arquivo de hosts

Certifique-se de que você já adicionou o URI de blob no arquivo de hosts para o cliente que você está usando para se conectar ao armazenamento de BLOBs. **Execute o bloco de notas como administrador** e adicione a seguinte entrada para o URI de blob no `C:\windows\system32\drivers\etc\hosts` :

`<Device IP> <storage account name>.blob.<Device name>.<DNS domain>`

Em um ambiente típico, você teria o DNS configurado para que todas as contas de armazenamento apontem para o dispositivo de borda Azure Stack com uma `*.blob.devicename.domainname.com` entrada.

### <a name="optional-install-certificates"></a>Adicional Instalar certificados

Ignore esta etapa se você for se conectar via Gerenciador de Armazenamento usando *http*. Se você estiver usando *https*, precisará instalar os certificados apropriados no Gerenciador de armazenamento. Nesse caso, instale o certificado de ponto de extremidade de BLOB. Para obter mais informações, consulte como criar e carregar certificados em [gerenciar certificados](azure-stack-edge-j-series-manage-certificates.md). 

### <a name="create-and-upload-a-vhd"></a>Criar e carregar um VHD

Verifique se você tem uma imagem de disco virtual que você pode usar para carregar na etapa posterior. Siga as etapas em [criar uma imagem de VM](azure-stack-edge-j-series-create-virtual-machine-image.md). 

Copie as imagens de disco a serem usadas em blobs de páginas na conta de armazenamento local que você criou nas etapas anteriores. Você pode usar uma ferramenta como [Gerenciador de armazenamento](https://azure.microsoft.com/features/storage-explorer/) ou [AzCopy para carregar o VHD na conta de armazenamento](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md#upload-a-vhd) que você criou nas etapas anteriores. 

### <a name="use-storage-explorer-for-upload"></a>Usar Gerenciador de Armazenamento para carregar

1. Abra o Explorer do Armazenamento. Vá para **Editar** e verifique se o aplicativo está definido como **destino Azure Stack APIs**.

    ![Definir destino para Azure Stack APIs](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/set-target-apis-1.png)

2. Instale o certificado do cliente no formato PEM. Vá para **editar > certificados SSL > importar certificados**. Aponte para o certificado do cliente.

    ![Importar certificado de ponto de extremidade de armazenamento de BLOB](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/import-blob-storage-endpoint-certificate-1.png)

    - Se você estiver usando certificados gerados pelo dispositivo, baixe e converta o certificado do ponto de extremidade do armazenamento de BLOBs `.cer` em um `.pem` formato. Execute o seguinte comando. 
    
        ```powershell
        PS C:\windows\system32> Certutil -encode 'C:\myasegpu1_Blob storage (1).cer' .\blobstoragecert.pem
        Input Length = 1380
        Output Length = 1954
        CertUtil: -encode command completed successfully.
        ```
    - Se você estiver trazendo seu próprio certificado, use o certificado raiz da cadeia de assinatura no `.pem` formato.

3. Depois de importar o certificado, reinicie Gerenciador de Armazenamento para que as alterações entrem em vigor.

    ![Reiniciar o Gerenciador de Armazenamento](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/restart-storage-explorer-1.png)

4. No painel esquerdo, clique com o botão direito do mouse em **contas de armazenamento** e selecione **conectar ao armazenamento do Azure**. 

    ![Conectar-se ao armazenamento do Azure 1](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-1.png)

5. Selecione **Usar um nome e uma chave da conta de armazenamento**. Selecione **Avançar**.

    ![Conectar-se ao armazenamento do Azure 2](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-2.png)

6. Em **conectar com nome e chave**, forneça o **nome de exibição**, o nome da conta de **armazenamento**, a chave da **conta**de armazenamento do Azure. Selecione **outro** domínio de armazenamento e forneça a `<device name>.<DNS domain>` cadeia de conexão. Se você não instalou um certificado no Gerenciador de Armazenamento, marque a opção **usar http** . Selecione **Avançar**.

    ![Conectar-se com o nome e a chave](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-name-key-1.png)

7. Examine o **Resumo da conexão** e selecione **conectar**.

8. A conta de armazenamento aparece no painel esquerdo. Selecione e expanda a conta de armazenamento. Selecione **contêineres de blob**, clique com o botão direito do mouse e selecione **criar contêiner de blob**. Forneça um nome para o contêiner de BLOB.

9. Selecione o contêiner que você acabou de criar e, no painel direito, selecione **carregar > carregar arquivos**. 

    ![Carregar arquivo VHD 1](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-1.png)

10. Procure e aponte para o VHD que você deseja carregar nos **arquivos selecionados**. Selecione o **tipo de blob** como blob de **páginas** e selecione **carregar**.

    ![Carregar arquivo VHD 2](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-2.png)

11. Depois que o VHD for carregado no contêiner de BLOB, selecione o VHD, clique com o botão direito do mouse e selecione **Propriedades**. 

    ![Carregar arquivo VHD 3](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-3.png)

12. Copie e salve o **URI** , pois você o usará em etapas posteriores.

    ![Copiar URI](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/copy-uri-1.png)

<!--### Use AzCopy for upload

Before you use AzCopy, make sure that the [AzCopy is configured correctly](#configure-azcopy) for use with the blob storage REST API version that you are using with your Azure Stack Edge device.


```powershell
AzCopy /Source:<sourceDirectoryForVHD> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Y /S /V /NC:32  /BlobType:page /destType:blob 
```

> ![NOTE]
> Set `BlobType` to page for creating a managed disk out of VHD. Set `BlobType` to block when writing to tiered storage accounts using AzCopy.

You can download the disk images from the marketplace. For detailed steps, go to [Get the virtual disk image from Azure marketplace](azure-stack-edge-j-series-create-virtual-machine-image.md).

A sample output using AzCopy 7.3 is shown below. For more information on this command, go to [Upload VHD file to storage account using AzCopy](../devtest-labs/devtest-lab-upload-vhd-using-azcopy.md).


```powershell
AzCopy /Source:\\hcsfs\scratch\vm_vhds\linux\ /Dest:http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages /DestKey:gJKoyX2Amg0Zytd1ogA1kQ2xqudMHn7ljcDtkJRHwMZbMK== /Y /S /V /NC:32 /BlobType:page /destType:blob /z:2e7d7d27-c983-410c-b4aa-b0aa668af0c6
```-->

## <a name="create-image-and-vnet-for-your-vm"></a>Criar imagem e VNet para sua VM

Para criar uma imagem e uma rede virtual para sua VM, você precisará editar o `CreateImageAndVnet.parameters.json` arquivo de parâmetros e, em seguida, implantar o modelo `CreateImageAndVnet.json` que usa esse arquivo de parâmetro.


### <a name="edit-parameters-file"></a>Editar arquivo de parâmetros

O arquivo `CreateImageAndVnet.parameters.json` usa os seguintes parâmetros: 

```json
"parameters": {
        "imageName": {
            "value": "<Name for the VM iamge>"
        },
        "imageUri": {
      "value": "<Path to the VHD that you uploaded in the Storage account>"
        },
        "vnetName": {
            "value": "<Name for the virtual network where you will deploy the VM>"
        },
        "subnetName": {
            "value": "<Name for the subnet for the VNet>"
        },
        "addressPrefix": {
            "value": "<Address prefix for the virtual network>"
        },
        "subnetPrefix": {
            "value": "<Subnet prefix for the subnet for the Vnet>"
        }
    }
```

Edite o arquivo `CreateImageAndVnet.parameters.json` para incluir o seguinte para seu dispositivo Azure Stack Edge:

1. Forneça o tipo de sistema operacional correspondente ao VHD que será carregado. O tipo de sistema operacional pode ser Windows ou Linux.

    ```json
    "parameters": {
            "osType": {
              "value": "Windows"
            },
    ```

2. Altere o URI da imagem para o URI da imagem que você carregou na etapa anterior:

    ```json
    "imageUri": {
        "value": "https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd"
        },
    ```
    Se você estiver usando *http* com Gerenciador de armazenamento, altere-o para um URI *https* .

3. Altere o `addressPrefix` e o `subnetPrefix` . Na interface do usuário local do seu dispositivo, vá para a página **rede** . Localize a porta que você habilitou para computação. Obtenha o endereço IP da rede base e adicione a máscara de sub-rede para criar a notação CIDR. Se você tiver uma sub-rede 255.255.255.0 padrão, faça isso substituindo o último número do endereço IP por 0 e adicionando/24 ao final. Portanto, 10.126.68.0 com uma máscara de sub-rede 255.255.255.0 se torna 10.126.68.0/24. 
    
    ```json
    "addressPrefix": {
                "value": "10.126.68.0/24"
            },
            "subnetPrefix": {
                "value": "10.126.68.0/24"
            }
    ```  

4. Forneça o nome exclusivo da imagem, o nome da VNet e o nome da sub-rede para os parâmetros.

    Aqui está um exemplo de JSON que é usado neste artigo.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
      "parameters": {
        "osType": {
          "value": "Windows"
        },
        "imageName": {
          "value": "image1"
        },
        "imageUri": {
          "value": "https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd"
        },
        "vnetName": {
          "value": "vnet1"
        },
        "subnetName": {
          "value": "subnet1"
        },
        "addressPrefix": {
          "value": "10.126.68.0/24"
        },
        "subnetPrefix": {
          "value": "10.126.68.0/24"
        }
      }
    }
    ```
5. Salve o arquivo de parâmetros.


### <a name="deploy-template"></a>Implantar modelo 

Implante o modelo `CreateImageAndVnet.json` . Este modelo implanta os recursos de VNet e de imagem que serão usados para criar VMs na etapa posterior.

> [!NOTE]
> Ao implantar o modelo, se você receber um erro de autenticação, suas credenciais do Azure para esta sessão poderão ter expirado. Execute `login-AzureRM` novamente o comando para se conectar ao Azure Resource Manager em seu dispositivo do Azure Stack Edge novamente.

1. Execute o seguinte comando: 
    
    ```powershell
    $templateFile = "Path to CreateImageAndVnet.json"
    $templateParameterFile = "Path to CreateImageAndVnet.parameters.json"
    $RGName = "<Name of your resource group>"
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $RGName `
        -TemplateFile $templateFile `
        -TemplateParameterFile $templateParameterFile `
        -Name "<Name for your deployment>"
    ```

2. Verifique se a imagem e os recursos de VNet foram provisionados com êxito. Aqui está um exemplo de saída de uma imagem e VNet criadas com êxito.
    
    ```powershell
    PS C:\07-30-2020> login-AzureRMAccount -EnvironmentName aztest1 -TenantId c0257de7-538f-415c-993a-1b87a031879d
    
    Account               SubscriptionName              TenantId                             Environment
    -------               ----------------              --------                             -----------
    EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d aztest1
    
    PS C:\07-30-2020> $templateFile = "C:\07-30-2020\CreateImageAndVnet.json"
    PS C:\07-30-2020> $templateParameterFile = "C:\07-30-2020\CreateImageAndVnet.parameters.json"
    PS C:\07-30-2020> $RGName = "myasegpurgvm"
    PS C:\07-30-2020> New-AzureRmResourceGroupDeployment `
    >>     -ResourceGroupName $RGName `
    >>     -TemplateFile $templateFile `
    >>     -TemplateParameterFile $templateParameterFile `
    >>     -Name "Deployment1"
    
    DeploymentName          : Deployment1
    ResourceGroupName       : myasegpurgvm
    ProvisioningState       : Succeeded
    Timestamp               : 7/30/2020 5:53:32 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              osType           String                     Windows
                              imageName        String                     image1
                              imageUri         String
                              https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd
                              vnetName         String                     vnet1
                              subnetName       String                     subnet1
                              addressPrefix    String                     10.126.68.0/24
                              subnetPrefix     String                     10.126.68.0/24
    
    Outputs                 :
    DeploymentDebugLogLevel :
    
    PS C:\07-30-2020>
    ```
    
## <a name="create-vm"></a>Criar VM

### <a name="edit-parameters-file-to-create-vm"></a>Editar arquivo de parâmetros para criar a VM
 
Para criar uma VM, use o `CreateVM.parameters.json` arquivo de parâmetro. Ele usa os seguintes parâmetros.
    
```json
"vmName": {
            "value": "<Name for your VM>"
        },
        "adminUsername": {
            "value": "<Username to log into the VM>"
        },
        "Password": {
            "value": "<Password to log into the VM>"
        },
        "imageName": {
            "value": "<Name for your image>"
        },
        "vmSize": {
            "value": "<A supported size for your VM>"
        },
        "vnetName": {
            "value": "<Name for the virtual network you created earlier>"
        },
        "subnetName": {
            "value": "<Name for the subnet you created earlier>"
        },
        "nicName": {
            "value": "<Name for the network interface>"
        },
        "privateIPAddress": {
            "value": "<Private IP address, enter a static IP in the subnet created earlier or leave empty to assign DHCP>"
        },
        "IPConfigName": {
            "value": "<Name for the ipconfig associated with the network interface>"
        }
```    

Atribua parâmetros apropriados no `CreateVM.parameters.json` para seu dispositivo Azure Stack Edge.

1. Forneça um nome exclusivo, o nome da interface de rede e o nome do ipconfig. 
1. Insira um nome de usuário, uma senha e um tamanho de VM com suporte.
1. Dê o mesmo nome para **VnetName**, **subnetName**e **ImageName** , conforme fornecido nos parâmetros para `CreateImageAndVnet.parameters.json` . Por exemplo, se você tiver fornecido VnetName, subnetName e ImageName como **vnet1**, **subnet1**e **image1**, mantenha os mesmos valores para os parâmetros nesse modelo também.
1. Agora você precisará de um endereço IP estático para atribuir à VM que está na rede de sub-rede definida acima. Substitua **PrivateIPAddress** por esse endereço no arquivo de parâmetro. Para que a VM obtenha um endereço IP do servidor DCHP local, deixe o `privateIPAddress` valor em branco.  
    
    ```json
    "privateIPAddress": {
                "value": "5.5.153.200"
            },
    ```
    
4. Salve o arquivo de parâmetros.

    Aqui está um exemplo de JSON que é usado neste artigo.
    
    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "vmName": {
                "value": "mywindowsvm"
            },
            "adminUsername": {
                "value": "Administrator"
            },
            "Password": {
                "value": "Password1"
            },
            "imageName": {
                "value": "image1"
            },
            "vmSize": {
                "value": "Standard_D1_v2"
            },
            "vnetName": {
                "value": "vnet1"
            },
            "subnetName": {
                "value": "subnet1"
            },
            "nicName": {
                "value": "nic1"
            },
            "privateIPAddress": {
                "value": "10.126.68.186"
            },
            "IPConfigName": {
                "value": "ipconfig1"
            }
        }
    }
    ```      

### <a name="deploy-template-to-create-vm"></a>Implantar modelo para criar VM

Implante o modelo de criação de VM `CreateVM.json` . Este modelo cria uma interface de rede a partir da VNet existente e cria a VM a partir da imagem implantada.

1. Execute o seguinte comando: 
    
    ```powershell
    Command:
        
        $templateFile = "<Path to CreateVM.json>"
        $templateParameterFile = "<Path to CreateVM.parameters.json>"
        $RGName = "RG1"
             
        New-AzureRmResourceGroupDeployment `
            -ResourceGroupName $RGName `
            -TemplateFile $templateFile `
            -TemplateParameterFile $templateParameterFile `
            -Name "<DeploymentName>"
    ```   

    A criação da VM levará 15-20 minutos. Aqui está um exemplo de saída de uma VM criada com êxito.
    
    ```powershell
    PS C:\07-30-2020> $templateFile = "C:\07-30-2020\CreateWindowsVM.json"
        PS C:\07-30-2020> $templateParameterFile = "C:\07-30-2020\CreateWindowsVM.parameters.json"
        PS C:\07-30-2020> $RGName = "myasegpurgvm"
        PS C:\07-30-2020> New-AzureRmResourceGroupDeployment `
        >>     -ResourceGroupName $RGName `
        >>     -TemplateFile $templateFile `
        >>     -TemplateParameterFile $templateParameterFile `
        >>     -Name "Deployment2"    
        
        DeploymentName          : Deployment2
        ResourceGroupName       : myasegpurgvm
        ProvisioningState       : Succeeded
        Timestamp               : 7/30/2020 6:21:09 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                                  Name             Type                       Value
                                  ===============  =========================  ==========
                                  vmName           String                     MyWindowsVM
                                  adminUsername    String                     Administrator
                                  password         String                     Password1
                                  imageName        String                     image1
                                  vmSize           String                     Standard_D1_v2
                                  vnetName         String                     vnet1
                                  subnetName       String                     subnet1
                                  nicName          String                     Nic1
                                  ipConfigName     String                     ipconfig1
                                  privateIPAddress  String                    10.126.68.186
        
        Outputs                 :
        DeploymentDebugLogLevel :    
        
        PS C:\07-30-2020>
    ```   
 
7. Verifique se a VM foi provisionada com êxito. Execute o seguinte comando:

    `Get-AzureRmVm`


## <a name="connect-to-a-vm"></a>Como conectar-se a uma VM

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

<!--## Manage VM

The following section describes some of the common operations around the VM that you will create on your Azure Stack Edge device.

[!INCLUDE [azure-stack-edge-gateway-manage-vm](../../includes/azure-stack-edge-gateway-manage-vm.md)]-->


## <a name="supported-vm-sizes"></a>Tamanhos de VM com suporte

[!INCLUDE [azure-stack-edge-gateway-supported-vm-sizes](../../includes/azure-stack-edge-gateway-supported-vm-sizes.md)]

## <a name="unsupported-vm-operations-and-cmdlets"></a>Cmdlets e operações de VM sem suporte

Não há suporte para extensões, conjuntos de dimensionamento, conjuntos de disponibilidade, instantâneos.

<!--## Configure AzCopy

When you install the latest version of AzCopy, you will need to configure AzCopy to ensure that it matches the blob storage REST API version of your Azure Stack Edge device.

On the client used to access your Azure Stack Edge device, set up a global variable to match the blob storage REST API version.

### On Windows client 

`$Env:AZCOPY_DEFAULT_SERVICE_API_VERSION = "2017-11-09"`

### On Linux client

`export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09`

To verify if the environment variable for AzCopy was set correctly, take the following steps:

1. Run "azcopy env".
2. Find `AZCOPY_DEFAULT_SERVICE_API_VERSION` parameter. This should have the value you set in the preceding steps.-->


## <a name="next-steps"></a>Próximas etapas

[Azure Resource Manager cmdlets](https://docs.microsoft.com/powershell/module/azurerm.resources/?view=azurermps-6.13.0)
