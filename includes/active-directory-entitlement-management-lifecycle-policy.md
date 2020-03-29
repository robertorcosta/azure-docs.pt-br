---
title: incluir arquivo
description: incluir arquivo
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: e971af5d415ea788f97ab11b58541e629c7eb0ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76694338"
---
## <a name="lifecycle"></a>Ciclo de vida

Na **guia Ciclo de vida,** você especifica quando a atribuição de um usuário ao pacote de acesso expira. Você também pode especificar se os usuários podem estender suas atribuições.

1. Na seção **Expiração,** as **atribuições do pacote Access expiram** até **a data em que**se tem, número de **dias**ou **nunca**.

    Para **a data on,** selecione uma data de validade no futuro.

    Para **número de dias,** especifique um número entre 0 e 3660 dias.

    Com base na sua seleção, a atribuição de um usuário ao pacote de acesso expira em uma determinada data, um certo número de dias após sua aprovação, ou nunca.

1. Clique **em Mostrar configurações avançadas de expiração** para mostrar configurações adicionais.

    ![Pacote de acesso - Configurações de expiração do ciclo de vida](./media/active-directory-entitlement-management-lifecycle-policy/expiration.png)

1. Para permitir que o usuário amplie suas atribuições, defina **Permitir que os usuários ampliem o acesso** a **Sim**.

    Se as extensões forem permitidas na diretiva, o usuário receberá um e-mail 14 dias e também 1 dia antes de sua atribuição de pacote de acesso expirar, levando-o a estender a atribuição. Se o usuário enviar uma solicitação para estender o acesso, a data de extensão deve ser antes de as atribuições expirarem conforme definido na política que foi usada para conceder ao usuário acesso ao pacote de acesso. Por exemplo, se a diretiva indicar que as atribuições estão definidas para expirar em 30 de junho, a extensão máxima que um usuário pode solicitar é 30 de junho.

    Se o acesso de um usuário for estendido, ele não poderá solicitar o pacote de acesso após a data de extensão especificada (data definida no fuso horário do usuário que criou a política).

1. Para exigir aprovação para conceder uma prorrogação, defina **a aprovação necessária para conceder a extensão** ao **Yes**.

    As mesmas configurações de aprovação especificadas na guia **Solicitações** serão usadas.

1. Clique **em Avançar** ou **Atualizar**.
