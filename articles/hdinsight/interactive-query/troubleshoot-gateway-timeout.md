---
title: Exceção ao executar consultas do Apache Ambari Hive View no Azure HDInsight
description: Solucionando etapas ao executar consultas apache hive através do Apache Ambari Hive View no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 12/23/2019
ms.openlocfilehash: 809b2e383eb57b730fd76ec2194764178aa810c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895043"
---
# <a name="exception-when-running-queries-from-apache-ambari-hive-view-in-azure-hdinsight"></a>Exceção ao executar consultas do Apache Ambari Hive View no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Ao executar uma consulta da Colmeia Apache do Apache Ambari Hive View, você recebe a seguinte mensagem de erro intermitentemente:

```error
Cannot create property 'errors' on string '<!DOCTYPE html PUBLIC '-//W3C//DTD XHTML 1.0 Strict//EN' 'http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd'>
<html xmlns='http://www.w3.org/1999/xhtml'>
<head>
<title>IIS 8.5 Detailed Error - 502.3 - Bad Gateway</title>
```

## <a name="cause"></a>Causa

Um tempo fora do Gateway.

O valor do tempo do Gateway é de 2 minutos. As consultas da Exibição ambari Hive são submetidas ao `/hive2` ponto final através do gateway. Uma vez que a consulta seja compilada e `queryid`aceita com sucesso, o HiveServer retorna um . Em seguida, os clientes continuam pesquisando para o status da consulta. Durante esse processo, se o HiveServer não retornar uma resposta HTTP dentro de 2 minutos, o HDI Gateway lançará um erro de tempo do Gateway 502.3 para o chamador. Os erros podem acontecer quando a consulta é submetida ao processamento (mais provável) e também na chamada get status (menos provável). Os usuários podiam ver qualquer um deles.

O segmento http handler deve ser rápido: prepare `queryid`o trabalho e devolva um . No entanto, devido a várias razões, todos os threads do manipulador podem estar ocupados resultando em tempo out para novas consultas e as chamadas de status get.

### <a name="responsibilities-of-the-http-handler-thread"></a>Responsabilidades do segmento de manipulador HTTP

Quando o cliente envia uma consulta ao HiveServer, ele faz o seguinte no segmento de primeiro plano:

* Analise o pedido, faça verificação semântica
* Adquira bloqueio
* Pesquisa do Metastore, se necessário
* Compilar a consulta (DDL ou DML)
* Prepare um plano de consulta
* Executar a autorização (Execute todas as políticas de ranger aplicáveis em clusters seguros)

## <a name="resolution"></a>Resolução

Algumas recomendações gerais para você melhorar a situação:

* Se estiver usando um metastore externo da colmeia, verifique as métricas do DB e certifique-se de que o banco de dados não está sobrecarregado. Considere escalar a camada de banco de dados metastore.

* Certifique-se de que as operações paralelas estão ligadas (isso permite que os segmentos do manipulador HTTP seja executados em paralelo). Para verificar o valor, inicie [o Apache Ambari](../hdinsight-hadoop-manage-ambari.md) e navegue até o**site de colmeias personalizadas****avançadas** > **Advanced** > da **Colmeia.** >  O valor `hive.server2.parallel.ops.in.session` para `true`deve ser .

* Certifique-se de que o VM SKU do cluster não é muito pequeno para a carga. Considere dividir o trabalho entre vários clusters. Para obter mais informações, consulte [Escolha um tipo de cluster](../hdinsight-capacity-planning.md#choose-a-cluster-type).

* Se a Ranger estiver instalada no cluster, verifique se há muitas políticas de Ranger que precisam ser avaliadas para cada consulta. Procure por políticas duplicadas ou desnecessárias.

* Verifique o valor do tamanho do **heap hiveServer2** da Ambari. Navegue até a**otimização**das**configurações** > **Settings** > da **colmeia** > . Certifique-se de que o valor é maior que 10 GB. Ajuste conforme necessário para otimizar o desempenho.

* Certifique-se de que a consulta da Colmeia está bem ajustada. Para obter mais informações, consulte [Otimizar as consultas da Colmeia Apache no Azure HDInsight](../hdinsight-hadoop-optimize-hive-query.md).

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais [detalhadas, consulte Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
