---
title: Red Hat Enterprise Linux traz imagens do Azure por assinatura própria | Microsoft Docs
description: Saiba mais sobre as imagens de sua assinatura própria para o Red Hat Enterprise Linux no Azure.
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
ms.openlocfilehash: 825d26307f2b462d51b143b88127e229508f2f25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264590"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Red Hat Enterprise Linux traz sua própria assinatura Imagens de Ouro no Azure

As imagens red hat enterprise linux (RHEL) estão disponíveis no Azure através de um modelo pay-as-you-go ou bring-your-own-subscription (BYOS) (Red Hat Gold Image). Este artigo fornece uma visão geral das Imagens de Ouro do Chapéu Vermelho no Azure.

>[!NOTE]
> RHEL BYOS Gold Images estão disponíveis nas nuvens Azure Public (comercial) e Azure Government. Eles não estão disponíveis na China Azul ou nas nuvens azure Blackforest.

## <a name="important-points-to-consider"></a>Pontos importantes a serem considerados

- As Imagens de Ouro da Red Hat fornecidas neste programa são imagens RHEL prontas para produção semelhantes às imagens rhel pay-as-you-go no Azure Marketplace.
- As imagens seguem as políticas atuais descritas nas [imagens do Red Hat Enterprise Linux no Azure](./redhat-images.md).
- As políticas de suporte padrão aplicam-se às VMs criadas a partir dessas imagens.
- As VMs fornecidas pela Red Hat Gold Images não possuem taxas RHEL associadas a imagens rhel pay-as-you-go.
- As imagens não têm direito. Você deve usar o Red Hat Subscription-Manager para se registrar e assinar as VMs para obter atualizações diretamente da Red Hat.
- Atualmente, não é possível alternar dinamicamente entre byos e modelos de cobrança pay-as-you-go para imagens Linux. Para alternar o modelo de cobrança, você deve reimplantar a VM da respectiva imagem.

>[!NOTE]
> As imagens da Geração 2 RHEL BYOS não estão disponíveis atualmente através da oferta de marketplace. Se você precisar de uma imagem Generation 2 RHEL BYOS, visite o painel de acesso ao nuvem no gerenciamento de assinaturas Red Hat. Para obter mais informações, consulte a [documentação](https://access.redhat.com/articles/4847681)do Chapéu Vermelho .

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>Requisitos e condições para acessar a Red Hat Gold Images

1. Familiarize-se com os termos do [programa Red Hat Cloud Access.](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) Habilite suas assinaturas red hat para acesso à nuvem no [Red Hat Subscription-Manager](https://access.redhat.com/management/cloud). Você precisa ter em mãos as assinaturas do Azure que serão registradas no Cloud Access.

1. Se as assinaturas red hat habilitadas para o Cloud Access atenderem aos requisitos de elegibilidade, suas assinaturas do Azure serão automaticamente habilitadas para acesso ao Gold Image.

### <a name="expected-time-for-image-access"></a>Tempo esperado para acesso à imagem

Depois de terminar as etapas de ativação do Acesso à Nuvem, a Red Hat valida sua elegibilidade para as Imagens de Ouro do Chapéu Vermelho. Se a validação for bem sucedida, você receberá acesso às Imagens de Ouro dentro de três horas.

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>Use as Imagens de Ouro do Chapéu Vermelho do portal Azure

1. Depois que sua assinatura do Azure recebe acesso à Red Hat Gold Images, você pode localizá-las no [portal Azure](https://portal.azure.com). Ir para **Criar um recurso** > **Ver tudo**.

1. No topo da página, você verá que tem ofertas privadas.

    ![Ofertas privadas de marketplace](./media/rhel-byos-privateoffers.png)

1. Selecione o link roxo ou role até a parte inferior da página para ver suas ofertas privadas.

1. O resto do provisionamento na ui não é diferente de qualquer outra imagem existente do Red Hat. Escolha sua versão RHEL e siga as instruções para provisionar sua VM. Este processo também permite que você aceite os termos da imagem na etapa final.

>[!NOTE]
>Essas etapas até agora não permitirão a sua Imagem Ouro do Chapéu Vermelho para implantação programática. Uma etapa adicional é necessária conforme descrito na seção "Informações adicionais".

O resto deste documento se concentra no método CLI para prover e aceitar termos na imagem. A UI e a CLI são totalmente intercambiáveis no que diz respeito ao resultado final (um VM de imagem ouro rhel provisionado).

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>Use as Imagens de Ouro do Chapéu Vermelho da CLI do Azure

As instruções a seguir orientam você através do processo inicial de implantação de um VM RHEL usando o Cli Azure. Estas instruções supõem que você tenha [o Azure CLI instalado](https://docs.microsoft.com/cli/azure/install-azure-cli).

>[!IMPORTANT]
>Certifique-se de usar todas as letras minúsculas no editor, oferecer, planejar e referências de imagem para todos os seguintes comandos.

1. Verifique se você está na sua assinatura desejada.

    ```azurecli
    az account show -o=json
    ```

1. Crie um grupo de recursos para sua VM de imagem ouro do Chapéu Vermelho.

    ```azurecli
    az group create --name <name> --location <location>
    ```

1. Aceite os termos da imagem.

    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn RedHat:rhel-byos:rhel-lvm8:8.0.20190620
    ```

    >[!NOTE]
    >Esses termos precisam ser aceitos *uma vez por assinatura do Azure, por imagem SKU*.

1. (Opcional) Valide sua implantação de VM com o seguinte comando:

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image RedHat:rhel-byos:rhel-lvm75:7.5.20190620
    ```

1. Provisionar sua VM executando o mesmo comando mostrado `--validate` no exemplo anterior sem o argumento.

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. SSH em sua VM e verifique se você tem uma imagem sem direito. Para fazer este `sudo yum repolist`passo, corra. Para RHEL 8, use `sudo dnf repolist`. A saída pede que você use o Subscription-Manager para registrar a VM com o Red Hat.

>[!NOTE]
>No RHEL `dnf` 8, e `yum` são intercambiáveis. Para obter mais informações, consulte o [guia de admin RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/installing-software-with-yum_configuring-basic-system-settings).

## <a name="use-the-red-hat-gold-images-from-powershell"></a>Use as imagens de ouro do chapéu vermelho da PowerShell

O seguinte script é um exemplo. Substitua o grupo de recursos, localização, nome da VM, informações de login e outras variáveis com a configuração de sua escolha. As informações do editor e do plano devem ser minúsculas.

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

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>Criptografe o Red Hat Enterprise Linux bring-your-own-subscription Gold Images

Red Hat Enterprise Linux BYOS Gold Images podem ser protegidos através do uso do [Azure Disk Encryption](../../linux/disk-encryption-overview.md). A assinatura *deve* ser registrada antes que você possa habilitar a criptografia. Para obter mais informações sobre como registrar uma Imagem Ouro RHEL BYOS, consulte [Como se cadastrar e assinar um sistema no Portal do Cliente Red Hat usando o Red Hat Subscription-Manager](https://access.redhat.com/solutions/253273). Se você tiver uma assinatura ativa do Red Hat, você também pode ler [Criando chaves de ativação do portal do cliente red hat](https://access.redhat.com/articles/1378093).

A criptografia de disco do Azure não é suportada em [imagens personalizadas do Red Hat](../../linux/redhat-create-upload-vhd.md). Requisitos adicionais de criptografia de disco azure e pré-requisitos são documentados no [Azure Disk Encryption for Linux VMs](../../linux/disk-encryption-overview.md#additional-vm-requirements).

Para obter etapas para aplicar o Azure Disk Encryption, consulte [cenários de criptografia de disco do Azure em VMs Linux](../../linux/disk-encryption-linux.md) e artigos relacionados.

## <a name="additional-information"></a>Informações adicionais

- Se você tentar provisionar uma VM em uma assinatura que não está habilitada para esta oferta, você receberá a seguinte mensagem:

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    Neste caso, entre em contato com a Microsoft ou a Red Hat para habilitar sua assinatura.

- Se você modificar um instantâneo de uma imagem RHEL BYOS e tentar publicar essa imagem personalizada na [Galeria de Imagens Compartilhadas,](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries)você deve fornecer informações de plano que correspondam à fonte original do snapshot. Por exemplo, o comando pode ser assim:

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

    Observe os parâmetros do plano na linha final.

    [A criptografia de disco do Azure](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) não é suportada em imagens personalizadas.

- Se você usar a automação para fornecer VMs a partir das imagens RHEL BYOS, você deve fornecer parâmetros de plano semelhantes aos mostrados nos comandos de amostra. Por exemplo, se você usar o Terraform, você fornecerá as informações do plano em um [bloco de planos](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan).

## <a name="next-steps"></a>Próximas etapas

- Para obter guias passo-a-passo e detalhes do programa para acesso à nuvem, consulte a [documentação do Red Hat Cloud Access](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index).
- Para saber mais sobre a infra-estrutura de atualização do Chapéu Vermelho, consulte [Azure Red Hat Update Infrastructure](./redhat-rhui.md).
- Para saber mais sobre todas as imagens do Red Hat no Azure, consulte a [página de documentação](./redhat-images.md).
- Para obter informações sobre as políticas de suporte da Red Hat para todas as versões do RHEL, consulte a página do ciclo de vida do [Red Hat Enterprise Linux.](https://access.redhat.com/support/policy/updates/errata)
- Para obter documentação adicional sobre a RHEL Gold Images, consulte a [documentação](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure)do Chapéu Vermelho .
