---
title: Tutorial de rotação para recursos com dois conjuntos de credenciais
description: Use este tutorial para aprender a automatizar a rotação de um segredo para recursos que usam dois conjuntos de credenciais de autenticação.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 06/22/2020
ms.author: jalichwa
ms.openlocfilehash: 0d2ee8fbcb71d8703702f2c72e0bf629563667b9
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542188"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-with-two-sets-of-authentication-credentials"></a>Automatizar a rotação de um segredo para recursos com dois conjuntos de credenciais de autenticação

A melhor maneira de se autenticar nos serviços do Azure é usando uma [identidade gerenciada](../general/managed-identity.md), mas há alguns cenários em que essa não é uma opção. Nesses casos, são usadas chaves de acesso ou senhas. A rotação das chaves de acesso e das senhas deve ser realizada com frequência.

Este tutorial mostra como automatizar a rotação periódica de segredos para bancos de dados e serviços que usam dois conjuntos de credenciais de autenticação. Especificamente, este tutorial realiza a rotação das senhas da conta de Armazenamento do Azure armazenadas como segredos no Azure Key Vault usando uma função disparada pela notificação da Grade de Eventos do Azure. :

> [!NOTE]
> As chaves da conta de armazenamento podem ser gerenciadas automaticamente no Key Vault fornecendo tokens de assinatura de acesso compartilhado para acesso delegado à conta de armazenamento. Há serviços que exigem a cadeia de conexão da conta de armazenamento com a chave de acesso e, para esse cenário, essa solução é recomendada

![Diagrama de solução de rotação](../media/secrets/rotation-dual/rotation-diagram.png)

Na solução acima, o Azure Key Vault armazena as chaves de acesso individuais da conta de Armazenamento como versões do mesmo segredo, alternando entre a chave primária e a secundária nas versões subsequentes. Quando uma chave de acesso é armazenada na versão mais recente do segredo, a chave alternativa é regenerada e adicionada ao Key Vault como uma versão nova e mais recente do segredo. Essa solução fornece um ciclo de rotação inteiro aos aplicativos, a fim de atualizar para a chave regenerada mais recente. 

1. 30 dias antes da data de validade de um segredo, o Key Vault publica o evento de "expiração próxima" na Grade de Eventos.
1. A Grade de Eventos verifica as assinaturas de eventos e usa HTTP POST para chamar o ponto de extremidade do aplicativo de funções assinado para o evento.
1. O aplicativo de funções identifica a chave alternativa (diferente da mais recente) e chama a conta de armazenamento para regenerá-la
1. O aplicativo de funções adiciona nova chave regenerada ao Azure Key Vault como uma nova versão do segredo.

## <a name="prerequisites"></a>Pré-requisitos
* Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Cofre de Chave do Azure
* Duas contas de Armazenamento do Azure

O link de implantação abaixo poderá ser usado se você não tiver um cofre de chaves nem contas de armazenamento existentes:

[![Imagem mostrando um botão rotulado como "Implantar no Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json)

1. Em **Grupo de recursos**, selecione **Criar**. Nomeie o grupo **akvrotation** e clique em **Ok**.
1. Selecione **Revisar + Criar**.
1. Escolha **Criar**

    ![Criar um grupo de recursos](../media/secrets/rotation-dual/dual-rotation-1.png)

Agora você terá um cofre de chaves e duas contas de armazenamento. Você pode verificar essa configuração na CLI do Azure executando o seguinte comando:

```azurecli
az resource list -o table -g akvrotation
```

O resultado terá uma aparência semelhante à da seguinte saída:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv         akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotationstorage     akvrotation      eastus      Microsoft.Storage/storageAccounts
akvrotationstorage2    akvrotation      eastus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-storage-account-key-rotation-function"></a>Criar e implantar a função de rotação de chaves da conta de armazenamento

Em seguida, crie um aplicativo de funções com uma identidade gerenciada pelo sistema, juntamente com os outros componentes necessários e, por fim, implante as funções de rotação de chaves da conta de armazenamento

As funções de rotação do aplicativo de funções exigem estes componentes e configuração:
- Um plano do Serviço de Aplicativo do Azure
- Uma conta de armazenamento necessária para o gerenciamento de gatilho do aplicativo de funções
- Uma política de acesso para acessar segredos no Key Vault
- Atribuição de uma função do serviço de operador da chave da conta de armazenamento ao aplicativo de funções para acessar as chaves de acesso da conta de armazenamento
- Funções de rotação de chaves da conta de armazenamento com gatilho de evento e gatilho HTTP (rotação sob demanda)
- Assinatura de evento da Grade de Eventos para o evento **SecretNearExpiry**

1. Selecione o link de implantação de modelo do Azure: 

   [![Imagem mostrando um botão rotulado como "Implantar no Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json)

1. Na lista **Grupo de recursos**, selecione **akvrotation**.
1. No **Nome da Conta de Armazenamento**, digite o nome da conta de armazenamento com as chaves de acesso cuja rotação deverá ser realizada
1. No **Nome do Cofre de Chaves**, digite o nome do cofre de chaves
1. No **Nome do Aplicativo de Funções**, digite o nome do aplicativo de funções
1. No **Nome do Segredo**, digite o nome do segredo em que as chaves de acesso seriam armazenadas
1. Na **URL do Repositório**, digite a localização do GitHub do código de função ( **https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell.git** )
1. Selecione **Revisar + Criar**.
1. Escolha **Criar**

   ![Examinar + Criar](../media/secrets/rotation-dual/dual-rotation-2.png)

Depois de concluir as etapas anteriores, você terá uma conta de armazenamento, um farm de servidores, um aplicativo de funções e insights do aplicativo. Você deverá ver a tela abaixo depois que a implantação for concluída: ![Implantação concluída](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> No caso de qualquer falha, você pode clicar em **reimplantar** para concluir a implantação dos componentes restantes.


Os modelos de implantação e o código de funções de rotação podem ser encontrados no [GitHub](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell).

## <a name="add-storage-account-access-key-to-key-vault"></a>Adicionar chave de acesso da conta de armazenamento ao Key Vault

Primeiro, defina sua política de acesso para conceder as permissões para *gerenciar segredos* a usuários:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

Agora você pode criar um segredo, usando uma chave de acesso da conta de armazenamento como valor. Você também precisará da ID do recurso da conta de armazenamento, do período de validade do segredo e da ID da chave a ser adicionada ao segredo, para que a função de rotação possa regenerar a chave na conta de armazenamento.

Recupere a ID do recurso da conta de armazenamento. O valor pode ser encontrado sob a propriedade `id`
```azurecli
az storage account show -n akvrotationstorage
```

Listar as chaves de acesso da conta de armazenamento para recuperar valores de chave

```azurecli
az storage account keys list -n akvrotationstorage 
```

Popular valores recuperados para **key1Value** e **storageAccountResourceId**

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name storageKey --vault-name akvrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

A criação de um segredo com uma data de validade curta publicará um evento `SecretNearExpiry` em questão de minutos, o que, por sua vez, disparará a função para realizar a rotação do segredo.

Você pode verificar se as chaves de acesso são regeneradas recuperando e comparando as chaves da conta de armazenamento e o segredo do Key Vault.

Você pode mostrar informações secretas usando o comando abaixo:
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey
```
Observe que a `CredentialId` é atualizada para o `keyName` alternativo e o `value` é regenerado ![Exibição do Segredo](../media/secrets/rotation-dual/dual-rotation-4.png)

Recuperar chaves de acesso para validar o valor
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Lista de chaves de acesso](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-additional-storage-accounts-for-rotation"></a>Adicionar outras contas de armazenamento para rotação

O mesmo aplicativo de funções pode ser reutilizado para realizar a rotação de várias contas de armazenamento. 

A adição de outras chaves de conta de armazenamento à função existente para rotação requer:
- Atribuição de uma função do serviço de operador da chave da conta de armazenamento ao aplicativo de funções para acessar as chaves de acesso da conta de armazenamento
- Assinatura de evento da Grade de Eventos para o evento **SecretNearExpiry**

1. Selecione o link de implantação de modelo do Azure: 

   [![Imagem mostrando um botão rotulado como "Implantar no Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FAdd-Event-Subscription%2Fazuredeploy.json)

1. Na lista **Grupo de recursos**, selecione **akvrotation**.
1. No **Nome da Conta de Armazenamento**, digite o nome da conta de armazenamento com as chaves de acesso cuja rotação deverá ser realizada
1. No **Nome do Cofre de Chaves**, digite o nome do cofre de chaves
1. No **Nome do Aplicativo de Funções**, digite o nome do aplicativo de funções
1. No **Nome do Segredo**, digite o nome do segredo em que as chaves de acesso seriam armazenadas
1. Selecione **Revisar + Criar**.
1. Escolha **Criar**

   ![Examinar + Criar](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>Adicionar outra chave de acesso da conta de armazenamento ao Key Vault

Recupere a ID do recurso da conta de armazenamento. O valor pode ser encontrado sob a propriedade `id`
```azurecli
az storage account show -n akvrotationstorage2
```

Listar as chaves de acesso da conta de armazenamento para recuperar key2Value

```azurecli
az storage account keys list -n akvrotationstorage2 
```

Popular os valores recuperados para **key2Value** e **storageAccountResourceId**

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name storageKey2 --vault-name akvrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Mostre informações secretas usando o comando abaixo:
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey2
```
Observe que a `CredentialId` é atualizada para o `keyName` alternativo e o `value` é regenerado ![Exibição do Segredo](../media/secrets/rotation-dual/dual-rotation-8.png)

Recuperar chaves de acesso para validar o valor
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Lista de chaves de acesso](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="available-key-vault-dual-credential-rotation-functions"></a>Funções disponíveis de rotação de credenciais duplas do Key Vault

- [Conta de armazenamento](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell)
- [Cache Redis](https://github.com/jlichwa/KeyVault-Rotation-RedisCacheKey-PowerShell)

## <a name="learn-more"></a>Saiba mais
- Visão geral: [Monitoramento do Key Vault com a Grade de Eventos do Azure (versão prévia)](../general/event-grid-overview.md)
- Como fazer: [Criar sua primeira função no portal do Azure](../../azure-functions/functions-create-first-azure-function.md)
- Como fazer: [Receber emails quando o status do cofre de chaves secreto é alterado](../general/event-grid-logicapps.md)
- [Esquema de eventos da Grade de Eventos do Azure para o Azure Key Vault (versão prévia)](../../event-grid/event-schema-key-vault.md)
