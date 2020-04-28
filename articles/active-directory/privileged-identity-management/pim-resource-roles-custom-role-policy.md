---
title: Usar funções personalizadas para recursos do Azure no PIM – Azure AD | Microsoft Docs
description: Saiba como usar funções personalizadas para recursos do Azure no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbe08cff2b57155f8f3315f5d3454abfbdad47a0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "73847057"
---
# <a name="use-custom-roles-for-azure-resources-in-privileged-identity-management"></a>Usar funções personalizadas para recursos do Azure no Privileged Identity Management

Talvez seja necessário aplicar configurações de PIM (Privileged Identity Management estrita) a alguns usuários em uma função privilegiada em sua organização do Azure Active Directory (Azure AD), fornecendo, ao mesmo tempo, autonomia maior para outras pessoas. Considere, por exemplo, um cenário em que sua organização contrata vários associados de contrato para auxiliar no desenvolvimento de um aplicativo que será executado em uma assinatura do Azure.

Como administrador de recursos, você quer que os funcionários sejam qualificados para o acesso sem a necessidade de aprovação. No entanto, todos os colaboradores contratados devem ser aprovados quando solicitam acesso aos recursos da organização.

Siga as etapas descritas na próxima seção para definir as configurações de Privileged Identity Management direcionadas para funções de recurso do Azure.

## <a name="create-the-custom-role"></a>Criar a função personalizada

Para criar uma função personalizada para um recurso, siga as etapas descritas em [Criar funções personalizadas para o Controle de Acesso Baseado em Função do Azure](../role-based-access-control-custom-roles.md).

Ao criar uma função personalizada, inclua um nome descritivo para que você possa facilmente se lembrar de qual função interna você pretende duplicar.

> [!NOTE]
> Certifique-se de que a função personalizada seja uma duplicação da função interna que você quer duplicar e que seu escopo corresponda à função interna.

## <a name="apply-pim-settings"></a>Aplicar configurações de PIM

Depois que a função for criada em sua organização do Azure AD, vá para a página **Privileged Identity Management-recursos do Azure** no portal do Azure. Selecione o recurso ao qual a função se aplica.

![O painel “Privileged Identity Management - Recursos do Azure”](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

[Defina Privileged Identity Management configurações de função](pim-resource-roles-configure-role-settings.md) que devem ser aplicadas a esses membros da função.

Por fim, [atribua funções](pim-resource-roles-assign-roles.md) ao grupo distinto de membros que você deseja ter como destino com essas configurações.

## <a name="next-steps"></a>Próximas etapas

- [Definir configurações de função de recurso do Azure no Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Funções personalizadas no Azure](../../role-based-access-control/custom-roles.md)
