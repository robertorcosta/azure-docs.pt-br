---
title: Solucionar problemas falhas de conexão do cluster do Azure Data Explorer
description: Este artigo descreve etapas de solução de problemas para se conectar a um cluster no Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: c71af799f614e9cd28221d79634666cbc3b2c987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "60827029"
---
# <a name="troubleshoot-failure-to-connect-to-a-cluster-in-azure-data-explorer"></a>Solucionar problemas: Falha ao se conectar a um cluster no Data Explorer do Azure

Se você não conseguir se conectar a um cluster no Data Explorer do Azure, siga estas etapas.

1. Certifique-se de que sua cadeia de conexão está correta. Deve estar no formato: `https://<ClusterName>.<Region>.kusto.windows.net`, como no seguinte exemplo: `https://docscluster.westus.kusto.windows.net`.

1. Certifique-se de ter as permissões adequadas. Se você não fizer isso, obterá uma resposta de *não autorizado*.

    Para obter mais informações sobre permissões, consulte [Gerenciar permissões de banco de dados](manage-database-permissions.md). Se necessário, trabalhe junto ao administrador ou do cluster para que você possa ser adicionado à função apropriada.

1. Verifique se o cluster ainda não foi excluído: Examine o log de atividades em sua assinatura.

1. Verifique o [painel de integridade do serviço do Azure](https://azure.microsoft.com/status/). Procure o status do Data Explorer do Azure na região em que você está tentando se conectar a um cluster.

    Se o status não for **bom** (marca de verificação verde), tente conectar-se ao cluster depois que o status melhorar.

1. Se você ainda precisar de ajuda para resolver seu problema, abra uma solicitação de suporte no [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).