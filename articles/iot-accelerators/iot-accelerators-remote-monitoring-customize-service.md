---
title: Adicione um serviço à solução de monitoramento remoto da interface do usuário – Azure | Microsoft Docs
description: Este artigo mostra como adicionar um novo serviço na interface do usuário Web do acelerador de solução de Monitoramento Remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: e44aa8ade512a6005959e795cb1d4ad861da1338
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "61447039"
---
# <a name="add-a-custom-service-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Adicione um serviço personalizado à interface do usuário da Web do acelerador de solução de Monitoramento Remoto

Este artigo mostra como adicionar um novo serviço na interface do usuário Web do acelerador de solução de Monitoramento Remoto. O artigo descreve:

- Como preparar um ambiente de desenvolvimento local.
- Como adicionar um novo serviço à interface do usuário Web.

O serviço de exemplo deste artigo fornece os dados para uma grade que a seção [Adicionar uma grade personalizada ao artigo explicativo de interface do usuário da Web do acelerador de solução de Monitoramento Remoto](iot-accelerators-remote-monitoring-customize-grid.md) mostra como adicionar.

Em um aplicativo React, um serviço normalmente interage com um serviço de back-end. Exemplos no acelerador de solução de Monitoramento Remoto incluem serviços que interagem com o gerenciador do hub IoT e microsserviços de configuração.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste guia de instruções, você precisa ter o seguinte software instalado em seu computador de desenvolvimento local:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Antes de começar

Você deve concluir as etapas no artigo de instruções [Adicionar uma página personalizada à interface do usuário Web do acelerador de solução de Monitoramento Remoto](iot-accelerators-remote-monitoring-customize-page.md) antes de continuar.

## <a name="add-a-service"></a>Adicionar um serviço

Para adicionar um serviço à interface do usuário da Web, você precisa adicionar os arquivos de origem que definem o serviço e modificar alguns arquivos existentes para criar a interface do usuário Web com suporte do novo serviço.

### <a name="add-the-new-files-that-define-the-service"></a>Adicione os novos arquivos que definem o serviço

Para começar, a pasta **src/walkthrough/services** contém os arquivos que definem um serviço simples:

**exampleService.js**

[!code-javascript[Example service](~/remote-monitoring-webui/src/walkthrough/services/exampleService.js?name=service "Example service")]

Para saber mais sobre como os serviços são implementados, confira [Introdução à Programação Reativa você está perdendo](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754).

**model/exampleModels.js**

[!code-javascript[Example model](~/remote-monitoring-webui/src/walkthrough/services/models/exampleModels.js?name=models "Example model")]

Copie **exampleService.js** para a pasta **src/services** e copie **exampleModels.js** para a pasta **src/services/models**.

Atualize o arquivo **index.js** na pasta **src/services** para exportar o novo serviço:

```js
export * from './exampleService';
```

Atualize o arquivo **index.js** na pasta **src/services/models** para exportar o novo modelo:

```js
export * from './exampleModels';
```

### <a name="set-up-the-calls-to-the-service-from-the-store"></a>Configurar chamadas para o serviço da loja

Para começar, a pasta **src/walkthrough/store/reducers** contém um redutor de exemplo:

**exampleReducer.js**

[!code-javascript[Example reducer](~/remote-monitoring-webui/src/walkthrough/store/reducers/exampleReducer.js?name=reducer "Example reducer")]

Copie **exampleReducer.js** para a pasta **store/src/redutores**.

Para saber mais sobre o redutor e **Épicos**, confira [redux-observable](https://redux-observable.js.org/).

### <a name="configure-the-middleware"></a>Configurar o middleware

Para configurar o middleware, adicione o redutor ao arquivo **rootReducer.js** na pasta **src/store**:

```js
import { reducer as exampleReducer } from './reducers/exampleReducer';

const rootReducer = combineReducers({
  ...appReducer,
  ...devicesReducer,
  ...rulesReducer,
  ...simulationReducer,
  ...exampleReducer
});
```

Adicionar os épicos ao arquivo **rootEpics.js** na pasta **src/store**:

```js
import { epics as exampleEpics } from './reducers/exampleReducer';

// Extract the epic function from each property object
const epics = [
  ...appEpics.getEpics(),
  ...devicesEpics.getEpics(),
  ...rulesEpics.getEpics(),
  ...simulationEpics.getEpics(),
  ...exampleEpics.getEpics()
];
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre os recursos disponíveis para ajudá-lo a adicionar ou personalizar serviços na interface do usuário da Web no acelerador de solução de monitoramento remoto.

Agora que você definiu um serviço, a próxima etapa é [Adicionar uma grade personalizada para a interface do usuário Web do acelerador de solução de Monitoramento Remoto](iot-accelerators-remote-monitoring-customize-grid.md) que exibe dados retornados pelo serviço.

Para obter mais informações conceituais sobre o acelerador da solução de Monitoramento Remoto, veja [Arquitetura de Monitoramento Remoto](iot-accelerators-remote-monitoring-sample-walkthrough.md).
