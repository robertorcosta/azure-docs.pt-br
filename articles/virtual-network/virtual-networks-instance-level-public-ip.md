---
title: Endereços IP públicos (Clássico) em nível de instância do Azure | Microsoft Docs
description: Entenda os endereços ILPIP (IP públicos no nível de instância) e como gerenciá-los usando o PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: 07eef6ec-7dfe-4c4d-a2c2-be0abfb48ec5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2018
ms.author: genli
ms.openlocfilehash: 74d10c8fbe2f82d6148f5e13cb57c46dd645f76f
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979549"
---
# <a name="instance-level-public-ip-classic-overview"></a>Visão geral de IP público em nível de instância (Clássico)
Um ILPIP (IP público em nível de instância) é um endereço IP público que pode ser atribuído diretamente a uma VM ou instância de função dos Serviços de Nuvem, em vez de ao serviço de nuvem no qual a VM ou a instância de função reside. Ele não substitui o VIP (IP virtual) que é atribuído ao serviço de nuvem. Ao contrário, é um endereço IP adicional que você pode usar para se conectar diretamente à sua VM ou instância de função.

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e clássico](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Este artigo aborda o uso do modelo de implantação clássica. A Microsoft recomenda a criação de VMs por meio do Resource Manager. Certifique-se de que você entenda como os [endereços IP](virtual-network-ip-addresses-overview-classic.md) funcionam no Azure.

![Diferença entre ILPIP e VIP](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Conforme mostrado na Figura 1, o serviço de nuvem é acessado por meio de um VIP, enquanto as VMs individuais são normalmente acessadas usando VIP:&lt;número da porta&gt;. Ao atribuir um ILPIP a uma VM específica, essa VM pode ser acessada diretamente por meio desse endereço IP.

Quando você cria um serviço de nuvem no Azure, registros DNS A correspondentes são criados automaticamente para permitir o acesso ao serviço por meio de um FQDN (nome de domínio totalmente qualificado) em vez de usar o VIP real. O mesmo processo ocorre para um ILPIP, permitindo o acesso à VM ou à instância de função pelo FQDN, em vez do ILPIP. Por exemplo, se você criar um serviço de nuvem chamado *contosoadservice* e configurar uma função web chamada *contosoweb* com duas instâncias, e no .cscfg `domainNameLabel` for definido como *WebPublicIP*, o Azure registrará os seguintes registros A para as instâncias:


* WebPublicIP.0.contosoadservice.cloudapp.net
* WebPublicIP.1.contosoadservice.cloudapp.net
* ...


> [!NOTE]
> Você pode atribuir apenas um ILPIP para cada VM ou instância de função. É possível usar até 5 ILPIPs por assinatura. Não há suporte para ILPIPs em VMs com várias NICs.
> 
> 

## <a name="why-would-i-request-an-ilpip"></a>Por que solicitar um ILPIP?
Se você quiser ser capaz de conectar a sua VM ou instância de função por um endereço IP atribuído diretamente a ela, em vez de usar o &lt;número da porta&gt; do VIP de serviço de nuvem, solicite um ILPIP para sua VM ou instância de função.

* **FTP Ativo** – com a atribuição de um ILPIP a uma VM, ele pode receber o tráfego em qualquer porta. Pontos de extremidade não são necessários para que a VM receba o tráfego.  Veja [Visão Geral do Protocolo FTP](https://en.wikipedia.org/wiki/File_Transfer_Protocol#Protocol_overview) para detalhes sobre o protocolo FTP.
* **IP de saída** – O tráfego de saída proveniente da VM é mapeado para o ILPIP, conforme a fonte e o ILPIP identificam exclusivamente a VM para entidades externas.

> [!NOTE]
> Anteriormente, um endereço ILPIP era conhecido como um endereço PIP (IP público).
> 

## <a name="manage-an-ilpip-for-a-vm"></a>Gerenciar um ILPIP de uma VM
As seguintes tarefas permitem criar, atribuir e remover ILPIPs de VMs:

### <a name="how-to-request-an-ilpip-during-vm-creation-using-powershell"></a>Como solicitar um ILPIP durante a criação da VM usando PowerShell
O seguinte script do PowerShell cria um serviço de nuvem chamado *FTPService*, recupera uma imagem do Azure, cria uma VM chamada *FTPInstance* usando a imagem recuperada, define a VM para usar um ILPIP e adiciona a VM ao novo serviço:

```powershell
New-AzureService -ServiceName FTPService -Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

#Set "current" storage account for the subscription. It will be used as the location of new VM disk

Set-AzureSubscription -SubscriptionName <SubName> -CurrentStorageAccountName <StorageAccountName>

#Create a new VM configuration object

New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"

```
Se você quiser especificar outra conta de armazenamento como o local do novo disco VM, você pode usar o parâmetro **MediaLocation**:

```powershell
    New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
     -MediaLocation https://management.core.windows.net/<SubscriptionID>/services/storageservices/<StorageAccountName> `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"
```

### <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>Como recuperar informações de ILPIP para uma máquina virtual
Para exibir as informações do ILPIP para a VM criada com o script anterior, execute o seguinte comando do PowerShell e observe os valores de *PublicIPAddress* e *PublicIPName*:

```powershell
Get-AzureVM -Name FTPInstance -ServiceName FTPService
```

Saída esperada:
 
    DeploymentName              : FTPService
    Name                        : FTPInstance
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : ReadyRole
    IpAddress                   : 100.74.118.91
    InstanceStateDetails        : 
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : FTPInstance
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : FTPInstance
    AvailabilitySetName         : 
    DNSName                     : http://ftpservice888.cloudapp.net/
    Status                      : ReadyRole
    GuestAgentStatus            :   Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 104.43.142.188
    PublicIPName                : ftpip
    NetworkInterfaces           : {}
    ServiceName                 : FTPService
    OperationDescription        : Get-AzureVM
    OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
    OperationStatus             : OK

### <a name="how-to-remove-an-ilpip-from-a-vm"></a>Como remover um ILPIP de uma máquina virtual
Para remover o ILPIP adicionado à VM no script anterior, execute o seguinte comando do PowerShell:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Remove-AzurePublicIP | Update-AzureVM
```

### <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>Como adicionar um ILPIP a uma VM existente
Para adicionar um ILPIP à VM criada usando o script anterior, execute o seguinte comando:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Set-AzurePublicIP -PublicIPName ftpip2 | Update-AzureVM
```

## <a name="manage-an-ilpip-for-a-cloud-services-role-instance"></a>Gerenciar um ILPIP de uma instância de função dos Serviços de Nuvem

Para adicionar um ILPIP a uma instância de função dos Serviços de Nuvem, conclua as seguintes etapas:

1. Baixe o arquivo .cscfg do serviço de nuvem concluindo as etapas do artigo [Como configurar os Serviços de Nuvem](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg).
2. Atualize o arquivo .cscfg adicionando o elemento `InstanceAddress`. A seguinte amostra adiciona um ILPIP chamado *MyPublicIP* a uma instância de função chamada *WebRole1*: 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ILPIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
          <ConfigurationSettings>
        <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
          </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <InstanceAddress roleName="WebRole1">
        <PublicIPs>
          <PublicIP name="MyPublicIP" domainNameLabel="WebPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>
    ```
3. Carregue o arquivo .cscfg do serviço de nuvem concluindo as etapas do artigo [Como configurar os Serviços de Nuvem](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg).

### <a name="how-to-retrieve-ilpip-information-for-a-cloud-service"></a>Como recuperar informações de ILPIP para um Serviço de Nuvem
Para exibir as informações de ILPIP da instância de função, execute o seguinte comando do PowerShell e observe os valores de *PublicIPAddress*, *PublicIPName*, *PublicIPDomainNameLabel* e *PublicIPFqdns*:

```powershell
Add-AzureAccount

$roles = Get-AzureRole -ServiceName <Cloud Service Name> -Slot Production -RoleName WebRole1 -InstanceDetails

$roles[0].PublicIPAddress
$roles[1].PublicIPAddress
```

Você também pode usar `nslookup` para consultar o registro A do subdomínio:

```batch
nslookup WebPublicIP.0.<Cloud Service Name>.cloudapp.net
``` 

## <a name="next-steps"></a>Próximos passos
* Entenda como o [endereçamento IP](virtual-network-ip-addresses-overview-classic.md) funciona no modelo de implantação clássica.
* Saiba mais sobre [IPs reservados](virtual-networks-reserved-public-ip.md).
