---
title: Início rápido – Habilitar o Gerenciamento Automático do Azure para VMs no portal do Azure
description: Saiba como habilitar rapidamente o Gerenciamento Automático para máquinas virtuais em uma VM nova ou existente no portal do Azure.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: jushiman
ms.openlocfilehash: 6e0e582ed37230ba3f379f193a229cfec06f066c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101648026"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>Início Rápido: Habilitar o gerenciamento do Azure para máquinas virtuais no portal do Azure

Introdução ao Gerenciamento Automático do Azure para máquinas virtuais usando o portal do Azure para habilitar o gerenciamento em uma máquina virtual nova ou existente.


## <a name="prerequisites"></a>Pré-requisitos

Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) antes de começar.

> [!NOTE]
> As contas de avaliação gratuita não têm acesso às máquinas virtuais usadas neste tutorial. Atualize para uma assinatura de Pagamento Conforme o Uso.

> [!IMPORTANT]
> Você precisa ter a função de **Colaborador** no grupo de recursos que contém suas VMs para habilitar o Gerenciamento Automatizado usando uma Conta de Gerenciamento Automatizado existente. Se você estiver habilitando o Gerenciamento Automático com uma nova Conta de Gerenciamento Automático, precisará das seguintes permissões: Função de **Proprietário** ou **Colaborador** junto com funções de **Administrador de Acesso do Usuário** em sua assinatura.


## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://aka.ms/AutomanagePortal-Ignite21).

## <a name="enable-automanage-for-a-single-vm"></a>Habilitar o Gerenciamento Automatizado em uma VM individual

1. Procure a Máquina Virtual que deseja habilitar.

2. Clique na entrada **Gerenciamento Automatizado (Versão prévia)** no Sumário em **Operações**.

3. Selecione **Introdução**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\VMManage-GetStarted.png" alt-text="Introdução à VM individual.":::

4. Escolha suas configurações de Gerenciamento Automatizado (ambiente, preferências, conta do Gerenciamento Automatizado) e clique em **Habilitar**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\VMManage-Enable.png" alt-text="Habilitação em uma VM individual.":::

## <a name="enable-automanage-for-multiple-vms"></a>Habilitar o Gerenciamento Automatizado em várias VMs

1. Na barra de pesquisa, procure e selecione **Gerenciamento Automatizado – Melhores práticas para computadores do Azure**.

2. Selecione **Habilitar em VM existente**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="Habilitar na VM existente.":::

3. Na folha **Selecionar computadores**:
    1. Filtre a lista de VMs pela sua **Assinatura** e **Grupo de Recursos**.
    1. Marque a caixa de seleção de cada máquina virtual que você deseja integrar.
    1. Clique no botão **Selecionar**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="Selecione a VM existente na lista de VMs disponíveis.":::

4. Em **Perfil de configuração**, clique em **Procurar e alterar perfis e preferências**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="Procurar e alterar perfis e preferências.":::

5. Na folha **Selecionar perfil de configuração + preferências**:
    1. Selecione um perfil à esquerda: *Desenvolvimento/Teste* para teste, *Prod* para produção.
    1. Clique no botão **Selecionar**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Procurar perfil de configuração de produção.":::

6. Clique no botão **Habilitar**.


## <a name="enable-automanage-for-a-new-vm"></a>Habilitar o Gerenciamento Automatizado em uma nova VM

Entre no portal do Azure [aqui](https://aka.ms/AutomanagePortal-Ignite21) para criar uma VM e habilitar o Gerenciamento Automático.

1. Escolha **Criar um recurso** no canto superior esquerdo do portal do Azure.

2. Na caixa de pesquisa acima da lista de recursos do Azure Marketplace, procure e selecione a imagem que deseja usar e escolha **Criar**.

> [!NOTE]
> Verifique as [distribuições do Linux](automanage-linux.md#supported-linux-distributions-and-versions) e as [versões do Windows Server](automanage-windows-server.md#supported-windows-server-versions) compatíveis com o Gerenciamento Automatizado.

3. Preencha a guia **Informações Básicas** com os detalhes da VM.

> [!NOTE]
> Verifique as [regiões com suporte](automanage-virtual-machines#supported-regions) do Gerenciamento Automatizado.

4. Procure a guia **Gerenciamento** e escolha seu **Ambiente de Gerenciamento Automatizado**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\VMCreate-Management-Tab.png" alt-text="Habilitar o Gerenciamento Automatizado na guia Gerenciamento.":::

5. Deixe os padrões restantes e, em seguida, selecione o botão **Examinar + criar** na parte inferior da página.

6. Quando você receber a mensagem informando que a validação foi aprovada, selecione **Criar**.

## <a name="disable-automanage-for-vms"></a>Desabilitar o Gerenciamento Automático para VMs

Pare rapidamente de usar o Gerenciamento Automático do Azure para máquinas virtuais desabilitando o gerenciamento automático.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Desabilitar o Gerenciamento Automático em uma máquina virtual.":::

1. Acesse a página **Gerenciamento Automático – melhores práticas de máquina virtual do Azure**, que lista todas as suas VMs gerenciadas automaticamente.
1. Marque a caixa de seleção ao lado da máquina virtual que você deseja desabilitar.
1. Clique no botão **Desabilitar gerenciamento automático**.
1. Leia atentamente as mensagens no pop-up resultante antes de concordar em **Desabilitar**.


## <a name="clean-up-resources"></a>Limpar os recursos

Caso você tenha criado um grupo de recursos para experimentar o Gerenciamento Automático do Azure para máquinas virtuais e não precise mais dele, pode excluí-lo. Excluir o grupo exclui também a VM e todos os recursos no grupo de recursos.

O Gerenciamento Automático do Azure cria grupos de recursos padrão nos quais para armazenar recursos. Verifique os grupos de recursos que têm a convenção de nomenclatura "DefaultResourceGroupRegionName" e "AzureBackupRGRegionName" para limpar todos os recursos.

1. Selecione o **Grupo de recursos**.
1. Na página do grupo de recursos, selecione **Excluir**.
1. Quando solicitado, confirme o nome do grupo de recursos e depois selecione **Excluir**.


## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você habilitou o Gerenciamento Automático do Azure para VMs.

Descubra como você pode criar e aplicar preferências personalizadas ao habilitar o Gerenciamento Automático em sua máquina virtual.

> [!div class="nextstepaction"]
> [Gerenciamento Automatizado do Azure para VMs – Perfil de configuração personalizado](virtual-machines-custom-preferences.md)
