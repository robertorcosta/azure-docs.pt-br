---
title: Compartilhe os painéis do portal Azure usando o controle de acesso baseado em função
description: Este artigo explica como compartilhar um painel no portal do Azure usando o Controle de Acesso Baseado em Função.
services: azure-portal
documentationcenter: ''
author: mgblythe
manager: mtillman
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: 4eef5a9e3f010e19871471d007ff2a0cc24d3834
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461371"
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>Compartilhar painéis do Azure usando o Controle de Acesso Baseado em Função

Depois de configurar um painel, você pode publicá-lo e compartilhá-lo com outros usuários na sua organização. Você permite que outros visualizem seu painel usando o RBAC (Azure [Role-Based Access Control).](../role-based-access-control/role-assignments-portal.md) Atribuir um usuário ou grupo de usuários a uma função. Essa função define se esses usuários podem visualizar ou modificar o painel publicado.

Todos os painéis publicados são implementados como recursos do Azure. Eles existem como itens gerenciáveis dentro de sua assinatura e estão contidos em um grupo de recursos. Do ponto de vista do controle de acesso, os painéis não são diferentes de outros recursos, como uma máquina virtual ou uma conta de armazenamento.

> [!TIP]
> Blocos individuais no painel impõem seus próprios requisitos de controle de acesso com base nos recursos que exibem. Você pode compartilhar um painel amplamente protegendo os dados em telhas individuais.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Noções básicas de controle de acesso de painéis

Com o RBAC (Controle de Acesso Baseado em Função), você pode atribuir usuários a funções em três níveis de escopo diferentes:

* subscription
* grupo de recursos
* recurso

As permissões que você atribui herdar da assinatura até o recurso. O painel publicado é um recurso. Você já pode ter usuários atribuídos a funções para a assinatura que se aplicam ao painel publicado.

Digamos que você tenha uma assinatura do Azure e vários membros de sua equipe receberam as funções de *proprietário*, *colaborador* ou *leitor* da assinatura. Os usuários que são proprietários ou contribuintes podem listar, visualizar, criar, modificar ou excluir painéis dentro da assinatura. Os usuários que são leitores podem listar e visualizar painéis, mas não podem modificá-los ou excluí-los. Os usuários com acesso ao leitor podem fazer edições locais em um painel publicado, como ao solucionar problemas de um problema, mas não podem publicar essas alterações de volta ao servidor. Eles podem fazer uma cópia privada do painel para si mesmos.

Você também pode atribuir permissões ao grupo de recursos que contém vários dashboards ou a um painel individual. Por exemplo, você pode decidir que um grupo de usuários deve ter permissões limitadas na assinatura, mas um maior acesso a um painel específico. Atribuir esses usuários a uma função para esse painel.

## <a name="publish-dashboard"></a>Publicar painel

Vamos supor que você configure um painel que você deseja compartilhar com um grupo de usuários em sua assinatura. As etapas a seguir mostram como compartilhar um painel para um grupo chamado Storage Managers. Você pode nomear seu grupo o que quiser. Para obter mais informações, consulte [Gerenciando grupos no Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Antes de atribuir acesso, você deve publicar o painel.

1. No painel, selecione **Compartilhar**.

    ![selecionar compartilhar para o seu painel](./media/azure-portal-dashboard-share-access/share-dashboard-for-access-control.png)

1. Em **Compartilhar + controle de acesso,** selecione **Publicar**.

    ![publicar seu painel](./media/azure-portal-dashboard-share-access/publish-dashboard-for-access-control.png)

     Por padrão, o compartilhamento publica seu painel em um grupo de recursos chamado **dashboards**. Para selecionar um grupo de recursos diferente, limpe a caixa de seleção.

Seu painel agora foi publicado. Se as permissões herdadas da assinatura forem adequadas, você não precisa fazer mais nada. Outros usuários da sua organização podem acessar e modificar o painel com base na função de nível de assinatura.

## <a name="assign-access-to-a-dashboard"></a>Atribuir acesso a um painel

Você pode atribuir um grupo de usuários a uma função para esse painel.

1. Após a publicação do painel, selecione a opção **Compartilhar** ou **Não compartilhar** para acessar o controle de compartilhamento + **acesso**.

1. No **compartilhamento + controle de acesso,** selecione Gerenciar **usuários**.

    ![gerenciar usuários para um painel](./media/azure-portal-dashboard-share-access/manage-users-for-access-control.png)

1. Selecione **as atribuições de função** para ver os usuários existentes que já estão atribuídos uma função para este painel.

1. Para adicionar um novo usuário ou grupo, **selecione Adicionar** e **adicionar atribuição de função**.

    ![adicionar um usuário para acesso ao painel](./media/azure-portal-dashboard-share-access/manage-users-existing-users.png)

1. Selecione a função que representa as permissões para conceder. Neste exemplo, selecione **Colaborador**.

1. Selecione o usuário ou grupo para atribuir à função. Se você não ver o usuário ou grupo que você está procurando na lista, use a caixa de pesquisa. Sua lista de grupos disponíveis depende dos grupos que você criou no Active Directory.

1. Quando terminar de adicionar usuários ou grupos, selecione **Salvar**.

## <a name="next-steps"></a>Próximas etapas

* Para obter uma lista de funções, consulte [Funções incorporadas para os recursos do Azure](../role-based-access-control/built-in-roles.md).
* Para saber mais sobre o gerenciamento de recursos, consulte [Gerenciar recursos do Azure usando o portal Azure](resource-group-portal.md).

