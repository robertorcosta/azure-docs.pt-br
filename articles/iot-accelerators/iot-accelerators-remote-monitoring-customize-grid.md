---
title: Adicionar uma grade à interface do usuário da solução de monitoramento remoto - Azure | Microsoft Docs
description: Este artigo mostra como adicionar um novo GID em uma página na interface da Web da web do acelerador de solução de monitoramento remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/04/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: 7fc878e0c9e099b201264c1c3981c603668214d3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017756"
---
# <a name="add-a-custom-grid-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Adicionar uma grade personalizada à interface da Web da web do acelerador de solução de monitoramento remoto

Este artigo mostra como adicionar uma nova grade a uma página na interface da Web da web do acelerador de solução de monitoramento remoto. O artigo descreve:

- Como preparar um ambiente de desenvolvimento local.
- Como adicionar uma nova grade a uma página na interface do usuário da web.

A grade de exemplo deste artigo exibe os dados do serviço que o artigo explicativo [Adicionar um serviço personalizado ao objeto de instruções da Web da aceleração de solução de Monitoramento Remoto](iot-accelerators-remote-monitoring-customize-service.md) mostra como adicionar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste guia de instruções, você precisa ter o seguinte software instalado em seu computador de desenvolvimento local:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Antes de começar

Você deve concluir as etapas nos seguintes artigos antes de continuar:

- [Adicione uma página personalizada à interface do usuário Web do acelerador de solução de monitoramento remoto](iot-accelerators-remote-monitoring-customize-page.md).
- [Adicione um serviço personalizado à interface do usuário da Web do acelerador de solução de Monitoramento Remoto](iot-accelerators-remote-monitoring-customize-service.md)

## <a name="add-a-grid"></a>Adicionar uma grade

Para adicionar uma grade à interface do usuário da web, você precisa adicionar os arquivos de origem que definem a grade e modificar alguns arquivos existentes para tornar a IU da web ciente do novo componente.

### <a name="add-the-new-files-that-define-the-grid"></a>Adicione os novos arquivos que definem a grade

Para começar, a pasta **src / walkthrough / components / pages / pageWithGrid / exampleGrid** contém os arquivos que definem uma grade:

**exampleGrid.js**

[!code-javascript[Example grid](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGrid.js?name=grid "Example grid")]

**exampleGridConfig.js**

[!code-javascript[Example grid configuration](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGridConfig.js?name=gridconfig "Example grid configuration")]

Cópia de **src/passo a passo/componentes/páginas/pageWithGrid/exampleGrid** pasta para o **src/componentes/páginas/exemplo** pasta.

### <a name="add-the-grid-to-the-page"></a>Adicionar a grade para a página

Modificar a **src/components/pages/example/basicPage.container.js** da seguinte forma para importar as definições de serviço:

```js
import { connect } from 'react-redux';
import { translate } from 'react-i18next';

import {
  epics as exampleEpics,
  getExamples,
  getExamplesError,
  getExamplesLastUpdated,
  getExamplesPendingStatus
} from 'store/reducers/exampleReducer';
import { BasicPage } from './basicPage';

// Pass the data
const mapStateToProps = state => ({
  data: getExamples(state),
  error: getExamplesError(state),
  isPending: getExamplesPendingStatus(state),
  lastUpdated: getExamplesLastUpdated(state)
});

// Wrap the dispatch method
const mapDispatchToProps = dispatch => ({
  fetchData: () => dispatch(exampleEpics.actions.fetchExamples())
});

export const BasicPageContainer = translate()(connect(mapStateToProps, mapDispatchToProps)(BasicPage));
```

Modificar a **src/components/pages/example/basicPage.js** da seguinte maneira para adicionar a grade:

```js
// Copyright (c) Microsoft. All rights reserved.

import React, { Component } from 'react';

import {
  AjaxError,
  ContextMenu,
  PageContent,
  RefreshBar
} from 'components/shared';
import { ExampleGrid } from './exampleGrid';

import './basicPage.css';

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null };
  }

  componentDidMount() {
    const { isPending, lastUpdated, fetchData } = this.props;
    if (!lastUpdated && !isPending) fetchData();
  }

  onGridReady = gridReadyEvent => this.gridApi = gridReadyEvent.api;

  onContextMenuChange = contextBtns => this.setState({ contextBtns });

  render() {
    const { t, data, error, isPending, lastUpdated, fetchData } = this.props;
    const gridProps = {
      onGridReady: this.onGridReady,
      rowData: isPending ? undefined : data || [],
      onContextMenuChange: this.onContextMenuChange,
      t: this.props.t
    };

    return [
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
      </ContextMenu>,
      <PageContent className="basic-page-container" key="page-content">
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
    ];
  }
}
```

Modificar a **src/components/pages/example/basicPage.test.js** da seguinte forma para atualizar os testes:

```js
// Copyright (c) Microsoft. All rights reserved.

import React from 'react';
import { shallow } from 'enzyme';
import 'polyfills';

import { BasicPage } from './basicPage';

describe('BasicPage Component', () => {
  it('Renders without crashing', () => {

    const fakeProps = {
      data: undefined,
      error: undefined,
      isPending: false,
      lastUpdated: undefined,
      fetchData: () => { },
      t: () => { },
    };

    const wrapper = shallow(
      <BasicPage {...fakeProps} />
    );
  });
});
```

## <a name="test-the-grid"></a>A grade de teste

Se a interface do usuário da web ainda não estiver em execução localmente, execute o seguinte comando na raiz da sua cópia local do repositório:

```cmd/sh
npm start
```

O comando anterior é executado localmente na interface do usuário em `http://localhost:3000/dashboard`. Navegue até a página **Exemplo** para ver os dados de exibição da grade do serviço.

## <a name="select-rows"></a>Selecionar linhas

Existem duas opções para permitir que um usuário selecione linhas na grade:

### <a name="hard-select-rows"></a>Linhas de seleção de disco rígido

Se um usuário precisar atuar em várias linhas ao mesmo tempo, use as caixas de seleção nas linhas:

1. Ative a seleção de linhas rígidas adicionando **checkboxColumn** ao **columnDefs** fornecido à grade. **checkboxColumn** é definido em **/src/components/shared/pcsGrid/pcsGrid.js**:

    ```js
    this.columnDefs = [
      checkboxColumn,
      exampleColumnDefs.id,
      exampleColumnDefs.description
    ];
    ```

1. Para acessar os itens selecionados, você recebe uma referência à API da grade interna:

    ```js
    onGridReady = gridReadyEvent => {
      this.gridApi = gridReadyEvent.api;
      // Call the onReady props if it exists
      if (isFunc(this.props.onGridReady)) {
        this.props.onGridReady(gridReadyEvent);
      }
    };
    ```

1. Forneça botões de contexto à página quando uma linha na grade for selecionada de forma rígida:

    ```js
    this.contextBtns = [
      <Btn key="context-btn-1" svg={svgs.reconfigure} onClick={this.doSomething()}>Button 1</Btn>,
      <Btn key="context-btn-2" svg={svgs.trash} onClick={this.doSomethingElse()}>Button 2</Btn>
    ];
    ```

    ```js
    onHardSelectChange = (selectedObjs) => {
      const { onContextMenuChange, onHardSelectChange } = this.props;
      // Show the context buttons when there are rows checked.
      if (isFunc(onContextMenuChange)) {
        onContextMenuChange(selectedObjs.length > 0 ? this.contextBtns : null);
      }
      //...
    }
    ```

1. Quando um botão de contexto é clicado, pegue os itens selecionados para fazer seu trabalho:

    ```js
    doSomething = () => {
      //Just for demo purposes. Don't console log in a real grid.
      console.log('Hard selected rows', this.gridApi.getSelectedRows());
    };
    ```

### <a name="soft-select-rows"></a>Linhas de Soft-select

Se o usuário só precisa agir em uma única linha, configurar um link de soft-select para uma ou mais colunas de **columnDefs**.

1. Em **exampleGridConfig.js**, adicione **SoftSelectLinkRenderer** como o **cellRendererFramework** para um **columnDef**.

    ```js
    export const exampleColumnDefs = {
      id: {
        headerName: 'examples.grid.name',
        field: 'id',
        sort: 'asc',
        cellRendererFramework: SoftSelectLinkRenderer
      }
    };
    ```

1. Quando um link de seleção suave é clicado, ele aciona o evento **onSoftSelectChange**. Execute qualquer ação for desejada para aquela linha, como abrir um submenu de detalhes. Este exemplo simplesmente grava no console:

    ```js
    onSoftSelectChange = (rowId, rowData) => {
      //Note: only the Id is reliable, rowData may be out of date
      const { onSoftSelectChange } = this.props;
      if (rowId) {
        //Just for demo purposes. Don't console log a real grid.
        console.log('Soft selected', rowId);
        this.setState({ softSelectedId: rowId });
      }
      if (isFunc(onSoftSelectChange)) {
        onSoftSelectChange(rowId, rowData);
      }
    }
    ```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre os recursos disponíveis para ajudá-lo a adicionar ou personalizar páginas na interface do usuário da Web no acelerador de solução de monitoramento remoto.

Agora você definiu uma grade, a próxima etapa é [Adicionar um flyout personalizado à interface da Web da Web do acelerador de solução de Monitoramento Remoto](iot-accelerators-remote-monitoring-customize-flyout.md) exibida na página de exemplo.

Para obter mais informações conceituais sobre o acelerador de solução de monitoramento remoto, consulte [arquitetura de monitoramento remoto](iot-accelerators-remote-monitoring-sample-walkthrough.md).
