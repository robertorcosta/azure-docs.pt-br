---
title: Compartilhar portal do Azure dashboards usando o controle de acesso baseado em função
description: Este artigo explica como compartilhar um painel no portal do Azure usando o Controle de Acesso Baseado em Função.
services: azure-portal
documentationcenter: ''
author: mgblythe
manager: mtillman
editor: tysonn
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/29/2020
ms.author: mblythe
ms.openlocfilehash: e8d251cef9e67cb8fc0c11df8ce546383f75a679
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900804"
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>Compartilhar painéis do Azure usando o Controle de Acesso Baseado em Função

Depois de configurar um painel, você pode publicá-lo e compartilhá-lo com outros usuários na sua organização. Você permite que outras pessoas exibam seu painel usando o RBAC ( [controle de acesso baseado em função](../role-based-access-control/role-assignments-portal.md) ) do Azure. Atribuir um usuário ou grupo de usuários a uma função. Essa função define se esses usuários podem exibir ou modificar o painel publicado.

Todos os painéis publicados são implementados como recursos do Azure. Elas existem como itens gerenciáveis em sua assinatura e estão contidas em um grupo de recursos. Do ponto de vista do controle de acesso, os painéis não são diferentes de outros recursos, como uma máquina virtual ou uma conta de armazenamento.

> [!TIP]
> Blocos individuais no painel impõem seus próprios requisitos de controle de acesso com base nos recursos que exibem. Você pode compartilhar um Dashboard em larga escala ao proteger os dados em blocos individuais.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Noções básicas de controle de acesso de painéis

Com o RBAC (Controle de Acesso Baseado em Função), você pode atribuir usuários a funções em três níveis de escopo diferentes:

* subscription
* grupo de recursos
* recurso

As permissões que você atribui herdam da assinatura para o recurso. O painel publicado é um recurso. Talvez você já tenha usuários atribuídos a funções para a assinatura que se aplicam ao painel publicado.

Digamos que você tenha uma assinatura do Azure e vários membros de sua equipe receberam as funções de *proprietário*, *colaborador* ou *leitor* da assinatura. Os usuários que são proprietários ou colaboradores podem listar, exibir, criar, modificar ou excluir painéis dentro da assinatura. Os usuários que são leitores podem listar e exibir painéis, mas não podem modificá-los ou excluí-los. Os usuários com acesso de leitor podem fazer edições locais em um painel publicado, como ao solucionar um problema, mas não podem publicar essas alterações de volta no servidor. Eles podem fazer uma cópia privada do painel para si mesmos.

Você também pode atribuir permissões ao grupo de recursos que contém vários dashboards ou a um Dashboard individual. Por exemplo, você pode decidir que um grupo de usuários deve ter permissões limitadas na assinatura, mas um maior acesso a um painel específico. Atribua esses usuários a uma função para esse painel.

## <a name="publish-dashboard"></a>Publicar painel

Vamos supor que você configure um dashboard que deseja compartilhar com um grupo de usuários em sua assinatura. As etapas a seguir mostram como compartilhar um painel para um grupo chamado gerenciadores de armazenamento. Você pode nomear o grupo como desejar. Para obter mais informações, consulte [Managing groups in Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Antes de atribuir acesso, você deve publicar o painel.

1. No painel, selecione **Compartilhar**.

    ![Selecione compartilhar para seu painel](./media/azure-portal-dashboard-share-access/share-dashboard-for-access-control.png)

1. Em **compartilhamento + controle de acesso**, selecione **publicar**.

    ![publicar seu painel](./media/azure-portal-dashboard-share-access/publish-dashboard-for-access-control.png)

     Por padrão, o compartilhamento publica o painel em um grupo de recursos denominado **dashboards**.

Seu painel agora foi publicado. Se as permissões herdadas da assinatura forem adequadas, você não precisará fazer mais nada. Outros usuários em sua organização podem acessar e modificar o Dashboard com base em sua função de nível de assinatura.

## <a name="assign-access-to-a-dashboard"></a>Atribuir acesso a um painel

Você pode atribuir um grupo de usuários a uma função para esse painel.

1. Depois de publicar o painel, em **compartilhamento + controle de acesso**, selecione **gerenciar usuários**.

    ![gerenciar usuários para um painel](./media/azure-portal-dashboard-share-access/manage-users-for-access-control.png)

    Para acessar o **compartilhamento + controle de acesso** de um painel, selecione a opção **compartilhar** ou **descompartilhar** .

1. Selecione **atribuições de função** para ver os usuários existentes que já atribuiram uma função para esse painel.

1. Para adicionar um novo usuário ou grupo, selecione **Adicionar**.

    ![Adicionar um usuário para acesso ao painel](./media/azure-portal-dashboard-share-access/manage-users-existing-users.png)

1. Selecione a função que representa as permissões a serem concedidas. Neste exemplo, selecione **Colaborador**.

1. Selecione o usuário ou grupo para atribuir à função. Se você não vir o usuário ou grupo que está procurando na lista, use a caixa de pesquisa. A lista de grupos disponíveis depende dos grupos que você criou no Active Directory.

1. Quando você terminar de adicionar usuários ou grupos, selecione **OK**.

    A nova atribuição é adicionada à lista de usuários. Seu **acesso** é listado como **atribuído** , em vez de **herdado**.

    ![funções atribuídas](./media/azure-portal-dashboard-share-access/assigned-roles.png)

## <a name="next-steps"></a>Próximos passos

* Para obter uma lista de funções, consulte [funções internas para recursos do Azure](../role-based-access-control/built-in-roles.md).
* Para saber mais sobre como gerenciar recursos, consulte [gerenciar recursos do Azure usando o portal do Azure](resource-group-portal.md).

