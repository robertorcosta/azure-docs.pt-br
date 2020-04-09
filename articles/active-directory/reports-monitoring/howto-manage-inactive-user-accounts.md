---
title: Como gerenciar contas de usuários inativos no Ad do Azure | Microsoft Docs
description: Saiba como detectar e lidar com contas de usuários no Azure AD que se tornaram obsoletas
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/07/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56e44059268037cfd839fc7c877c5d6c972dead8
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886034"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>Como: Gerenciar contas de usuário inativas no Azure AD

Em ambientes grandes, as contas de usuário nem sempre são excluídas quando os funcionários saem de uma organização. Como administrador de TI, você deseja detectar e lidar com essas contas de usuário obsoletas porque elas representam um risco de segurança.

Este artigo explica um método para lidar com contas de usuário obsoletas no Azure AD. 

## <a name="what-are-inactive-user-accounts"></a>O que são contas de usuário inativas?

Contas inativas são contas de usuário que não são mais exigidas por membros de sua organização para ter acesso aos seus recursos. Um dos principais identificadores de contas inativas é que elas não são usadas *há algum tempo* para fazer login no seu ambiente. Como as contas inativas estão vinculadas à atividade de login, você pode usar o carimbo de data e hora do último login que foi bem sucedido para detectá-las. 

O desafio deste método é definir o que *por um tempo* significa no caso do seu ambiente. Por exemplo, os usuários podem não fazer login em um ambiente *por um tempo,* porque eles estão de férias. Ao definir qual é o seu delta para contas de usuário inativas, você precisa considerar todas as razões legítimas para não fazer login no seu ambiente. Em muitas organizações, o delta para contas de usuários inativos é entre 90 e 180 dias. 

O último login bem-sucedido fornece informações potenciais sobre a necessidade contínua de acesso aos recursos por um usuário.  Ele pode ajudar a determinar se a adesão ao grupo ou acesso ao aplicativo ainda é necessário ou pode ser removido. Para o gerenciamento externo do usuário, você pode entender se um usuário externo ainda está ativo dentro do inquilino ou deve ser limpo. 

    
## <a name="how-to-detect-inactive-user-accounts"></a>Como detectar contas de usuários inativas

Você detecta contas inativas avaliando a **última propriedadeSignInDateTime** exposta pelo tipo de recurso **signInActivity** da API **do Microsoft Graph.** Usando esta propriedade, você pode implementar uma solução para os seguintes cenários:

- **Usuários por nome**: Neste cenário, você procura um usuário específico por nome, o que permite avaliar o últimoSignInDate:`https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- **Usuários por data**: Neste cenário, você solicita uma lista de usuários com um últimoSignInDateTime antes de uma data especificada:`https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`






## <a name="what-you-need-to-know"></a>O que você precisa saber

Esta seção lista o que você precisa saber sobre a última propriedadeSignInDateTime.

### <a name="how-can-i-access-this-property"></a>Como posso acessar essa propriedade?

A **última propriedadeSignInDateTime** é exposta pelo tipo de [recurso signInActivity](https://docs.microsoft.com/graph/api/resources/signinactivity?view=graph-rest-beta) da [API Microsoft Graph REST](https://docs.microsoft.com/graph/overview?view=graph-rest-beta#whats-in-microsoft-graph).   

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>A última propriedadeSignInDateTime está disponível através do cmdlet Get-AzureAdUser?

Não.

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>Que edição do Azure AD eu preciso para acessar a propriedade?

Você pode acessar esta propriedade em todas as edições do Azure AD.

### <a name="what-permission-do-i-need-to-read-the-property"></a>Que permissão preciso para ler a propriedade?

Para ler esta propriedade, você precisa conceder os seguintes direitos: 

- Loglogs de auditoria.Read.All
- Organização.Leia.Todos  


### <a name="when-does-azure-ad-update-the-property"></a>Quando o Azure AD atualiza a propriedade?

Cada login interativo que foi bem sucedido resulta em uma atualização do armazenamento de dados subjacente. Normalmente, os logins bem-sucedidos aparecem no relatório de login relacionado dentro de 10 minutos.
 

### <a name="what-does-a-blank-property-value-mean"></a>O que significa um valor de propriedade em branco?

Para gerar um último carimbo de data-hora do SignInDateTime, você precisa de um login bem-sucedido. Como a última propriedadeSignInDateTime é um novo recurso, o valor da última propriedadeSignInDateTime pode estar em branco se:

- O último login bem-sucedido de um usuário ocorreu antes do lançamento deste recurso (1º de dezembro de 2019).
- A conta de usuário afetada nunca foi usada para um login bem-sucedido.

## <a name="next-steps"></a>Próximas etapas

* [Obter dados usando a API de Relatório do Azure AD com certificados](tutorial-access-api-with-certificates.md)
* [Referência da API de auditoria](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Referência da API de relatório de atividade de entrada](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
