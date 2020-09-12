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
ms.date: 09/04/2020
ms.openlocfilehash: a2d27f892e79d99f515032b72e1ec090ab1f6a31
ms.sourcegitcommit: 206629373b7c2246e909297d69f4fe3728446af5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2020
ms.locfileid: "89500344"
---
# <a name="azure-sql-edge-release-notes"></a>Notas de versão do Azure SQL Edge 

Este artigo descreve o que há de novo e o que mudou em todas as novas compilações do Azure SQL Edge.

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
2. Imporvements para preservar a vida útil da bateria no modo ocioso. 
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
