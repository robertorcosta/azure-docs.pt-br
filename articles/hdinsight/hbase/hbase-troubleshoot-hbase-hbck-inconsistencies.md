---
title: hbase hbck retorna inconsistências no Azure HDInsight
description: hbase hbck retorna inconsistências no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: fa02ac0dfe229f3e82d1c1c62d83ca06a81efca6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887318"
---
# <a name="scenario-hbase-hbck-command-returns-inconsistencies-in-azure-hdinsight"></a>Cenário: `hbase hbck` comando retorna inconsistências no Azure HDInsight

Este artigo descreve etapas de solução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue-region-is-not-in-hbasemeta"></a>Questão: Região não está em`hbase:meta`

Região xxx no HDFS, `hbase:meta` mas não listada ou implantada em qualquer servidor da região.

### <a name="cause"></a>Causa

Varia.

### <a name="resolution"></a>Resolução

1. Corrigir a tabela meta executando:

    ```
    hbase hbck -ignorePreCheckPermission –fixMeta
    ```

1. Atribuir regiões a Servidores regionais executando:

    ```
    hbase hbck -ignorePreCheckPermission –fixAssignment
    ```
---

## <a name="issue-region-is-offline"></a>Problema: Região está offline

Região xxx não implantada em nenhum RegionServer. Isso significa que `hbase:meta`a região está dentro, mas offline.

### <a name="cause"></a>Causa

Varia.

### <a name="resolution"></a>Resolução

Incomode as regiões online executando:

```
hbase hbck -ignorePreCheckPermission –fixAssignment
```

---

## <a name="issue-regions-have-the-same-startend-keys"></a>Problema: As regiões têm as mesmas chaves de início/fim

### <a name="cause"></a>Causa

Varia.

### <a name="resolution"></a>Resolução

Fundir manualmente essas regiões sobrepostas. Vá para a seção de tabela de UI da Web HBase HMaster, selecione o link da tabela, que tem o problema. Você verá a chave de início/chave final de cada região pertencente a essa tabela. Em seguida, fundir essas regiões sobrepostas. Em hbase shell, fazer `merge_region 'xxxxxxxx','yyyyyyy', true`. Por exemplo: 

```
RegionA, startkey:001, endkey:010,

RegionB, startkey:001, endkey:080,

RegionC, startkey:010, endkey:080.
```

Neste cenário, você precisa mesclar RegionA e RegionC e obter RegionD com a mesma faixa de chave que RegionB, em seguida, fundir RegionB e RegionD. xxxxxxx e yyyyyy y são a string hash no final de cada nome da região. Tenha cuidado aqui para não fundir duas regiões descontínuas. Após cada mesclagem, como mesclar A e C, o HBase iniciará uma compactação no RegionD. Aguarde que a compactação termine antes de fazer outra fusão com a RegionD. Você pode encontrar o status de compactação na página do servidor da região na UI HBase HMaster.

---

## <a name="issue-cant-load-regioninfo"></a>Problema: Não é possível carregar`.regioninfo`

Não posso `.regioninfo` carregar `/hbase/data/default/tablex/regiony`para a região.

### <a name="cause"></a>Causa

Isso é provavelmente devido à exclusão parcial da região quando o RegionServer falha ou reinicializa a VM. Atualmente, o Azure Storage é um sistema de arquivos flat blob e algumas operações de arquivo não são atômicas.

### <a name="resolution"></a>Resolução

Limpe manualmente esses arquivos e pastas restantes:

1. Execute `hdfs dfs -ls /hbase/data/default/tablex/regiony` para verificar quais pastas/arquivos ainda estão ele.

1. Execute `hdfs dfs -rmr /hbase/data/default/tablex/regiony/filez` para excluir todos os arquivos/pastas do filho

1. Execute `hdfs dfs -rmr /hbase/data/default/tablex/regiony` para excluir a pasta região.

---

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure através [do Azure Community Support](https://azure.microsoft.com/support/community/).

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Conectando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se você precisar de mais ajuda, você pode enviar uma solicitação de suporte do [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o centro **de suporte Ajuda +.** Para obter informações mais [detalhadas, consulte Como criar uma solicitação de suporte ao Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e suporte ao faturamento está incluído na assinatura do Microsoft Azure, e o suporte técnico é fornecido através de um dos Planos de Suporte do [Azure](https://azure.microsoft.com/support/plans/).
