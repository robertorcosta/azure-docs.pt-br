---
title: Pesquisar e filtrar membros e proprietários de grupos (pré-visualização) - Azure Active Directory | Microsoft Docs
description: Pesquise e filtre membros e proprietários de grupos no portal Azure.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 02/28/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a815446b79b3e5ec0a75e5d179953956643b16c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206105"
---
# <a name="search-groups-and-members-preview-in-azure-active-directory"></a>Grupos de pesquisa e membros (visualização) no Azure Active Directory

Este artigo diz como pesquisar membros e proprietários de um grupo e como usar filtros de pesquisa como parte da visualização de melhoria de grupos no portal Azure Active Directory (Azure AD). Existem muitas melhorias nas experiências dos grupos para ajudá-lo a gerenciar seus grupos, incluindo membros e proprietários, de forma rápida e fácil. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

As alterações nesta pré-visualização incluem:

- Novos recursos de pesquisa de grupos, como pesquisa de substring em nomes de grupos
- Novas opções de filtragem e classificação em listas de membros e proprietários
- Novos recursos de pesquisa para listas de membros e proprietários
- Contagens de grupos mais precisas para grandes grupos

## <a name="enabling-and-managing-the-preview"></a>Habilitando e gerenciando a visualização

Facilitamos a adesão à pré-visualização:

  1. Faça login no [portal Azure AD](https://portal.azure.com)e selecione **Grupos**.
  2. Na página Grupos – Todos os grupos, selecione o banner no topo da página para participar da pré-visualização.

Você também pode verificar os recursos e melhorias mais recentes selecionando **informações de visualização** na página **Todos os grupos.** Depois de participar da pré-visualização, você pode ver a tag de visualização em todas as páginas de grupos que têm melhorias e fazem parte da pré-visualização. Nem todas as páginas de grupos foram atualizadas como parte desta pré-visualização.

Se você estiver tendo algum problema, você pode mudar a experiência do legado selecionando o banner na parte superior da página **Todos os grupos.** Agradecemos seu feedback para que possamos melhorar nossa experiência.

## <a name="group-search-and-sorting"></a>Busca e classificação em grupo

A pesquisa de lista de grupos foi aprimorada para que, quando você `startswith` possa inserir uma seqüência de pesquisa, a pesquisa execute automaticamente uma pesquisa e substring na lista de nomes de grupos. A pesquisa de substring é realizada apenas em palavras inteiras, e não inclui caracteres especiais. A busca por subcordas é sensível a maiúsculas e minúsculas.

![novas pesquisas de substring na página Todos os Grupos](./media/groups-members-owners-search/groups-search-preview.png)

Por exemplo, uma busca por "política" agora retornará tanto a "política de MDM – Oeste" quanto "Grupo político". Um grupo chamado "New_policy" não seria devolvido.

- Você também pode realizar a mesma pesquisa em listas de membros de grupo.
- Agora você pode classificar a lista de grupos por nome usando as setas à direita do título da coluna nome para classificar a lista em ordem ascendente ou descendente.

## <a name="group-member-search-and-filtering"></a>Pesquisa e filtragem de membros do grupo

### <a name="search-group-member-and-owner-lists"></a>Listas de membros e proprietários do grupo de pesquisa

Agora você pode pesquisar os membros de um grupo específico por nome e executar a mesma pesquisa na lista de proprietários do grupo também. Na nova experiência, se você inserir uma seqüência na caixa de pesquisa, uma inicialização com pesquisa será realizada automaticamente. Por exemplo, uma busca por "Scott" retornará Scott Wilkinson.

![novas pesquisas de substring nas listas de membros do grupo e proprietários](./media/groups-members-owners-search/members-list.png)

### <a name="filter-member-and-owners-list"></a>Lista de membros e proprietários do filtro

Além da pesquisa, agora você pode filtrar as listas de membros e proprietários por tipo de usuário. Esta é a informação encontrada na coluna Tipo de usuário da lista. Assim, você pode filtrar a lista por membros e convidados para determinar se há algum convidado no grupo.

### <a name="view-and-manage-membership"></a>Exibir e gerenciar a adesão

Além de visualizar os membros diretos de um grupo específico, agora você pode visualizar a lista de todos os membros do grupo na página Membros. A lista de membros inclui todos os membros únicos do grupo, incluindo quaisquer membros transitivos.

Você também pode pesquisar e filtrar a lista de membros diretos e a lista de todos os membros individualmente. Filtrar a lista de todos os membros não afeta os filtros aplicados na lista de membros diretos.

## <a name="improved-group-member-counts"></a>Contagem melhorada de membros do grupo

Melhoramos a página **visão geral** do grupo para fornecer contagem de membros do grupo para grupos de todos os tamanhos. Você pode ver a contagem de membros mesmo para grupos com mais de 1.000 membros. Agora você pode ver o número total de membros diretos para um grupo e a contagem total de membros (todos os membros únicos do grupo, incluindo membros transitivos) na página **Visão Geral.**

![Maior precisão na contagem de membros em grupo](./media/groups-members-owners-search/member-numbers.png)

## <a name="next-steps"></a>Próximas etapas

Esses artigos fornecem informações adicionais sobre o trabalho com grupos no Azure AD.

- [Exibir grupos e membros](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Gerenciar associação ao grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Gerenciar regras dinâmicas para usuários em um grupo](groups-create-rule.md)
- [Editar as configurações de grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Gerenciar acesso a recursos usando grupos](../fundamentals/active-directory-manage-groups.md)
- [Gerenciar acesso a aplicativos SaaS usando grupos](groups-saasapps.md)
- [Gerenciar grupos usando comandos do PowerShell](groups-settings-v2-cmdlets.md)
- [Adicione uma assinatura do Azure ao Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
