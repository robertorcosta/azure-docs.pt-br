---
title: Plug-in de reagir para Application Insights SDK do JavaScript
description: Como instalar e usar o plug-in reajam para Application Insights SDK do JavaScript.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 3a11f77384c520bed9824841269be4ad998adba4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90056193"
---
# <a name="react-plugin-for-application-insights-javascript-sdk"></a>Plug-in de reagir para Application Insights SDK do JavaScript

O plug-in reajam para o SDK do Application Insights JavaScript permite:

- Acompanhamento de alterações de rota
- Estatísticas de uso de componentes reagir

## <a name="getting-started"></a>Introdução

Instale o pacote NPM:

```bash

npm install @microsoft/applicationinsights-react-js

```

## <a name="basic-usage"></a>Uso básico

```javascript
import React from 'react';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactPlugin, withAITracking } from '@microsoft/applicationinsights-react-js';
import { createBrowserHistory } from "history";

const browserHistory = createBrowserHistory({ basename: '' });
var reactPlugin = new ReactPlugin();
var appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [reactPlugin],
        extensionConfig: {
          [reactPlugin.identifier]: { history: browserHistory }
        }
    }
});
appInsights.loadAppInsights();

// To instrument various React components usage tracking, apply the `withAITracking` higher-order
// component function.

class MyComponent extends React.Component {
    ...
}

export default withAITracking(reactPlugin,appInsights, MyComponent);

```

## <a name="configuration"></a>Configuração

| Nome    | Padrão | Descrição                                                                                                    |
|---------|---------|----------------------------------------------------------------------------------------------------------------|
| history | null    | Reagir o histórico do roteador. Para obter mais informações, consulte a [documentação do pacote reagir-router](https://reactrouter.com/web/api/history). Para saber como acessar o objeto de histórico fora dos componentes, consulte as [perguntas frequentes sobre reagir-roteador](https://github.com/ReactTraining/react-router/blob/master/FAQ.md#how-do-i-access-the-history-object-outside-of-components)    |

### <a name="react-components-usage-tracking"></a>Acompanhamento de uso de componentes reajam

Para instrumentar vários rastreamentos de uso de componentes reajam, aplique a `withAITracking` função de componente de ordem superior.

Ele medirá o tempo do `ComponentDidMount` evento por meio do `ComponentWillUnmount` evento. No entanto, para tornar isso mais preciso, ele subtrairá a hora em que o usuário esteve ocioso `React Component Engaged Time = ComponentWillUnmount timestamp - ComponentDidMount timestamp - idle time` .

Para ver essa métrica no portal do Azure você precisa navegar até o recurso de Application Insights, selecione a guia "métricas" e configure os gráficos vazios para exibir o nome da métrica personalizada "tempo envolvido do componente de reajam (segundos)", selecione agregação (Sum, AVG, etc.) de sua métrica e aplique dividir ser "nome do componente".

![Captura de tela do gráfico que exibe a métrica personalizada "reagir o componente de tempo envolvido (segundos)" dividir por "nome do componente"](./media/javascript-react-plugin/chart.png)

Você também pode executar consultas personalizadas para dividir Application Insights dados para gerar relatórios e visualizações de acordo com seus requisitos. Na portal do Azure navegue até o recurso de Application Insights, selecione "análise" no menu superior da guia Visão geral e execute a consulta.

![Captura de tela dos resultados da consulta de métrica personalizada.](./media/javascript-react-plugin/query.png)

> [!NOTE]
> Pode levar até 10 minutos para que novas métricas personalizadas apareçam no portal do Azure.

## <a name="sample-app"></a>Aplicativo de exemplo

Confira a [demonstração reagir Application insights](https://github.com/Azure-Samples/application-insights-react-demo).

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre o SDK do JavaScript, consulte a [documentação do SDK do Application insights JavaScript](javascript.md).
- Para saber mais sobre a linguagem de consulta Kusto e consultar dados em Log Analytics, consulte a [visão geral de consulta de log](../../azure-monitor/log-query/log-query-overview.md).
