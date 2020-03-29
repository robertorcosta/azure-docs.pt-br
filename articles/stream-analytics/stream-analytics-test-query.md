---
title: Testar um trabalho do Azure Stream Analytics com dados de exemplo
description: Este artigo descreve como usar o portal do Azure para testar um trabalho do Azure Stream Analytics, uma entrada de exemplo e fazer o upload de dados de exemplo.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/6/2020
ms.custom: seodec18
ms.openlocfilehash: de2b99666dbed4eaec3db3e56800e9a7352e10e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898395"
---
# <a name="test-an-azure-stream-analytics-job-in-the-portal"></a>Teste um trabalho do Azure Stream Analytics no portal

No Azure Stream Analytics, você pode testar sua consulta sem iniciar ou parar seu trabalho. Você pode testar consultas sobre dados de entrada de suas fontes de streaming ou enviar dados de amostra de um arquivo local no Portal Azure. Você também pode testar consultas localmente a partir de seus dados de amostra locais ou dados ao vivo no [Visual Studio](stream-analytics-live-data-local-testing.md) e Visual [Studio Code](visual-studio-code-local-run-live-input.md).

## <a name="automatically-sample-incoming-data-from-input"></a>Amostrar automaticamente os dados de entrada da entrada

O Azure Stream Analytics busca automaticamente eventos de suas entradas de streaming. Você pode executar consultas na amostra padrão ou definir um período de tempo específico para a amostra.

1. Entre no portal do Azure.

2. Localize e selecione seu trabalho de Stream Analytics existente.

3. Na página de trabalho do Stream Analytics, sob o título **Topologia de Trabalho**, selecione **Consulta** para abrir a janela Editor de consultas. 

4. Para ver uma lista de exemplo de eventos de entrada, selecione a entrada com o ícone de arquivo e os eventos de amostra aparecerão automaticamente na **visualização Entrada**.

   a. O tipo de serialização de seus dados é detectado automaticamente se seu JSON ou CSV. Você pode alterá-lo manualmente também para JSON, CSV, AVRO alterando a opção no menu suspenso.
    
   b. Use o seletor para visualizar seus dados no formato **Table** ou **Raw.**
    
   c. Se os dados mostrados não forem atualizados, **selecione Atualizar** para ver os eventos mais recentes.

   A tabela a seguir é um exemplo de dados no **formato Tabela**:

   ![Entrada de amostra do Azure Stream Analytics em formato de tabela](./media/stream-analytics-test-query/asa-sample-table.png)

   A tabela a seguir é um exemplo de dados no **formato Raw**:

   ![Entrada de amostra do Azure Stream Analytics em formato bruto](./media/stream-analytics-test-query/asa-sample-raw.png)

5. Para testar sua consulta com dados de entrada, selecione **Testar consulta**. Os resultados aparecem na guia Resultados do **Teste.** Você também pode selecionar **Baixar resultados** para baixar os resultados.

   ![Resultados da consulta de teste de amostra do Azure Stream Analytics](./media/stream-analytics-test-query/asa-test-query.png)

6. Para testar sua consulta em um intervalo de tempo específico de eventos de entrada, **selecione Selecionar intervalo de tempo**.
   
   ![Intervalo de tempo do Azure Stream Analytics para eventos de amostra de entrada](./media/stream-analytics-test-query/asa-select-time-range.png)

7. Defina o intervalo de tempo dos eventos que deseja usar para testar sua consulta e selecionar **Amostra**. Dentro desse prazo, você pode recuperar até 1000 eventos ou 1 MB, o que vier primeiro.

   ![Azure Stream Analytics define intervalo de tempo para eventos de amostra de entrada](./media/stream-analytics-test-query/asa-set-time-range.png)

8. Uma vez que os eventos são amostrados para o intervalo de tempo selecionado, eles aparecem na guia **Desinversia entrada.**

   ![Azure Stream Analytics visualiza resultados de testes](./media/stream-analytics-test-query/asa-view-test-results.png)

9. Selecione **Redefinir** para ver a lista de amostras de eventos de entrada. Se você selecionar **Redefinir,** sua seleção de intervalo de tempo será perdida. Selecione **a consulta teste** para testar sua consulta e revise os resultados na guia Resultados do **Teste.**

10. Quando você fizer alterações na consulta, **selecione Salvar consulta** para testar a nova lógica de consulta. Isso permite que você modifique sua consulta iterativamente e teste-a novamente para ver como a saída muda.

11. Depois de verificar os resultados mostrados no navegador, você está pronto para **iniciar** o trabalho.

## <a name="upload-sample-data-from-a-local-file"></a>Carregar dados de amostra de um arquivo local

Em vez de usar dados ao vivo, você pode usar dados de amostra de um arquivo local para testar sua consulta do Azure Stream Analytics.

1. Entre no portal do Azure.
   
2. Localize o trabalho existente do Stream Analytics e selecione-o.

3. Na página de trabalho do Stream Analytics, sob o título **Topologia de Trabalho**, selecione **Consulta** para abrir a janela Editor de consultas.

4. Para testar sua consulta com um arquivo local, selecione **'Enviar's' sample's input** na guia **'Inserção' visualização.** 

   ![Azure Stream Analytics enviar arquivo de amostra](./media/stream-analytics-test-query/asa-upload-sample-file.png)

5. Faça upload do seu arquivo local para testar a consulta. Você só pode carregar arquivos com os formatos JSON, CSV ou AVRO. Selecione **OK**.

   ![Azure Stream Analytics enviar arquivo de amostra](./media/stream-analytics-test-query/asa-upload-sample-json-file.png)

6. Assim que você carregar o arquivo, você também pode ver o conteúdo do arquivo no formulário como uma tabela ou em seu formato bruto. Se você selecionar **Redefinir,** os dados da amostra retornarão aos dados de entrada explicados na seção anterior. Você pode carregar qualquer outro arquivo para testar a consulta a qualquer momento.

7. Selecione **a consulta teste** para testar sua consulta no arquivo de amostra enviado.

8. Os resultados dos testes são mostrados com base na sua consulta. Você pode alterar sua consulta e selecionar **Salvar consulta** para testar a nova lógica de consulta. Isso permite que você modifique sua consulta iterativamente e teste-a novamente para ver como a saída muda.

9. Quando você usa várias saídas na consulta, os resultados são mostrados com base na saída selecionada. 

   ![Saída selecionada do Azure Stream Analytics](./media/stream-analytics-test-query/asa-sample-test-selected-output.png)

10. Depois de verificar os resultados mostrados no navegador, você pode **iniciar o** trabalho.

## <a name="next-steps"></a>Próximas etapas
* [Construa uma solução de IoT usando o Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics): este tutorial irá guiá-lo a construir uma solução completa com um gerador de dados que simulará o tráfego em uma cabine de pedágio.

* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)

* [Exemplos de consulta para padrões de uso do Stream Analytics](stream-analytics-stream-analytics-query-patterns.md)

* [Entender as entradas para o Azure Stream Analytics](stream-analytics-add-inputs.md)

* [Entender as saídas do Azure Stream Analytics](stream-analytics-define-outputs.md)
