---
title: Solucionar problemas no desempenho da atividade de cópia
description: Saiba como solucionar problemas no desempenho da atividade de cópia na Fábrica de Dados do Azure.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/11/2020
ms.openlocfilehash: 963b86852a7df557ad7179e444e7c3a2692f57d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531445"
---
# <a name="troubleshoot-copy-activity-performance"></a>Solucionar problemas no desempenho da atividade de cópia

Este artigo descreve como solucionar problemas de desempenho de atividades na Fábrica de Dados Do Azure. 

Depois de executar uma atividade de cópia, você pode coletar o resultado da execução e as estatísticas de desempenho na exibição [de monitoramento de atividades](copy-activity-monitoring.md) de cópia. A seguir, é mostrado um exemplo.

![Monitorar detalhes da execução da atividade de cópia](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="performance-tuning-tips"></a>Dicas de ajuste de desempenho

Em alguns cenários, quando você executa uma atividade de cópia na Fábrica de Dados, você verá **"Dicas de ajuste de desempenho"** na parte superior, como mostrado no exemplo acima. As dicas dizem o gargalo identificado pela ADF para esta execução de cópia em particular, juntamente com sugestão de como impulsionar o throughput de cópia. Tente fazer a mudança reordenada e execute a cópia novamente.

Como referência, atualmente as dicas de ajuste de desempenho fornecem sugestões para os seguintes casos:

| Categoria              | Dicas de ajuste de desempenho                                      |
| --------------------- | ------------------------------------------------------------ |
| Armazenamento de dados específico   | Carregando dados no **Azure Synpase Analytics (anteriormente SQL DW)**: sugiro o uso da declaração PolyBase ou COPY se não for usada. |
| &nbsp;                | Copiar dados do Banco de **Dados SQL do Azure:** quando o DTU estiver alta utilização, sugira a atualização para um nível mais alto. |
| &nbsp;                | Copiar dados de/para **OZure Cosmos DB**: quando o RU estiver alta utilização, sugira a atualização para ru maior. |
| &nbsp;                | Inserindo dados do **Amazon Redshift**: sugira o uso do UNLOAD se ele não for usado. |
| Estrangulamento do armazenamento de dados | Se uma série de operações de leitura/gravação forem estranguladas pelo armazenamento de dados durante a cópia, sugira a verificação e aumente a taxa de solicitação permitida para o armazenamento de dados ou reduza a carga de trabalho simultânea. |
| Tempo de execução da integração  | Se você usar um **IR (Integration Time de integração auto-hospedado)** e copiar atividades esperar muito tempo na fila até que o IR tenha recursos disponíveis para executar, sugiro a escalação do seu IR. |
| &nbsp;                | Se você usar um **Runtime de integração do Azure** que esteja em uma região não ideal, resultando em leitura/gravação lenta, sugiro a configuração para usar um IR em outra região. |
| Tolerância a falhas       | Se você configurar a tolerância a falhas e pular linhas incompatíveis resulta em desempenho lento, sugiro garantir que os dados de origem e sumidouro sejam compatíveis. |
| Cópia em etapas           | Se a cópia em estágio estiver configurada, mas não útil para o par de pia de origem, sugiro removê-la. |
| Continuar                | Quando a atividade de cópia for retomada do último ponto de falha, mas por acaso você alterar a configuração DIU após a execução original, observe que a nova configuração DIU não faz efeito. |

## <a name="understand-copy-activity-execution-details"></a>Entenda os detalhes da execução da atividade de cópia

Os detalhes e durações da execução na parte inferior da exibição de monitoramento da atividade de cópia descrevem as principais etapas pelas quais sua atividade de cópia passa (veja exemplo no início deste artigo), o que é especialmente útil para solucionar problemas no desempenho da cópia. O gargalo da sua cópia é aquele com maior duração. Consulte a tabela a seguir na definição de cada estágio e aprenda como [solucionar problemas de atividade de cópia no Azure IR](#troubleshoot-copy-activity-on-azure-ir) e [solucionar problemas na atividade de cópia auto-hospedada](#troubleshoot-copy-activity-on-self-hosted-ir) com essas informações.

| Estágio           | Descrição                                                  |
| --------------- | ------------------------------------------------------------ |
| Fila           | O tempo decorrido até que a atividade de cópia realmente comece no tempo de execução da integração. |
| Script de pré-cópia | O tempo decorrido entre a atividade de cópia começando no IR e o acabamento da atividade de cópia executando o script de pré-cópia no armazenamento de dados do dissipador. Aplicar quando você configurar o script de pré-cópia para sinks de banco de dados, por exemplo, ao gravar dados no Banco de Dados SQL do Azure, faça a limpeza antes de copiar novos dados. |
| Transferir        | O tempo decorrido entre o final da etapa anterior e o IR transferindo todos os dados da fonte para o afundamento. Subpassos em "Transferência" são executados em paralelo.<br><br>- **Hora do primeiro byte:** O tempo decorrido entre o final da etapa anterior e o momento em que o IR recebe o primeiro byte do armazenamento de dados de origem. Aplica-se a fontes não baseadas em arquivos.<br>- **Fonte da listagem:** A quantidade de tempo gasto em enumerar arquivos de origem ou partições de dados. Este último se aplica quando você configura opções de partição para fontes de banco de dados, por exemplo, quando copia dados de bancos de dados como Oracle/SAP HANA/Teradata/Netezza/etc.<br/>-**Leitura da fonte:** A quantidade de tempo gasto na recuperação de dados do armazenamento de dados de origem.<br/>- **Escrevendo para afundar:** A quantidade de tempo gasto na gravação de dados para afundar o armazenamento de dados. |

## <a name="troubleshoot-copy-activity-on-azure-ir"></a>Solucionar problemas na atividade de cópia no Azure IR

Siga as [etapas de ajuste de desempenho](copy-activity-performance.md#performance-tuning-steps) para planejar e realizar o teste de desempenho para o seu cenário. 

Quando o desempenho da atividade de cópia não atender à sua expectativa, para solucionar problemas de atividade de cópia única em execução no Azure Integration Runtime, se você ver [dicas de ajuste de desempenho](#performance-tuning-tips) exibidas na exibição de monitoramento de cópia, aplique a sugestão e tente novamente. Caso contrário, [entenda os detalhes da execução da atividade de cópia,](#understand-copy-activity-execution-details)verifique qual estágio tem **a maior** duração e aplique a orientação abaixo para aumentar o desempenho da cópia:

- **"Script de pré-cópia" teve longa duração:** significa que o script de pré-cópia em execução no banco de dados sink leva muito tempo para ser concluído. Ajuste a lógica de script de pré-cópia especificada para melhorar o desempenho. Se você precisar de mais ajuda para melhorar o script, entre em contato com sua equipe de banco de dados.

- **"Transfer - Time to first byte" experimentou longa duração de trabalho:** significa que sua consulta de origem leva muito tempo para retornar quaisquer dados. Verifique e otimize a consulta ou servidor. Se precisar de mais ajuda, entre em contato com sua equipe de armazenamento de dados.

- **"Transfer - Listing source" experimentou longa duração de trabalho:** significa que enumerar arquivos de origem ou partições de dados de banco de dados de origem é lento.

  - Ao copiar dados da fonte baseada em arquivos, se você usar **o filtro curinga** no`modifiedDatetimeStart` caminho`modifiedDatetimeEnd`da pasta ou no nome do arquivo (ou),`wildcardFolderPath` `wildcardFileName`ou usar o **filtro de tempo modificado último** arquivo (ou), note que tal filtro resultaria na atividade de cópia listando todos os arquivos a pasta especificada para o lado do cliente e, em seguida, aplicar o filtro. Essa enumeração de arquivos pode se tornar o gargalo especialmente quando apenas um pequeno conjunto de arquivos cumpriu a regra do filtro.

    - Verifique se você pode [copiar arquivos com base no caminho ou nome do arquivo particionado datado .](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md) Dessa forma, não traz ônus para listar o lado da fonte.

    - Verifique se você pode usar o filtro nativo do data store em vez disso, especificamente "**prefixo**" para Amazon S3 e Azure Blob. O filtro prefixo é um filtro do lado do servidor do armazenamento de dados e teria um desempenho muito melhor.

    - Considere dividir um único conjunto de dados grandes em vários conjuntos de dados menores e permitir que esses trabalhos de cópia sejam executados simultaneamente cada uma delas aborda parte dos dados. Você pode fazer isso com o Lookup/GetMetadata + ForEach + Copy. Consulte [arquivos Copiar de vários contêineres](solution-template-copy-files-multiple-containers.md) ou migrar dados do Amazon S3 para modelos de solução [ADLS Gen2](solution-template-migration-s3-azure.md) como exemplo geral.

  - Verifique se o ADF relata algum erro de estrangulamento na origem ou se o seu armazenamento de dados está em estado de alta utilização. Se assim for, reduza suas cargas de trabalho no armazenamento de dados ou tente entrar em contato com o administrador do seu armazenamento de dados para aumentar o limite de estrangulamento ou recurso disponível.

  - Use o Azure IR na mesma ou perto da região do seu armazenamento de dados de origem.

- **"Transferência - leitura da fonte" experimentou longa duração de trabalho:** 

  - Adote as práticas recomendadas de carregamento de dados específicas do conector, se for em caso de inscrição. Por exemplo, ao copiar dados do [Amazon Redshift,](connector-amazon-redshift.md)configure para usar Redshift UNLOAD.

  - Verifique se o ADF relata algum erro de estrangulamento na origem ou se seu armazenamento de dados está alta utilização. Se assim for, reduza suas cargas de trabalho no armazenamento de dados ou tente entrar em contato com o administrador do seu armazenamento de dados para aumentar o limite de estrangulamento ou recurso disponível.

  - Verifique a origem da sua cópia e o padrão da pia: 

    - Se o seu padrão de cópia suportar maiores que 4 DIUs (Data Integration Units, unidades de integração de dados) - consulte [esta seção](copy-activity-performance.md#data-integration-units) sobre detalhes, geralmente você pode tentar aumentar os DIUs para obter um melhor desempenho. 

    - Caso contrário, considere dividir um único conjunto de dados grandes em vários conjuntos de dados menores e permitir que esses trabalhos de cópia sejam executados simultaneamente cada uma delas aborda parte dos dados. Você pode fazer isso com o Lookup/GetMetadata + ForEach + Copy. Consulte [copiar arquivos de vários contêineres,](solution-template-copy-files-multiple-containers.md) [migrar dados do Amazon S3 para ODLS Gen2](solution-template-migration-s3-azure.md)ou copiar em massa com [modelos de](solution-template-bulk-copy-with-control-table.md) solução de tabela de controle como exemplo geral.

  - Use o Azure IR na mesma ou perto da região do seu armazenamento de dados de origem.

- **"Transfer - writing to sink" experimentou longa duração de trabalho:**

  - Adote as práticas recomendadas de carregamento de dados específicas do conector, se for em caso de inscrição. Por exemplo, ao copiar dados no [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) (anteriormente SQL DW), use a declaração PolyBase ou COPY. 

  - Verifique se o ADF relata algum erro de estrangulamento na pia ou se o seu armazenamento de dados está alta utilização. Se assim for, reduza suas cargas de trabalho no armazenamento de dados ou tente entrar em contato com o administrador do seu armazenamento de dados para aumentar o limite de estrangulamento ou recurso disponível.

  - Verifique a origem da sua cópia e o padrão da pia: 

    - Se o seu padrão de cópia suportar maiores que 4 DIUs (Data Integration Units, unidades de integração de dados) - consulte [esta seção](copy-activity-performance.md#data-integration-units) sobre detalhes, geralmente você pode tentar aumentar os DIUs para obter um melhor desempenho. 

    - Caso contrário, ajuste gradualmente as [cópias paralelas,](copy-activity-performance-features.md)note que muitas cópias paralelas podem até prejudicar o desempenho.

  - Use o Azure IR na mesma ou perto da região do seu armazenamento de dados da pia.

## <a name="troubleshoot-copy-activity-on-self-hosted-ir"></a>Solucionar problemas na atividade de cópia no IR auto-hospedado

Siga as [etapas de ajuste de desempenho](copy-activity-performance.md#performance-tuning-steps) para planejar e realizar o teste de desempenho para o seu cenário. 

Quando o desempenho da cópia não atender à sua expectativa, para solucionar problemas de atividade de cópia única em execução no Azure Integration Runtime, se você ver [dicas de ajuste de desempenho](#performance-tuning-tips) exibidas na exibição de monitoramento de cópia, aplique a sugestão e tente novamente. Caso contrário, [entenda os detalhes da execução da atividade de cópia,](#understand-copy-activity-execution-details)verifique qual estágio tem **a maior** duração e aplique a orientação abaixo para aumentar o desempenho da cópia:

- **"Fila" teve longa duração:** significa que a atividade de cópia espera muito tempo na fila até que seu IR auto-hospedado tenha recursos para executar. Verifique a capacidade e o uso do IR e [faça a escala ou a sua](create-self-hosted-integration-runtime.md#high-availability-and-scalability) carga horária.

- **"Transfer - Time to first byte" experimentou longa duração de trabalho:** significa que sua consulta de origem leva muito tempo para retornar quaisquer dados. Verifique e otimize a consulta ou servidor. Se precisar de mais ajuda, entre em contato com sua equipe de armazenamento de dados.

- **"Transfer - Listing source" experimentou longa duração de trabalho:** significa que enumerar arquivos de origem ou partições de dados de banco de dados de origem é lento.

  - Verifique se a máquina de RI auto-hospedada tem baixa latência conectando-se ao armazenamento de dados de origem. Se a sua fonte estiver no Azure, você pode usar [esta ferramenta](http://www.azurespeed.com/Azure/Latency) para verificar a latência da máquina de IR auto-hospedada para a região do Azure, quanto menos melhor.

  - Ao copiar dados da fonte baseada em arquivos, se você usar **o filtro curinga** no`modifiedDatetimeStart` caminho`modifiedDatetimeEnd`da pasta ou no nome do arquivo (ou),`wildcardFolderPath` `wildcardFileName`ou usar o **filtro de tempo modificado último** arquivo (ou), note que tal filtro resultaria na atividade de cópia listando todos os arquivos a pasta especificada para o lado do cliente e, em seguida, aplicar o filtro. Essa enumeração de arquivos pode se tornar o gargalo especialmente quando apenas um pequeno conjunto de arquivos cumpriu a regra do filtro.

    - Verifique se você pode [copiar arquivos com base no caminho ou nome do arquivo particionado datado .](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md) Dessa forma, não traz ônus para listar o lado da fonte.

    - Verifique se você pode usar o filtro nativo do data store em vez disso, especificamente "**prefixo**" para Amazon S3 e Azure Blob. O filtro prefixo é um filtro do lado do servidor do armazenamento de dados e teria um desempenho muito melhor.

    - Considere dividir um único conjunto de dados grandes em vários conjuntos de dados menores e permitir que esses trabalhos de cópia sejam executados simultaneamente cada uma delas aborda parte dos dados. Você pode fazer isso com o Lookup/GetMetadata + ForEach + Copy. Consulte [arquivos Copiar de vários contêineres](solution-template-copy-files-multiple-containers.md) ou migrar dados do Amazon S3 para modelos de solução [ADLS Gen2](solution-template-migration-s3-azure.md) como exemplo geral.

  - Verifique se o ADF relata algum erro de estrangulamento na origem ou se o seu armazenamento de dados está em estado de alta utilização. Se assim for, reduza suas cargas de trabalho no armazenamento de dados ou tente entrar em contato com o administrador do seu armazenamento de dados para aumentar o limite de estrangulamento ou recurso disponível.

- **"Transferência - leitura da fonte" experimentou longa duração de trabalho:** 

  - Verifique se a máquina de RI auto-hospedada tem baixa latência conectando-se ao armazenamento de dados de origem. Se a sua fonte estiver no Azure, você pode usar [esta ferramenta](http://www.azurespeed.com/Azure/Latency) para verificar a latência da máquina de IR auto-hospedada para as regiões do Azure, quanto menos melhor.

  - Verifique se a máquina de IR auto-hospedada tem largura de banda de entrada suficiente para ler e transferir os dados de forma eficiente. Se o seu armazenamento de dados de origem estiver no Azure, você pode usar [esta ferramenta](https://www.azurespeed.com/Azure/Download) para verificar a velocidade de download.

  - Verifique a tendência de uso de memória e CPU do IR hospedado no portal Azure - > sua página de visão geral de fábrica de dados - >. Considere [escalar o IR](create-self-hosted-integration-runtime.md#high-availability-and-scalability) se o uso da CPU estiver alto ou a memória disponível estiver baixa.

  - Adote as práticas recomendadas de carregamento de dados específicas do conector, se for em caso de inscrição. Por exemplo: 

    - Ao copiar dados da [Oracle,](connector-oracle.md#oracle-as-source) [Netezza,](connector-netezza.md#netezza-as-source) [Teradata,](connector-teradata.md#teradata-as-source) [SAP HANA,](connector-sap-hana.md#sap-hana-as-source) [SAP Table](connector-sap-table.md#sap-table-as-source)e [SAP Open Hub,](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)habilite as opções de partição de dados para copiar dados em paralelo.

    - Ao copiar dados do [HDFS,](connector-hdfs.md)configure-o para usar ODIs.

    - Ao copiar dados do [Amazon Redshift,](connector-amazon-redshift.md)configure para usar Redshift UNLOAD.

  - Verifique se o ADF reporta algum erro de estrangulamento na origem ou se seu armazenamento de dados está alta utilização. Se assim for, reduza suas cargas de trabalho no armazenamento de dados ou tente entrar em contato com o administrador do seu armazenamento de dados para aumentar o limite de estrangulamento ou recurso disponível.

  - Verifique a origem da sua cópia e o padrão da pia: 

    - Se você copiar dados de armazenamentos de dados habilitados para opção de partição, considere ajustar gradualmente as [cópias paralelas,](copy-activity-performance-features.md)observe que muitas cópias paralelas podem até prejudicar o desempenho.

    - Caso contrário, considere dividir um único conjunto de dados grandes em vários conjuntos de dados menores e permitir que esses trabalhos de cópia sejam executados simultaneamente cada uma delas aborda parte dos dados. Você pode fazer isso com o Lookup/GetMetadata + ForEach + Copy. Consulte [copiar arquivos de vários contêineres,](solution-template-copy-files-multiple-containers.md) [migrar dados do Amazon S3 para ODLS Gen2](solution-template-migration-s3-azure.md)ou copiar em massa com [modelos de](solution-template-bulk-copy-with-control-table.md) solução de tabela de controle como exemplo geral.

- **"Transfer - writing to sink" experimentou longa duração de trabalho:**

  - Adote as práticas recomendadas de carregamento de dados específicas do conector, se for em caso de inscrição. Por exemplo, ao copiar dados no [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) (anteriormente SQL DW), use a declaração PolyBase ou COPY. 

  - Verifique se a máquina de IR auto-hospedada tem baixa latência conectando-se ao armazenamento de dados da pia. Se a sua pia estiver no Azure, você pode usar [esta ferramenta](http://www.azurespeed.com/Azure/Latency) para verificar a latência da máquina de IR auto-hospedada para a região do Azure, quanto menos melhor.

  - Verifique se a máquina de IR auto-hospedada tem largura de banda de saída suficiente para transferir e gravar os dados de forma eficiente. Se o seu armazenamento de dados da pia estiver no Azure, você pode usar [esta ferramenta](https://www.azurespeed.com/Azure/UploadLargeFile) para verificar a velocidade de upload.

  - Verifique se a tendência de uso de CPU e memória do IR hospedado no portal Azure - > sua página de visão geral de fábrica de dados - >. Considere [escalar o IR](create-self-hosted-integration-runtime.md#high-availability-and-scalability) se o uso da CPU estiver alto ou a memória disponível estiver baixa.

  - Verifique se o ADF relata algum erro de estrangulamento na pia ou se o seu armazenamento de dados está alta utilização. Se assim for, reduza suas cargas de trabalho no armazenamento de dados ou tente entrar em contato com o administrador do seu armazenamento de dados para aumentar o limite de estrangulamento ou recurso disponível.

  - Considere afinar gradualmente as [cópias paralelas,](copy-activity-performance-features.md)note que muitas cópias paralelas podem até prejudicar o desempenho.

## <a name="other-references"></a>Outras referências

Aqui estão as referências de monitoramento e ajuste do desempenho para alguns dos armazenamentos de dados com suporte:

* Armazenamento Azure Blob: [Metas de escalabilidade e desempenho para armazenamento Blob](../storage/blobs/scalability-targets.md) e [lista de verificação de desempenho e escalabilidade para armazenamento Blob](../storage/blobs/storage-performance-checklist.md).
* Armazenamento de tabela do Azure: [metas de escalabilidade e desempenho para armazenamento de tabela](../storage/tables/scalability-targets.md) e [verificação de desempenho e escalabilidade para armazenamento de tabela](../storage/tables/storage-performance-checklist.md).
* Banco de dados Azure SQL: Você pode [monitorar o desempenho](../sql-database/sql-database-single-database-monitor.md) e verificar a porcentagem da Unidade de Transações de Banco de Dados (DTU).
* Azure SQL Data Warehouse: Sua capacidade é medida em Unidades de Data Warehouse (DWUs). Consulte [Gerenciar o poder de computação no Azure SQL Data Warehouse (Visão geral)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Azure Cosmos DB: [Níveis de desempenho no Azure Cosmos DB](../cosmos-db/performance-levels.md).
* Servidor SQL no local: [monitore e ajuste para desempenho](https://msdn.microsoft.com/library/ms189081.aspx).
* Servidor de arquivos no local: [ajuste de desempenho para servidores de arquivos](https://msdn.microsoft.com/library/dn567661.aspx).

## <a name="next-steps"></a>Próximas etapas
Consulte os outros artigos de atividade de cópia:

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Copiar o desempenho da atividade e o guia de escalabilidade](copy-activity-performance.md)
- [Recursos de otimização do desempenho da atividade de cópia](copy-activity-performance-features.md)
- [Use a Fábrica de Dados do Azure para migrar dados do seu lago de dados ou data warehouse para o Azure](data-migration-guidance-overview.md)
- [Migrar dados do Amazon S3 para o Armazenamento do Azure](data-migration-guidance-s3-azure-storage.md)
