---
title: Adicionar um painel à interface do usuário da solução de Monitoramento Remoto – Azure | Microsoft Docs
description: Este artigo mostra como adicionar um novo painel ao dashboard na interface do usuário da Web do acelerador de solução de Monitoramento Remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: 1dcca8409022ba4cf1f988b7c777e3a1fa511060
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318357"
---
# <a name="add-a-custom-panel-to-the-dashboard-in-the-remote-monitoring-solution-accelerator-web-ui"></a>Adicionar um painel personalizado ao dashboard na interface do usuário da Web do acelerador de solução de Monitoramento Remoto

Este artigo mostra como adicionar um novo painel a uma página de dashboard na interface do usuário da Web do acelerador de solução de Monitoramento Remoto. O artigo descreve:

- Como preparar um ambiente de desenvolvimento local.
- Como adicionar um novo painel para uma página de dashboard na interface do usuário da Web.

O painel de exemplo neste artigo é exibido na página do dashboard existente.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste guia de instruções, você precisa ter o seguinte software instalado em seu computador de desenvolvimento local:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Antes de começar

Conclua as etapas no artigo [Adicionar uma página personalizada à interface do usuário da Web do acelerador de solução de Monitoramento Remoto](iot-accelerators-remote-monitoring-customize-page.md) antes de continuar.

## <a name="add-a-panel"></a>Adicionar um painel

Para adicionar um painel à interface do usuário da Web, é preciso adicionar os arquivos de origem que definem o painel e, em seguida, modificar o dashboard para exibir o painel.

### <a name="add-the-new-files-that-define-the-panel"></a>Adicionar os novos arquivos que definem o painel

Para começar, a pasta **src/walkthrough/components/pages/dashboard/panels/examplePanel** pasta contém os arquivos que definem um painel, incluindo:

**examplePanel.js**

[!code-javascript[Example panel](~/remote-monitoring-webui/src/walkthrough/components/pages/dashboard/panels/examplePanel/examplePanel.js?name=panel "Example panel")]

Copie a pasta **src/walkthrough/components/pages/dashboard/panels/examplePanel** para a pasta **src/components/pages/dashboard/panels**.

Adicione a exportação a seguir ao arquivo **src/walkthrough/components/pages/dashboard/panels/index.js**:

```js
export * from './examplePanel';
```

### <a name="add-the-panel-to-the-dashboard"></a>Adicionar o painel ao dashboard

Modifique o **src/components/pages/dashboard/dashboard.js** para adicioná-lo ao painel.

Adicione o painel de exemplo à lista de importações de painéis:

```js
import {
  OverviewPanel,
  AlertsPanel,
  TelemetryPanel,
  AnalyticsPanel,
  MapPanel,
  transformTelemetryResponse,
  chartColorObjects,
  ExamplePanel
} from './panels';
```

Adicione a seguinte definição de célula para a grade no conteúdo da página:

```js
          <Cell className="col-2">
            <ExamplePanel t={t} />
          </Cell>
```

## <a name="test-the-flyout"></a>Testar o submenu

Se a interface do usuário da web ainda não estiver em execução localmente, execute o seguinte comando na raiz da sua cópia local do repositório:

```cmd/sh
npm start
```

O comando anterior é executado localmente na interface do usuário em `http://localhost:3000/dashboard`. Navegue até a página **Dashboard** para exibir o novo painel.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre os recursos disponíveis para ajudar você a adicionar ou personalizar dashboards na interface do usuário da Web no acelerador de solução de monitoramento remoto.

Para obter mais informações conceituais sobre o acelerador de solução de monitoramento remoto, consulte [arquitetura de monitoramento remoto](iot-accelerators-remote-monitoring-sample-walkthrough.md).
