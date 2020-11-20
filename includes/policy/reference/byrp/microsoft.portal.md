---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c3c9e698aeddbe4818e7b77596eed81e60cb38e5
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "95002134"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Os painéis compartilhados não devem ter blocos de redução com conteúdo embutido](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |Não permita a criação de um painel compartilhado que tenha conteúdo embutido em blocos de redução e imponha que o conteúdo deve ser armazenado como um arquivo de redução hospedado online. Se você usar conteúdo embutido no bloco de redução, não poderá gerenciar a criptografia do conteúdo. Ao configurar seu próprio armazenamento, você pode criptografar, criptografar duas vezes e até mesmo trazer suas próprias chaves. Habilitar essa política restringe os usuários a usarem a versão 2020-09-01-Preview ou superior da API REST dos painéis compartilhados. |Audit, Deny, desabilitado |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |
