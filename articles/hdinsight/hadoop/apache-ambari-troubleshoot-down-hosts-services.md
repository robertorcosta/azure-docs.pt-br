---
title: UI Apache Ambari mostra hosts e serviços no Azure HDInsight
description: Solução de problemas de um problema de UI Apache Ambari quando ele mostra hosts e serviços no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/02/2019
ms.openlocfilehash: 5340b1c7a6510595376789bc5777e6fb6f07dd4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895645"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>Cenário: UI Apache Ambari mostra hosts e serviços no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Apache Ambari UI é acessível, mas a interface do u mostra que quase todos os serviços caíram, todos os hosts mostram batimentos cardíacos perdidos.

## <a name="cause"></a>Causa

Na maioria dos cenários, este é um problema com o servidor Ambari não sendo executado no headnode ativo. Verifique qual cabeçade é o cabeçada ativo e certifique-se de que seu servidor ambari seja executado no direito. Não inicie manualmente o ambari-servidor, deixe que o serviço do controlador failover seja responsável por iniciar o ambari-servidor no cabeçada direito. Reinicie o cabeçada ativo para forçar um failover.

Problemas de rede também podem causar esse problema. De cada nó de cluster, `headnodehost`veja se você pode ping . Há uma situação rara em que `headnodehost`nenhum nó de cluster pode se conectar a:

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>Resolução

Normalmente, reiniciar o cabeçanode ativo atenuará esse problema. Se não entrar em contato com a equipe de suporte do HDInsight.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais detalhadas, consulte [Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
