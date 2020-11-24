---
title: Notas de versão do Azure SQL Edge
description: Notas de versão detalhando o que é novo ou o que mudou nas imagens do Azure SQL Edge
keywords: Borda do SQL das notas de versão
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 11/24/2020
ms.openlocfilehash: bb0c176ee8c145e1268827d79b453628e6f1bc28
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95808620"
---
# <a name="azure-sql-edge-release-notes"></a>Notas de versão do Azure SQL Edge 

Este artigo descreve o que há de novo e o que mudou em todas as novas compilações do Azure SQL Edge.

## <a name="azure-sql-edge---101"></a>Azure SQL Edge-1.0.1

### <a name="sql-engine-build-number---15020001553"></a>Número de Build do mecanismo SQL-15.0.2000.1553

### <a name="whats-new"></a>Novidades

- Permitir expressão Datebucket definida em colunas computadas

### <a name="fixes"></a>Correções

- Correção de política de retenção para descartar tabela com política de retenção habilitada com tempo limite infinito
- Suporte na implantação do DacFx para recursos de política de retenção e transmissão 
- Correção de implantação do DacFx para habilitar a implantação da pasta aninhada na URL SAS 
- PREVER correção para dar suporte a nomes de coluna longos em mensagens de erro

## <a name="azure-sql-edge---100-rtm"></a>Azure SQL Edge-1.0.0 (RTM)

### <a name="sql-engine-build-number---15020001552"></a>Número de Build do mecanismo SQL-15.0.2000.1552

### <a name="whats-new"></a>Novidades
1. Imagens de contêiner baseadas no Ubuntu 18, 4. 
2. Suporte para `IGNORE NULL` e `RESPECT NULL` sintaxe com `LAST_VALUE()` e `FIRST_VALUE()` funções. 
3. Melhorias de confiabilidade para prever com ONNX.
4. Suporte para limpeza baseada em política de retenção de dados.
   - Suporte ao buffer de anéis para a tarefa de limpeza de retenção para solução de problemas.
5. Novo suporte a recursos 
   - Recuperação rápida
   - Ajuste automático de consultas
   - Habilitar cenários de execução paralela
6. Melhorias de economia de energia para o modo de baixa energia
7. Suporte ao novo recurso de streaming 
   - [Janelas de instantâneo](/stream-analytics-query/snapshot-window-azure-stream-analytics) : novo tipo de janela que permite agrupar por eventos que chegam ao mesmo tempo. 
   - Habilite [TopOne](/stream-analytics-query/topone-azure-stream-analytics) e [CollectTop](/stream-analytics-query/collecttop-azure-stream-analytics) como função analítica, isso permitirá que o retorne os registros ordenados pela coluna de sua escolha, sem que seja necessário fazer parte de uma janela. 
   - Melhorias no [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics). 

### <a name="fixes"></a>Correções
1. Mensagens de erro adicionais e detalhes para solução de problemas de operações de streaming de TSQL. 
2. Melhorias para preservar a vida útil da bateria no modo ocioso. 
3. Correções do mecanismo de streaming TSQL: 
   - Limpeza do trabalho de streaming interrompido 
   - Correções para melhorias de localização e manipulação de Unicode
   - Melhore a depurabilidade para o compartilhamento TSQL de borda, permita que os usuários consultem erros de falha de trabalho de get_streaming_job.
4. Limpeza baseada em política de retenção de dados
   - Correções para a criação de política de retenção e cenários de limpeza.
5. Correções para tarefas de temporizador em segundo plano para melhorar a economia de energia para o modo de baixa energia.

### <a name="known-issues"></a>Problemas conhecidos 
1. Date_Bucket função T-SQL não pode ser usada em uma coluna computada.


## <a name="ctp-23"></a>CTP 2.3
### <a name="sql-engine-build-number---15020001549"></a>Número de Build do mecanismo SQL-15.0.2000.1549
### <a name="whats-new"></a>Novidades
1. Suporte para origens personalizadas na função Date_Bucket (). 
2. Suporte para arquivos BacPac como parte da implantação do SQL.
3. Suporte para limpeza baseada em política de retenção de dados.      
   - Suporte a DDL para habilitar a política de retenção 
   - Limpar procedimentos armazenados e tarefa de limpeza em segundo plano
   - Eventos estendidos para monitorar tarefas de limpeza

### <a name="fixes"></a>Correções
1. Mensagens de erro adicionais e detalhes para solução de problemas de operações de streaming de TSQL. 
2. Melhorias para preservar a vida útil da bateria no modo ocioso. 
3. Correções do mecanismo de streaming TSQL: 
   - Corrigir o problema de marca d' água presa com a janela de salto com Subfluxo 
   - Corrigir o tratamento de exceção de estrutura para garantir que ele seja coletado como um erro de ação do usuário


## <a name="ctp-22"></a>CTP 2.2
### <a name="sql-engine-build-number---15020001546"></a>Número de Build do mecanismo SQL-15.0.2000.1546
### <a name="whats-new"></a>Novidades
1. Suporte para contêineres não raiz 
2. Suporte para coleta de dados de diagnóstico e uso 
3. Atualizações de streaming T-SQL
   - Suporte para caracteres Unicode para nomes de objetos de fluxo

### <a name="fixes"></a>Correções
1. Atualizações de streaming T-SQL
   - Aprimoramentos na limpeza de processos
   - Aprimoramentos no log e diagnóstico
2. Melhoria de desempenho para ingestão de dados

## <a name="ctp-21"></a>CTP 2.1 
### <a name="sql-engine-build-number---15020001545"></a>Número de Build do mecanismo SQL-15.0.2000.1545
### <a name="fixes"></a>Correções
1. Corrigir a previsão com modelos ONNX para lidar com o problema de CPUID no ARM 
2. Correção para melhorar o tratamento do caminho de falha na inicialização do streaming TSQL 
3. Corrija o valor incorreto do atraso de marca d' água em métricas de trabalho quando não houver dados. 
4. Corrija o problema com o adaptador de saída quando o adaptador tem um esquema variável entre os lotes.  

## <a name="ctp-20"></a>CTP 2.0 
### <a name="sql-engine-build-number---15020001401"></a>Número de Build do mecanismo SQL-15.0.2000.1401
### <a name="whats-new"></a>Novidades
1.  Nome do produto atualizado para ' Azure SQL Edge '
1.  Função Date_bucket

    i.  Suporte para data, hora, tipo DateTime
3.  PREVER com ONNX
    
    i.  Parâmetro de tempo de execução necessário para ONNX 
    
4.  Suporte a streaming TSQL (versão prévia limitada) 
 
### <a name="known-issues"></a>Problemas conhecidos

1. <b>Problema:</b> Falhas em potencial com a aplicação do dacpac na inicialização devido ao problema de tempo.

    <b>Solução alternativa:</b> A reinicialização SQL Server ou contêiner tentará aplicar o dacpac e corrigir o problema
### <a name="request-support"></a>Solicitar suporte
1. Você pode solicitar suporte na [página de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

4. Verifique se os campos a seguir estão selecionados: 
    * Tipo de problema-técnico 
    * IoT Edge de serviço
    * Tipo de problema-meu problema está relacionado a um módulo de IoT Edge
    * Subtipo de problema-Azure SQL Edge

   ![Tíquete de suporte de exemplo](media/get-support/support-ticket.png)

## <a name="ctp-15"></a>CTP 1,5
### <a name="sql-engine-build-number---15020001331"></a>Número de Build do mecanismo SQL-15.0.2000.1331
### <a name="whats-new"></a>Novidades
1. Função Date_bucket
    
    i. Suporte para o tipo DateTimeOffset
2. PREVER com modelos ONNX

    i. suporte a nvarchar
 
## <a name="ctp-14"></a>CTP 1,4
### <a name="sql-engine-build-number---15020001247"></a>Número de Build do mecanismo SQL-15.0.2000.1247
### <a name="whats-new"></a>Novidades
1.  PREVER com modelos ONNX
 
    i.  Suporte a varchar
    
    ii. Migração para o ONNX Runtime versão 1,0 
2.  Suporte a recursos-os seguintes recursos estão habilitados:

    i.   Suporte a CDC

    ii.  Tabela de histórico com compactação

    III. Fator de escala mais alto para leitura antecipada de log

    iv.  Aplicação de filtro ES do modo de lote

    V.   Otimizações de leitura antecipada
 
## <a name="ctp-13"></a>CTP 1,3
### <a name="sql-engine-build-number---15020001147"></a>Número de Build do mecanismo SQL-15.0.2000.1147
### <a name="whats-new"></a>Novidades
1. Implantação do portal do Azure IOT 

    i.   Suporte para implantação de imagens AMD64 e ARM

    ii.  Suporte para criação de trabalhos de streaming

    III. Implantação do dacpac
2. PREVER com modelos ONNX

    i. Suporte a tipo numérico
3. Suporte a recursos-os seguintes recursos estão habilitados:

    i.  Verificação de aplicação agregada ao repositório de coluna

    ii. Verificações em boas viagens
4. Trabalho de redução de consumo de memória e superfície
