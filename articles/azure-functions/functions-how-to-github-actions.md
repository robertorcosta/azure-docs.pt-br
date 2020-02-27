---
title: Usar ações do GitHub para fazer atualizações de código no Azure Functions
description: Saiba como usar ações do GitHub para definir um fluxo de trabalho para criar e implantar Azure Functions projetos no GitHub.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: cshoe
ms.openlocfilehash: dd74fd5c38e5a8800d2092afc1db1b412b126861
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77649901"
---
# <a name="continuous-delivery-by-using-github-action"></a>Entrega contínua usando a ação do GitHub

As [ações do GitHub](https://github.com/features/actions) permitem definir um fluxo de trabalho para criar e implantar automaticamente o código de funções no aplicativo de funções no Azure. 

Em ações do GitHub, um [fluxo de trabalho](https://help.github.com/articles/about-github-actions#workflow) é um processo automatizado que você define em seu repositório github. Esse processo informa ao GitHub como criar e implantar seu projeto de aplicativo do Functions no GitHub. 

Um fluxo de trabalho é definido por um arquivo YAML (. yml) no caminho `/.github/workflows/` em seu repositório. Essa definição contém as várias etapas e parâmetros que compõem o fluxo de trabalho. 

Para um fluxo de trabalho Azure Functions, o arquivo tem três seções: 

| Seção | Tarefas |
| ------- | ----- |
| **Autenticação** | <ol><li>Defina uma entidade de serviço.</li><li>Baixar o perfil de publicação.</li><li>Crie um segredo do GitHub.</li></ol>|
| **Compilar** | <ol><li>Configure o ambiente.</li><li>Compile o aplicativo de funções.</li></ol> |
| **Implantar** | <ol><li>Implante o aplicativo de funções.</li></ol>|

> [!NOTE]
> Você não precisará criar uma entidade de serviço se decidir usar o perfil de publicação para autenticação.

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço

Você pode criar uma [entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) usando o comando [AZ ad SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) na [CLI do Azure](/cli/azure/). Você pode executar esse comando usando [Azure cloud Shell](https://shell.azure.com) na portal do Azure ou selecionando o botão **experimentar** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

Neste exemplo, substitua os espaços reservados no recurso por sua ID de assinatura, grupo de recursos e nome do aplicativo de funções. A saída são as credenciais de atribuição de função que fornecem acesso ao seu aplicativo de funções. Copie esse objeto JSON, que você pode usar para autenticar do GitHub.

> [!IMPORTANT]
> É sempre uma boa prática conceder acesso mínimo. É por isso que o escopo no exemplo anterior é limitado ao aplicativo de funções específico e não ao grupo de recursos inteiro.

## <a name="download-the-publishing-profile"></a>Baixar o perfil de publicação

Você pode baixar o perfil de publicação do seu aplicativo de funções, acessando a página **visão geral** do seu aplicativo e clicando em **obter perfil de publicação**.

   ![Baixar perfil de publicação](media/functions-how-to-github-actions/get-publish-profile.png)

Copie o conteúdo do arquivo.

## <a name="configure-the-github-secret"></a>Configurar o segredo do GitHub

1. No [GitHub](https://github.com), navegue até o repositório, selecione **configurações** > **segredos** > **Adicionar um novo segredo**.

   ![Adicionar segredo](media/functions-how-to-github-actions/add-secret.png)

1. Adicione um novo segredo.

   * Se você estiver usando a entidade de serviço que você criou usando o CLI do Azure, use `AZURE_CREDENTIALS` para o **nome**. Em seguida, Cole a saída do objeto JSON copiado para o **valor**e selecione **Adicionar segredo**.
   * Se você estiver usando um perfil de publicação, use `SCM_CREDENTIALS` para o **nome**. Em seguida, use o conteúdo do arquivo do perfil de publicação para **valor**e selecione **Adicionar segredo**.

O GitHub agora pode se autenticar no seu aplicativo de funções no Azure.

## <a name="set-up-the-environment"></a>Configurar o ambiente 

A configuração do ambiente é feita usando uma ação de instalação de publicação específica a um idioma.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra a parte do fluxo de trabalho que usa a ação `actions/setup-node` para configurar o ambiente:

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir mostra a parte do fluxo de trabalho que usa a ação `actions/setup-python` para configurar o ambiente:

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra a parte do fluxo de trabalho que usa a ação `actions/setup-dotnet` para configurar o ambiente:

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

# <a name="java"></a>[Java](#tab/java)

O exemplo a seguir mostra a parte do fluxo de trabalho que usa a ação `actions/setup-java` para configurar o ambiente:

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```
---

## <a name="build-the-function-app"></a>Compilar o aplicativo de funções

Isso depende do idioma e dos idiomas com suporte pelo Azure Functions, esta seção deve ser as etapas de compilação padrão de cada idioma.

O exemplo a seguir mostra a parte do fluxo de trabalho que cria o aplicativo de funções, que é específico do idioma:

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

# <a name="python"></a>[Python](#tab/python)

```yaml
    - name: 'Run pip'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for pip in pushd
        pushd .
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/python3.6/site-packages"
        popd
```

# <a name="c"></a>[C#](#tab/csharp)

```yaml
    - name: 'Run dotnet build'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

# <a name="java"></a>[Java](#tab/java)

```yaml
    - name: 'Run mvn'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for maven build in pushd
        pushd . ./POM_ARTIFACT_ID
        mvn clean package
        mvn azure-functions:package
        popd
```
---

## <a name="deploy-the-function-app"></a>Implantar o aplicativo de funções

Para implantar seu código em um aplicativo de funções, será necessário usar a ação `Azure/functions-action`. Esta ação tem dois parâmetros:

|Parâmetro |Explicação  |
|---------|---------|
|**_nome do aplicativo_** | Obrigatório O nome do seu aplicativo de funções. |
|_**nome do slot**_ | Adicional O nome do [slot de implantação](functions-deployment-slots.md) no qual você deseja implantar. O slot já deve estar definido em seu aplicativo de funções. |


O exemplo a seguir usa a versão 1 do `functions-action`:

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>Próximas etapas

Para exibir um Workflow. YAML completo, consulte um dos arquivos no repositório de [exemplos de fluxo de trabalho de ações do Azure GitHub](https://aka.ms/functions-actions-samples) que têm `functionapp` no nome. Você pode usar esses exemplos em um ponto de partida para seu fluxo de trabalho.

> [!div class="nextstepaction"]
> [Saiba mais sobre as ações do GitHub](https://help.github.com/en/articles/about-github-actions)
