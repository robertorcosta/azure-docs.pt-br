---
title: Anotações de versão para o Application Insights | Microsoft Docs
description: Adicione uma implantação ou compile marcadores para seus gráficos do gerenciador de métricas no Application Insights.
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: e0e2a106b276110e13b3c68889e4d1d349ba73a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666506"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Anotações sobre gráficos de métricas no Application Insights

Anotações nos [gráficos do Metrics Explorer](../../azure-monitor/app/metrics-explorer.md) mostram onde você implantou uma nova compilação ou outros eventos significativos. As anotações facilitam a veja se suas alterações tiveram algum efeito no desempenho do aplicativo. Eles podem ser criados automaticamente pelo sistema de compilação [Azure Pipelines.](https://docs.microsoft.com/azure/devops/pipelines/tasks/) Você também pode criar anotações para sinalizar eventos desejados criando-as no PowerShell.

> [!NOTE]
> Este artigo reflete a experiência de métricas clássicas **reprovada**. Anotações só estão disponíveis no momento em que a experiência clássica em **[pastas de trabalho](../../azure-monitor/app/usage-workbooks.md)**. Para saber mais sobre a experiência atual de métricas, consulte [Recursos avançados do Azure Metrics Explorer](../../azure-monitor/platform/metrics-charts.md).

![Exemplo de anotações](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-pipelines-build"></a>Lançar anotações com a construção de pipelines Azure

As anotações de lançamento são uma característica do serviço Azure Pipelines baseado em nuvem do Azure DevOps.

### <a name="install-the-annotations-extension-one-time"></a>Instalar a extensão Annotations (uma vez)
Para poder criar anotações de lançamento, você precisará instalar uma das muitas extensões Azure DevOps disponíveis no Visual Studio Marketplace.

1. Faça login no seu projeto [Azure DevOps.](https://azure.microsoft.com/services/devops/)
   
1. Na página de [extensão Denotações de Lançamento](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) do Visual Studio Marketplace, selecione a organização Azure DevOps e selecione **Instalar** para adicionar a extensão à sua organização Azure DevOps.
   
   ![Selecione uma organização Azure DevOps e selecione Instalar.](./media/annotations/1-install.png)
   
Você só precisa instalar a extensão uma vez para sua organização Azure DevOps. Agora você pode configurar anotações de lançamento para qualquer projeto em sua organização.

### <a name="configure-release-annotations"></a>Criar anotações de versão

Crie uma chave de API separada para cada um dos modelos de versão do Azure Pipelines.

1. Faça login no [portal Do Zure](https://portal.azure.com) e abra o recurso Application Insights que monitora sua aplicação. Ou se você não tiver um, [crie um novo recurso application insights](../../azure-monitor/app/app-insights-overview.md).
   
1. Abra a **guia API Access** e copie o **ID do Application Insights**.
   
   ![Em Acesso à API, copie o ID do aplicativo.](./media/annotations/2-app-id.png)

1. Em uma janela separada do navegador, abra ou crie o modelo de versão que gerencia as implantações do Azure Pipelines.
   
1. Selecione **Adicionar tarefa**e selecione a tarefa **Anotação de versão de aplicativos no** menu.
   
   ![Selecione Adicionar tarefa e selecione Anotação de versão de insights de aplicativos.](./media/annotations/3-add-task.png)

   > [!NOTE]
   > A tarefa de anotação de lançamento atualmente suporta apenas agentes baseados no Windows; ele não será executado no Linux, macOS ou outros tipos de agentes.
   
1. Em **ID do aplicativo,** cole o ID de insights do aplicativo que você copiou da guia **API Access.**
   
   ![Cole o ID de insights do aplicativo](./media/annotations/4-paste-app-id.png)
   
1. De volta à janela **API Access** do Application Insights, selecione **Criar a chave de API**. 
   
   ![Na guia API Access, selecione Criar a chave de API.](./media/annotations/5-create-api-key.png)
   
1. Na **janela criar a api,** digite uma descrição, selecione **Escrever anotações**e, em seguida, selecione **Gerar tecla**. Copie a nova chave.
   
   ![Na janela criar a api, digite uma descrição, selecione Escrever anotações e, em seguida, selecione Gerar tecla.](./media/annotations/6-create-api-key.png)
   
1. Na janela modelo de versão, na guia **Variáveis,** selecione **Adicionar** para criar uma definição variável para a nova tecla API.

1. Em **Nome** `ApiKey`, digite e em **Valor,** cole a tecla API que você copiou da guia **API Access.**
   
   ![Na guia Variáveis Azure DevOps, selecione Adicionar, nomear a variável ApiKey e colar a tecla API em Valor.](./media/annotations/7-paste-api-key.png)
   
1. Selecione **Salvar** na janela principal do modelo de versão para salvar o modelo.

## <a name="view-annotations"></a>Exibir anotações
Agora, sempre que você usa o modelo de versão para implantar uma nova versão, uma anotação é enviada ao Application Insights. As anotações aparecem em gráficos no **Metrics Explorer**.

Selecione qualquer marcador de anotação (seta cinza claro) para abrir detalhes sobre a liberação, incluindo solicitante, ramo de controle de origem, pipeline de liberação e ambiente.

![Selecione um marcador de anotação de versão.](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>Criar anotações personalizadas no PowerShell
Você pode usar o script [CreateReleaseAnnotation](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) PowerShell do GitHub para criar anotações de qualquer processo que você quiser, sem usar o Azure DevOps. 

1. Faça uma cópia local de [CreateReleaseAnnotation.ps1](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).
   
1. Use as etapas do procedimento anterior para obter seu ID de Insights de Aplicativo e criar uma chave de API a partir de sua guia **API Access** do Application Insights.
   
1. Ligue para o script PowerShell com o seguinte código, substituindo os espaços reservados com suporte a ângulo por seus valores. Os `-releaseProperties` são opcionais. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

Você pode modificar o script, por exemplo, para criar anotações para o passado.

## <a name="next-steps"></a>Próximas etapas

* [Criar um item de trabalho](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Automação com o PowerShell](../../azure-monitor/app/powershell.md)
