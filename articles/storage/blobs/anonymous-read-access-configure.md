---
title: Configurar acesso de leitura público anônimo para contêineres e blobs
titleSuffix: Azure Storage
description: Saiba como habilitar ou desabilitar o acesso anônimo a dados de BLOB para a conta de armazenamento. Defina a configuração de acesso público do contêiner para tornar contêineres e blobs disponíveis para acesso anônimo.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/29/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: af589874021baaf04a423b7bbaa0e36528eda93c
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209232"
---
# <a name="configure-anonymous-public-read-access-for-containers-and-blobs"></a>Configurar acesso de leitura público anônimo para contêineres e blobs

O armazenamento do Azure dá suporte ao acesso de leitura público anônimo para contêineres e blobs. Por padrão, todas as solicitações para um contêiner e seus BLOBs devem ser autorizadas usando o Azure Active Directory (Azure AD) ou a autorização de chave compartilhada. Quando você define a configuração de nível de acesso público de um contêiner para permitir acesso anônimo, os clientes podem ler dados nesse contêiner sem autorizar a solicitação.

> [!WARNING]
> Quando um contêiner é configurado para acesso público, qualquer cliente pode ler dados nesse contêiner. O acesso público apresenta um risco de segurança potencial, portanto, se o seu cenário não exigir, a Microsoft recomenda que você o desabilite para a conta de armazenamento. Para obter mais informações, consulte [impedir o acesso de leitura público anônimo a contêineres e blobs](anonymous-read-access-prevent.md).

Para configurar o acesso público para um contêiner, você deve executar duas etapas:

1. Habilitar acesso público para a conta de armazenamento
1. Definir a configuração de acesso público do contêiner

Este artigo descreve como configurar o acesso de leitura público anônimo para um contêiner e seus BLOBs. Para obter informações sobre como acessar dados de blob anonimamente de um aplicativo cliente, consulte [acessar contêineres públicos e blobs anonimamente com o .net](anonymous-read-access-client.md).

## <a name="enable-or-disable-public-read-access-for-a-storage-account"></a>Habilitar ou desabilitar o acesso de leitura público para uma conta de armazenamento

Por padrão, o acesso público está habilitado para uma conta de armazenamento. Desabilitar o acesso público impede todo acesso anônimo a contêineres e blobs nessa conta. Para maior segurança, a Microsoft recomenda que você desabilite o acesso público para suas contas de armazenamento, a menos que seu cenário exija que os usuários acessem os recursos de blob anonimamente.

> [!WARNING]
> Desabilitar o acesso público para uma conta de armazenamento substitui as configurações de acesso público para todos os contêineres nessa conta de armazenamento. Quando o acesso público estiver desabilitado para a conta de armazenamento, qualquer solicitação anônima futura para essa conta falhará.

Para habilitar ou desabilitar o acesso público para uma conta de armazenamento, use o portal do Azure ou CLI do Azure para configurar a propriedade **blobPublicAccess** da conta. Essa propriedade está disponível para todas as contas de armazenamento que são criadas com o modelo de implantação Azure Resource Manager. Para obter mais informações, consulte [visão geral da conta de armazenamento](../common/storage-account-overview.md).

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para habilitar ou desabilitar o acesso público para uma conta de armazenamento no portal do Azure, siga estas etapas:

1. Navegue até sua conta de armazenamento no portal do Azure.
1. Localize a definição de **configuração** em **configurações**.
1. Defina **acesso público de blob** como **desabilitado** ou **habilitado**.

    :::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="Captura de tela mostrando como habilitar ou desabilitar o acesso público de BLOB para a conta":::

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para habilitar ou desabilitar o acesso público para uma conta de armazenamento com CLI do Azure, primeiro obtenha a ID de recurso para sua conta de armazenamento chamando o comando [AZ Resource show](/cli/azure/resource#az-resource-show) . Em seguida, chame o comando [AZ Resource Update](/cli/azure/resource#az-resource-update) para definir a propriedade **allowBlobPublicAccess** para a conta de armazenamento. Para habilitar o acesso público, defina a propriedade **allowBlobPublicAccess** como true; para desabilitar, defina-o como **false**.

O exemplo a seguir desabilita o acesso de blob público para a conta de armazenamento. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```azurecli-interactive
storage_account_id=$(az resource show \
    --name anonpublicaccess \
    --resource-group storagesamples-rg \
    --resource-type Microsoft.Storage/storageAccounts \
    --query id \
    --output tsv)

az resource update \
    --ids $storage_account_id \
    --set properties.allowBlobPublicAccess=false
    ```
```

Para verificar se o acesso público está habilitado com CLI do Azure, chame o comando [AZ Resource show](/cli/azure/resource#az-resource-show) e consulte a propriedade **allowBlobPublicAccess** :

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowBlobPublicAccess \
    --output tsv
```

---

> [!NOTE]
> Desabilitar o acesso público para uma conta de armazenamento não afeta os sites estáticos hospedados nessa conta de armazenamento. O contêiner **$Web** sempre é acessível publicamente.

## <a name="check-the-public-access-setting-for-a-storage-account"></a>Verificar a configuração de acesso público para uma conta de armazenamento

Para verificar a configuração de acesso público de uma conta de armazenamento, obtenha o valor da propriedade **allowBlobPublicAccess** . Para verificar essa propriedade para um número grande de contas de armazenamento de uma só vez, use o Gerenciador de grafo de recursos do Azure.

A propriedade **allowBlobPublicAccess** não é definida por padrão e não retorna um valor até que você a defina explicitamente. A conta de armazenamento assume como padrão a permissão de acesso público quando o valor da propriedade é nulo.

### <a name="check-the-public-access-setting-for-a-single-storage-account"></a>Verificar a configuração de acesso público para uma única conta de armazenamento

Para verificar a configuração de acesso público para uma única conta de armazenamento usando CLI do Azure, chame o comando [AZ Resource show](/cli/azure/resource#az-resource-show) e a consulta para a propriedade **allowBlobPublicAccess** :

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowBlobPublicAccess \
    --output tsv
```

### <a name="check-the-public-access-setting-for-a-set-of-storage-accounts"></a>Verificar a configuração de acesso público para um conjunto de contas de armazenamento

Para verificar a configuração de acesso público em um conjunto de contas de armazenamento com o desempenho ideal, você pode usar o Gerenciador de grafo de recursos do Azure no portal do Azure. Para saber mais sobre como usar o Gerenciador de grafo de recursos, consulte [início rápido: executar sua primeira consulta de grafo de recursos usando o Gerenciador de grafo de recursos do Azure](/azure/governance/resource-graph/first-query-portal).

A execução da consulta a seguir no Gerenciador de grafo de recursos retorna uma lista de contas de armazenamento e exibe o valor da propriedade **allowBlobPublicAccess** para cada conta:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowBlobPublicAccess = parse_json(properties).allowBlobPublicAccess
| project subscriptionId, resourceGroup, name, allowBlobPublicAccess
| order by subscriptionId, resourceGroup, name asc
```

## <a name="set-the-public-access-level-for-a-container"></a>Definir o nível de acesso público para um contêiner

Para conceder aos usuários anônimos acesso de leitura a um contêiner e seus BLOBs, primeiro habilite o acesso público para a conta de armazenamento e, em seguida, defina o nível de acesso público do contêiner. Se o acesso público estiver desabilitado para a conta de armazenamento, você não poderá configurar o acesso público para um contêiner.

Quando o acesso público estiver habilitado para uma conta de armazenamento, você poderá configurar um contêiner com as seguintes permissões:

- **Sem acesso de leitura público:** O contêiner e seus BLOBs podem ser acessados somente com uma solicitação autorizada. Essa opção é o padrão para todos os novos contêineres.
- **Acesso de leitura público somente para BLOBs:** Os BLOBs dentro do contêiner podem ser lidos por solicitação anônima, mas os dados do contêiner não estão disponíveis anonimamente. Os clientes não podem enumerar os blobs no contêiner.
- **Acesso de leitura público para contêiner e seus BLOBs:** Os dados de contêiner e BLOB podem ser lidos por solicitação anônima, exceto para configurações de permissões de contêiner e metadados de contêiner. Os clientes podem enumerar os blobs no contêiner por meio solicitação anônima, mas não podem enumerar os contêineres na conta de armazenamento.

Você não pode alterar o nível de acesso público para um blob individual. O nível de acesso público é definido somente no nível de contêiner.

Para definir o nível de acesso público de um contêiner, use o portal do Azure ou CLI do Azure. Você pode definir o nível de acesso público do contêiner ao criar o contêiner ou atualizar essa configuração em um contêiner existente.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para atualizar o nível de acesso público para um ou mais contêineres existentes no portal do Azure, siga estas etapas:

1. Navegue até a visão geral da sua conta de armazenamento na portal do Azure.
1. Em **serviço blob** na folha do menu, selecione **contêineres**.
1. Selecione os contêineres para os quais você deseja definir o nível de acesso público.
1. Use o botão **alterar nível de acesso** para exibir as configurações de acesso público.
1. Selecione o nível de acesso público desejado na lista suspensa **nível de acesso público** e clique no botão OK para aplicar a alteração aos contêineres selecionados.

    ![Captura de tela mostrando como definir o nível de acesso público no portal](./media/anonymous-read-access-configure/configure-public-access-container.png)

Quando o acesso público está desabilitado para a conta de armazenamento, o nível de acesso público de um contêiner não pode ser definido. Se você tentar definir o nível de acesso público do contêiner, verá que a configuração está desabilitada porque o acesso público é proibido para a conta.

:::image type="content" source="media/anonymous-read-access-configure/container-public-access-blocked.png" alt-text="Captura de tela mostrando que definir o nível de acesso público do contêiner é bloqueado quando o acesso público está desabilitado":::

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para atualizar o nível de acesso público para um ou mais contêineres com CLI do Azure, chame o comando [AZ Storage container Set Permission](/cli/azure/storage/container#az-storage-container-set-permission) . Autorize essa operação passando sua chave de conta, uma cadeia de conexão ou uma assinatura de acesso compartilhado (SAS). A operação [definir ACL de contêiner](/rest/api/storageservices/set-container-acl) que define o nível de acesso público do contêiner não oferece suporte à autorização com o Azure AD. Para obter mais informações, consulte [permissões para chamar operações de BLOB e de dados de fila](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

O exemplo a seguir define a configuração de acesso público para um contêiner para habilitar o acesso anônimo ao contêiner e a seus BLOBs. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```azurecli-interactive
az storage container set-permission \
    --name <container-name> \
    --account-name <account-name> \
    --public-access container \
    --account-key <account-key> \
    --auth-mode key
```

Quando o acesso público está desabilitado para a conta de armazenamento, o nível de acesso público de um contêiner não pode ser definido. Se você tentar definir o nível de acesso público do contêiner, ocorrerá um erro indicando que o acesso público não é permitido na conta de armazenamento.

---

## <a name="check-the-container-public-access-setting"></a>Verificar a configuração de acesso público do contêiner

Para verificar a configuração de acesso público de um ou mais contêineres, você pode usar o portal do Azure, o PowerShell CLI do Azure, uma das bibliotecas de cliente de armazenamento do Azure ou o provedor de recursos de armazenamento do Azure. As seções a seguir oferecem alguns exemplos.  

### <a name="check-the-public-access-setting-for-a-single-container"></a>Verificar a configuração de acesso público para um único contêiner

Para obter o nível de acesso público para um ou mais contêineres com CLI do Azure, chame o comando [AZ Storage container show Permission](/cli/azure/storage/container#az-storage-container-show-permission) . Autorize essa operação passando sua chave de conta, uma cadeia de conexão ou uma assinatura de acesso compartilhado (SAS). A operação [obter ACL de contêiner](/rest/api/storageservices/get-container-acl) que retorna o nível de acesso público de um contêiner não oferece suporte à autorização com o Azure AD. Para obter mais informações, consulte [permissões para chamar operações de BLOB e de dados de fila](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

O exemplo a seguir lê a configuração de acesso público para um contêiner. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```azurecli-interactive
az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key>
    --auth-mode key
```

### <a name="check-the-public-access-setting-for-a-set-of-containers"></a>Verificar a configuração de acesso público para um conjunto de contêineres

É possível verificar quais contêineres em uma ou mais contas de armazenamento estão configuradas para acesso público, listando os contêineres e verificando a configuração de acesso público. Essa abordagem é uma opção prática quando uma conta de armazenamento não contém um grande número de contêineres ou quando você está verificando a configuração em um pequeno número de contas de armazenamento. No entanto, o desempenho poderá ser prejudicado se você tentar enumerar um grande número de contêineres.

O exemplo a seguir usa o PowerShell para obter a configuração de acesso público para todos os contêineres em uma conta de armazenamento. Lembre-se de substituir os valores de espaço reservado entre colchetes por seus próprios valores:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Get-AzStorageContainer -Context $ctx | Select Name, PublicAccess
```

## <a name="next-steps"></a>Próximas etapas

- [Impedir acesso de leitura público anônimo a contêineres e blobs](anonymous-read-access-prevent.md)
- [Acesse contêineres públicos e blobs anonimamente com o .NET](anonymous-read-access-client.md)
- [Autorizar acesso ao Armazenamento do Microsoft Azure](../common/storage-auth.md)