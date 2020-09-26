---
title: Adicionar um submenu à interface do usuário da solução de monitoramento remoto - Azure | Microsoft Docs
description: Este artigo mostra como adicionar um novo submenu em uma página na interface da Web da web do acelerador de solução de monitoramento remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: afd8c5b01b9f36606bf6bd43fbed747ca83ba320
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318408"
---
# <a name="add-a-custom-flyout-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Adicionar um desdobrável personalizado à interface de usuário da web do acelerador de solução de monitoramento remoto

Este artigo mostra como adicionar um novo desdobrável em uma página na interface da Web da web do acelerador de solução de monitoramento remoto. O artigo descreve:

- Como preparar um ambiente de desenvolvimento local.
- Como adicionar um novo flyout a uma página na interface do usuário da web.

O flyout de exemplo deste artigo é exibido na página com a grade que a seção [Adicionar uma grade personalizada ao artigo explicativo de UI da Web do acelerador de solução de Monitoramento Remoto](iot-accelerators-remote-monitoring-customize-grid.md) mostra como adicionar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste guia de instruções, você precisa ter o seguinte software instalado em seu computador de desenvolvimento local:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Antes de começar

Você deve concluir as etapas nos seguintes artigos antes de continuar:

- [Adicione uma página personalizada à interface do usuário Web do acelerador de solução de monitoramento remoto](iot-accelerators-remote-monitoring-customize-page.md).
- [Adicione um serviço personalizado à interface do usuário da Web do acelerador de solução de Monitoramento Remoto](iot-accelerators-remote-monitoring-customize-service.md)
- [Adicionar uma grade personalizada à interface da Web da web do acelerador de solução de monitoramento remoto](iot-accelerators-remote-monitoring-customize-grid.md)

## <a name="add-a-flyout"></a>Adicionar um submenu

Para adicionar um submenu à interface do usuário da web, você precisa adicionar os arquivos de origem que definem o submenu e modificar alguns arquivos existentes para tornar a interface do usuário da web ciente do novo componente.

### <a name="add-the-new-files-that-define-the-flyout"></a>Adicione os novos arquivos que definem o submenu

Para começar, o **src/passo a passo/componentes/páginas/pageWithFlyout/flyouts/exampleFlyout** pasta contém os arquivos que definem um submenu:

**exampleFlyout.container.js**

[!code-javascript[Example flyout container](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.container.js?name=flyoutcontainer "Example flyout container")]

**exampleFlyout.js**

[!code-javascript[Example flyout](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.js?name=flyout "Example flyout")]

Cópia de **src/passo a passo/componentes/páginas/pageWithFlyout/flyouts** pasta para o **src/componentes/páginas/exemplo** pasta.

### <a name="add-the-flyout-to-the-page"></a>Adicionar submenu à página

Modificar a **src/components/pages/example/basicPage.js** para adicionar o submenu.

Adicione **Btn** para as importações do **componentes/shared** e adicione as importações para **svgs** e **ExampleFlyoutContainer**:

```js
import {
  AjaxError,
  ContextMenu,
  PageContent,
  RefreshBar,
  Btn
} from 'components/shared';
import { ExampleGrid } from './exampleGrid';
import { svgs } from 'utilities';
import { ExampleFlyoutContainer } from './flyouts/exampleFlyout';
```

Adicionar um **const** definição **closedFlyoutState** e adicioná-lo para o estado no construtor:

```js
const closedFlyoutState = { openFlyoutName: undefined };

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null, closedFlyoutState };
  }
```

Adicione as seguintes funções para o **página básica** classe:

```js
  closeFlyout = () => this.setState(closedFlyoutState);

  openFlyout = (name) => () => this.setState({ openFlyoutName: name });
```

Adicione o seguinte **const** definições para o **renderizar** função:

```js
    const { openFlyoutName } = this.state;

    const isExampleFlyoutOpen = openFlyoutName === 'example';
```

Adicione um botão para abrir o flyout no menu de contexto:

```js
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
        <Btn svg={svgs.reconfigure} onClick={this.openFlyout('example')}>{t('walkthrough.pageWithFlyout.open')}</Btn>
      </ContextMenu>,
```

Adicione algum texto e o contêiner flyout ao conteúdo da página:

```js
      <PageContent className="basic-page-container" key="page-content">
        {t('walkthrough.pageWithFlyout.pageBody')}
        { isExampleFlyoutOpen && <ExampleFlyoutContainer onClose={this.closeFlyout} /> }
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
```

## <a name="test-the-flyout"></a>Testar o submenu

Se a interface do usuário da web ainda não estiver em execução localmente, execute o seguinte comando na raiz da sua cópia local do repositório:

```cmd/sh
npm start
```

O comando anterior é executado localmente na interface do usuário em `http://localhost:3000/dashboard`. Navegue até a **exemplo** da página e clique em **flutuante aberto**.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre os recursos disponíveis para ajudá-lo a adicionar ou personalizar páginas na interface do usuário da Web no acelerador de solução de monitoramento remoto.

Agora você definiu um submenu em uma página, a próxima etapa é [Adicionar um painel ao painel na IU da Web do acelerador de solução de Monitoramento Remoto](iot-accelerators-remote-monitoring-customize-panel.md).

Para obter mais informações conceituais sobre o acelerador de solução de monitoramento remoto, consulte [arquitetura de monitoramento remoto](iot-accelerators-remote-monitoring-sample-walkthrough.md).
