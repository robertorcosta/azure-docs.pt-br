---
title: Fluxos de trabalho do GitHub Actions para Aplicativos Web Estáticos do Azure
description: Saiba como usar repositórios do GitHub para configurar a implantação contínua em Aplicativos Web Estáticos do Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 92d445991aa8b90a343ad7d015787cff35ddf183
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85340925"
---
# <a name="github-actions-workflows-for-azure-static-web-apps-preview"></a>Fluxos de trabalho do GitHub Actions para Aplicativos Web Estáticos do Azure – Visualização

Quando você cria um novo recurso de Aplicativo Web Estático do Azure, o Azure gera um fluxo do trabalho do GitHub Actions para controlar a implantação contínua do aplicativo. O fluxo de trabalho é controlado por um arquivo YAML. Este artigo detalha a estrutura e as opções do arquivo de fluxo de trabalho.

As implantações são iniciadas por [gatilhos](#triggers), que executam [trabalhos](#jobs) que são definidos por [etapas](#steps) individuais.

## <a name="file-location"></a>Local do arquivo

Quando você vincula seu repositório do GitHub aos Aplicativos Web Estáticos do Azure, um arquivo de fluxo de trabalho é adicionado ao repositório.

Siga estas etapas para exibir o arquivo de fluxo de trabalho gerado.

1. Abra o repositório do aplicativo no GitHub.
1. Na guia _Código_, clique na pasta `.github/workflows`.
1. Clique no arquivo com um nome parecido com `azure-static-web-apps-<RANDOM_NAME>.yml`.

O arquivo YAML em seu repositório será semelhante ao exemplo a seguir:

```yml
name: Azure Static Web Apps CI/CD

on:
  push:
    branches:
    - master
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
    - master

jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
    - uses: actions/checkout@v2
      with:
        submodules: true
    - name: Build And Deploy
      id: builddeploy
      uses: Azure/static-web-apps-deploy@v0.0.1-preview
      with:
        azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
        repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
        action: 'upload'
        ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
        app_location: '/' # App source code path
        api_location: 'api' # Api source code path - optional
        app_artifact_location: 'dist' # Built app content directory - optional
        ###### End of Repository/Build Configurations ######

  close_pull_request_job:
    if: github.event_name == 'pull_request' && github.event.action == 'closed'
    runs-on: ubuntu-latest
    name: Close Pull Request Job
    steps:
    - name: Close Pull Request
      id: closepullrequest
      uses: Azure/static-web-apps-deploy@v0.0.1-preview
      with:
        azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
        action: 'close'
```

## <a name="triggers"></a>Gatilhos

Um [gatilho](https://help.github.com/actions/reference/events-that-trigger-workflows) do GitHub Actions notifica um fluxo de trabalho do GitHub Actions para executar um trabalho com base em gatilhos de evento. Os gatilhos são listados usando a propriedade `on` no arquivo de fluxo de trabalho.

```yml
on:
  push:
    branches:
    - master
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
    - master
```

Por meio das configurações associadas à propriedade `on`, você pode definir quais ramificações disparam um trabalho, bem como definir gatilhos a serem acionados para estados de solicitação de pull diferentes.

Neste exemplo, um fluxo de trabalho é iniciado quando a ramificação _mestre_ é alterada. As alterações que iniciam o fluxo de trabalho incluem envio por push de confirmações e abertura de solicitações de pull na ramificação escolhida.

## <a name="jobs"></a>Trabalhos

Cada gatilho de evento requer um manipulador de eventos. Os [trabalhos](https://help.github.com/actions/reference/workflow-syntax-for-github-actions#jobs) definem o que acontece quando um evento é disparado.

No arquivo de fluxo de trabalho de Aplicativos Web Estáticos, há dois trabalhos disponíveis.

| Nome  | Descrição |
|---------|---------|
|`build_and_deploy_job` | É executado quando você envia por push as confirmações ou abre uma solicitação de pull em relação à ramificação listada na propriedade `on`. |
|`close_pull_request_job` | Executa somente quando você fecha uma solicitação de pull que remove o ambiente de preparo criado de solicitações de pull. |

## <a name="steps"></a>Etapas

As etapas são tarefas sequenciais para um trabalho. Uma etapa executa ações como a instalação de dependências, a execução de testes e a implantação de seu aplicativo para produção.

Um arquivo de fluxo de trabalho define as etapas a seguir.

| Trabalho  | Etapas  |
|---------|---------|
| `build_and_deploy_job` |<ol><li>Verifica o repositório no ambiente da ação.<li>Compila e implanta o repositório nos Aplicativos Web Estáticos do Azure.</ol>|
| `close_pull_request_job` | <ol><li>Notifica os Aplicativos Web Estáticos do Azure que uma solicitação de pull fechou.</ol>|

## <a name="build-and-deploy"></a>Criar e implantar

A etapa chamada `Build and Deploy` compila e implanta em sua instância dos Aplicativos Web Estáticos do Azure. Na seção `with`, você pode personalizar os valores a seguir para sua implantação.

```yml
with:
    azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
    repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
    action: 'upload'
    ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
    app_location: '/' # App source code path
    api_location: 'api' # Api source code path - optional
    app_artifact_location: 'dist' # Built app content directory - optional
    ###### End of Repository/Build Configurations ######
```

| Propriedade | Descrição | Obrigatório |
|---|---|---|
| `app_location` | Local do código do aplicativo.<br><br>Por exemplo, digite `/` se o código-fonte do aplicativo estiver na raiz do repositório ou `/app` se o código do aplicativo estiver em um diretório chamado `app`. | Sim |
| `api_location` | Local do seu código de Azure Functions.<br><br>Por exemplo, digite `/api` se o código do aplicativo estiver em uma pasta chamada `api`. Se nenhum aplicativo Azure Functions for detectado na pasta, a compilação não falhará; o fluxo de trabalho pressupõe que você não deseja uma API. | Não |
| `app_artifact_location` | Local do diretório de saída de compilação relativo ao `app_location`.<br><br>Por exemplo, se o código-fonte do aplicativo estiver localizado em `/app`, e o script de compilação gerar arquivos para a pasta `/app/build`, defina `build` como o valor `app_artifact_location`. | Não |

Os valores `repo_token`, `action` e `azure_static_web_apps_api_token` são definidos para você pelos Aplicativos Web Estáticos do Azure e não devem ser alterados manualmente.

## <a name="custom-build-commands"></a>Comandos de compilação personalizados

Você pode ter um controle refinado sobre quais comandos são executados durante uma implantação. Os comandos a seguir podem ser definidos na seção `with` de um trabalho.

A implantação sempre chama `npm install` antes de qualquer comando personalizado.

| Comando            | Descrição |
|---------------------|-------------|
| `app_build_command` | Define um comando personalizado a ser executado durante a implantação do aplicativo de conteúdo estático.<br><br>Por exemplo, para configurar uma compilação de produção para um aplicativo angular, insira `ng build --prod`. Se for deixado em branco, o fluxo de trabalho tentará executar os comandos `npm run build` ou `npm run build:Azure`.  |
| `api_build_command` | Define um comando personalizado a ser executado durante a implantação do aplicativo da API do Azure Functions. |

## <a name="route-file-location"></a>Local do arquivo de rota

Você pode personalizar o fluxo de trabalho para procurar o [routes.json](routes.md) em qualquer pasta de seu repositório. A propriedade a seguir pode ser definida na seção `with` de um trabalho.

| Propriedade            | Descrição |
|---------------------|-------------|
| `routes_location` | Define o local do diretório onde o arquivo _routes.json_ é encontrado. Esse local é relativo à raiz do repositório. |

 Ser explícito sobre o local do seu arquivo _routes.json_ é particularmente importante se a etapa de compilação da estrutura de front-end não mover esse arquivo para o `app_artifact_location` por padrão.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Examinar solicitações de pull em ambientes de pré-produção](review-publish-pull-requests.md)
