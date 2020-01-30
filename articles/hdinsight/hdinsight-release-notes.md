---
title: Notas da versão mais recente do Microsoft Azure HDInsight
description: Notas da versão mais recente do Microsoft Azure HDInsight. Obtenha dicas e detalhes de desenvolvimento para Hadoop, Spark, R Server, Hive e muito mais.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 9d484afb1d80ee6b110438cc3ddea1d3d67ad999
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844676"
---
# <a name="release-notes"></a>Notas de versão

Este artigo fornece informações sobre as atualizações de versão **mais recentes** do Azure HDInsight. Para obter informações sobre versões anteriores, consulte o [Arquivo morto de notas de versão do HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Resumo

O Azure HDInsight é um dos serviços mais populares entre clientes empresariais para análise de software livre no Azure.

## <a name="release-date-01092020"></a>Data de lançamento: 01/09/2020

Esta versão se aplica tanto ao HDInsight 3,6 quanto ao 4,0. A versão do HDInsight é disponibilizada para todas as regiões durante vários dias. A data de lançamento aqui indica a data de lançamento da primeira região. Se você não vir as alterações abaixo, aguarde até que a liberação esteja ativa em sua região em vários dias.

> [!IMPORTANT]  
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para saber mais, confira o [artigo sobre controle de versão do HDInsight](hdinsight-component-versioning.md).

## <a name="new-features"></a>Novos recursos
### <a name="tls-12-enforcement"></a>Imposição do TLS 1.2
Os protocolos TLS e SSL são protocolos criptográficos que fornecem segurança de comunicações em uma rede de computadores. Saiba mais sobre o [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0). O HDInsight usa o TLS 1,2 em pontos de extremidade HTTPs públicos, mas o TLS 1,1 ainda tem suporte para compatibilidade com versões anteriores. 

Com esta versão, os clientes podem optar pelo TLS 1,2 apenas para todas as conexões por meio do ponto de extremidade do cluster público. Para dar suporte a isso, a nova propriedade **minSupportedTlsVersion** é introduzida e pode ser especificada durante a criação do cluster. Se a propriedade não estiver definida, o cluster ainda dará suporte a TLS 1,0, 1,1 e 1,2, que é o mesmo comportamento de hoje. Os clientes podem definir o valor dessa propriedade como "1,2", o que significa que o cluster só oferece suporte a TLS 1,2 e superior. Para obter mais informações, consulte [planejar uma rede virtual – segurança da camada de transporte](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#transport-layer-security).

### <a name="bring-your-own-key-for-disk-encryption"></a>Traga sua própria chave para a criptografia de disco
Todos os discos gerenciados no HDInsight são protegidos com o SSE (Criptografia do Serviço de Armazenamento) do Azure. Por padrão, os dados nesses discos são criptografados por chaves gerenciadas pela Microsoft. A partir desta versão, você pode Bring Your Own Key (BYOK) para a criptografia de disco e gerenciá-lo usando Azure Key Vault. A criptografia BYOK é uma configuração de uma etapa durante a criação do cluster sem custo adicional. Basta registrar o HDInsight como uma identidade gerenciada com Azure Key Vault e adicionar a chave de criptografia ao criar o cluster. Para obter mais informações, consulte [criptografia de disco de chave gerenciada pelo cliente](https://docs.microsoft.com/azure/hdinsight/disk-encryption).

## <a name="deprecation"></a>Reprovação
Nenhuma substituição para esta versão. Para se preparar para [as futuras substituições](#upcoming-changes), confira alterações futuras.

## <a name="behavior-changes"></a>Alterações de comportamento
Nenhuma alteração de comportamento para esta versão. Para se preparar para as alterações futuras, confira [alterações futuras](#upcoming-changes).

## <a name="upcoming-changes"></a>Alterações futuras
As seguintes alterações ocorrerão em versões futuras. 

### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>Uma VM de 4 núcleos mínima é necessária para o nó de cabeçalho 
Uma VM de 4 núcleos mínima é necessária para o nó de cabeçalho para garantir a alta disponibilidade e a confiabilidade dos clusters HDInsight. A partir de 6 de abril de 2020, os clientes podem escolher apenas 4 núcleos ou acima da VM como nó principal para os novos clusters HDInsight. Os clusters existentes continuarão sendo executados conforme o esperado. 

### <a name="esp-spark-cluster-node-size-change"></a>Alteração do tamanho do nó do cluster do Spark do ESP 
Na próxima versão, o tamanho mínimo de nó permitido para o cluster do Spark do ESP será alterado para Standard_D13_V2. As VMs da série a podem causar problemas de cluster ESP devido à capacidade relativamente baixa de CPU e memória. As VMs da série A serão preteridas para a criação de novos clusters ESP.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Migrando para conjuntos de dimensionamento de máquinas virtuais do Azure
O HDInsight agora usa máquinas virtuais do Azure para provisionar o cluster. Na próxima versão, o HDInsight usará os conjuntos de dimensionamento de máquinas virtuais do Azure em vez disso. Veja mais sobre os conjuntos de dimensionamento de máquinas virtuais do Azure.

### <a name="hbase-20-to-21"></a>HBase 2,0 a 2,1
Na próxima versão do HDInsight 4,0, a versão do HBase será atualizada da versão 2,0 para a 2,1.

## <a name="bug-fixes"></a>Correções de bug
O HDInsight continua a tornar as melhorias de desempenho e confiabilidade do cluster. 

## <a name="component-version-change"></a>Alteração de versão do componente
Nenhuma alteração de versão de componente para esta versão. Você pode encontrar as versões de componente atuais para o HDInsight 4,0 Ad HDInsight 3,6 aqui.

## <a name="known-issues"></a>Problemas conhecidos

A partir de 24 de janeiro de 2020, há um problema ativo no qual você pode receber um erro ao tentar usar um notebook Jupyter. Use as etapas abaixo para corrigir o problema. Você também pode consultar esta [postagem do MSDN](https://social.msdn.microsoft.com/Forums/en-us/8c763fb4-79a9-496f-a75c-44a125e934ac/hdinshight-create-not-create-jupyter-notebook?forum=hdinsight) ou esta [postagem do StackOverflow](https://stackoverflow.com/questions/59687614/azure-hdinsight-jupyter-notebook-not-working/59831103) para obter informações atualizadas ou para fazer perguntas adicionais. Esta página será atualizada quando o problema for corrigido.

**Erros**

* ValueError: não é possível converter o bloco de anotações em V5 porque essa versão não existe
* Erro ao carregar o bloco de anotações ocorreu um erro desconhecido ao carregar este bloco de anotações. Esta versão pode carregar formatos de bloco de anotações v4 ou anterior

**Causa** 

O arquivo _version. py no cluster foi atualizado para 5. x, em vez de 4.4. x. # #.

**Solução**

Se você criar um novo notebook Jupyter e receber um dos erros listados acima, execute as etapas a seguir para corrigir o problema.

1. Abra o Ambari em um navegador da Web acessando https://CLUSTERNAME.azurehdinsight.net, em que CLUSTERname é o nome do cluster.
1. No Ambari, no menu à esquerda, clique em **Jupyter**e, em seguida, em **ações de serviço**, clique em **parar**.
1. SSH no cluster cabeçalho onde o serviço Jupyter está em execução.
1. Abra o seguinte arquivo/usr/bin/Anaconda/lib/python2.7/site-Packages/nbformat/_version. py no modo sudo.
1. A entrada existente deve mostrar algo semelhante ao código a seguir: 

    version_info = (5, 0, 3)

    Modifique a entrada para: 
    
    version_info = (4, 4, 0)
1. Salve o arquivo.
1. Volte para Ambari e, em **ações de serviço**, clique em **reiniciar tudo**.
