---
title: Solucionar problemas de fluxos de dados
description: Saiba como solucionar problemas de fluxo de dados no Azure Data Factory.
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.openlocfilehash: c9ac8d7ea465a26d29bf8f8fbc15dcefaf9d7575
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187272"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>Solucionar problemas de fluxos de dados no Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo explora métodos comuns de solução de problemas para fluxos de dados em Azure Data Factory.

## <a name="common-errors-and-messages"></a>Erros e mensagens comuns

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Código de erro: DF-executor-SourceInvalidPayload
- **Mensagem**: falha na execução da visualização de dados, depuração e fluxo de dados de pipeline porque o contêiner não existe
- **Causas**: quando DataSet contém um contêiner que não existe no armazenamento
- **Recomendação**: Certifique-se de que o contêiner referenciado em seu conjunto de entrada existe ou acessível.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Código de erro: DF-executor-SystemImplicitCartesian

- **Mensagem**: não há suporte para o produto cartesiano implícito para a junção interna. em vez disso, use junção cruzada. As colunas usadas em Join devem criar uma chave exclusiva para linhas.
- **Causas**: não há suporte para o produto cartesiano implícito para junção interna entre planos lógicos. Se as colunas usadas na junção criarem a chave exclusiva, pelo menos uma coluna de ambos os lados da relação será necessária.
- **Recomendação**: para junções não baseadas em igualdade, você precisa optar por uma junção cruzada personalizada.

### <a name="error-code-df-executor-systeminvalidjson"></a>Código de erro: DF-executor-SystemInvalidJson

- **Mensagem**: erro de análise de JSON, codificação sem suporte ou multilinha
- **Causas**: possíveis problemas com o arquivo JSON: codificação sem suporte, bytes corrompidos ou uso de origem JSON como um único documento em várias linhas aninhadas
- **Recomendação**: Verifique se há suporte para a codificação do arquivo JSON. Na transformação origem que está usando um conjunto de uma JSON, expanda ' configurações de JSON ' e ative ' único documento '.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Código de erro: DF-executor-BroadcastTimeout

- **Mensagem**: erro de tempo limite de junção de difusão, verifique se o fluxo de difusão produz dados em 60 segundos em execuções de depuração e 300 segundos em execuções de trabalho
- **Causas**: a difusão tem um tempo limite padrão de 60 segundos em execuções de depuração e 300 segundos em execuções de trabalho. O fluxo escolhido para difusão parece grande para produzir dados dentro desse limite.
- **Recomendação**: marque a guia otimizar em suas transformações de fluxo de dados para Join, Exists e Lookup. A opção padrão para Broadcast é "auto". Se estiver definido, ou se você estiver configurando manualmente o lado esquerdo ou direito para difundir em "fixo", poderá definir uma configuração de Azure Integration Runtime maior ou desativar a difusão. A abordagem recomendada para o melhor desempenho em fluxos de dados é permitir que o Spark transmita usando "auto" e use uma Azure IR com otimização de memória.

### <a name="error-code-df-executor-conversion"></a>Código de erro: DF-executor-Conversion

- **Mensagem**: falha na conversão de uma data ou hora devido a um caractere inválido
- **Causas**: os dados não estão no formato esperado
- **Recomendação**: usar o tipo de dados correto

### <a name="error-code-df-executor-invalidcolumn"></a>Código de erro: DF-executor-InvalidColumn

- **Mensagem**: o nome da coluna precisa ser especificado na consulta, definir um alias se estiver usando uma função SQL
- **Causas**: nenhum nome de coluna foi especificado
- **Recomendação**: defina um alias se estiver usando uma função SQL como min ()/Max (), etc.

### <a name="error-code-getcommand-outputasync-failed"></a>Código de erro: falha de GetCommand OutputAsync

- **Mensagem**: durante a depuração de fluxo de dados e a visualização de dados: GetCommand OutputAsync falhou com...
- **Causa**: Este é um erro de serviço de back-end. Você pode repetir a operação e também reiniciar a sessão de depuração.
- **Recomendação**: se tentar novamente e reiniciar não resolver o problema, entre em contato com o atendimento ao cliente.

### <a name="error-code-hit-unexpected-exception-and-execution-failed"></a>Código de erro: clique em exceção inesperada e a execução falhou

- **Mensagem**: durante a execução da atividade de fluxo de dados: clique em exceção inesperada e a execução falhou.
- **Causa**: Este é um erro de serviço de back-end. Você pode repetir a operação e também reiniciar a sessão de depuração.
- **Recomendação**: se tentar novamente e reiniciar não resolver o problema, entre em contato com o atendimento ao cliente.

## <a name="general-troubleshooting-guidance"></a>Diretrizes gerais de solução de problemas

1. Verifique o status das suas conexões de conjunto de conexão. Em cada transformação de origem e coletor, visite o serviço vinculado para cada conjunto de um que você está usando e teste as conexões.
1. Verifique o status das conexões de arquivo e de tabela do designer de fluxo de dados. Ative a depuração e clique em visualização de dados em suas transformações de origem para garantir que você possa acessar seus dados.
1. Se tudo estiver correto na visualização de dados, vá para o designer de pipeline e coloque seu fluxo de dados em uma atividade de pipeline. Depure o pipeline para um teste de ponta a ponta.

## <a name="next-steps"></a>Próximas etapas

Para obter mais ajuda para solução de problemas, Experimente estes recursos:
*  [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory solicitações de recursos](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow Fórum para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Guia de desempenho de fluxos de dados de mapeamento do ADF](concepts-data-flow-performance.md)
