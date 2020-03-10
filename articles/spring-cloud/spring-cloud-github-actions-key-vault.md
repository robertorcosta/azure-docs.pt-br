---
title: Autenticar o Azure Spring Cloud com Key Vault em ações do GitHub
description: Como usar o cofre de chaves com fluxo de trabalho de CI/CD para o Azure Spring Cloud com ações do GitHub
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/20/2019
ms.openlocfilehash: 78cd5945e394219be0551bbe97afef07f18b61f7
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945480"
---
# <a name="authenticate-azure-spring-cloud-with-key-vault-in-github-actions"></a>Autenticar o Azure Spring Cloud com Key Vault em ações do GitHub
O Key Vault é um local seguro para armazenar chaves. Os usuários corporativos precisam armazenar credenciais para ambientes de CI/CD no escopo que controlam. A chave para obter credenciais no cofre de chaves deve ser limitada ao escopo do recurso.  Ele tem acesso apenas ao escopo do cofre de chaves, não ao escopo do Azure inteiro. É como uma chave que só pode abrir uma caixa forte, e não uma chave mestra, que pode abrir todas as portas em um edifício. É uma maneira de obter uma chave com outra chave, que é útil em um fluxo de trabalho CICD. 

## <a name="generate-credential"></a>Gerar credencial
Para gerar uma chave para acessar o cofre de chaves, execute o comando abaixo no computador local:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.KeyVault/vaults/<KEY_VAULT> --sdk-auth
```
O escopo especificado pelo parâmetro `--scopes` limita o acesso à chave para o recurso.  Ele só pode acessar a caixa forte.

Com resultados:
```
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Em seguida, salve os resultados em **segredos** do GitHub conforme descrito em [configurar seu repositório do GitHub e autenticar com o Azure](./spring-cloud-howto-github-actions.md#set-up-github-repository-and-authenticate).

## <a name="add-access-policies-for-the-credential"></a>Adicionar políticas de acesso para a credencial
A credencial que você criou acima pode obter apenas informações gerais sobre o Key Vault, não o conteúdo que ele armazena.  Para obter os segredos armazenados no Key Vault, você precisa definir políticas de acesso para a credencial.

Vá para o painel de **Key Vault** em portal do Azure, clique no menu **controle de acesso** e abra a guia **atribuições de função** . Selecione **aplicativos** para **tipo** e `This resource` para **escopo**.  Você deve ver a credencial criada na etapa anterior:

 ![Definir política de acesso](./media/github-actions/key-vault1.png)

Copie o nome da credencial, por exemplo, `azure-cli-2020-01-19-04-39-02`. Abra o menu **políticas de acesso** , clique em **+ Adicionar** link de política de acesso.  Selecione `Secret Management` para **modelo**e, em seguida, selecione **entidade de segurança**. Cole o nome da credencial no **Principal**/**selecione** a caixa de entrada:

 ![Selecionar](./media/github-actions/key-vault2.png)

 Clique no botão **Adicionar** na caixa de diálogo **Adicionar política de acesso** e, em seguida, clique em **salvar**.

## <a name="generate-full-scope-azure-credential"></a>Gerar credencial do Azure de escopo completo
Essa é a chave mestra para abrir todas as portas na construção. O procedimento é semelhante à etapa anterior, mas aqui alteramos o escopo para gerar a chave mestra:

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

Novamente, os resultados:
```
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
Copie a cadeia de caracteres JSON inteira.  Volte para **Key Vault** painel. Abra o menu **segredos** e clique no botão **gerar/importar** . Insira o nome do segredo, como `AZURE-CRENDENTIALS-FOR-SPRING`. Cole a cadeia de caracteres de credencial JSON na caixa de entrada **valor** . Você pode notar que a caixa de entrada valor é um campo de texto de uma linha, em vez de uma área de texto de várias linhas.  Você pode colar a cadeia de caracteres JSON completa lá.

 ![Credencial de escopo completo](./media/github-actions/key-vault3.png)

## <a name="combine-credentials-in-github-actions"></a>Combinar credenciais em ações do GitHub
Defina as credenciais usadas quando o pipeline CICD for executado:

```
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}           # Strong box key you generated in the first step
    - uses: Azure/get-keyvault-secrets@v1.0
      with:
        keyvault: "zlhe-test"
        secrets: "AZURE-CREDENTIALS-FOR-SPRING"           # Master key to open all doors in the building
      id: keyvaultaction
    - uses: azure/login@v1
      with:
        creds: ${{ steps.keyvaultaction.outputs.AZURE-CREDENTIALS-FOR-SPRING }}
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud             # Spring CLI commands from here
          az spring-cloud list

```

## <a name="next-steps"></a>Próximas etapas
* [Ações do GitHub do Spring Cloud](./spring-cloud-howto-github-actions.md)