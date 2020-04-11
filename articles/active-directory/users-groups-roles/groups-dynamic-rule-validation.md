---
title: Validar regras para membros de grupo dinâmico (pré-visualização) - Azure AD | Microsoft Docs
description: Como testar membros contra uma regra de associação para grupos dinâmicos no Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/10/2020
ms.author: curtand
ms.reviewer: yukarppa
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e081f62949f4cf3f7ae375b2c60ee5b032d974
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115515"
---
# <a name="validate-a-dynamic-group-membership-rule-preview-in-azure-active-directory"></a>Valide uma regra de membro de grupo dinâmico (pré-visualização) no Azure Active Directory

O Azure Active Directory (Azure AD) agora fornece os meios para validar regras dinâmicas de grupo (em visualização pública). Na guia **Validar regras,** você pode validar sua regra dinâmica contra membros do grupo de amostra para confirmar se a regra está funcionando conforme o esperado. Ao criar ou atualizar regras dinâmicas de grupo, os administradores querem saber se um usuário ou um dispositivo será um membro do grupo. Isso ajuda a avaliar se o usuário ou dispositivo atende aos critérios de regra e ajuda na solução de problemas quando a adesão não é esperada.

## <a name="step-by-step-walk-through"></a>Passo a passo

Para começar, vá para **Azure Active Directory** > **Groups**. Selecione um grupo dinâmico existente ou crie um novo grupo dinâmico e clique em regras de associação dinâmicas. Em seguida, você pode ver a guia **Validar regras.**

![Encontre a guia Validar regras e comece com uma regra existente](./media/groups-dynamic-rule-validation/validate-tab.png)

Na guia **Validar regras,** você pode selecionar usuários para validar suas adesões. 20 usuários ou dispositivos podem ser selecionados ao mesmo tempo.

![Adicionar usuários para validar a regra existente contra](./media/groups-dynamic-rule-validation/validate-tab-add-users.png)

Depois de escolher os usuários ou dispositivos do seletor e **Selecionar,** a validação será iniciada automaticamente e os resultados de validação serão exibidos.

![Veja os resultados da validação da regra](./media/groups-dynamic-rule-validation/validate-tab-results.png)

Os resultados dizem se um usuário é um membro do grupo ou não. Se a regra não for válida ou houver um problema de rede, o resultado será exibido como **Desconhecido**. No caso de **Desconhecido,** a mensagem de erro detalhada descreverá o problema e as ações necessárias.

![Veja os detalhes dos resultados da validação da regra](./media/groups-dynamic-rule-validation/validate-tab-view-details.png)

Você pode modificar a regra e a validação das adesões será acionada. Para ver por que o usuário não é membro do grupo, clique em "Exibir detalhes" e os detalhes de verificação mostrarão o resultado de cada expressão que compõe a regra. Clique **em OK** para sair.

## <a name="next-steps"></a>Próximas etapas

- [Regras dinâmicas de adesão para grupos](groups-dynamic-membership.md)
