---
title: Gerenciando funções do Azure AD no Privilegiado de Identidade (PIM) | Microsoft Docs
description: Como gerenciar funções azure AD para atribuição de controle de identidade privilegiada (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5d63577cbdbdf18cb17618bdb5c9e3b5de0e44a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245974"
---
# <a name="management-capabilities-for-azure-ad-roles-in-privileged-identity-management"></a>Recursos de gerenciamento para funções azure AD em gerenciamento de identidade privilegiado

A experiência de gerenciamento das funções do Azure AD no Gerenciamento de Identidade Privilegiada foi atualizada para unificar como as funções de Ad do Azure e os recursos do Azure são gerenciados. Anteriormente, o Privileged Identity Management para funções de recursos do Azure teve alguns recursos-chave que não estavam disponíveis para as funções do Azure AD.

Com a atualização sendo lançada no momento, estamos mesclando os dois em uma única experiência de gerenciamento, e nele você obtenha a mesma funcionalidade para as funções do Azure AD como para as funções de recurso do Azure. Este artigo informa sobre os recursos atualizados e quaisquer requisitos.


## <a name="time-bound-assignments"></a>Atribuições com limite de tempo

Anteriormente em Gerenciamento de Identidade Privilegiada para funções AD do Azure, você estava familiarizado com atribuições de funções com dois estados possíveis – *elegíveis* e *permanentes*. Agora você pode definir um horário de início e fim para cada tipo de tarefa. Esta adição lhe dá quatro estados possíveis em que você pode colocar uma atribuição:

- Elegível permanentemente
- Ativo permanentemente
- Elegível, com datas de início/término especificadas para atribuição
- Ativo, com datas de início/término especificadas para atribuição

Em muitos casos, mesmo que você não queira que os usuários tenham atribuição elegível e ative funções todas as vezes, você ainda pode proteger sua organização Azure AD definindo um tempo de validade para atribuições. Por exemplo, se você tiver alguns usuários temporários que são elegíveis, considere definir uma expiração para removê-los automaticamente da atribuição da função quando seu trabalho estiver concluído.

## <a name="new-role-settings"></a>Novas configurações de função

Também estamos adicionando novas configurações para funções Azure AD. Anteriormente, você só podia configurar configurações de ativação por função. Ou seja, as configurações de ativação, como requisitos de autenticação de vários fatores e requisitos de ticket incidente/solicitação foram aplicadas a todos os usuários elegíveis para uma função especificada. Agora, você pode configurar se um usuário individual precisa executar a autenticação multifatorial antes que ele possa ativar uma função. Além disso, você pode ter controle avançado sobre seus e-mails de Gerenciamento de Identidade Privilegiado relacionado a funções específicas.

## <a name="extend-and-renew-assignments"></a>Estender e renovar atribuições

Assim que você descobrir a atribuição de tempo limite, a primeira pergunta que você pode fazer é o que acontece se um papel expirar? Nesta nova versão, oferecemos duas opções para este cenário:

- Estender – Quando uma atribuição de função se aproxima de seu vencimento, o usuário pode usar o Gerenciamento de Identidade Privilegiada para solicitar uma extensão para essa atribuição de função
- Renovar – Quando uma atribuição de função expirar, o usuário pode usar o Gerenciamento de Identidade Privilegiada para solicitar uma renovação para essa atribuição de função

Ambas as ações iniciadas pelo usuário exigem a aprovação de um administrador global ou administrador de funções privilegiadas. Os administradores não precisarão mais estar no negócio de gerenciar essas expirações. Eles só precisam esperar pela prorrogação ou pedidos de renovação e aprová-los se a solicitação for válida.

## <a name="api-changes"></a>Alterações de API

Quando os clientes tiverem a versão atualizada distribuída para sua organização Azure AD, a API de gráfico existente deixará de funcionar. Você deve fazer a transição para usar a [API do Gráfico para funções de recurso do Azure](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-resources?view=graph-rest-beta). Para gerenciar as funções azure AD usando essa API, troque `/azureResources` com `/aadroles` a assinatura e use o ID do Diretório para o `resourceId`.

Nós tentamos o nosso melhor para chegar a todos os clientes que estão usando a API anterior para que eles saibam sobre essa mudança antes do tempo. Se a sua organização Azure AD foi transferida para a nova versão e você pim_preview@microsoft.comainda depende da Antiga API, entre em contato com a equipe em .

## <a name="powershell-change"></a>Mudança de PowerShell

Para clientes que estão usando o módulo PowerShell de gerenciamento de identidade privilegiado para funções Ad do Azure, o PowerShell deixará de trabalhar com a atualização. No lugar dos cmdlets anteriores, você deve usar os cmdlets privileged Identity Management dentro do módulo PowerShell de visualização Ad Azure. Instale o módulo Azure AD PowerShell na [Galeria PowerShell](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Agora você pode [ler a documentação e amostras para operações pim neste módulo PowerShell](powershell-for-azure-ad-roles.md).

## <a name="next-steps"></a>Próximas etapas

- [Atribuir uma função personalizada do Azure AD](azure-ad-custom-roles-assign.md)
- [Remover ou atualizar uma atribuição de função personalizada do Azure AD](azure-ad-custom-roles-update-remove.md)
- [Configurar uma atribuição de função personalizada do Azure AD](azure-ad-custom-roles-configure.md)
- [Definições de função no Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
