---
title: Monitorar aplicativos do Docker no Azure Application Insights | Microsoft Docs
description: Contadores de desempenho, eventos e exceções do Docker podem ser exibidos no Application Insights, juntamente com a telemetria dos aplicativos contidos.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 272b4e8af7b1ed3d01d8af0979b56954585f795d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90977583"
---
# <a name="monitor-docker-applications-in-application-insights-deprecated"></a>Monitorar os aplicativos do Docker no Application Insights (preterido)

> [!NOTE]
> Esta solução foi reprovada. Para saber mais sobre nossos investimentos atuais no monitoramento de contêineres, recomendamos que você faça o check-out do [Azure Monitor para contêineres](../insights/container-insights-overview.md).

Os eventos de ciclo de vida e os contadores de desempenho dos contêineres [Docker](https://www.docker.com/) podem ser representados em gráfico no Application Insights. Instale a imagem [Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) em um contêiner em seu host e ele exibirá os contadores de desempenho para o host, bem como para outras imagens.

Com o Docker, você distribui os aplicativos em contêineres leves com todas as dependências. Eles serão executados em qualquer máquina host que executa um Mecanismo de Docker.

Ao executar a [imagem do Application Insights](https://hub.docker.com/r/microsoft/applicationinsights/) no host do Docker, você obtém estes benefícios:

* A telemetria do ciclo de vida sobre todos os contêineres em execução no host - início, parada e assim por diante.
* Contadores de desempenho para todos os contêineres. CPU, memória, uso da rede e muito mais.
* Se você [instalou o SDK do Application Insights para Java](./java-get-started.md) nos aplicativos em execução nos contêineres, toda a telemetria desses aplicativos terá propriedades adicionais que identificam o contêiner e o computador host. Portanto, por exemplo, se você tiver instâncias de um aplicativo em execução em mais de um host, poderá filtrar a telemetria do aplicativo pelo host com facilidade.

## <a name="set-up-your-application-insights-resource"></a>Configurar seu recurso do Application Insights

1. Entre no [portal do Microsoft Azure](https://azure.com) e abra o recurso do Application Insights de seu aplicativo ou [crie um novo](./create-new-resource.md). 
   
    *Qual recurso devo usar?* Se os aplicativos que estão em execução no host foram desenvolvidos por outra pessoa, você precisa [criar um novo recurso do Application Insights](./create-new-resource.md). Esse é o local em que você pode exibir e analisar a telemetria. (Selecione 'Geral' para o tipo de aplicativo.)
   
    Mas se você for o desenvolvedor dos aplicativos, esperamos que você tenha [adicionado o SDK do Application Insights](./java-get-started.md) a cada um deles. Se eles forem realmente todos os componentes de um único aplicativo de negócios, você poderá configurar todos eles para enviar a telemetria para um recurso e usará esse mesmo recurso para exibir os dados de desempenho e do ciclo de vida do Docker. 
   
    Um terceiro cenário é que você desenvolveu a maioria dos aplicativos, mas está usando recursos separados para exibir a telemetria deles. Nesse caso, provavelmente, você também desejará criar um recurso separado para os dados do Docker.

2. Clique no menu suspenso **Informações gerais** e copie a Chave de Instrumentação. Você usará isso para informar ao SDK o local em que sua telemetria será enviada.

Mantenha essa janela do navegador à mão, pois você voltará a ele em breve para examinar a telemetria.

## <a name="run-the-application-insights-monitor-on-your-host"></a>Executar o monitor do Application Insights em seu host

Agora que você tem algum lugar para exibir a telemetria, configure o aplicativo contido que a coletará e enviará.

1. Conecte-se ao seu host do Docker.
2. Edite a chave de instrumentação nesse comando e, em seguida, execute-a:
   
   ```
   
   docker run -v /var/run/docker.sock:/docker.sock -d microsoft/applicationinsights ikey=000000-1111-2222-3333-444444444
   ```

Apenas uma imagem do Application Insights é necessária por host do Docker. Se o seu aplicativo for implantado em vários hosts do Docker, repita o comando em todos os hosts.

## <a name="update-your-app"></a>Atualizar seu aplicativo
Se seu aplicativo for instrumentado com o [SDK do Application Insights para Java](./java-get-started.md), adicione a seguinte linha ao arquivo ApplicationInsights.xml em seu projeto, sob o elemento `<TelemetryInitializers>`:

```xml

    <Add type="com.microsoft.applicationinsights.extensibility.initializer.docker.DockerContextInitializer"/> 
```

Isso adiciona informações do Docker como o contêiner e a ID do host a cada item de telemetria enviado do seu aplicativo.

## <a name="view-your-telemetry"></a>Exibir sua telemetria
Volte ao recurso do Application Insights no Portal do Azure.

Clique por meio do bloco do Docker.

Em breve, você verá os dados recebidos do aplicativo do Docker, especialmente se tiver outros contêineres em execução em seu mecanismo do Docker.

### <a name="docker-container-events"></a>Eventos de contêiner do Docker
![A captura de tela mostra a seleção de pesquisa, uma janela resultados da pesquisa de diagnóstico com uma seta apontando de um evento personalizado para uma janela com dados personalizados.](./media/docker/13.png)

Para investigar os eventos individuais, clique em [Pesquisar](./diagnostic-search.md). Pesquise e filtre para localizar os eventos desejados. Clique em qualquer evento para obter mais detalhes.

### <a name="exceptions-by-container-name"></a>Exceções por nome do contêiner
![Captura de tela mostra a página Metrics Explorer com um gráfico selecionado e a janela detalhes do gráfico aberta no lado direito.](./media/docker/14.png)

### <a name="docker-context-added-to-app-telemetry"></a>Contexto do Docker adicionado à telemetria do aplicativo
Solicitar telemetria enviada do aplicativo instrumentado com o AI SDK é enriquecida com informações de contexto do Docker.

## <a name="q--a"></a>Perguntas e Respostas
*O que o Application Insights me proporciona que eu não consigo obter com o Docker?*

* Análise detalhada dos contadores de desempenho por contêiner e imagem.
* Integração dos dados de contêiner e do aplicativo em um painel.
* [Exporte a telemetria](export-telemetry.md) para uma análise adicional em um banco de dados, no Power BI ou em outro painel.

*Como posso obter a telemetria do próprio aplicativo?*

* Instale o SDK do Application Insights no aplicativo. Saiba mais para: [aplicativos Web Java](./java-get-started.md), [aplicativos Web Windows](./asp-net.md).

## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Próximas etapas

* [Application Insights para Java](./java-get-started.md)
* [Application Insights para Node.js](./nodejs.md)
* [Application Insights para ASP.NET](./asp-net.md)

