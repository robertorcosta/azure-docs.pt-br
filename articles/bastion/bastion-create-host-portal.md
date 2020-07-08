---
title: 'Criar um host de bastiões do Azure: Portal'
description: Neste artigo, saiba como criar um host de bastiões do Azure usando o portal
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 04/24/2020
ms.author: cherylmc
ms.openlocfilehash: d18d520419e77a225431d9c2a395f62411656537
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84744265"
---
# <a name="create-an-azure-bastion-host-using-the-portal"></a>Criar um host de bastiões do Azure usando o portal

Este artigo mostra como criar um host de bastiões do Azure usando o portal do Azure. Depois de provisionar o serviço de bastiões do Azure em sua rede virtual, a experiência ininterrupta de RDP/SSH estará disponível para todas as VMs na mesma rede virtual. A implantação do Azure Bastion é feita por rede virtual, não por assinatura/conta ou máquina virtual.

Você pode criar um novo recurso de host bastião no portal especificando todas as configurações manualmente ou usando as configurações que correspondem a uma VM existente. Para criar um host de bastiões usando as configurações de VM, consulte o artigo de [início rápido](quickstart-host-portal.md) . Opcionalmente, você pode usar [Azure PowerShell](bastion-create-host-powershell.md) para criar um host de bastiões do Azure.

## <a name="before-you-begin"></a>Antes de começar

A bastiões está disponível nas seguintes regiões públicas do Azure:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Criar um bastion host

Esta seção ajuda você a criar um novo recurso de bastiões do Azure do portal do Azure.

1. No menu do [portal do Azure](https://portal.azure.com) ou na **Página Inicial**, selecione **Criar um recurso**.

1. Na página **novo** , no campo *Pesquisar no Marketplace* , digite **bastiões**e clique em **Enter** para obter os resultados da pesquisa.

1. Nos resultados, clique em **bastiões**. Verifique se o fornecedor é a *Microsoft* e se a categoria é *Rede*.

1. Na página de **bastiões** , clique em **criar** para abrir a página **criar uma bastiões** .

1. Na página **criar uma bastiões** , configure um novo recurso de bastiões. Especifique as definições de configuração para seu recurso de bastiões.

    ![criar uma bastiões](./media/bastion-create-host-portal/settings.png)

    * **Assinatura**: a assinatura do Azure que você deseja usar para criar um novo recurso de bastiões.
    * **Grupo de recursos**: o grupo de recursos do Azure no qual o novo recurso de bastiões será criado. Se você não tiver um grupo de recursos existente, poderá criar um novo.
    * **Nome**: o nome do novo recurso de bastiões
    * **Região**: a região pública do Azure em que o recurso será criado.
    * **Rede virtual**: a rede virtual na qual o recurso de bastiões será criado. Você pode criar uma nova rede virtual no portal durante esse processo ou usar uma rede virtual existente. Se você estiver usando uma rede virtual existente, verifique se a rede virtual existente tem espaço de endereço livre suficiente para acomodar os requisitos de sub-rede de bastiões.
    * **Sub-rede**: a sub-rede em sua rede virtual em que o novo host de bastiões será implantado. A sub-rede será dedicada ao host de bastiões e deve ser nomeada como **AzureBastionSubnet**. Essa sub-rede deve ser pelo menos/27 ou maior.
    
       **AzureBastionSubnet** não dá suporte a [rotas definidas pelo usuário](../virtual-network/virtual-networks-udr-overview.md#custom-routes), mas oferece suporte a [grupos de segurança de rede](bastion-nsg.md).
    * **Endereço IP público**: o IP público do recurso de bastiões no qual o RDP/SSH será acessado (pela porta 443). Crie um IP público ou use um existente. O endereço IP público precisa estar na mesma região que o recurso do Bastion que você está criando.
    * **Nome do endereço IP público**: o nome do recurso de endereço IP público.
    * **SKU de endereço IP público**: essa configuração é preenchida previamente por padrão para **Standard**. A bastiões do Azure usa/dá suporte apenas à SKU de IP público padrão.
    * **Atribuição**: essa configuração é preenchida por padrão como **estática**.

1. Quando terminar de especificar as configurações, clique em **revisar + criar**. Isso valida os valores. Depois que a validação for aprovada, você poderá iniciar o processo de criação.
1. Na página **criar uma bastiões** , clique em **criar**.
1. Você verá uma mensagem informando que a implantação está em andamento. O status será exibido nessa página conforme os recursos são criados. Leva cerca de 5 minutos para que o recurso de bastiões seja criado e implantado.

## <a name="next-steps"></a>Próximas etapas

* Leia as [perguntas frequentes sobre bastiões](bastion-faq.md) para obter informações adicionais.

* Para usar grupos de segurança de rede com a sub-rede do Azure Bastion, confira [Trabalhar com NSGs](bastion-nsg.md).
