---
title: Solucionar problemas de fluxos de dados
description: Saiba como solucionar problemas de fluxos de dados no Azure Data Factory.
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.openlocfilehash: 2edd5b661240b6156cf8a02059b2b9a668c402f3
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: pt-BR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83829113"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>Solucionar problemas de fluxos de dados no Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo explora métodos comuns de solução de problemas para fluxos de dados no Azure Data Factory.

## <a name="common-errors-and-messages"></a>Erros e mensagens comuns

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Código de erro: DF-Executor-SourceInvalidPayload
- **Mensagem**: a execução de fluxo de dados de pipeline, depuração e visualização de dados falhou porque o contêiner não existe
- **Causas**: quando o conjunto de dados contém um contêiner que não existe no armazenamento
- **Recomendação**: verifique se o contêiner referenciado no conjunto de dados existe ou está acessível.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Código de erro: DF-Executor-SystemImplicitCartesian

- **Mensagem**: o produto cartesiano implícito para junção interna não é compatível, use a junção cruzada em vez disso. As colunas usadas na junção devem criar uma chave exclusiva para as linhas.
- **Causas**: o produto cartesiano implícito para junção interna entre planos lógicos não é compatível. Se as colunas usadas na junção criarem a chave exclusiva, pelo menos uma coluna de cada um dos dois lados da relação será necessária.
- **Recomendação**: para junções não baseadas em igualdade, você precisa optar por uma junção cruzada personalizada.

### <a name="error-code-df-executor-systeminvalidjson"></a>Código de erro: DF-Executor-SystemInvalidJson

- **Mensagem**: erro de análise de JSON, multilinha ou codificação incompatível
- **Causas**: possíveis problemas com o arquivo JSON: codificação incompatível, bytes corrompidos ou uso de origem JSON como um único documento em várias linhas aninhadas
- **Recomendação**: verifique se a codificação do arquivo JSON é compatível. Na transformação da origem que está usando um conjunto de dados JSON, expanda 'Configurações de JSON' e ative 'Documento Único'.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Código de erro: DF-Executor-BroadcastTimeout

- **Mensagem**: erro de tempo limite de junção de difusão, verifique se o fluxo de difusão produz dados em 60 segundos em execuções de depuração e em 300 segundos em execuções de trabalho
- **Causas**: a difusão tem um tempo limite padrão de 60 segundos em execuções de depuração e de 300 segundos em execuções de trabalho. O fluxo escolhido para difusão parece grande demais para produzir dados dentro desse limite.
- **Recomendação**: marque a guia Otimizar em suas transformações de fluxo de dados para Junção, Existe e Pesquisa. A opção padrão para Difusão é "Auto". Se ela estiver definida ou se você estiver configurando manualmente o lado esquerdo ou direito para difusão em "Fixo", poderá definir uma configuração maior do Azure Integration Runtime ou desativar a difusão. A abordagem recomendada para o melhor desempenho em fluxos de dados é permitir que o Spark transmita usando "Auto" e use um Azure IR otimizado para memória.

### <a name="error-code-df-executor-conversion"></a>Código de erro: DF-Executor-Conversion

- **Mensagem**: falha ao converter em uma data ou hora devido a um caractere inválido
- **Causas**: os dados não estão no formato esperado
- **Recomendação**: usar o tipo de dados correto

### <a name="error-code-df-executor-invalidcolumn"></a>Código de erro: DF-Executor-InvalidColumn

- **Mensagem**: o nome da coluna precisa ser especificado na consulta, defina um alias se você estiver usando uma função SQL
- **Causas**: nenhum nome de coluna foi especificado
- **Recomendação**: defina um alias se você estiver usando uma função SQL como min()/max() etc.

### <a name="error-code-getcommand-outputasync-failed"></a>Código de erro: falha de GetCommand OutputAsync

- **Mensagem**: durante a depuração do Fluxo de Dados e a visualização dos dados: GetCommand OutputAsync falhou com...
- **Causas**: este é um erro de serviço de back-end. Você pode repetir a operação e também reiniciar a sessão de depuração.
- **Recomendação**: se tentar novamente e reiniciar não resolver o problema, entre em contato com o atendimento ao cliente.

### <a name="error-code-hit-unexpected-exception-and-execution-failed"></a>Código de erro: ocorreu uma exceção inesperada e a execução falhou

- **Mensagem**: durante a execução da atividade do Fluxo de Dados: ocorreu uma exceção inesperada e a execução falhou.
- **Causas**: este é um erro de serviço de back-end. Você pode repetir a operação e também reiniciar a sessão de depuração.
- **Recomendação**: se tentar novamente e reiniciar não resolver o problema, entre em contato com o atendimento ao cliente.

## <a name="general-troubleshooting-guidance"></a>Diretrizes gerais de solução de problemas

1. Verifique o status das suas conexões de conjunto de dados. Em cada transformação de origem e coletor, visite o serviço vinculado para cada conjunto de dados que você estiver usando e teste as conexões.
1. Verifique o status das conexões de arquivo e de tabela do designer de fluxo de dados. Ative a Depuração e clique em Visualização de Dados em suas Transformações de origem para garantir que você possa acessar seus dados.
1. Se tudo estiver correto na visualização de dados, vá para o Designer de pipeline e coloque seu fluxo de dados em uma atividade de pipeline. Depure o pipeline para um teste de ponta a ponta.

## <a name="next-steps"></a>Próximas etapas

Para obter mais ajuda com a solução de problemas, experimente estes recursos:
*  [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Solicitações de recurso do Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Página de perguntas e respostas da Microsoft](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Fórum do Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre o Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Guia de desempenho de fluxos de dados de mapeamento do ADF](concepts-data-flow-performance.md)
