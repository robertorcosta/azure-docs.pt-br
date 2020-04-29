---
title: Como gerenciar contas de usuários inativos no Azure AD | Microsoft Docs
description: Saiba mais sobre como detectar e manipular contas de usuário no Azure AD que se tornaram obsoletos
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80886034"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>Como gerenciar contas de usuários inativos no Azure AD

Em ambientes grandes, as contas de usuário nem sempre são excluídas quando os funcionários deixam uma organização. Como administrador de ti, você deseja detectar e lidar com essas contas de usuário obsoletas, pois elas representam um risco à segurança.

Este artigo explica um método para lidar com contas de usuário obsoletas no Azure AD. 

## <a name="what-are-inactive-user-accounts"></a>O que são contas de usuário inativas?

Contas inativas são contas de usuário que não são mais necessárias por membros da sua organização para obter acesso aos seus recursos. Um identificador de chave para contas inativas é que elas não foram usadas *por um tempo* para entrar em seu ambiente. Como as contas inativas são ligadas à atividade de entrada, você pode usar o carimbo de data/hora da última entrada que foi bem-sucedida para detectá-las. 

O desafio desse método é definir o que significa *um tempo* no caso do seu ambiente. Por exemplo, os usuários podem não entrar em um ambiente *por algum tempo*, porque estão em férias. Ao definir qual é seu Delta para contas de usuários inativos, você precisa considerar todos os motivos legítimos para não entrar em seu ambiente. Em muitas organizações, o Delta para contas de usuários inativos é entre 90 e 180 dias. 

A última entrada bem-sucedida fornece informações em potencial sobre a necessidade contínua do usuário de acesso aos recursos.  Ele pode ajudar a determinar se a associação de grupo ou o acesso ao aplicativo ainda é necessário ou pode ser removido. Para o gerenciamento de usuários externos, você pode entender se um usuário externo ainda está ativo dentro do locatário ou se deve ser limpo. 

    
## <a name="how-to-detect-inactive-user-accounts"></a>Como detectar contas de usuários inativos

Você detecta contas inativas avaliando a propriedade **lastSignInDateTime** exposta pelo tipo de recurso **signInActivity** da API **Microsoft Graph** . Usando essa propriedade, você pode implementar uma solução para os seguintes cenários:

- **Usuários por nome**: nesse cenário, você procura um usuário específico por nome, o que permite avaliar o lastSignInDate:`https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- **Usuários por data**: nesse cenário, você solicita uma lista de usuários com um lastSignInDateTime antes de uma data especificada:`https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`






## <a name="what-you-need-to-know"></a>O que você precisa saber

Esta seção lista o que você precisa saber sobre a propriedade lastSignInDateTime.

### <a name="how-can-i-access-this-property"></a>Como posso acessar essa propriedade?

A propriedade **lastSignInDateTime** é exposta pelo [tipo de recurso SIGNINACTIVITY](https://docs.microsoft.com/graph/api/resources/signinactivity?view=graph-rest-beta) da [API REST do Microsoft Graph](https://docs.microsoft.com/graph/overview?view=graph-rest-beta#whats-in-microsoft-graph).   

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>A propriedade lastSignInDateTime está disponível por meio do cmdlet Get-AzureAdUser?

Não.

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>Qual edição do Azure AD eu preciso acessar a propriedade?

Você pode acessar essa propriedade em todas as edições do Azure AD.

### <a name="what-permission-do-i-need-to-read-the-property"></a>Qual permissão preciso para ler a propriedade?

Para ler essa propriedade, você precisa conceder os seguintes direitos: 

- AuditLogs. Read. All
- Organização. Read. All  


### <a name="when-does-azure-ad-update-the-property"></a>Quando o Azure AD atualiza a propriedade?

Cada entrada interativa que foi bem-sucedida resulta em uma atualização do armazenamento de dados subjacente. Normalmente, as entradas bem-sucedidas aparecem no relatório de entrada relacionado em 10 minutos.
 

### <a name="what-does-a-blank-property-value-mean"></a>O que significa um valor de propriedade em branco?

Para gerar um carimbo de data/hora lastSignInDateTime, você precisará de uma entrada bem-sucedida. Como a propriedade lastSignInDateTime é um novo recurso, o valor da propriedade lastSignInDateTime pode ficar em branco se:

- A última entrada bem-sucedida de um usuário ocorreu antes do lançamento desse recurso (1º de dezembro de 2019).
- A conta de usuário afetada nunca foi usada para uma entrada bem-sucedida.

## <a name="next-steps"></a>Próximas etapas

* [Obter dados usando a API de Relatório do Azure AD com certificados](tutorial-access-api-with-certificates.md)
* [Referência da API de auditoria](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Referência da API de relatório de atividade de entrada](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
