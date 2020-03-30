---
title: Use as ações do GitHub para fazer atualizações de código nas funções do Azure
description: Saiba como usar as ações do GitHub para definir um fluxo de trabalho para construir e implantar projetos de funções do Azure no GitHub.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: cshoe
ms.openlocfilehash: dd74fd5c38e5a8800d2092afc1db1b412b126861
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649901"
---
# <a name="continuous-delivery-by-using-github-action"></a>Entrega contínua usando o GitHub Action

[O GitHub Actions](https://github.com/features/actions) permite definir um fluxo de trabalho para criar e implantar automaticamente o código de suas funções para funcionar o aplicativo no Azure. 

No GitHub Actions, um [fluxo de trabalho](https://help.github.com/articles/about-github-actions#workflow) é um processo automatizado que você define em seu repositório GitHub. Esse processo diz ao GitHub como criar e implantar seu projeto de aplicativo de funções no GitHub. 

Um fluxo de trabalho é definido por um arquivo `/.github/workflows/` YAML (.yml) no caminho em seu repositório. Esta definição contém as várias etapas e parâmetros que compõem o fluxo de trabalho. 

Para um fluxo de trabalho funções do Azure, o arquivo tem três seções: 

| Seção | Tarefas |
| ------- | ----- |
| **Autenticação** | <ol><li>Defina um diretor de serviço.</li><li>Baixe o perfil de publicação.</li><li>Crie um segredo do GitHub.</li></ol>|
| **Construir** | <ol><li>Configure o ambiente.</li><li>Compile o aplicativo de funções.</li></ol> |
| **Implantar** | <ol><li>Implantar o aplicativo de função.</li></ol>|

> [!NOTE]
> Você não precisa criar um diretor de serviço se decidir usar o perfil de publicação para autenticação.

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço

Você pode criar um [principal de serviço](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) usando o comando [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) no [Azure CLI](/cli/azure/). Você pode executar este comando usando [o Azure Cloud Shell](https://shell.azure.com) no portal Azure ou selecionando o botão **Tente.o.**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

Neste exemplo, substitua os espaços reservados no recurso por seu ID de assinatura, grupo de recursos e nome do aplicativo de função. A saída são as credenciais de atribuição de função que fornecem acesso ao seu aplicativo de função. Copie este objeto JSON, que você pode usar para autenticar do GitHub.

> [!IMPORTANT]
> É sempre uma boa prática conceder acesso mínimo. É por isso que o escopo no exemplo anterior está limitado ao aplicativo de função específica e não a todo o grupo de recursos.

## <a name="download-the-publishing-profile"></a>Baixe o perfil de publicação

Você pode baixar o perfil de publicação do seu aplicativo de função, indo para a página **Visão Geral** do seu aplicativo e clicando em Obter o perfil **de publicação**.

   ![Baixar perfil de publicação](media/functions-how-to-github-actions/get-publish-profile.png)

Copie o conteúdo do arquivo.

## <a name="configure-the-github-secret"></a>Configure o segredo do GitHub

1. No [GitHub,](https://github.com)navegue até o seu repositório, selecione**Segredos** >  **de Configurações** > **Adicionar um novo segredo**.

   ![Adicionar segredo](media/functions-how-to-github-actions/add-secret.png)

1. Adicione um novo segredo.

   * Se você estiver usando o principal de serviço que você criou `AZURE_CREDENTIALS` usando o Azure CLI, use para o **Nome**. Em seguida, cole a saída do objeto JSON copiada para **valor**e selecione **Adicionar segredo**.
   * Se você estiver usando um perfil `SCM_CREDENTIALS` de publicação, use o **Nome**. Em seguida, use o conteúdo do arquivo do perfil de publicação para **Valor**e selecione **Adicionar segredo**.

O GitHub agora pode autenticar para o seu aplicativo de função no Azure.

## <a name="set-up-the-environment"></a>Configurar o ambiente 

A configuração do ambiente é feita usando uma ação de configuração de publicação específica do idioma.

# <a name="javascript"></a>[Javascript](#tab/javascript)

O exemplo a seguir mostra a parte `actions/setup-node` do fluxo de trabalho que usa a ação para configurar o ambiente:

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

O exemplo a seguir mostra a parte `actions/setup-python` do fluxo de trabalho que usa a ação para configurar o ambiente:

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

# <a name="c"></a>[C #](#tab/csharp)

O exemplo a seguir mostra a parte `actions/setup-dotnet` do fluxo de trabalho que usa a ação para configurar o ambiente:

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

O exemplo a seguir mostra a parte `actions/setup-java` do fluxo de trabalho que usa a ação para configurar o ambiente:

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

## <a name="build-the-function-app"></a>Construa o aplicativo de função

Isso depende do idioma e para os idiomas suportados pelas Funções Azure, esta seção deve ser as etapas padrão de construção de cada idioma.

O exemplo a seguir mostra a parte do fluxo de trabalho que constrói o aplicativo de função, que é específico do idioma:

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

# <a name="c"></a>[C #](#tab/csharp)

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

Para implantar seu código em um aplicativo de `Azure/functions-action` função, você precisará usar a ação. Esta ação tem dois parâmetros:

|Parâmetro |Explicação  |
|---------|---------|
|**_nome de aplicativo_** | (Obrigatório) O nome do seu aplicativo de função. |
|_**nome de caça-níquel**_ | (Opcional) O nome do [slot de implantação](functions-deployment-slots.md) para o seu nome que você deseja implantar. O slot já deve ser definido em seu aplicativo de função. |


O exemplo a seguir `functions-action`usa a versão 1 do:

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>Próximas etapas

Para exibir um fluxo de trabalho completo .yaml, consulte um dos arquivos no [azure GitHub Actions amostras de relo](https://aka.ms/functions-actions-samples) que têm `functionapp` no nome. Você pode usar essas amostras como ponto de partida para o seu fluxo de trabalho.

> [!div class="nextstepaction"]
> [Saiba mais sobre as ações do GitHub](https://help.github.com/en/articles/about-github-actions)
