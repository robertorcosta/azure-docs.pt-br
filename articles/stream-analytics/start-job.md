---
title: Como iniciar um trabalho do Azure Stream Analytics
description: Este artigo descreve como iniciar um trabalho de Stream Analytics no portal Azure, PowerShell e Visual Studio.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: c393eb782c2ff16eb5b3e5967b39938dfe2f1534
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426458"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Como iniciar um trabalho do Azure Stream Analytics

Você pode iniciar seu trabalho no Azure Stream Analytics usando o portal Azure, Visual Studio e PowerShell. Quando você começa um trabalho, você seleciona um horário para o trabalho começar a criar saída. Portal Azure, Visual Studio e PowerShell têm métodos diferentes para definir a hora de início. Esses métodos são descritos abaixo.

## <a name="start-options"></a>Opções de inicialização
As três opções a seguir estão disponíveis para iniciar um trabalho. Observe que todas as vezes mencionadas abaixo são as especificadas no [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics). Se o TIMESTAMP BY não for especificado, o tempo de chegada será usado.
* **Agora**: Faz com que o ponto de partida do fluxo de evento de saída seja o mesmo de quando o trabalho é iniciado. Se um operador temporal for usado (por exemplo, janela de tempo, LAG ou JOIN), o Azure Stream Analytics olhará automaticamente para os dados na fonte de entrada. Por exemplo, se você iniciar um trabalho "Agora" e se sua consulta usar uma janela de tombamento de 5 minutos, o Azure Stream Analytics buscará dados de 5 minutos atrás na entrada.
O primeiro evento de saída possível teria um carimbo de data e hora igual ou maior do que o tempo atual, e a ASA garante que todos os eventos de entrada que possam contribuir logicamente para a saída foram contabilizados. Por exemplo, não são gerados agregados parciais de janelas. É sempre o valor agregado completo.

* **Personalizado**: Você pode escolher o ponto de partida da saída. Da mesma forma que a opção **Now,** o Azure Stream Analytics lerá automaticamente os dados antes desse tempo se um operador temporal for usado 

* **Quando parou pela última vez.** Esta opção está disponível quando o trabalho foi iniciado anteriormente, mas foi parado manualmente ou falhou. Ao escolher essa opção, o Azure Stream Analytics usará o último tempo de saída para reiniciar o trabalho para que nenhum dado seja perdido. Da mesma forma que as opções anteriores, o Azure Stream Analytics lerá automaticamente os dados antes desse tempo se um operador temporal for usado. Uma vez que várias partições de entrada podem ter tempo diferente, o tempo de parada mais antigo de todas as partições é usado, como resultado, algumas duplicatas podem ser vistas na saída. Mais informações sobre o processamento exatamente uma vez estão disponíveis na página [Garantias de Entrega de Eventos](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).


## <a name="azure-portal"></a>Portal do Azure

Navegue até o seu trabalho no portal Azure e selecione **Iniciar** na página de visão geral. Selecione a **hora de início da saída de trabalho** e, em seguida, selecione **Iniciar**.

Escolha uma das opções para **o tempo de início da saída do trabalho**. As opções são *Now*, *Custom*, e, se o trabalho foi executado anteriormente, *quando a última parada*. Veja acima para obter mais informações sobre essas opções.

## <a name="visual-studio"></a>Visual Studio

Na exibição de trabalho, selecione o botão seta verde para iniciar o trabalho. Defina o **modo de início da saída de trabalho** e selecione **Iniciar**. O status do trabalho mudará para **Running**.

Há três opções para **o modo de início da saída de trabalho**: *JobStartTime*, *CustomTime*e *LastOutputEventTime*. Se esta propriedade estiver ausente, o padrão é *JobStartTime*. Veja acima para obter mais informações sobre essas opções.


## <a name="powershell"></a>PowerShell

Use o cmdlet a seguir para iniciar seu trabalho usando o PowerShell:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

Existem três opções para **OutputStartMode**: *JobStartTime,* *CustomTime*e *LastOutputEventTime*. Se esta propriedade estiver ausente, o padrão é *JobStartTime*. Veja acima para obter mais informações sobre essas opções.

Para obter mais `Start-AzStreamAnalyitcsJob` informações sobre o cmdlet, consulte a [referência Start-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob).

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: criar um trabalho do Stream Analytics usando o portal do Azure](stream-analytics-quick-create-portal.md)
* [Quickstart: Crie um trabalho de Stream Analytics usando o Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Início Rápido: Criar um trabalho do Stream Analytics usando ferramentas do Azure Stream Analytics para Visual Studio](stream-analytics-quick-create-vs.md)
