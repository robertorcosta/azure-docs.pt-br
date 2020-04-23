---
title: Permissões na Central de Segurança do Azure | Microsoft Docs
description: Este artigo explica como a Central de Segurança do Azure usa o controle de acesso baseado em função para atribuir permissões aos usuários e identifica as ações permitidas para cada função.
services: security-center
cloud: na
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: 6e61571400930d4a781d6d67647bd662a7f2d350
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106212"
---
# <a name="permissions-in-azure-security-center"></a>Permissões na Central de Segurança do Azure

A Central de Segurança do Azure usa o [RBAC (Controle de Acesso Baseado em Função)](../role-based-access-control/role-assignments-portal.md), que fornece [funções internas](../role-based-access-control/built-in-roles.md) que podem ser atribuídas a usuários, grupos e serviços no Azure.

A Central de Segurança avalia a configuração de seus recursos para identificar problemas de segurança e vulnerabilidades. Na Central de Segurança, você vê apenas as informações relacionadas a um recurso quando for atribuído à função de Proprietário, Colaborador ou Leitor da assinatura ou do grupo de recursos ao qual o recurso pertence.

Além dessas funções, há duas funções específicas da Central de Segurança:

* **Leitor de Segurança**: um usuário que pertence a essa função tem direitos de exibição para a Central de Segurança. O usuário pode exibir as recomendações, alertas, uma política de segurança e estados de segurança, mas não pode fazer alterações.
* **Administrador de segurança**: um usuário que pertence a essa função tem os mesmos direitos que o leitor de segurança e também pode atualizar a política de segurança e ignorar alertas e recomendações.

> [!NOTE]
> As funções de segurança, o leitor de segurança e o administrador de segurança têm acesso apenas na central de segurança. As funções de segurança não têm acesso a outras áreas de serviço do Azure como Armazenamento, Web e Móveis ou Internet das Coisas.
>
>

## <a name="roles-and-allowed-actions"></a>Funções e ações permitidas

A tabela a seguir exibe as funções e as ações permitidas na Central de Segurança.

| Função | Editar política de segurança | Aplicar as recomendações de segurança a um recurso</br> (incluindo com ' correção rápida! ') | Ignorar alertas e recomendações | Exibir alertas e recomendações |
|:--- |:---:|:---:|:---:|:---:|
| Proprietário da assinatura | ✔ | ✔ | ✔ | ✔ |
| Colaborador da assinatura | -- | ✔ | ✔ | ✔ |
| Proprietário do Grupo de Recursos | -- | ✔ | -- | ✔ |
| Colaborador do Grupo de Recursos | -- | ✔ | -- | ✔ |
| Leitor | -- | -- | -- | ✔ |
| Administrador de Segurança | ✔ | -- | ✔ | ✔ |
| Leitor de segurança | -- | -- | -- | ✔ |

> [!NOTE]
> Recomendamos que você atribua a função menos permissiva necessária para os usuários realizarem suas tarefas. Por exemplo, atribua a função Leitor aos usuários que precisam apenas exibir informações sobre a integridade da segurança de um recurso, mas que não precisam executar nenhuma ação, como aplicar recomendações ou editar políticas.
>
>

## <a name="next-steps"></a>Próximas etapas
Este artigo explicou como a Central de Segurança usa o RBAC para atribuir permissões aos usuários e identificou as ações permitidas para cada função. Agora que você está familiarizado com as atribuições de função necessárias para monitorar o estado de segurança de sua assinatura, editar as políticas de segurança e aplicar recomendações, saiba como:

- [Definir políticas de segurança na Central de Segurança](tutorial-security-policy.md)
- [Gerenciando recomendações de segurança na Central de Segurança do Azure](security-center-recommendations.md)
- [Monitoramento da integridade da segurança na Central de Segurança do Azure](security-center-monitoring.md)
- [Gerenciar e responder a alertas de segurança na central de segurança](security-center-managing-and-responding-alerts.md)
- [Monitoring partner solutions with Azure Security Center](security-center-partner-solutions.md) (Monitorando soluções de parceiros com a Central de Segurança do Azure)
