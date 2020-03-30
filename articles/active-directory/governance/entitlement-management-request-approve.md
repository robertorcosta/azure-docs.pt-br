---
title: Aprovar ou negar pedidos de acesso - Gerenciamento de direitos do Azure AD
description: Saiba como usar o portal Meu Acesso para aprovar ou negar solicitações a um pacote de acesso no gerenciamento de direitos do Azure Active Directory.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/27/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20ec63efe16f1120ca6e7d07c8917d8ad2b3a0e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261717"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management"></a>Aprovar ou negar pedidos de acesso no gerenciamento de direitos do Azure AD

Com o gerenciamento de direitos ad do Azure, você pode configurar políticas para exigir aprovação para pacotes de acesso e escolher um ou mais aprovadores. Este artigo descreve como os aprovadores designados podem aprovar ou negar pedidos de pacotes de acesso.

## <a name="open-request"></a>Solicitação aberta

O primeiro passo para aprovar ou negar pedidos de acesso é encontrar e abrir a solicitação de acesso pendente de aprovação. Há duas maneiras de abrir a solicitação de acesso.

**Papel pré-requisito:** Aprovador

1. Procure um e-mail do Microsoft Azure que peça que você aprove ou negue uma solicitação. Aqui está um e-mail de exemplo:

    ![Aprovar solicitação para acessar o e-mail do pacote](./media/entitlement-management-shared/approver-request-email.png)

1. Clique no link **Aprovar ou negar solicitação** para abrir a solicitação de acesso.

1. Faça login no portal Meu Acesso.

Se você não tiver o e-mail, você pode encontrar as solicitações de acesso pendentes de sua aprovação seguindo estas etapas.

1. Faça login no portal [https://myaccess.microsoft.com](https://myaccess.microsoft.com)Meu Acesso em .  (Para o governo dos EUA, o domínio `myaccess.microsoft.us`no link do portal Meu Acesso será .)

1. No menu à esquerda, clique em **Aprovações** para ver uma lista de solicitações de acesso pendentes de aprovação.

1. Na **guia Pendente,** encontre a solicitação.

## <a name="approve-or-deny-request"></a>Aprovar ou negar pedido

Depois de abrir uma solicitação de acesso pendente de aprovação, você pode ver detalhes que o ajudarão a tomar uma decisão aprovada ou negar.

**Papel pré-requisito:** Aprovador

1. Clique no link **Exibir** para abrir o painel Solicitação de acesso.

1. Clique **em Detalhes** para ver detalhes sobre a solicitação de acesso.

    Os detalhes incluem o nome do usuário, organização, data de início e término do acesso, se fornecido, justificativa do negócio, quando a solicitação foi submetida e quando a solicitação expirará.

1. Clique **em Aprovar** ou **Negar**.

1. Se necessário, digite uma razão.

    ![Portal Meus Acessos – Solicitação de acesso](./media/entitlement-management-request-approve/my-access-approve-request.png)

1. Clique em **Enviar** para enviar sua decisão.

    Se uma política for configurada com vários aprovadores, apenas um aprovador precisa tomar uma decisão sobre a aprovação pendente. Após um aprovador submeter sua decisão ao pedido de acesso, a solicitação é concluída e não está mais disponível para os outros aprovadores aprovarem ou negarem o pedido. Os demais aprovados podem ver a decisão da solicitação e o tomador de decisão em seu portal Meu Acesso. Neste momento, apenas a aprovação em um estágio é suportada.

    Se nenhum dos aprovadores configurados for capaz de aprovar ou negar a solicitação de acesso, a solicitação expira após a duração da solicitação configurada. O usuário é notificado de que sua solicitação de acesso expirou e que precisa reenviar a solicitação de acesso.

## <a name="next-steps"></a>Próximas etapas

- [Solicite acesso a um pacote de acesso](entitlement-management-request-access.md)
- [Solicitação de processos e notificações por e-mail](entitlement-management-process.md)
