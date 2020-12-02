---
title: Tutorial de rotação para recursos com dois conjuntos de credenciais
description: Use este tutorial para aprender a automatizar a rotação de um segredo para recursos que usam dois conjuntos de credenciais de autenticação.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 06/22/2020
ms.author: jalichwa
ms.openlocfilehash: f208752f13848f0f54648d934d1dfb518e2ea1fd
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95500332"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-have-two-sets-of-authentication-credentials"></a>Automatizar a rotação de um segredo para recursos com dois conjuntos de credenciais de autenticação

A melhor maneira de se autenticar nos serviços do Azure é usando uma [identidade gerenciada](../general/authentication.md), mas há alguns cenários em que essa não é uma opção. Nesses casos, são usadas chaves de acesso ou senhas. Você deve fazer a rotação das chaves de acesso e senhas com frequência.

Este tutorial mostra como automatizar a rotação periódica de segredos para bancos de dados e serviços que usam dois conjuntos de credenciais de autenticação. Especificamente, este tutorial mostra como fazer a rotação das chaves de conta do Armazenamento do Azure armazenadas no Azure Key Vault como segredos. Você usará uma função disparada pela notificação da Grade de Eventos do Azure. 

> [!NOTE]
> As chaves da conta de armazenamento poderão ser gerenciadas automaticamente no Key Vault se você fornecer tokens de assinatura de acesso compartilhado para acesso delegado à conta de armazenamento. Há serviços que exigem cadeias de conexão de conta de armazenamento com chaves de acesso. Para esse cenário, recomendamos esta solução.

Aqui está a solução de rotação descrita neste tutorial: 

![Diagrama que mostra a solução de rotação.](../media/secrets/rotation-dual/rotation-diagram.png)

Nesta solução, o Azure Key Vault armazena as chaves de acesso individuais da conta de Armazenamento como versões do mesmo segredo, alternando entre a chave primária e a secundária nas versões subsequentes. Quando uma chave de acesso é armazenada na versão mais recente do segredo, a chave alternativa é regenerada e adicionada ao Key Vault como a nova versão mais recente do segredo. A solução fornece um ciclo de rotação inteiro do aplicativo a fim de fazer a atualização para a chave regenerada mais recente. 

1. Trinta dias antes da data de validade de um segredo, o Key Vault publica o evento de expiração próxima na Grade de Eventos.
1. A Grade de Eventos verifica as assinaturas de eventos e usa HTTP POST para chamar o ponto de extremidade do aplicativo de funções assinado para o evento.
1. O aplicativo de funções identifica a chave alternativa (não a mais recente) e chama a conta de armazenamento para regenerá-la.
1. O aplicativo de funções adiciona nova chave regenerada ao Azure Key Vault como uma nova versão do segredo.

## <a name="prerequisites"></a>Pré-requisitos
* Uma assinatura do Azure. [Crie uma gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Azure Key Vault.
* Duas contas de armazenamento do Azure.

Você poderá usar este link de implantação se não tiver um cofre de chaves existente e contas de armazenamento existentes:

[![Link rotulado como Fazer a implantação no Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json)

1. Em **Grupo de recursos**, selecione **Criar**. Dê ao grupo o nome **akvrotation** e selecione **Ok**.
1. Selecione **Examinar + criar**.
1. Selecione **Criar**.

    ![Captura de tela que mostra como criar um grupo de recursos.](../media/secrets/rotation-dual/dual-rotation-1.png)

Agora você terá um cofre de chaves e duas contas de armazenamento. Você pode verificar essa configuração na CLI do Azure executando o seguinte comando:

```azurecli
az resource list -o table -g akvrotation
```

Os resultados serão algo parecido com esta saída:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv         akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotationstorage     akvrotation      eastus      Microsoft.Storage/storageAccounts
akvrotationstorage2    akvrotation      eastus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-the-key-rotation-function"></a>Criar e implantar a função de rotação de chaves

Em seguida, você criará um aplicativo de funções com uma identidade gerenciada pelo sistema, além dos outros componentes necessários. Você também implantará a função de rotação para as chaves da conta de armazenamento.

A função de rotação do aplicativo de funções exige os seguintes componentes e a seguinte configuração:
- Um plano do Serviço de Aplicativo do Azure
- Uma conta de armazenamento para gerenciar gatilhos do aplicativo de funções
- Uma política de acesso para acessar segredos no Key Vault
- A função do serviço de operador da chave da conta de armazenamento atribuída ao aplicativo de funções para que ela possa acessar as chaves de acesso da conta de armazenamento
- Uma função de rotação de chaves com um gatilho de evento e um gatilho HTTP (rotação sob demanda)
- Uma assinatura de evento da Grade de Eventos para o evento **SecretNearExpiry**

1. Selecione o link de implantação de modelo do Azure: 

   [![Link de implantação de modelo do Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FFunction%2Fazuredeploy.json)

1. Na lista **Grupo de recursos**, selecione **akvrotation**.
1. Na caixa **GR da Conta de Armazenamento**, insira o nome do grupo de recursos em que sua conta de armazenamento está localizada. Mantenha o valor padrão **[resourceGroup().name]** se sua conta de armazenamento já estiver localizada no mesmo grupo de recursos em que você implantará a função de rotação de chaves.
1. Na caixa **Nome da Conta de Armazenamento**, insira o nome da conta de armazenamento que contém as chaves de acesso cuja rotação será feita.
1. Na caixa **GR do Cofre de Chaves**, insira o nome do grupo de recursos no qual seu cofre de chaves estará localizado. Mantenha o valor padrão **[resourceGroup().name]** se seu cofre de chaves já existir no mesmo grupo de recursos em que você implantará a função de rotação de chaves.
1. Na caixa **Nome do Cofre de Chaves**, insira o nome do cofre de chaves.
1. Na caixa **Nome do Aplicativo de Funções**, insira o nome do aplicativo de funções.
1. Na caixa **Nome do Segredo**, insira o nome do segredo em que você armazenará as chaves de acesso.
1. Na caixa **URL de Repositório**, insira o local do GitHub do código de função: **https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell.git** .
1. Selecione **Examinar + criar**.
1. Selecione **Criar**.

   ![Captura de tela que mostra como criar a primeira conta de armazenamento.](../media/secrets/rotation-dual/dual-rotation-2.png)

Depois de concluir as etapas anteriores, você terá uma conta de armazenamento, um farm de servidores, um aplicativo de funções e o Application Insights. Quando a implantação estiver concluída, você verá esta página: ![Captura de tela que mostra a página Sua implantação está concluída.](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> Se você encontrar uma falha, poderá selecionar **Reimplantar** para concluir a implantação dos componentes.


Você pode encontrar os modelos de implantação e o código para a função de rotação no [GitHub](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell).

## <a name="add-the-storage-account-access-keys-to-key-vault"></a>Adicionar chaves de acesso da conta de armazenamento ao Key Vault

Primeiro, defina sua política de acesso para conceder as permissões para **gerenciar segredos** a usuários:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

Agora você pode criar um segredo com uma chave de acesso da conta de armazenamento como valor. Você também precisará da ID do recurso da conta de armazenamento, do período de validade do segredo e da ID da chave a ser adicionada ao segredo para que a função de rotação possa regenerar a chave na conta de armazenamento.

Determine a ID do recurso da conta de armazenamento. É possível encontrar esse valor na propriedade `id`.
```azurecli
az storage account show -n akvrotationstorage
```

Liste as chaves de acesso da conta de armazenamento para que você possa obter os principais valores:

```azurecli
az storage account keys list -n akvrotationstorage 
```

Execute este comando, usando os valores recuperados para `key1Value` e `storageAccountResourceId`:

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name storageKey --vault-name akvrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Se você criar um segredo com uma data de validade curta, um evento `SecretNearExpiry` será publicado em vários minutos. Esse evento vai disparar a função para girar o segredo.

Você pode verificar se as chaves de acesso foram regeneradas recuperando a chave da conta de armazenamento e o segredo do Key Vault e comparando-os.

Use este comando para obter as informações secretas:
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey
```
Observe que a `CredentialId` é atualizada para o `keyName` alternativo e o `value` é regenerado: ![Captura de tela que mostra a saída do comando a z keyvault secret show para a primeira conta de armazenamento.](../media/secrets/rotation-dual/dual-rotation-4.png)

Recupere as chaves de acesso para comparar os valores:
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Captura de tela que mostra a saída do comando a z storage account keys list para a primeira conta de armazenamento.](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-storage-accounts-for-rotation"></a>Adicionar chaves de armazenamento para rotação

Você pode reutilizar o mesmo aplicativo de funções para fazer a rotação das chaves para várias contas de armazenamento. 

Para adicionar chaves da conta de armazenamento a uma função existente para rotação, você precisa:
- Da função do serviço de operador da chave da conta de armazenamento atribuída ao aplicativo de funções para que ela possa acessar as chaves de acesso da conta de armazenamento.
- De uma assinatura de evento da Grade de Eventos para o evento **SecretNearExpiry**.

1. Selecione o link de implantação de modelo do Azure: 

   [![Link de implantação de modelo do Azure.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FAdd-Event-Subscription%2Fazuredeploy.json)

1. Na lista **Grupo de recursos**, selecione **akvrotation**.
1. Na caixa **Nome da Conta de Armazenamento**, insira o nome da conta de armazenamento que contém as chaves de acesso cuja rotação será feita.
1. Na caixa **Nome do Cofre de Chaves**, insira o nome do cofre de chaves.
1. Na caixa **Nome do Aplicativo de Funções**, insira o nome do aplicativo de funções.
1. Na caixa **Nome do Segredo**, insira o nome do segredo em que você armazenará as chaves de acesso.
1. Selecione **Examinar + criar**.
1. Selecione **Criar**.

   ![Captura de tela que mostra como criar uma conta de armazenamento adicional.](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>Adicionar outra chave de acesso da conta de armazenamento ao Key Vault

Determine a ID do recurso da conta de armazenamento. É possível encontrar esse valor na propriedade `id`.
```azurecli
az storage account show -n akvrotationstorage2
```

Liste as chaves de acesso da conta de armazenamento para que você possa obter o valor key2:

```azurecli
az storage account keys list -n akvrotationstorage2 
```

Execute este comando, usando os valores recuperados para `key2Value` e `storageAccountResourceId`:

```azurecli
tomorrowDate=`date -d tomorrow -Iseconds -u | awk -F'+' '{print $1"Z"}'`
az keyvault secret set --name storageKey2 --vault-name akvrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Use este comando para obter as informações secretas:
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey2
```
Observe que a `CredentialId` é atualizada para o `keyName` alternativo e o `value` é regenerado: ![Captura de tela que mostra a saída do comando a z keyvault secret show para a segunda conta de armazenamento.](../media/secrets/rotation-dual/dual-rotation-8.png)

Recupere as chaves de acesso para comparar os valores:
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Captura de tela que mostra a saída do comando a z storage account keys list para a segunda conta de armazenamento.](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="key-vault-dual-credential-rotation-functions"></a>Funções de rotação de credenciais duplas do Key Vault

- [Conta de armazenamento](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell)
- [Cache Redis](https://github.com/jlichwa/KeyVault-Rotation-RedisCacheKey-PowerShell)

## <a name="next-steps"></a>Próximas etapas
- Visão geral: [Monitoramento do Key Vault com a Grade de Eventos do Azure](../general/event-grid-overview.md)
- Como fazer: [Criar sua primeira função no portal do Azure](../../azure-functions/functions-create-first-azure-function.md)
- Como [Receber emails quando um segredo do Key Vault for alterado](../general/event-grid-logicapps.md)
- Referência: [Esquema de eventos da Grade de Eventos do Azure para o Azure Key Vault](../../event-grid/event-schema-key-vault.md)
