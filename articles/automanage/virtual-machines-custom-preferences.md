---
title: Criar uma preferência personalizada no autogerenciamento do Azure para VMs
description: Saiba como ajustar o perfil de configuração no Azure automanage para VMs e definir suas próprias preferências.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: cf8e7340ce3554bf27364335457a5b7eadcdc049
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90932934"
---
# <a name="create-a-custom-preference-in-azure-automanage-for-vms"></a>Criar uma preferência personalizada no autogerenciamento do Azure para VMs

O autogerenci do Azure para práticas recomendadas de máquina virtual tem perfis de configuração padrão que podem ser ajustados, se necessário. Este artigo explicará como você pode definir suas próprias preferências de perfil de configuração ao habilitar o autogerenciamento em uma VM nova ou existente.

Atualmente, damos suporte a personalizações no [backup do Azure](..\backup\backup-azure-arm-vms-prepare.md#create-a-custom-policy) e [Microsoft Antimalware](../security/fundamentals/antimalware.md#default-and-custom-antimalware-configuration).


> [!NOTE]
> Você não pode alterar o perfil de configuração ou a preferência em sua VM enquanto o autogerenci está habilitado. Será necessário desabilitar o autogerenci para essa VM e reabilitar o autogerenci com o perfil de configuração desejado e as preferências.


## <a name="prerequisites"></a>Pré-requisitos

Caso não tenha uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) antes de começar.

> [!NOTE]
> As contas de avaliação gratuita não têm acesso às máquinas virtuais usadas neste tutorial. Atualize para uma assinatura de Pagamento Conforme o Uso.

> [!IMPORTANT]
> A permissão RBAC a seguir é necessária para habilitar autogerenciar: função de **proprietário** ou **colaborador** junto com as funções de **administrador de acesso do usuário** .


## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com/).


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>Habilitar o autogerenci para VMs em uma VM existente

1. Na barra de pesquisa, procure e selecione **autogerenciar – práticas recomendadas da máquina virtual do Azure**.

2. Selecione **habilitar na VM existente**.

3. Na folha **Selecionar computadores** :
    1. Filtre a lista de VMs por sua **assinatura** e **grupo de recursos**.
    1. Marque a caixa de seleção de cada máquina virtual que você deseja carregar.
    1. Clique no botão **Selecionar**.

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-select-machine.png" alt-text="Selecione VM existente na lista de VMs disponíveis.":::

4. Em **perfil de configuração**, clique em **procurar e alterar perfis e preferências**.

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-quick-create.png" alt-text="Procurar e alterar perfis e preferências.":::

5. Na folha **selecionar perfil de configuração + preferências** , selecione um perfil no lado esquerdo: *desenvolvimento/teste* para teste, *prod* para produção.

    :::image type="content" source="media\virtual-machine-custom-preferences\browse-production-profile.png" alt-text="Procurar perfil de configuração de produção.":::

6. No perfil escolhido, em **preferências de configuração** , há uma lista suspensa onde você pode ajustar para determinados serviços.
    1. Clique em **criar novas preferências**.
    1. Na folha **criar uma preferência de configuração** , preencha a guia noções básicas:
        1. Subscription
        1. Resource group
        1. Nome da preferência
        1. Região

    :::image type="content" source="media\virtual-machine-custom-preferences\create-preference.png" alt-text="Preencha as preferências de configuração.":::

7. Vá para a guia preferências e ajuste as preferências de configuração desejadas.
        
    > [!NOTE]
    > Somente os ajustes que ainda couberem em nossas práticas recomendadas limites superiores e inferiores serão permitidos durante a alteração das configurações de perfil.

8. Examine seu perfil de configuração.
9. Selecione o botão **Criar**.

10. Clique no botão **Habilitar**.


## <a name="disable-automanage-for-vms"></a>Desabilitar autogerenci para VMs

Pare rapidamente de usar o Azure autogerenci para máquinas virtuais desabilitando o autogerenciamento.

:::image type="content" source="media\virtual-machine-custom-preferences\disable-step-1.png" alt-text="Desabilitando o autogerenci em uma máquina virtual.":::

1. Vá para a página **autogerenciar – práticas recomendadas da máquina virtual do Azure** que lista todas as VMs gerenciadas automaticamente.
1. Marque a caixa de seleção ao lado da máquina virtual que você deseja desabilitar.
1. Clique no botão **desabilitar automanagent** .
1. Leia atentamente por meio do sistema de mensagens no pop-up resultante antes de concordar em **desabilitar**.


## <a name="clean-up-resources"></a>Limpar os recursos

Se você criou um novo grupo de recursos para experimentar o autogerenciamento do Azure para máquinas virtuais e não precisar mais dela, poderá excluir o grupo de recursos. Excluir o grupo também exclui a VM e todos os recursos no grupo de recursos.

O autogerenci do Azure cria grupos de recursos padrão para armazenar recursos no. Verifique os grupos de recursos que têm a Convenção de nomenclatura "DefaultResourceGroupRegionName" e "AzureBackupRGRegionName" para limpar todos os recursos.

1. Selecione o **Grupo de recursos**.
1. Na página do grupo de recursos, selecione **Excluir**.
1. Quando solicitado, confirme o nome do grupo de recursos e, em seguida, selecione **excluir**.


## <a name="next-steps"></a>Próximas etapas 

Obtenha as perguntas mais frequentes respondidas em nossas perguntas frequentes. 

> [!div class="nextstepaction"]
> [Perguntas frequentes](faq.md)