---
title: Solucionar problemas nos fluxos de dados
description: Saiba como solucionar problemas de fluxo de dados na Fábrica de Dados do Azure.
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 04/02/2020
ms.openlocfilehash: e9e9b10cc9bae029fe11fb2bd1f8b76cf120744a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417803"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>Solucionar problemas de fluxos de dados na fábrica de dados do Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo explora métodos comuns de solução de problemas para fluxos de dados na Fábrica de Dados Do Azure.

## <a name="common-errors-and-messages"></a>Erros e mensagens comuns

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Código de erro: DF-Executor-SourceInvalidPayload
- **Mensagem**: A visualização de dados, a depuração e a execução do fluxo de dados do pipeline falharam porque o contêiner não existe
- **Causas:** Quando o conjunto de dados contém um recipiente que não existe no armazenamento
- **Recomendação**: Certifique-se de que o contêiner referenciado em seu conjunto de dados existe ou acessível.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Código de erro: DF-Executor-SystemImplicitCartesian

- **Mensagem**: O produto cartesiano implícito para a adesão INNER não é suportado, use CROSS JOIN em vez disso. As colunas usadas no join devem criar uma chave única para linhas.
- **Causas**: O produto cartesiano implícito para a juntação INNER entre planos lógicos não é suportado. Se as colunas usadas na parte criarem a chave única, pelo menos uma coluna de ambos os lados da relação será necessária.
- **Recomendação**: Para a adesão baseada em não-igualdade, você tem que optar por CUSTOM CROSS JOIN.

### <a name="error-code-df-executor-systeminvalidjson"></a>Código de erro: DF-Executor-SystemInvalidJson

- **Mensagem**: Erro de análise JSON, codificação não suportada ou multilinha
- **Causas**: Possíveis problemas com o arquivo JSON: codificação sem suporte, bytes corrompidos ou usar a fonte JSON como documento único em muitas linhas aninhadas
- **Recomendação**: Verifique se a codificação do arquivo JSON é suportada. Na transformação De Origem que está usando um conjunto de dados JSON, expanda 'Configurações JSON' e ative 'Documento Único'.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Código de erro: DF-Executor-BroadcastTimeout

- **Mensagem**: Broadcast join timeout error, certifique-se de que a transmissão produz dados dentro de 60 segundos em corridas de depuração e 300 segundos em corridas de trabalho
- **Causas**: O Broadcast tem um tempo de 60 segundos em corridas de depuração e 300 segundos em corridas de trabalho. O fluxo escolhido para transmissão parece grande para produzir dados dentro deste limite.
- **Recomendação**: Evite transmitir grandes fluxos de dados onde o processamento pode levar mais de 60 segundos. Escolha uma transmissão menor para transmitir em vez disso. Grandes tabelas SQL/DW e arquivos de origem são tipicamente candidatos ruins.

### <a name="error-code-df-executor-conversion"></a>Código de erro: DF-Executor-Conversão

- **Mensagem**: A conversão para uma data ou hora falhou devido a um caractere inválido
- **Causas**: Os dados não estão no formato esperado
- **Recomendação**: Use o tipo de dados correto

### <a name="error-code-df-executor-invalidcolumn"></a>Código de erro: DF-Executor-InvalidColumn

- **Mensagem**: O nome da coluna precisa ser especificado na consulta, defina um alias se estiver usando uma função SQL
- **Causas**: Nenhum nome da coluna foi especificado
- **Recomendação**: Defina um alias se usar uma função SQL como min()/max(), etc.

### <a name="error-code-getcommand-outputasync-failed"></a>Código de erro: falha no GetCommand OutputAsync

- **Mensagem:** Durante a depuração do fluxo de dados e a visualização de dados: GetCommand OutputAsync falhou com ...
- **Causas**: Este é um erro de serviço back-end. Você pode tentar novamente a operação e também reiniciar sua sessão de depuração.
- **Recomendação**: Se a tentativa e a reinicialização não resolverem o problema, entre em contato com o suporte ao cliente.

### <a name="error-code-hit-unexpected-exception-and-execution-failed"></a>Código de erro: Aperte a exceção inesperada e falha na execução

- **Mensagem:** Durante a execução da atividade de fluxo de dados: Aperte a exceção inesperada e a execução falhou.
- **Causas**: Este é um erro de serviço back-end. Você pode tentar novamente a operação e também reiniciar sua sessão de depuração.
- **Recomendação**: Se a tentativa e a reinicialização não resolverem o problema, entre em contato com o suporte ao cliente.

## <a name="general-troubleshooting-guidance"></a>Orientação geral de solução de problemas

1. Verifique o status de suas conexões com o conjunto de dados. Em cada transformação de Fonte e Sink, visite o Serviço Vinculado para cada conjunto de dados que você está usando e teste conexões.
1. Verifique o status das conexões de arquivo e tabela do designer de fluxo de dados. Ligue depurar e clique em Data Preview em suas transformações de Origem para garantir que você seja capaz de acessar seus dados.
1. Se tudo parecer bom a partir da visualização de dados, vá para o designer pipeline e coloque seu fluxo de dados em uma atividade de pipeline. Depurar o oleoduto para um teste de ponta a ponta.

## <a name="next-steps"></a>Próximas etapas

Para obter mais ajuda para solucionar problemas, tente esses recursos:
*  [Blog da Fábrica de Dados](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Solicitações de recursos da Fábrica de Dados](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Fórum Stack Overflow para Fábrica de Dados](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Guia de desempenho de fluxos de mapeamento do ADF](concepts-data-flow-performance.md)
