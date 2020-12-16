---
title: Solucionar problemas de orquestração e gatilhos de pipeline no ADF
description: Use métodos diferentes para solucionar problemas de gatilho de pipeline no ADF
author: ssabat
ms.service: data-factory
ms.date: 12/15/2020
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: ed3728513820da9f4ef85d44cac983dc09c3fc7d
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97521813"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-adf"></a>Solucionar problemas de orquestração e gatilhos de pipeline no ADF

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Uma execução de pipeline no Azure Data Factory define uma instância de uma execução de pipeline. Por exemplo, você tem um pipeline que é executado às 8:00 A.M., 9:00 AM e 10:00 AM. Nesse caso, há três execuções separadas do pipeline ou execuções de pipeline. Cada execução de pipeline possui uma ID de execução de pipeline exclusiva. Uma ID de execução é um GUID (identificador global exclusivo) que define essa execução de pipeline específica.

As execuções de pipeline normalmente são instanciadas por meio da passagem de argumentos para parâmetros definidos no pipeline. Você pode executar um pipeline manualmente ou usando um gatilho. Consulte [execução de pipeline e gatilhos em Azure data Factory](concepts-pipeline-execution-triggers.md) para obter detalhes.

## <a name="common-issues-causes-and-solutions"></a>Problemas comuns, causas e soluções

### <a name="pipeline-with-azure-function-throws-error-with-private-end-point-connectivity"></a>Pipeline com o Azure function gera erro com conectividade de ponto de extremidade particular
 
#### <a name="issue"></a>Problema
Para algum contexto, você tem o ADF e o Azure Aplicativo de funções em execução em um ponto de extremidade privado. Você está tentando obter um pipeline que interage com o Aplicativo de funções do Azure para trabalhar. Você tentou três métodos diferentes, mas um erro retorna `Bad Request` , os outros dois métodos retornam `103 Error Forbidden` .

#### <a name="cause"></a>Causa 
Atualmente, o ADF não dá suporte a um conector de ponto de extremidade privado para o Azure Aplicativo de funções. E esse deve ser o motivo pelo qual o Azure Aplicativo de funções está rejeitando as chamadas, já que ela seria configurada para permitir apenas conexões de um link privado.

#### <a name="resolution"></a>Resolução
Você pode criar um ponto de extremidade privado do tipo **PrivateLinkService** e fornecer o DNS do seu aplicativo de funções, e a conexão deve funcionar.

### <a name="pipeline-run-is-killed-but-the-monitor-still-shows-progress-status"></a>A execução do pipeline é interrompida, mas o monitor ainda mostra o status do progresso

#### <a name="issue"></a>Problema
Geralmente, quando você interrompe uma execução de pipeline, o monitoramento de pipeline ainda mostra o status do progresso. Isso ocorre devido ao problema de cache no navegador e você não tem filtros corretos para monitoramento.

#### <a name="resolution"></a>Resolução
Atualize o navegador e aplique os filtros corretos para monitoramento.
 
### <a name="copy-pipeline-failure--found-more-columns-than-expected-column-count-delimitedtextmorecolumnsthandefined"></a>Falha ao copiar o pipeline – foram encontradas mais colunas do que a contagem de colunas esperada (DelimitedTextMoreColumnsThanDefined)

#### <a name="issue"></a>Problema  
Se os arquivos em uma pasta específica que você está copiando contiverem arquivos com esquemas diferentes, como número variável de colunas, delimitadores diferentes, configurações de caractere de cotação ou algum problema de dados, o pipeline do ADF terminará em execução neste erro:

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

#### <a name="resolution"></a>Resolução
Selecione a opção "cópia binária" ao criar a atividade de Copiar Dados. Dessa forma, para copiar em massa ou migrar seus dados de um Data Lake para outro, com a opção **Binary** , o ADF não abrirá os arquivos para ler o esquema, mas apenas tratará todos os arquivos como binários e os copiará para o outro local.

### <a name="pipeline-run-fails-when-capacity-limit-of-integration-runtime-is-reached"></a>A execução do pipeline falha quando o limite de capacidade do tempo de execução de integração é atingido

#### <a name="issue"></a>Problema
Mensagem de erro:

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

O erro indica a limitação de por tempo de execução de integração, que é atualmente 50. Consulte [os limites](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#version-2) para obter detalhes.

Se você executar uma grande quantidade de fluxo de dados usando o mesmo tempo de execução de integração simultaneamente, isso poderá causar esse tipo de erro.

#### <a name="resolution"></a>Resolução 
- Separe esses pipelines para que o tempo de gatilho diferente seja executado.
- Crie um novo Integration Runtime e divida esses pipelines entre vários tempos de execução de integração.

### <a name="how-to-monitor-pipeline-failures-on-regular-interval"></a>Como monitorar falhas de pipeline em intervalos regulares

#### <a name="issue"></a>Problema
Geralmente, há uma necessidade de monitorar os pipelines do ADF em intervalos, digamos 5 minutos. Você pode consultar e filtrar as execuções de pipeline de um data factory usando o ponto de extremidade. 

#### <a name="recommendation"></a>Recomendação
1. Configure um aplicativo lógico do Azure para consultar todos os pipelines com falha a cada 5 minutos.
2. Em seguida, você pode relatar incidentes para nosso sistema de emissão de tíquetes de acordo com o [QueryByFactory](https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory).

#### <a name="reference"></a>Referência
- [Notificações de envio externo do ADF](https://www.mssqltips.com/sqlservertip/5962/send-notifications-from-an-azure-data-factory-pipeline--part-2/)

### <a name="how-to-handle-activity-level-errors-and-failures-in-pipelines"></a>Como tratar erros e falhas de nível de atividade em pipelines

#### <a name="issue"></a>Problema
Azure Data Factory orquestração permite a lógica condicional e permite que o usuário execute caminhos diferentes com base nos resultados de uma atividade anterior. Ele permite quatro caminhos condicionais: "em caso de êxito (aprovação padrão)", "após a falha", "após a conclusão" e "após ignorar". O uso de caminhos diferentes é permitido.

Azure Data Factory define êxito e falha na execução do pipeline da seguinte maneira:

- Avaliar o resultado de todas as atividades de nível folha. Se uma atividade folha tiver sido ignorada, avaliaremos sua atividade pai em vez disso.
- O resultado do pipeline será bem-sucedido se e somente se todas as folhas forem bem-sucedidas.

#### <a name="recommendation"></a>Recomendação
- Implemente verificações de nível [de atividade seguindo como tratar erros e falhas de pipeline](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459).
- Use o aplicativo lógico do Azure para monitorar pipelines em intervalos regulares seguindo [consulta por datafactory]( https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory).

## <a name="next-steps"></a>Próximas etapas

Para obter mais ajuda com a solução de problemas, experimente estes recursos:

*  [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Solicitações de recurso do Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Página de P e R da Microsoft](/answers/topics/azure-data-factory.html)
*  [Informações do Twitter sobre o Data Factory](https://twitter.com/hashtag/DataFactory)