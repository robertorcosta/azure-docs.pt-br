---
title: Criar um host de bastiões do Azure | Microsoft Docs
description: Neste artigo, saiba como criar um host de bastiões do Azure
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: f907dcb4427fd07a2c212e5de91ccce5e8198960
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990412"
---
# <a name="create-an-azure-bastion-host"></a>Criar um host de bastiões do Azure

Este artigo mostra como criar um host de bastiões do Azure usando o portal do Azure. Depois de provisionar o serviço de bastiões do Azure em sua rede virtual, a experiência ininterrupta de RDP/SSH estará disponível para todas as VMs na mesma rede virtual. A implantação de bastiões do Azure é por rede virtual, não por assinatura/conta ou máquina virtual.

Você pode criar um novo recurso de host bastião no portal especificando todas as configurações manualmente ou usando as configurações que correspondem a uma VM existente. Opcionalmente, você pode usar o [Azure PowerShell](bastion-create-host-powershell.md) para criar um host de bastiões do Azure.

## <a name="before-you-begin"></a>Antes de começar

A bastiões está disponível nas seguintes regiões públicas do Azure:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="createhost"></a>Criar um host de bastiões-especificar configurações

Esta seção ajuda você a criar um novo recurso de bastiões do Azure do portal do Azure.

1. No menu [portal do Azure](https://portal.azure.com) ou na **Home** Page do, selecione **criar um recurso**.

1. Na página **novo** , no campo *Pesquisar no Marketplace* , digite **bastiões**e clique em **Enter** para obter os resultados da pesquisa.

1. Nos resultados, clique em **bastiões**. Verifique se o fornecedor é a *Microsoft* e se a categoria é *Rede*.

1. Na página de **bastiões** , clique em **criar** para abrir a página **criar uma bastiões** .

1. Na página **criar uma bastiões** , configure um novo recurso de bastiões. Especifique as definições de configuração para seu recurso de bastiões.

    ![criar uma bastiões](./media/bastion-create-host-portal/settings.png)

    * **Assinatura**: a assinatura do Azure que você deseja usar para criar um novo recurso de bastiões.
    * **Grupo de recursos**: o grupo de recursos do Azure no qual o novo recurso de bastiões será criado. Se você não tiver um grupo de recursos atualmente, crie um.
    * **Nome**: o nome do novo recurso de bastiões
    * **Região**: a região pública do Azure em que o recurso será criado.
    * **Rede virtual**: a rede virtual na qual o recurso de bastiões será criado. Você pode criar uma nova rede virtual no portal durante esse processo ou usar uma rede virtual existente. Se você estiver usando uma rede virtual existente, verifique se a rede virtual existente tem espaço de endereço livre suficiente para acomodar os requisitos de sub-rede de bastiões.
    * **Sub-rede**: a sub-rede em sua rede virtual para a qual o novo recurso de host bastião será implantado. Você deve criar uma sub-rede usando o nome valor **AzureBastionSubnet**. Esse valor permite que o Azure saiba em qual sub-rede implantar os recursos de bastiões. Isso é diferente de uma sub-rede de gateway. Você deve usar uma sub-rede de pelo menos/27 ou maior (/27,/26 e assim por diante).
    
       Crie o **AzureBastionSubnet** sem nenhuma tabela ou delegação de rota. Se você usar grupos de segurança de rede no **AzureBastionSubnet**, consulte o artigo [trabalhar com NSGs](bastion-nsg.md) .
    * **Endereço IP público**: o IP público do recurso de bastiões no qual o RDP/SSH será acessado (pela porta 443). Crie um novo IP público ou use um existente. O endereço IP público deve estar na mesma região que o recurso de bastiões que você está criando.
    * **Nome do endereço IP público**: o nome do recurso de endereço IP público.
    * **SKU de endereço IP público**: essa configuração é preenchida previamente por padrão para **Standard**. A bastiões do Azure usa/dá suporte apenas à SKU de IP público padrão.
    * **Atribuição**: essa configuração é preenchida por padrão como **estática**.

1. Quando terminar de especificar as configurações, clique em **revisar + criar**. Isso valida os valores. Depois que a validação for aprovada, você poderá iniciar o processo de criação.
1. Na página **criar uma bastiões** , clique em **criar**.
1. Você verá uma mensagem informando que a implantação está em andamento. O status será exibido nessa página conforme os recursos são criados. Leva cerca de 5 minutos para que o recurso de bastiões seja criado e implantado.

## <a name="createvmset"></a>Criar um host de bastiões-usar configurações de VM

Se você criar um host de bastiões no portal usando uma VM existente, várias configurações serão automaticamente padrão para corresponder à máquina virtual e/ou à rede virtual.

1. Abra o [Portal do Azure](https://portal.azure.com). Vá para sua máquina virtual e clique em **conectar**.

   ![Conexão de VM](./media/bastion-create-host-portal/vmsettings.png)
1. Na barra lateral direita, clique em **bastiões**e, em seguida, **use bastiões**.

   ![Bastion](./media/bastion-create-host-portal/vmbastion.png)
1. Na página de bastiões, preencha os seguintes campos de configuração:

   * **Nome**: o nome do host de bastiões que você deseja criar.
   * **Sub-rede**: a sub-rede dentro de sua rede virtual para a qual o recurso de bastiões será implantado. A sub-rede deve ser criada com o nome **AzureBastionSubnet**. Isso permite que o Azure saiba em qual sub-rede implantar o recurso de bastiões. Isso é diferente de uma sub-rede de gateway. Você deve usar uma sub-rede de pelo menos/27 ou maior (/27,/26 e assim por diante). Crie a sub-rede sem nenhum grupo de segurança de rede, tabelas de rotas ou delegações. Se, mais tarde, você optar por usar grupos de segurança de rede no **AzureBastionSubnet**, consulte [trabalhar com NSGs](bastion-nsg.md).
   
     Clique em **gerenciar configuração de sub-rede** para criar o **AzureBastionSubnet**.  Clique em **criar** para criar a sub-rede e prossiga com as próximas configurações.
   * **Endereço IP público**: o IP público do recurso de bastiões no qual o RDP/SSH será acessado (pela porta 443). Crie um novo IP público ou use um existente. O endereço IP público deve estar na mesma região que o recurso de bastiões que você está criando.
   * **Nome do endereço IP público**: o nome do recurso de endereço IP público.
1. Na tela validação, clique em **criar**. Aguarde cerca de 5 minutos para o recurso de bastiões criar e implantar.

## <a name="next-steps"></a>Próximos passos

* Leia as [perguntas frequentes sobre bastiões](bastion-faq.md) para obter informações adicionais.

* Para usar grupos de segurança de rede com a sub-rede de bastiões do Azure, confira [trabalhar com NSGs](bastion-nsg.md).