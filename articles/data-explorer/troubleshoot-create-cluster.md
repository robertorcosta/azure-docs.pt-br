---
title: Falha na solução de problemas da criação do cluster Do Azure Data Explorer
description: Este artigo descreve etapas de solução de problemas para criar um cluster no Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 863d9ecedf095e0ab284a0d7fd86363b69ae5658
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562404"
---
# <a name="troubleshoot-failed-cluster-creation-of-azure-data-explorer"></a>Solução de problemas: Criação de cluster falha do Azure Data Explorer

No caso improvável de falha na criação de cluster no Azure Data Explorer, siga estas etapas.

1. Certifique-se de ter as permissões adequadas. Para criar um cluster, você deve ser um membro da função *Contributor* ou *Owner* para a assinatura do Azure. Se necessário, trabalhe junto ao administrador da assinatura para que você possa ser adicionado à função apropriada.

1. Verifique se não há erros de validação relacionados ao nome do cluster digitado em **Criar cluster** no portal do Azure.

1. Verifique o [painel de integridade do serviço do Azure](https://azure.microsoft.com/status/). Procure o status do Azure Data Explorer na região em que você está tentando criar o cluster.

    Se o status não for **Bom** (marca de seleção verde), tente criar o cluster após a melhoria do status.

1. Se você ainda precisar de ajuda para resolver seu problema, abra uma solicitação de suporte no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
