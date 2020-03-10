---
title: Red Hat Enterprise Linux imagens do Azure traga sua própria assinatura | Microsoft Docs
description: Saiba mais sobre as imagens traga sua própria assinatura para Red Hat Enterprise Linux no Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 712626345e10ab0e4290ac91b0f121ff6960303e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396846"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Red Hat Enterprise Linux imagens Gold de sua própria assinatura no Azure

As imagens Red Hat Enterprise Linux (RHEL) estão disponíveis no Azure por meio de um modelo pago pelo uso (PAYG) ou traga sua própria assinatura (imagem do Red Hat Gold). Este documento fornece uma visão geral das imagens do Red Hat Gold no Azure.

>[!NOTE]
> As imagens do RHEL BYOS Gold estão disponíveis no Azure público (comercial) e nas nuvens do Azure governamental. Eles não estão disponíveis nas nuvens do Azure China ou do Azure Blackforest.

## <a name="important-points-to-consider"></a>Pontos importantes a considerar

- As imagens do Red Hat Gold fornecidas neste programa são imagens RHEL prontas para produção, semelhantes às imagens RHEL PAYG na galeria do Azure/Marketplace.

- As imagens seguem nossas políticas atuais descritas em [Red Hat Enterprise Linux imagens no Azure](./redhat-images.md)

- As políticas de suporte padrão se aplicam a VMs criadas com base nessas imagens

- As VMs provisionadas a partir de imagens Red Hat Gold não carregam as taxas de RHEL associadas às imagens RHEL PAYG

- As imagens não são qualificadas, portanto, você deve usar o Subscription-Manager para registrar e assinar as VMs para obter atualizações do Red Hat diretamente

- No momento, não é possível alternar dinamicamente entre os modelos de cobrança BYOS e PAYG para imagens do Linux. Reimplantar a VM da respectiva imagem é necessária para alternar o modelo de cobrança

>[!NOTE]
> As imagens de BYOS de geração 2 RHEL não estão disponíveis atualmente por meio da oferta do Marketplace. Se você precisar de uma imagem de BYOS de Ger 2 RHEL, visite o painel de acesso do coud no gerenciamento de assinaturas do Red Hat. Mais detalhes estão disponíveis na [documentação do Red Hat](https://access.redhat.com/articles/4847681).

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>Requisitos e condições para acessar as imagens do Red Hat Gold

1. Familiarize-se com os termos do [programa Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) e habilite suas assinaturas do Red Hat para acesso à nuvem no [Gerenciador de assinaturas do Red Hat](https://access.redhat.com/management/cloud). Você precisará ter disponível a assinatura (s) do Azure que será registrada para acesso à nuvem.

1. Se você tiver habilitado assinaturas do Red Hat para acesso à nuvem que atendam aos requisitos de qualificação adequados, suas assinaturas do Azure serão habilitadas automaticamente para acesso de imagem Gold.

### <a name="expected-time-for-image-access"></a>Tempo esperado para acesso à imagem

Após a conclusão das etapas de habilitação do acesso à nuvem, a Red Hat validará sua elegibilidade para as imagens do Red Hat Gold. Se a validação for bem-sucedida, você receberá acesso às imagens Gold dentro de três horas.

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>Use as imagens do Red Hat Gold do portal do Azure

1. Depois que sua assinatura do Azure receber acesso às imagens do Red Hat Gold, você poderá localizá-las no [portal do Azure](https://portal.azure.com) navegando para **criar um recurso** e, em seguida, **ver tudo**.

1. Na parte superior da página, você verá que tem ofertas privadas.

    ![Ofertas privadas do Marketplace](./media/rhel-byos-privateoffers.png)

1. Você pode clicar no link roxo ou rolar para baixo até a parte inferior da página para ver suas ofertas particulares.

1. O restante do provisionamento na interface do usuário não será diferente de qualquer outra imagem do Red Hat existente. Escolha a versão do RHEL e siga os prompts para provisionar sua VM. Esse processo também permitirá que você aceite os termos da imagem na etapa final.

>[!NOTE]
>Essas etapas até o momento não habilitarão a imagem de imagem do Red Hat Gold para implantação programática – uma etapa adicional será necessária, conforme descrito na seção "informações adicionais" abaixo.

O restante deste documento se concentra no método CLI para provisionar e aceitar os termos da imagem. A interface do usuário e a CLI são totalmente intercambiáveis no que diz respeito ao resultado final (uma VM de imagem dourada do RHEL, provisionada).

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>Use as imagens do Red Hat Gold do CLI do Azure
O seguinte conjunto de instruções orientará você pelo processo de implantação inicial para uma VM RHEL usando o CLI do Azure. Estas instruções pressupõem que você tenha o [CLI do Azure instalado](https://docs.microsoft.com/cli/azure/install-azure-cli).

>[!IMPORTANT]
>Certifique-se de usar todas as letras minúsculas nas referências de Publicador, oferta, plano e imagem para todos os comandos a seguir

1. Verifique se você está na sua assinatura desejada:
    ```azurecli
    az account show -o=json
    ```

1. Crie um grupo de recursos para sua VM de imagem do Red Hat Gold:
    ```azurecli
    az group create --name <name> --location <location>
    ```

1. Aceite os termos da imagem:
    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn RedHat:rhel-byos:rhel-lvm8:8.0.20190620
    ```
    >[!NOTE]
    >Esses termos precisam ser aceitos *uma vez por assinatura do Azure, por SKU de imagem*.

1. Adicional Valide sua implantação de VM com o seguinte comando:
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image RedHat:rhel-byos:rhel-lvm75:7.5.20190620
    ```

1. Provisione sua VM executando o mesmo comando acima sem o argumento `--validate`:
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. Use SSH em sua VM e verifique se você tem uma imagem não qualificada. Para fazer isso, execute `sudo yum repolist` (para o RHEL 8 Use `sudo dnf repolist`). A saída solicitará que você use Subscription-Manager para registrar a VM com o Red Hat.

>[!NOTE]
>No RHEL 8 `dnf` e `yum` são intercambiáveis, mais sobre isso no guia de [Administração do RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/installing-software-with-yum_configuring-basic-system-settings).


## <a name="use-the-red-hat-gold-images-from-powershell"></a>Usar as imagens do Red Hat Gold do PowerShell
Este é um script de exemplo. Você deve substituir o grupo de recursos, o local, o nome da VM, as informações de logon e outras variáveis pela configuração de sua escolha. As informações do editor e do plano devem estar em minúsculas.

```powershell-interactive
    # Variables for common values
    $resourceGroup = "testbyos"
    $location = "canadaeast"
    $vmName = "test01"

    # Define user name and blank password
    $securePassword = ConvertTo-SecureString 'TestPassword1!' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential("azureuser",$securePassword)
    Get-AzureRmMarketplaceTerms -Publisher RedHat -Product rhel-byos -Name rhel-lvm75 | SetAzureRmMarketplaceTerms -Accept

    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroup -Location $location

    # Create a subnet configuration
    $subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

    # Create a virtual network
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location
    $location `-Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

    # Create a public IP address and specify a DNS name
    $pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location
    $location `-Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

    # Create an inbound network security group rule for port 22
    $nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name
    myNetworkSecurityGroupRuleSSH -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -
    DestinationAddressPrefix * `-DestinationPortRange 22 -Access Allow

    # Create a network security group
    $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location
    $location `-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH

    # Create a virtual network card and associate with public IP address and NSG
    $nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -
    Location $location `-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

    # Create a virtual machine configuration
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D3_v2 |
    Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred |
    Set-AzureRmVMSourceImage -PublisherName redhat -Offer rhel-byos -Skus rhel-lvm75 -Version latest | Add-     AzureRmVMNetworkInterface -Id $nic.Id
    Set-AzureRmVMPlan -VM $vmConfig -Publisher redhat -Product rhel-byos -Name "rhel-lvm75"

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

    # Create a virtual machine
    New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>Criptografar imagens Gold de Red Hat Enterprise Linux traga sua própria assinatura

Red Hat Enterprise Linux imagens Gold de sua própria assinatura podem ser protegidas por meio do uso de [Azure Disk Encryption](../../linux/disk-encryption-overview.md). No entanto, a assinatura **deve** ser registrada antes de habilitar a criptografia.  Detalhes sobre como registrar uma imagem do RHEL BYOS Gold estão disponíveis no site do Red Hat. Consulte [como registrar e assinar um sistema para o portal do cliente Red Hat usando o Red Hat Subscription-Manager](https://access.redhat.com/solutions/253273); Se você tiver uma assinatura ativa do Red Hat, também poderá ler a [criação de chaves de ativação do portal do cliente do Red Hat](https://access.redhat.com/articles/1378093).

Não há suporte para Azure Disk Encryption em [imagens personalizadas do Red Hat](../../linux/redhat-create-upload-vhd.md). Requisitos e pré-requisitos adicionais de ADE são documentados em [Azure Disk Encryption para VMs do Linux](../../linux/disk-encryption-overview.md#additional-vm-requirements).

As etapas para aplicar Azure Disk Encryption estão disponíveis em [cenários de Azure Disk Encryption em VMs do Linux](../../linux/disk-encryption-linux.md) e artigos relacionados.

## <a name="additional-information"></a>{1&gt;{2&gt;Informações adicionais&lt;2}&lt;1}

- Se você tentar provisionar uma VM em uma assinatura que não está habilitada para esta oferta, você receberá o seguinte erro:

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    Nesse caso, contate a Microsoft ou a Red Hat para habilitar sua assinatura.

- Se você modificar um instantâneo de uma imagem RHEL BYOS e tentar publicar essa imagem personalizada na Galeria de [imagens compartilhadas](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries), deverá fornecer informações de plano que correspondam à origem original do instantâneo. Por exemplo, o comando pode ter a seguinte aparência:

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```
    Observe os parâmetros do plano na linha final acima.

    Não há suporte para [Azure Disk Encryption](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) em imagens personalizadas.

- Se você estiver usando a automação para provisionar VMs das imagens RHEL BYOS, deverá fornecer parâmetros de plano semelhantes ao que foi mostrado acima. Por exemplo, se você estiver usando Terraform, você forneceria as informações do plano em um [bloco de plano](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan).

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}
- Os guias passo a passo e os detalhes do programa para acesso à nuvem estão disponíveis na [documentação do Red Hat Cloud Access.](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index)
- Saiba mais sobre a [infraestrutura de atualização do Red Hat do Azure](./redhat-rhui.md).
- Para saber mais sobre todas as imagens do Red Hat no Azure, vá para a [página de documentação](./redhat-images.md).
- Informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL podem ser encontradas na página [Ciclo de vida do Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).
- Documentação adicional sobre as imagens do RHEL Gold podem ser encontradas na [documentação do Red Hat](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure).
