---
title: Monitorar os aplicativos do Docker no Aplicativo Azure insights | Microsoft Docs
description: Contadores de desempenho, eventos e exceções do Docker podem ser exibidos no Application Insights, juntamente com a telemetria dos aplicativos contidos.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 03/14/2019
ms.openlocfilehash: 66a2481d25c863bbdbf4d72c4683a309918776db
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677931"
---
# <a name="monitor-docker-applications-in-application-insights-deprecated"></a>Monitorar os aplicativos do Docker no Application Insights (preterido)

> [!NOTE]
> Esta solução foi preterida. Para saber mais sobre nossos investimentos atuais no monitoramento de contêiner, é recomendável fazer check-out [Azure monitor para contêineres](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview).

Os eventos de ciclo de vida e os contadores de desempenho dos contêineres [Docker](https://www.docker.com/) podem ser representados em gráfico no Application Insights. Instale a imagem [Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) em um contêiner em seu host e ele exibirá os contadores de desempenho para o host, bem como para outras imagens.

Com o Docker, você distribui seus aplicativos em contêineres leves completos com todas as dependências. Eles serão executados em qualquer computador host que executa um mecanismo do Docker.

Ao executar a [imagem de Application insights](https://hub.docker.com/r/microsoft/applicationinsights/) no host do Docker, você obtém estes benefícios:

* A telemetria do ciclo de vida sobre todos os contêineres em execução no host - início, parada e assim por diante.
* Contadores de desempenho para todos os contêineres. CPU, memória, uso de rede e muito mais.
* Se você [instalou o SDK do Application Insights para Java](../../azure-monitor/app/java-get-started.md) nos aplicativos em execução nos contêineres, toda a telemetria desses aplicativos terá propriedades adicionais que identificam o contêiner e o computador host. Por exemplo, se você tiver instâncias de um aplicativo em execução em mais de um host, poderá filtrar facilmente a telemetria do aplicativo por host.

## <a name="set-up-your-application-insights-resource"></a>Configurar seu recurso do Application Insights

1. Entre [portal do Microsoft Azure](https://azure.com) e abra o recurso de Application insights para seu aplicativo; ou [crie um novo](../../azure-monitor/app/create-new-resource.md ). 
   
    *Qual recurso devo usar?* Se os aplicativos que você está executando em seu host foram desenvolvidos por outra pessoa, você precisa [criar um novo recurso de Application insights](../../azure-monitor/app/create-new-resource.md ). Esse é o local em que você pode exibir e analisar a telemetria. (Selecione ' geral ' para o tipo de aplicativo.)
   
    Mas se você for o desenvolvedor dos aplicativos, esperamos que você tenha [adicionado o SDK do Application Insights](../../azure-monitor/app/java-get-started.md) a cada um deles. Se eles forem realmente componentes de um único aplicativo de negócios, você poderá configurar todos eles para enviar telemetria para um recurso e usará esse mesmo recurso para exibir o ciclo de vida do Docker e os dados de desempenho. 
   
    Um terceiro cenário é que você desenvolveu a maioria dos aplicativos, mas está usando recursos separados para exibir a telemetria deles. Nesse caso, você provavelmente também desejará criar um recurso separado para os dados do Docker.

2. Clique no menu suspenso **Informações gerais** e copie a Chave de Instrumentação. Use isso para informar ao SDK onde enviar sua telemetria.

Mantenha essa janela do navegador à mão, pois você voltará a ela em breve para examinar sua telemetria.

## <a name="run-the-application-insights-monitor-on-your-host"></a>Executar o monitor de Application Insights em seu host

Agora que você tem algum lugar para exibir a telemetria, você pode configurar o aplicativo em contêineres que o coletará e o enviará.

1. Conecte-se ao host do Docker.
2. Edite sua chave de instrumentação nesse comando e, em seguida, execute-a:
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Apenas uma imagem de Application Insights é necessária por host do Docker. Se seu aplicativo for implantado em vários hosts do Docker, repita o comando em cada host.

## <a name="update-your-app"></a>Atualizar seu aplicativo
Se seu aplicativo for instrumentado com o [SDK do Application insights para Java](../../azure-monitor/app/java-get-started.md), adicione a seguinte linha ao arquivo ApplicationInsights. xml em seu projeto, sob o elemento `<TelemetryInitializers>`:

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

Isso adiciona informações do Docker como o contêiner e a ID do host a cada item de telemetria enviado do seu aplicativo.

## <a name="view-your-telemetry"></a>Exibir sua telemetria
Volte para o recurso de Application Insights no portal do Azure.

Clique no bloco do Docker.

Em breve, você verá os dados que chegam do aplicativo Docker, especialmente se tiver outros contêineres em execução no mecanismo do Docker.

### <a name="docker-container-events"></a>Eventos de contêiner do Docker
![Exemplo](./media/docker/13.png)

Para investigar os eventos individuais, clique em [Pesquisar](../../azure-monitor/app/diagnostic-search.md). Pesquise e filtre para localizar os eventos desejados. Clique em qualquer evento para obter mais detalhes.

### <a name="exceptions-by-container-name"></a>Exceções por nome de contêiner
![Exemplo](./media/docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>Contexto do Docker adicionado à telemetria do aplicativo
A telemetria de solicitação enviada do aplicativo instrumentado com o SDK do ia é aprimorada com informações de contexto do Docker.

## <a name="q--a"></a>P e R
*O que o Application Insights me proporciona que eu não consigo obter com o Docker?*

* Análise detalhada dos contadores de desempenho por contêiner e imagem.
* Integre dados de contêiner e de aplicativo em um painel.
* [Exporte a telemetria](export-telemetry.md) para uma análise adicional em um banco de dados, no Power BI ou em outro painel.

*Como posso obter a telemetria do próprio aplicativo?*

* Instale o SDK do Application Insights no aplicativo. Saiba mais para: [aplicativos Web Java](../../azure-monitor/app/java-get-started.md), [aplicativos Web Windows](../../azure-monitor/app/asp-net.md).

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Próximos passos

* [Application Insights para Java](../../azure-monitor/app/java-get-started.md)
* [Application Insights para node. js](../../azure-monitor/app/nodejs.md)
* [Application Insights para ASP.NET](../../azure-monitor/app/asp-net.md)
