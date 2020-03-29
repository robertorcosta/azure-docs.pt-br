---
title: Authenticate Azure Spring Cloud com Key Vault em Ações gitHub
description: Como usar o cofre-chave com fluxo de trabalho CI/CD para Azure Spring Cloud com ações do GitHub
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/20/2019
ms.openlocfilehash: 78cd5945e394219be0551bbe97afef07f18b61f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945480"
---
# <a name="authenticate-azure-spring-cloud-with-key-vault-in-github-actions"></a>Authenticate Azure Spring Cloud com Key Vault em Ações gitHub
O cofre é um lugar seguro para guardar chaves. Os usuários corporativos precisam armazenar credenciais para ambientes CI/CD no escopo que eles controlam. A chave para obter credenciais no cofre de chaves deve ser limitada ao escopo de recursos.  Ele tem acesso apenas ao escopo do cofre chave, não a todo o escopo do Azure. É como uma chave que só pode abrir uma caixa forte, não uma chave mestra que pode abrir todas as portas de um prédio. É uma maneira de obter uma chave com outra chave, que é útil em um fluxo de trabalho CICD. 

## <a name="generate-credential"></a>Gerar credencial
Para gerar uma chave para acessar o cofre de chaves, execute o comando abaixo em sua máquina local:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.KeyVault/vaults/<KEY_VAULT> --sdk-auth
```
O escopo especificado `--scopes` pelo parâmetro limita o acesso à chave ao recurso.  Ele só pode acessar a caixa forte.

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
Em seguida, salve os resultados nos **segredos** do GitHub, conforme descrito no [Configurar seu repositório gitHub e autenticar com o Azure](./spring-cloud-howto-github-actions.md#set-up-github-repository-and-authenticate).

## <a name="add-access-policies-for-the-credential"></a>Adicionar políticas de acesso para a credencial
A credencial que você criou acima pode obter apenas informações gerais sobre o Key Vault, não o conteúdo que ele armazena.  Para obter segredos armazenados no Cofre de Chaves, você precisa definir políticas de acesso para a credencial.

Vá para o painel **do Cofre de Chaves** no portal Azure, clique no menu controle de **acesso** e, em seguida, abra a guia **'Funções'.** Selecione **Aplicativos** para **Tipo** e `This resource` para **escopo**.  Você deve ver a credencial que você criou na etapa anterior:

 ![Definir política de acesso](./media/github-actions/key-vault1.png)

Copiar o nome da credencial, por exemplo, `azure-cli-2020-01-19-04-39-02`. Abra o menu **políticas de acesso,** clique **em +Adicionar link política de acesso.**  Selecione `Secret Management` para **Modelo**e selecione **Principal**. Cole o nome da credencial na caixa de entrada **Principal**/**Selecione:**

 ![Selecionar](./media/github-actions/key-vault2.png)

 Clique no botão **Adicionar** na caixa de diálogo **Adicionar diretiva** de acesso e, em seguida, clique **em Salvar**.

## <a name="generate-full-scope-azure-credential"></a>Gerar credencial Azure de escopo completo
Esta é a chave principal para abrir todas as portas do prédio. O procedimento é semelhante ao passo anterior, mas aqui mudamos o escopo para gerar a chave mestra:

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

Novamente, resultados:
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
Copie toda a seqüência JSON.  Bo de volta ao painel **do Key Vault.** Abra o menu **Segredos** e clique no botão **Gerar/Importar.** Insira o nome `AZURE-CRENDENTIALS-FOR-SPRING`secreto, como . Cole a seqüência de credenciais JSON na caixa de entrada **Valor.** Você pode notar que a caixa de entrada de valor é um campo de texto de uma linha, em vez de uma área de texto de várias linhas.  Você pode colar a seqüência JSON completa lá.

 ![Credencial de escopo completo](./media/github-actions/key-vault3.png)

## <a name="combine-credentials-in-github-actions"></a>Combine credenciais em Ações do GitHub
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
* [Ações do Spring Cloud GitHub](./spring-cloud-howto-github-actions.md)