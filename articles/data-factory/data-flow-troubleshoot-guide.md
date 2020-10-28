---
title: Solucionar problemas de fluxos de dados de mapeamento
description: Saiba como solucionar problemas de fluxos de dados no Azure Data Factory.
services: data-factory
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/11/2020
ms.openlocfilehash: 8b05c7710b79f276886da8158ae5c8ce39cb44dc
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92634718"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Solucionar problemas de fluxos de dados de mapeamento em Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo explora métodos comuns de solução de problemas para mapeamento de fluxos de dados em Azure Data Factory.

## <a name="common-errors-and-messages"></a>Erros e mensagens comuns

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Código de erro: DF-Executor-SourceInvalidPayload
- **Mensagem** : a execução de fluxo de dados de pipeline, depuração e visualização de dados falhou porque o contêiner não existe
- **Causas** : quando o conjunto de dados contém um contêiner que não existe no armazenamento
- **Recomendação** : verifique se o contêiner referenciado no conjunto de dados existe ou está acessível.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Código de erro: DF-Executor-SystemImplicitCartesian

- **Mensagem** : o produto cartesiano implícito para junção interna não é compatível, use a junção cruzada em vez disso. As colunas usadas na junção devem criar uma chave exclusiva para as linhas.
- **Causas** : o produto cartesiano implícito para junção interna entre planos lógicos não é compatível. Se as colunas usadas na junção criarem a chave exclusiva, pelo menos uma coluna de ambos os lados da relação será necessária.
- **Recomendação** : para junções não baseadas em igualdade, você precisa optar por uma junção cruzada personalizada.

### <a name="error-code-df-executor-systeminvalidjson"></a>Código de erro: DF-Executor-SystemInvalidJson

- **Mensagem** : erro de análise de JSON, multilinha ou codificação incompatível
- **Causas** : possíveis problemas com o arquivo JSON: codificação incompatível, bytes corrompidos ou uso de origem JSON como um único documento em várias linhas aninhadas
- **Recomendação** : verifique se a codificação do arquivo JSON é compatível. Na transformação da origem que está usando um conjunto de dados JSON, expanda 'Configurações de JSON' e ative 'Documento Único'.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Código de erro: DF-Executor-BroadcastTimeout

- **Mensagem** : erro de tempo limite de junção de difusão, verifique se o fluxo de difusão produz dados em 60 segundos em execuções de depuração e em 300 segundos em execuções de trabalho
- **Causas** : a difusão tem um tempo limite padrão de 60 segundos em execuções de depuração e 300 segundos em execuções de trabalho. O fluxo escolhido para difusão parece muito grande para produzir dados dentro desse limite.
- **Recomendação** : marque a guia Otimizar em suas transformações de fluxo de dados para Junção, Existe e Pesquisa. A opção padrão para Difusão é "Auto". Se "auto" estiver definido ou se você estiver configurando manualmente o lado esquerdo ou direito para difundir em "fixed", poderá definir uma configuração de Azure Integration Runtime maior ou desativar a difusão. A abordagem recomendada para o melhor desempenho em fluxos de dados é permitir que o Spark transmita usando "Auto" e use um Azure IR otimizado para memória.

Se você estiver executando o fluxo de dados em uma execução de teste de depuração de uma execução de pipeline de depuração, poderá encontrar essa condição com mais frequência. Isso ocorre porque o ADF acelera o tempo limite de difusão para 60 segundos a fim de manter uma experiência de depuração mais rápida. Se você quiser estender isso para o tempo limite de 300 segundos de uma execução disparada, você pode usar a opção debug > use Activity Runtime para utilizar a Azure IR definida em sua atividade executar pipeline de fluxo de dados.

### <a name="error-code-df-executor-conversion"></a>Código de erro: DF-Executor-Conversion

- **Mensagem** : falha ao converter em uma data ou hora devido a um caractere inválido
- **Causas** : os dados não estão no formato esperado
- **Recomendação** : usar o tipo de dados correto

### <a name="error-code-df-executor-invalidcolumn"></a>Código de erro: DF-Executor-InvalidColumn

- **Mensagem** : o nome da coluna precisa ser especificado na consulta, defina um alias se você estiver usando uma função SQL
- **Causas** : nenhum nome de coluna foi especificado
- **Recomendação** : defina um alias se você estiver usando uma função SQL como min()/max() etc.

 ### <a name="error-code-df-executor-drivererror"></a>Código de erro: DF-executor-DriverError
- **Mensagem** : INT96 é um tipo de carimbo de data/hora herdado que não tem suporte pelo fluxo de mensagens do ADF Considere atualizar o tipo de coluna para os tipos mais recentes.
- **Causas** : erro de driver
- **Recomendação** : INT96 é o tipo de carimbo de data/hora herdado, que não tem suporte do fluxo de texto do ADF. Considere atualizar o tipo de coluna para os tipos mais recentes.

 ### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Código de erro: DF-executor-BlockCountExceedsLimitError
- **Mensagem** : a contagem de blocos não confirmados não pode exceder o limite máximo de 100.000 blocos. Verifique a configuração do blob.
- **Causas** : pode haver no máximo 100.000 blocos não confirmados em um blob.
- **Recomendação** : entre em contato com a equipe de produto da Microsoft sobre esse problema para obter mais detalhes

 ### <a name="error-code-df-executor-partitiondirectoryerror"></a>Código de erro: DF-executor-PartitionDirectoryError
- **Mensagem** : o caminho de origem especificado tem vários diretórios particionados (por exemplo, <Source Path> /<diretório raiz de partição 1>/a = 10/b = 20, <Source Path> /<diretório raiz da partição 2>/c = 10/d = 30) ou diretório particionado com outro arquivo ou diretório não particionado (por exemplo <Source Path> /diretório raiz da partição de <1>/a = 10/b = 20, <Source Path> /diretório 2/arquivo1), remova o diretório raiz da partição do caminho de origem e leia-o por meio de transformação de origem separada.
- **Causas** : o caminho de origem tem vários diretórios particionados ou diretório particionado com outro arquivo ou diretório não particionado.
- **Recomendação** : Remova o diretório raiz particionado do caminho de origem e leia-o por meio de transformação de origem separada.

 ### <a name="error-code-df-executor-outofmemoryerror"></a>Código de erro: DF-executor-OutOfMemoryError
- **Mensagem** : o cluster encontrou um problema de memória insuficiente durante a execução. tente novamente usando um Integration Runtime com maior contagem de núcleos e/ou tipo de computação com otimização de memória
- **Causas** : o cluster está ficando sem memória
- **Recomendação** : os clusters de depuração destinam-se a fins de desenvolvimento. Aproveite a amostragem de dados, o tipo de computação apropriado e o tamanho para executar a carga. Consulte o [Guia de desempenho do fluxo de dados de mapeamento](concepts-data-flow-performance.md) para ajuste para obter o melhor desempenho.

 ### <a name="error-code-df-executor-illegalargument"></a>Código de erro: DF-executor-illegalArgument
- **Mensagem** : Verifique se a chave de acesso em seu serviço vinculado está correta
- **Causas** : nome da conta ou chave de acesso incorretos
- **Recomendação** : Verifique se o nome da conta ou a chave de acesso especificada no serviço vinculado está correta. 

 ### <a name="error-code-df-executor-invalidtype"></a>Código de erro: DF-executor-invalidatype
- **Mensagem** : Verifique se o tipo de parâmetro corresponde ao tipo de valor passado. A passagem de parâmetros float de pipelines não tem suporte no momento.
- **Causas** : tipos de dados incompatíveis entre o tipo declarado e o valor real do parâmetro
- **Recomendação** : Verifique se os valores de parâmetro passados para um fluxo de dados correspondem ao tipo declarado.

 ### <a name="error-code-df-executor-columnunavailable"></a>Código de erro: DF-executor-ColumnUnavailable
- **Mensagem** : o nome da coluna usado na expressão está indisponível ou é inválido
- **Causas** : nome de coluna inválido ou indisponível usado em expressões
- **Recomendação** : verificar nome (s) de coluna usado em expressões

 ### <a name="error-code-df-executor-parseerror"></a>Código de erro: DF-executor-ParseError
- **Mensagem** : a expressão não pode ser analisada
- **Causas** : a expressão está analisando erros devido à formatação
- **Recomendação** : verificar a formatação na expressão

### <a name="error-code-getcommand-outputasync-failed"></a>Código de erro: falha de GetCommand OutputAsync

- **Mensagem** : durante a depuração do Fluxo de Dados e a visualização dos dados: GetCommand OutputAsync falhou com...
- **Causas** : este é um erro de serviço de back-end. Você pode repetir a operação e também reiniciar a sessão de depuração.
- **Recomendação** : se tentar novamente e reiniciar não resolver o problema, entre em contato com o atendimento ao cliente.

### <a name="error-code-hit-unexpected-exception-and-execution-failed"></a>Código de erro: ocorreu uma exceção inesperada e a execução falhou

- **Mensagem** : durante a execução da atividade do Fluxo de Dados: ocorreu uma exceção inesperada e a execução falhou.
- **Causas** : este é um erro de serviço de back-end. Você pode repetir a operação e também reiniciar a sessão de depuração.
- **Recomendação** : se tentar novamente e reiniciar não resolver o problema, entre em contato com o atendimento ao cliente.

### <a name="error-code-debug-data-preview-no-output-data-on-join"></a>Código de erro: visualização de dados de depuração não há dados de saída em junção

- **Mensagem** : há um grande número de valores nulos ou valores ausentes que podem ser causados por ter poucas linhas amostradas. Tente atualizar o limite de linha de depuração e atualizar os dados.
- **Causas** : a condição de junção não correspondeu a nenhuma linha ou resultou em um número alto de nulos durante a visualização de dados.
- **Recomendação** : Vá para configurações de depuração e aumente o número de linhas no limite de linha de origem. Verifique se você selecionou um Azure IR com um cluster de fluxo de dados grande o suficiente para lidar com mais dados.


## <a name="general-troubleshooting-guidance"></a>Diretrizes gerais de solução de problemas

1. Verifique o status das suas conexões de conjunto de dados. Em cada transformação de origem e coletor, visite o serviço vinculado para cada conjunto de dados que você estiver usando e teste as conexões.
1. Verifique o status das conexões de arquivo e de tabela do designer de fluxo de dados. Ative a Depuração e clique em Visualização de Dados em suas Transformações de origem para garantir que você possa acessar seus dados.
1. Se tudo estiver correto na visualização de dados, vá para o Designer de pipeline e coloque seu fluxo de dados em uma atividade de pipeline. Depure o pipeline para um teste de ponta a ponta.

## <a name="next-steps"></a>Próximas etapas

Para obter mais ajuda com a solução de problemas, experimente estes recursos:
*  [Blog de Data Factory](https://techcommunity.microsoft.com/t5/azure-data-factory/bg-p/AzureDataFactoryBlog)
*  [Solicitações de recurso do Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://www.youtube.com/channel/UC2S0k7NeLcEm5_IhHUwpN0g/videos)
*  [Página de perguntas e respostas da Microsoft](/answers/topics/azure-data-factory.html)
*  [Fórum do Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre o Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Guia de desempenho de fluxos de dados de mapeamento do ADF](concepts-data-flow-performance.md)