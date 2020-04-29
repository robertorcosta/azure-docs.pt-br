---
title: Desenvolvimento iterativo e depuração no Azure Data Factory
description: Saiba como desenvolver e depurar pipelines do Data Factory interativamente no Portal do Azure.
ms.date: 09/26/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 49e406f0730189439bb1c2529b23d8ad6d760a2f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81411521"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Desenvolvimento iterativo e depuração com o Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O Azure Data Factory permite desenvolver e depurar pipelines do Data Factory de forma iterativa.

Para ver uma introdução de oito minutos e uma demonstração desse recurso, assista ao seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="iterative-debugging-features"></a>Recursos de depuração iterativos
Criar pipelines e faça execuções de teste usando o recurso **Depurar** na tela de pipeline sem escrever uma única linha de código.

![Recurso Depurar na tela de pipeline](media/iterative-development-debugging/iterative-development-image1.png)

Exiba os resultados das execuções de teste na janela **Saída** da tela de pipeline.

![Janela de saída da tela de pipeline](media/iterative-development-debugging/iterative-development-image2.png)

Depois que uma execução de teste for bem-sucedida, adicione mais atividades ao seu pipeline e continue a depuração de maneira iterativa. Você também pode **Cancelar** uma execução de teste enquanto ela está em andamento.

![Cancelar uma execução de teste](media/iterative-development-debugging/iterative-development-image3.png)

Quando faz execuções de teste, você não precisa publicar suas alterações para o data factory antes de selecionar **Depurar**. Esse recurso é útil nos cenários em que você deseja garantir que as alterações funcionem conforme o esperado antes de atualizar o fluxo de trabalho de data factory.

> [!IMPORTANT]
> Selecionar **Depurar** realmente executa o pipeline. Assim, por exemplo, se o pipeline contiver uma atividade de cópia, a execução de teste copiará dados da origem para o destino. Como resultado, é recomendável que você use pastas de teste em suas atividades de cópia e outras atividades durante a depuração. Depois que você tiver depurado pipeline, alterne para as pastas reais que você deseja usar em operações normais.

## <a name="visualizing-debug-runs"></a>Visualizar execuções de depuração

Você pode visualizar todas as execuções de depuração que estão em andamento do seu data factory em um só lugar. Selecione **Exibir execuções de depuração** no canto superior direito da página. Esse recurso é útil em cenários nos quais você tem pipelines mestres iniciando execuções de depuração para os pipelines filhos, e você deseja uma única exibição para ver todas as execuções de depuração ativas.

![Selecione o ícone Exibir execuções de depuração ativas](media/iterative-development-debugging/view-debug-runs-image1.png)

![Lista de exemplo de execuções de depuração ativas](media/iterative-development-debugging/view-debug-runs-image2.png)

Se você tiver sessões de depuração de fluxo de dados ativas, essas sessões serão exibidas na parte inferior da janela de depuração ativa. Você pode selecionar uma sessão de fluxo de dados ativa e parar o respectivo cluster.

![Lista de exemplos de execuções de depuração de fluxo de dados ativas](media/data-flow/dfsessions.png)

## <a name="monitoring-debug-runs"></a>Monitorar execuções de depuração

As execuções de teste iniciadas com a capacidade de **depuração** não estão disponíveis na lista na guia **Monitor** . Você só pode ver execuções disparadas com gatilhos de **janela** de **gatilho agora**, **agendamento**ou em cascata na guia **monitorar** . Você pode ver a última execução de teste iniciada com a capacidade de **depuração** na janela **saída** da tela do pipeline.

## <a name="setting-breakpoints-for-debugging"></a>Definir pontos de interrupção para depuração

O Data Factory também lhe permite depurar até que você atinja uma determinada atividade na tela de pipeline. Apenas coloques um ponto de interrupção na atividade até a que você deseja testar e selecione **Depurar**. O Data Factory garante que o teste seja executado somente até a atividade de ponto de interrupção na tela de pipeline. Esse recurso *Depurar Até* é útil quando você não quer testar o pipeline de inteiro, mas apenas um subconjunto das atividades dentro do pipeline.

![Pontos de interrupção na tela de pipeline](media/iterative-development-debugging/iterative-development-image4.png)

Para definir um ponto de interrupção, selecione um elemento na tela do pipeline. Uma opção *Depurar Até* aparece como um círculo vermelho vazio no canto superior direito do elemento.

![Antes de configurar um ponto de interrupção no elemento selecionado](media/iterative-development-debugging/iterative-development-image5.png)

Depois de selecionar a opção *Depurar Até*, ele é alterado para um círculo vermelho preenchido para indicar que o ponto de interrupção está habilitado.

![Depois de configurar um ponto de interrupção no elemento selecionado](media/iterative-development-debugging/iterative-development-image6.png)

## <a name="next-steps"></a>Próximas etapas
[Integração e implementação contínuas no Azure Data Factory](continuous-integration-deployment.md)
