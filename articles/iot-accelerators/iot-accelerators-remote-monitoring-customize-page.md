---
title: Adicione uma página para a solução de monitoramento remoto da interface do usuário - Azure | Microsoft Docs
description: Este artigo mostra como adicionar uma nova página na interface do usuário web do acelerador de solução de Monitoramento Remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: c90f4166bf88a8df18a93e84903c93461b904d2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82187255"
---
# <a name="add-a-custom-page-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Adicione uma página personalizada à interface da Web da web do acelerador de solução de monitoramento remoto

Este artigo mostra como adicionar uma nova página na interface do usuário web do acelerador de solução de Monitoramento Remoto. O artigo descreve:

- Como preparar um ambiente de desenvolvimento local.
- Como adicionar uma nova página para o interface do usuário da web.

Outros guias de instruções ampliam esse cenário para adicionar mais recursos para a página que você adicionar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste guia de instruções, você precisa ter o seguinte software instalado em seu computador de desenvolvimento local:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Preparar um ambiente de desenvolvimento local para a interface do usuário

O código da interface do usuário do acelerador de solução de Monitoramento Remoto é implementado usando a estrutura JavaScript [Reagir](https://reactjs.org/). Você pode encontrar o código-fonte na [WebUI de Monitoramento Remoto](https://github.com/Azure/pcs-remote-monitoring-webui) repositório do GitHub.

Para fazer e testar as alterações na interface do usuário, você pode executá-la em seu computador de desenvolvimento local. Opcionalmente, a cópia local pode se conectar a uma instância implantada do acelerador de solução para habilitá-lo para interagir com os dispositivos simulados ou reais.

Para preparar o ambiente de desenvolvimento local, use o Git para clonar o repositório do [WebUI de monitoramento remoto](https://github.com/Azure/pcs-remote-monitoring-webui) em seu computador local:

```cmd/sh
git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
```

## <a name="add-a-page"></a>Adicionar uma página

Para adicionar uma página para a interface do usuário da web, você precisa adicionar os arquivos de origem que definem a página e modificar alguns arquivos existentes para criar a IU da web com suporte da nova página.

### <a name="add-the-new-files-that-define-the-page"></a>Adicione os novos arquivos que definem a página

Para começar, a pasta **src/walkthrough/components/pages/basicPage** contém seus arquivos que definem uma página simples:

**basicPage.container.js**

[!code-javascript[Page container source](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.container.js?name=container "Page container source")]

**basicPage.js**

[!code-javascript[Basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.js?name=page "Basic page")]

**basicPage.scss**

[!code-javascript[Page styling](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.scss?name=styles "Page styling")]

**basicPage.test.js**

[!code-javascript[Test code for basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.test.js?name=test "Test code for basic page")]

Crie uma nova pasta **src/components/pages/example** e copie esses arquivos nela.

### <a name="add-the-new-page-to-the-web-ui"></a>Adicione a nova página para a interface do usuário da web

Para adicionar a nova página para a interface da web, faça as seguintes alterações em arquivos existentes:

1. Adicione o novo contêiner de página para o arquivo **src/components/pages/index.js**:

    ```js
    export * from './example/basicPage.container';
    ```

1. (Opcional)  Adicione um ícone SVG para a nova página. Para obter mais informações, consulte [webui/src/utilities/README.md](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/utilities/README.md). Você pode usar um arquivo SVG existente.

1. Adicione o nome da página para o arquivo de traduções **public/locales/en/translations.json**. A interface do usuário usa [i18next](https://www.i18next.com/) para internacionalização.

    ```json
    "tabs": {
      "basicPage": "Example",
    },
    ```

1. Abra o arquivo **src/components/app.js** que define a página de aplicativo de nível superior. Adicione a nova página à lista de importações:

    ```javascript
    // Page Components
    import  {
      //...
      BasicPageContainer
    } from './pages';
    ```

1. No mesmo arquivo, adicione a nova página para a matriz `pagesConfig`. Defina o endereço `to` para a rota, referencie o ícone SVG e as traduções adicionadas anteriormente e defina o `component` ao contêiner da página:

    ```js
    const pagesConfig = [
      //...
      {
        to: '/basicpage',
        exact: true,
        svg: svgs.tabs.example,
        labelId: 'tabs.basicPage',
        component: BasicPageContainer
      },
      //...
    ];
    ```

1. Adicione quaisquer barras de trilha para a `crumbsConfig` matriz:

    ```js
    const crumbsConfig = [
      //...
      {
        path: '/basicpage', crumbs: [
          { to: '/basicpage', labelId: 'tabs.basicPage' }
        ]
      },
      //...
    ];
    ```

    Esta página de exemplo tem apenas uma barra de trilha, mas algumas páginas podem ter muito mais.

Salve todas as alterações. Você está pronto para executar a interface da web com a nova página adicionada.

### <a name="test-the-new-page"></a>Testar o a nova página

Em um prompt de comando, navegue até a raiz da sua cópia local do repositório e execute os seguintes comandos para instalar as bibliotecas necessárias e executar a IU da web localmente:

```cmd/sh
npm install
npm start
```

O comando anterior é executado localmente na interface do usuário em `http://localhost:3000/dashboard`.

Sem se conectar a sua instância local da web da interface do usuário para uma instância implantada do acelerador da solução, você verá erros no painel. Esses erros não afetam sua capacidade de testar sua nova página.

Agora você pode editar o código enquanto o site estiver em execução localmente e verá atualização da interface do usuário web dinamicamente.

## <a name="optional-connect-to-deployed-instance"></a>[Opcional] Conectar-se à instância implantada

Opcionalmente, você pode conectar sua cópia local de execução da web da interface do usuário para o acelerador de solução de monitoramento remoto na nuvem:

1. Implante uma instância **básica** do acelerador de solução usando a CLI **pcs**. Anote o nome da sua implantação e as credenciais fornecidas para a máquina virtual. Para saber mais, veja [Implantar usando a CLI](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Use o portal do Azure ou a [CLI do AZ](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para habilitar o acesso SSH à máquina virtual que hospeda os microserviços em sua solução. Por exemplo:

    ```azurecli
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Você só deve habilitar o acesso SSH durante o desenvolvimento e teste. Se você habilitar o SSH, [deverá desabilitá-lo novamente](../security/fundamentals/network-best-practices.md)assim que possível.

1. Use o portal do Azure ou a [CLI az](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para localizar o nome e o endereço IP público de sua máquina virtual. Por exemplo:

    ```azurecli
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Use o SSH para se conectar à sua máquina virtual usando o endereço IP da etapa anterior e as credenciais fornecidas ao executar **pcs** para implantar a solução.

1. Para permitir que o UX local se conecta, execute os seguintes comandos no shell bash na máquina virtual:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Depois de ver que o comando foi concluído e o site da Web for iniciado, você poderá se desconectar da máquina virtual.

1. Em sua cópia local do repositório [WebUI de Monitoramento remoto](https://github.com/Azure/pcs-remote-monitoring-webui), edite o arquivo **env** para adicionar a URL da sua implantação da solução:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre os recursos disponíveis para ajudá-lo a personalizar a interface do usuário da Web no acelerador de solução de Monitoramento Remoto.

Agora que definiu uma página, a próxima etapa é [Adicionar um serviço personalizado para a interface do usuário web do acelerador de solução de Monitoramento Remoto](iot-accelerators-remote-monitoring-customize-service.md) que recupera dados a serem exibidos na interface do usuário.

Para obter mais informações conceituais sobre o acelerador de solução de monitoramento remoto, consulte [arquitetura de monitoramento remoto](iot-accelerators-remote-monitoring-sample-walkthrough.md).
