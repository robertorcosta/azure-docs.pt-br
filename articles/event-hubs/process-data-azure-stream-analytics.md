---
title: Processar dados do Event Hubs Azure usando o Stream Analytics | Microsoft Docs
description: Este artigo mostra como processar dados do seu hub de eventos Azure usando um trabalho do Azure Stream Analytics.
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 07/09/2019
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: 531426656fe833752c9c4685688c00de3894895b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "69991941"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>Processe dados do seu hub de eventos usando o Azure Stream Analytics 
O serviço Azure Stream Analytics facilita a ingestão, o processamento e a análise de dados de streaming do Azure Event Hubs, permitindo insights poderosos para impulsionar ações em tempo real. Essa integração permite criar rapidamente um pipeline de análise de caminho quente. Você pode usar o portal Azure para visualizar dados de entrada e escrever uma consulta do Stream Analytics. Uma vez que sua consulta esteja pronta, você pode movê-la para a produção em apenas alguns cliques. 

## <a name="key-benefits"></a>Principais benefícios
Aqui estão os principais benefícios do Azure Event Hubs e da integração do Azure Stream Analytics: 
- **Visualizar dados** – Você pode visualizar os dados de entrada de um hub de eventos no portal Azure.
- **Teste sua consulta** – Prepare uma consulta de transformação e teste-a diretamente no portal Azure. Para obter a sintaxe do idioma de consulta, consulte a documentação [do Idioma de consulta do Stream Analytics.](/stream-analytics-query/built-in-functions-azure-stream-analytics)
- **Implantar sua consulta na produção** – Você pode implantar a consulta na produção criando e iniciando um trabalho do Azure Stream Analytics.

## <a name="end-to-end-flow"></a>Fluxo de ponta a ponta

1. Faça login no [portal Azure](https://portal.azure.com). 
1. Navegue até o **namespace do Event Hubs** e navegue até o **hub de eventos**, que tem os dados de entrada. 
1. Selecione **Dados de processo** na página do centro de eventos.  

    ![Ladrilhos de dados de processo](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. Selecione **Explorar** no **Ativar insights em tempo real a partir de ladrilhos de eventos.** 

    ![Selecionar Stream Analytics](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. Você vê uma página de consulta com valores já definidos para os seguintes campos:
    1. Seu **hub de eventos** como uma entrada para a consulta.
    1. Amostrar **a consulta SQL com a** declaração SELECT. 
    1. Um alias de **saída** para consultar os resultados do teste de consulta. 

        ![Editor de consultas](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  Quando você usa esse recurso pela primeira vez, esta página pede sua permissão para criar um grupo de consumidores e uma política para que seu hub de eventos visualize os dados de entrada.
1. Selecione **Criar** no painel **de visualização de entrada,** conforme mostrado na imagem anterior. 
1. Você verá imediatamente um instantâneo dos dados de entrada mais recentes nesta guia.
    - O tipo de serialização em seus dados é detectado automaticamente (JSON/CSV). Você pode alterá-lo manualmente também para JSON/CSV/AVRO.
    - Você pode visualizar os dados de entrada no formato da tabela ou formato bruto. 
    - Se os dados mostrados não forem atualizados, **selecione Atualizar** para ver os eventos mais recentes. 

        Aqui está um exemplo de dados ![no formato da **tabela**: Resultados no formato da tabela](./media/process-data-azure-stream-analytics/snapshot-results.png)

        Aqui está um exemplo de dados no **formato bruto:** 

        ![Resultados no formato bruto](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. Selecione **a consulta teste** para ver o instantâneo dos resultados do teste da sua consulta na guia Resultados do **Teste.** Você também pode baixar os resultados.

    ![Resultados da consulta de teste](./media/process-data-azure-stream-analytics/test-results.png)
1. Escreva sua própria consulta para transformar os dados. Consulte [a referência do idioma de consulta de análise de fluxo](/stream-analytics-query/stream-analytics-query-language-reference).
1. Depois de testar a consulta e quiser movê-la para a produção, selecione **Implantar consulta**. Para implantar a consulta, crie um trabalho do Azure Stream Analytics onde você pode definir uma saída para o seu trabalho e iniciar o trabalho. Para criar um trabalho do Stream Analytics, especifique um nome para o trabalho e selecione **Criar**.

      ![Criar um trabalho do Azure Stream Analytics](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  Recomendamos que você crie um grupo de consumidores e uma política para cada novo trabalho do Azure Stream Analytics que você cria a partir da página Event Hubs. Os grupos de consumidores permitem apenas cinco leitores simultâneos, portanto, fornecer um grupo de consumidores dedicado para cada trabalho evitará quaisquer erros que possam surgir de ultrapassar esse limite. Uma política dedicada permite que você gire sua chave ou revogue permissões sem afetar outros recursos. 
1. Seu trabalho de Stream Analytics agora é criado onde sua consulta é a mesma que você testou, e a entrada é o seu hub de eventos. 

9.  Para concluir o pipeline, defina a **saída** da consulta e **selecione Iniciar** para iniciar o trabalho.

    > [!NOTE]
    > Antes de iniciar o trabalho, não se esqueça de substituir as saídas pelo nome de saída que você criou no Azure Stream Analytics.

      ![Definir a saída e iniciar o trabalho](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>Limitações conhecidas
Ao testar sua consulta, os resultados do teste levam aproximadamente 6 segundos para serem carregados. Estamos trabalhando para melhorar o desempenho dos testes. No entanto, quando implantado na produção, o Azure Stream Analytics terá latência de subsito.

## <a name="streaming-units"></a>Unidades de transmissão
O trabalho do Azure Stream Analytics é padrão para três unidades de streaming (SUs). Para ajustar essa configuração, **selecione Escala** no menu esquerdo na página **de trabalho Stream Analytics** no portal Azure. Para saber mais sobre as unidades de streaming, consulte [Entender e ajustar unidades de streaming.](../stream-analytics/stream-analytics-streaming-unit-consumption.md)

![Unidades de streaming de escala](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as consultas do Stream Analytics, consulte [Stream Analytics Query Language](/stream-analytics-query/built-in-functions-azure-stream-analytics)
