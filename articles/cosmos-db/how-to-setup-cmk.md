---
title: Configurar chaves gerenciadas pelo cliente para sua conta de Azure Cosmos DB
description: Saiba como configurar chaves gerenciadas pelo cliente para sua conta de Azure Cosmos DB com Azure Key Vault
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: thweiss
ROBOTS: noindex, nofollow
ms.openlocfilehash: 8f58887a056c8ca0cd175a44127556562338de38
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81450025"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Configurar chaves gerenciadas pelo cliente para sua conta do Azure cosmos com Azure Key Vault

> [!NOTE]
> Neste momento, você deve solicitar acesso para usar esse recurso. Para fazer isso, entre em [azurecosmosdbcmk@service.microsoft.com](mailto:azurecosmosdbcmk@service.microsoft.com)contato com.

Os dados armazenados em sua conta do Azure cosmos são criptografados de forma automática e direta com chaves gerenciadas pela Microsoft (**chaves gerenciadas pelo serviço**). Opcionalmente, você pode optar por adicionar uma segunda camada de criptografia com as chaves gerenciadas (**chaves gerenciadas pelo cliente**).

![Camadas de criptografia em relação aos dados do cliente](./media/how-to-setup-cmk/cmk-intro.png)

Você deve armazenar chaves gerenciadas pelo cliente no [Azure Key Vault](../key-vault/general/overview.md) e fornecer uma chave para cada conta do Azure cosmos que é habilitada com chaves gerenciadas pelo cliente. Essa chave é usada para criptografar todos os dados armazenados nessa conta.

> [!NOTE]
> Atualmente, as chaves gerenciadas pelo cliente estão disponíveis apenas para novas contas do Azure Cosmos. Você deve configurá-los durante a criação da conta.

## <a name="register-the-azure-cosmos-db-resource-provider-for-your-azure-subscription"></a><a id="register-resource-provider"></a>Registrar o provedor de recursos de Azure Cosmos DB para sua assinatura do Azure

1. Entre no [portal do Azure](https://portal.azure.com/), vá para sua assinatura do Azure e selecione provedores de **recursos** na guia **configurações** :

   ![Entrada de "provedores de recursos" no menu à esquerda](./media/how-to-setup-cmk/portal-rp.png)

1. Procure o provedor de recursos **Microsoft. DocumentDB** . Verifique se o provedor de recursos já está marcado como registrado. Caso contrário, escolha o provedor de recursos e selecione **registrar**:

   ![Registrando o provedor de recursos Microsoft. DocumentDB](./media/how-to-setup-cmk/portal-rp-register.png)

## <a name="configure-your-azure-key-vault-instance"></a>Configurar sua instância de Azure Key Vault

O uso de chaves gerenciadas pelo cliente com o Azure Cosmos DB exige que você defina duas propriedades na instância Azure Key Vault que você planeja usar para hospedar suas chaves de criptografia. Essas propriedades incluem **exclusão reversível** e **não são limpas**. Essas propriedades não são habilitadas por padrão. Você pode habilitá-los usando o PowerShell ou o CLI do Azure.

Para saber como habilitar essas propriedades em uma instância existente do Azure Key Vault, consulte as seções "Habilitando a exclusão reversível" e "Habilitando a proteção de limpeza" em um dos seguintes artigos:

- [Como usar a exclusão reversível com o PowerShell](../key-vault/general/soft-delete-powershell.md)
- [Como usar a exclusão reversível com CLI do Azure](../key-vault/general/soft-delete-cli.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Adicionar uma política de acesso à sua instância do Azure Key Vault

1. Na portal do Azure, vá para a instância de Azure Key Vault que você planeja usar para hospedar suas chaves de criptografia. Selecione **políticas de acesso** no menu à esquerda:

   !["Políticas de acesso" no menu à esquerda](./media/how-to-setup-cmk/portal-akv-ap.png)

1. Selecione **+ Adicionar política de acesso**.

1. No menu **suspenso permissões de chave** , selecione as permissões **obter**, **desencapsular chave**e **encapsular chave** :

   ![Selecionando as permissões corretas](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

1. Em **selecionar entidade de segurança**, selecione **nenhum selecionado**. Em seguida, pesquise **Azure Cosmos DB** entidade de segurança e selecione-a (para facilitar a localização, você também pode pesquisar por ID da `a232010e-820c-4083-83bb-3ace5fc29d0b` entidade: para qualquer região do Azure, exceto regiões do Azure governamental `57506a73-e302-42a9-b869-6f12d9ec29e9`em que a ID da entidade é). Por fim, escolha **selecionar** na parte inferior. Se a entidade de segurança de **Azure Cosmos DB** não estiver na lista, talvez seja necessário registrar novamente o provedor de recursos **Microsoft. DocumentDB** conforme descrito na seção [registrar o provedor de recursos](#register-resource-provider) deste artigo.

   ![Selecionar a entidade de Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-add-ap.png)

1. Selecione **Adicionar** para adicionar a nova política de acesso.

## <a name="generate-a-key-in-azure-key-vault"></a>Gerar uma chave no Azure Key Vault

1. No portal do Azure, acesse a instância Azure Key Vault que você planeja usar para hospedar suas chaves de criptografia. Em seguida, selecione **chaves** no menu à esquerda:

   ![Entrada "Keys" no menu à esquerda](./media/how-to-setup-cmk/portal-akv-keys.png)

1. Selecione **gerar/importar**, forneça um nome para a nova chave e selecione um tamanho de chave RSA. É recomendável um mínimo de 3072 para melhor segurança. Em seguida, selecione **criar**:

   ![Criar uma nova chave](./media/how-to-setup-cmk/portal-akv-gen.png)

1. Depois que a chave for criada, selecione a chave recém-criada e, em seguida, sua versão atual.

1. Copie o identificador de **chave**da chave, exceto a parte após a última barra de avanço:

   ![Copiando o identificador de chave da chave](./media/how-to-setup-cmk/portal-akv-keyid.png)

## <a name="create-a-new-azure-cosmos-account"></a>Criar uma nova conta do Azure Cosmos

### <a name="using-the-azure-portal"></a>Usando o portal do Azure

Ao criar uma nova conta de Azure Cosmos DB da portal do Azure, escolha **chave gerenciada pelo cliente** na etapa de **criptografia** . No campo **URI da chave** , Cole o identificador de URI/chave da chave de Azure Key Vault que você copiou da etapa anterior:

![Definindo parâmetros CMK no portal do Azure](./media/how-to-setup-cmk/portal-cosmos-enc.png)

### <a name="using-azure-powershell"></a>Usando o PowerShell do Azure

Quando você cria uma nova conta de Azure Cosmos DB com o PowerShell:

- Passe o URI da chave de Azure Key Vault copiada anteriormente na propriedade **keyVaultKeyUri** em **propertyobject**.

- Use **2019-12-12** como a versão da API.

> [!IMPORTANT]
> Você deve definir o `Location` parâmetro explicitamente para que a conta seja criada com êxito com as chaves gerenciadas pelo cliente.

```powershell
$resourceGroupName = "myResourceGroup"
$accountLocation = "West US 2"
$accountName = "mycosmosaccount"

$failoverLocations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 }
)

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$failoverLocations;
    "keyVaultKeyUri" = "https://<my-vault>.vault.azure.net/keys/<my-key>";
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2019-12-12" -ResourceGroupName $resourceGroupName `
    -Location $accountLocation -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a name="using-an-azure-resource-manager-template"></a>Usando um modelo do Azure Resource Manager

Quando você cria uma nova conta do Azure Cosmos por meio de um modelo de Azure Resource Manager:

- Passe o URI da chave de Azure Key Vault que você copiou anteriormente na propriedade **keyVaultKeyUri** no objeto **Properties** .

- Use **2019-12-12** como a versão da API.

> [!IMPORTANT]
> Você deve definir o `Location` parâmetro explicitamente para que a conta seja criada com êxito com as chaves gerenciadas pelo cliente.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "keyVaultKeyUri": {
            "type": "string"
        }
    },
    "resources": 
    [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[parameters('accountName')]",
            "apiVersion": "2019-12-12",
            "kind": "GlobalDocumentDB",
            "location": "[parameters('location')]",
            "properties": {
                "locations": [ 
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    }
                ],
                "databaseAccountOfferType": "Standard",
                "keyVaultKeyUri": "[parameters('keyVaultKeyUri')]"
            }
        }
    ]
}

```

Implante o modelo com o seguinte script do PowerShell:

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

### <a name="using-the-azure-cli"></a>Usando a CLI do Azure

Ao criar uma nova conta do Azure Cosmos por meio da CLI do Azure, passe o URI da chave de Azure Key Vault que você copiou anteriormente no parâmetro **--Key-URI** .

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
keyVaultKeyUri = 'https://<my-vault>.vault.azure.net/keys/<my-key>'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --key-uri $keyVaultKeyUri
```

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="is-there-any-additional-charge-for-using-customer-managed-keys"></a>Há algum custo adicional para usar chaves gerenciadas pelo cliente?

Sim. Para considerar a carga de computação adicional necessária para gerenciar a criptografia e a descriptografia de dados com chaves gerenciadas pelo cliente, todas as operações executadas na conta do Azure Cosmos consomem um aumento de 25% em [unidades de solicitação](./request-units.md).

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>Quais dados são criptografados com as chaves gerenciadas pelo cliente?

Todos os dados armazenados em sua conta do Azure cosmos são criptografados com as chaves gerenciadas pelo cliente, exceto para os seguintes metadados:

- Os nomes de suas contas de Azure Cosmos DB [, bancos de dados e contêineres](./account-overview.md#elements-in-an-azure-cosmos-account)

- Os nomes dos [procedimentos armazenados](./stored-procedures-triggers-udfs.md)

- Os caminhos de propriedade declarados em suas [políticas de indexação](./index-policy.md)

- Os valores das [chaves de partição](./partitioning-overview.md) de seus contêineres

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>As chaves gerenciadas pelo cliente têm suporte para contas existentes do Azure Cosmos?

Este recurso está disponível apenas para novas contas.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>Há um plano para dar suporte à granularidade mais fina do que as chaves de nível de conta?

Atualmente não, mas as chaves de nível de contêiner estão sendo consideradas.

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>Como as chaves gerenciadas pelo cliente afetam um backup?

Azure Cosmos DB usa [backups regulares e automáticos](./online-backup-and-restore.md) dos dados armazenados em sua conta. Esta operação faz backup dos dados criptografados. Para usar o backup restaurado, a chave de criptografia que você usou no momento do backup é necessária. Isso significa que nenhuma revogação foi feita e a versão da chave que foi usada no momento do backup ainda estará habilitada.

### <a name="how-do-i-revoke-an-encryption-key"></a>Como fazer revogar uma chave de criptografia?

A revogação de chave é feita desabilitando a versão mais recente da chave:

![Desabilitar a versão de uma chave](./media/how-to-setup-cmk/portal-akv-rev2.png)

Como alternativa, para revogar todas as chaves de uma instância de Azure Key Vault, você pode excluir a política de acesso concedida à entidade de Azure Cosmos DB:

![Excluindo a política de acesso para a entidade de Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Quais operações estão disponíveis depois que uma chave gerenciada pelo cliente é revogada?

A única operação possível quando a chave de criptografia foi revogada é a exclusão da conta.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a [criptografia de dados em Azure Cosmos DB](./database-encryption-at-rest.md).
- Obtenha uma visão geral de [acesso seguro aos dados no cosmos DB](secure-access-to-data.md).
