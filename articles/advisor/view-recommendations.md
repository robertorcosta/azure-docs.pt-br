---
title: Veja as recomendações do Azure Advisor que importam para você
description: Visualize e filtre as recomendações do Azure Advisor para reduzir o ruído.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 10d7b16864f8e449dc51e870c5ff9f20d8c0dc87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422368"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>Veja as recomendações do Azure Advisor que importam para você

O Azure Advisor fornece recomendações para ajudá-lo a otimizar suas implantações do Azure. Dentro do Advisor, você tem acesso a alguns recursos que o ajudam a reduzir suas recomendações apenas para aqueles que importam para você.

## <a name="configure-subscriptions-and-resource-groups"></a>Configure assinaturas e grupos de recursos

O Advisor lhe dá a capacidade de selecionar Assinaturas e Grupos de Recursos que importam para você e sua organização. Você só vê recomendações para as assinaturas e grupos de recursos que você seleciona. Por padrão, todos são selecionados. As configurações se aplicam ao grupo de assinatura ou recurso, de modo que as mesmas configurações se aplicam a todos que têm acesso a esse grupo de assinatura ou recurso. As configurações de configuração podem ser alteradas no portal Azure ou programáticamente.

Para fazer alterações no portal Azure:

1. Abra [o Azure Advisor](https://aka.ms/azureadvisordashboard) no portal Azure.

1. Selecione **Configuração** no menu.

   ![Menu de configuração do advisor](./media/view-recommendations/configuration.png)

1. Verifique a caixa na coluna **Incluir** para obter assinaturas ou grupos de recursos para receber recomendações do Advisor. Se a caixa estiver desativada, você pode não ter permissão para fazer uma alteração de configuração nesse grupo de assinatura ou recurso. Saiba mais sobre [permissões no Azure Advisor](permissions.md).

1. Clique em **Aplicar** na parte inferior depois de fazer uma alteração.

## <a name="filtering-your-view-in-the-azure-portal"></a>Filtrando sua visão no portal Azure

As configurações permanecem ativas até que se alterem. Se você quiser limitar a visualização de recomendações para uma única visualização, você pode usar as gotas fornecidas na parte superior do painel Do Nono. A partir da visão geral, da alta disponibilidade, segurança, desempenho, custo e todos os painéis de recomendação, você pode selecionar as Assinaturas, tipos de recursos e o status de recomendação que deseja ver.

   ![Menu de filtragem de conselheiros](./media/view-recommendations/filtering.png)

## <a name="dismissing-and-postponing-recommendations"></a>Dispensando e adiando recomendações

O Azure Advisor permite que você descarte ou adie recomendações sobre um único recurso. Se você rejeitar uma recomendação, você não a verá novamente a menos que você a ativasse manualmente. No entanto, o adiamento de uma recomendação permite especificar uma duração após a qual a recomendação é ativada automaticamente novamente. O adiamento pode ser feito no portal Azure ou programáticamente.

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>Adiar uma única recomendação no portal Azure 

1. Abra [o Azure Advisor](https://aka.ms/azureadvisordashboard) no portal Azure.
1. Selecione uma categoria de recomendação para visualizar suas recomendações
1. Selecione uma recomendação na lista de recomendações
1. Selecione Adiar ou Demitir para a recomendação que deseja adiar ou demitir

     ![Menu de filtragem de conselheiros](./media/view-recommendations/postpone-dismiss.png)

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>Adiar ou descartar várias recomendações no portal Azure

1. Abra [o Azure Advisor](https://aka.ms/azureadvisordashboard) no portal Azure.
1. Selecione uma categoria de recomendação para visualizar suas recomendações.
1. Selecione uma recomendação na lista de recomendações.
1. Selecione a caixa de seleção à esquerda da linha para todos os recursos que deseja adiar ou rejeitar a recomendação.
1. Selecione **Adiar** ou **Desconsiderar** no canto superior esquerdo da tabela.

     ![Menu de filtragem de conselheiros](./media/view-recommendations/postpone-dismiss-multiple.png)

> [!NOTE]
> Você precisa de permissão do contribuinte ou proprietário para dispensar ou adiar uma recomendação. Saiba mais sobre as permissões no Azure Advisor.

> [!NOTE]
> Se as caixas de seleção estiverem desativadas, as recomendações ainda podem estar sendo carregadas. Por favor, aguarde que todas as recomendações carreguem antes de tentar adiar ou demitir.

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>Reativar uma recomendação adiada ou rejeitada

Você pode ativar uma recomendação que foi adiada ou descartada. Essa ação pode ser feita no portal Azure ou programática. No Portal do Azure:

1. Abra [o Azure Advisor](https://aka.ms/azureadvisordashboard) no portal Azure.

1. Altere o filtro no painel Visão Geral para **Adiado**. Em seguida, o conselheiro exibe recomendações adiadas ou rejeitadas.

    ![Menu de filtragem de conselheiros](./media/view-recommendations/activate-postponed.png)

1. Selecione uma categoria para ver recomendações **adiadas** e **rejeitadas.**

1. Selecione uma recomendação na lista de recomendações. Isso abre recomendações com a guia **Adiada & Descartada** já selecionada para mostrar os recursos para os quais essa recomendação foi adiada ou rejeitada.

1. Clique em **Ativar** no final da linha. Uma vez clicado, a recomendação está ativa para esse recurso e assim removida desta tabela. A recomendação agora está visível na guia **Ativo.**
 
     ![Menu de filtragem de conselheiros](./media/view-recommendations/activate-postponed-2.png)

## <a name="next-steps"></a>Próximas etapas

Este artigo explica como você pode ver recomendações que importam para você no Azure Advisor. Para saber mais sobre o Assistente, consulte: 

- [O que é o Azure Advisor?](advisor-overview.md)
- [Começando com o Advisor](advisor-get-started.md)
- [Permissões no Azure Advisor](permissions.md)



