---
title: Perguntas frequentes sobre a central de segurança do Azure-perguntas sobre permissões
description: Estas perguntas FREQUENTEs respondem a perguntas sobre permissões na central de segurança do Azure, um produto que ajuda você a prevenir, detectar e responder a ameaças.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 66a260fbb03f770ee98ec29a5f5e15e3d7dd1310
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599386"
---
# <a name="permissions"></a>Permissões

## <a name="how-do-permissions-work-in-azure-security-center"></a>Como as permissões funcionam na central de segurança do Azure?

A Central de Segurança do Azure usa o [RBAC (Controle de Acesso Baseado em Função)](../role-based-access-control/role-assignments-portal.md), que fornece [funções internas](../role-based-access-control/built-in-roles.md) que podem ser atribuídas a usuários, grupos e serviços no Azure.

A Central de Segurança avalia a configuração de seus recursos para identificar problemas de segurança e vulnerabilidades. Na Central de Segurança, você vê apenas as informações relacionadas a um recurso quando for atribuído à função de Proprietário, Colaborador ou Leitor da assinatura ou do grupo de recursos ao qual o recurso pertence.

Confira [Permissões na Central de Segurança do Azure](security-center-permissions.md) para saber mais sobre as funções e as ações permitidas na Central de Segurança.



## <a name="who-can-modify-a-security-policy"></a>Quem pode modificar uma política de segurança?

Para modificar uma política de segurança, você deve ser um Administrador de Segurança ou o Proprietário ou Colaborador dessa assinatura.

Para saber como configurar uma política de segurança, consulte a seção [Como configurar de políticas de segurança na Central de segurança do Azure](tutorial-security-policy.md).