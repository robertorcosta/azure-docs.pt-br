---
title: Configure chaves gerenciadas pelo cliente para sua conta Azure Cosmos DB
description: Saiba como configurar chaves gerenciadas pelo cliente para sua conta Azure Cosmos DB com o Azure Key Vault
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: thweiss
ROBOTS: noindex, nofollow
ms.openlocfilehash: 6e2a90b8f81b9b945905ee98beb1686c54a62e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063749"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Configure chaves gerenciadas pelo cliente para sua conta Azure Cosmos com o Azure Key Vault

> [!NOTE]
> Neste momento, você deve solicitar acesso para usar esse recurso. Para isso, entre [azurecosmosdbcmk@service.microsoft.com](mailto:azurecosmosdbcmk@service.microsoft.com)em contato.

Os dados armazenados em sua conta do Azure Cosmos são criptografados automaticamente e perfeitamente com chaves gerenciadas pela Microsoft **(chaves gerenciadas por serviço).** Opcionalmente, você pode optar por adicionar uma segunda camada de criptografia com chaves que você gerencia **(chaves gerenciadas pelo cliente).**

![Camadas de criptografia em torno dos dados dos clientes](./media/how-to-setup-cmk/cmk-intro.png)

Você deve armazenar chaves gerenciadas pelo cliente no [Azure Key Vault](../key-vault/key-vault-overview.md) e fornecer uma chave para cada conta do Azure Cosmos habilitada com chaves gerenciadas pelo cliente. Esta chave é usada para criptografar todos os dados armazenados nessa conta.

> [!NOTE]
> Atualmente, as chaves gerenciadas pelo cliente estão disponíveis apenas para novas contas do Azure Cosmos. Você deve configurá-los durante a criação da conta.

## <a name="register-the-azure-cosmos-db-resource-provider-for-your-azure-subscription"></a><a id="register-resource-provider"></a>Registre o provedor de recursos Azure Cosmos DB para sua assinatura do Azure

1. Faça login no [portal Azure,](https://portal.azure.com/)vá para sua assinatura do Azure e selecione **provedores de recursos** na guia **Configurações:**

   ![Entrada "Provedores de recursos" no menu esquerdo](./media/how-to-setup-cmk/portal-rp.png)

1. Procure o provedor de recursos **Microsoft.DocumentDB.** Verifique se o provedor de recursos já está marcado como registrado. Caso não, escolha o provedor de recursos e selecione **Registrar**:

   ![Registrando o provedor de recursos Microsoft.DocumentDB](./media/how-to-setup-cmk/portal-rp-register.png)

## <a name="configure-your-azure-key-vault-instance"></a>Configure a instância do Cofre de Chaves do Azure

O uso de chaves gerenciadas pelo cliente com o Azure Cosmos DB exige que você defina duas propriedades na instância do Azure Key Vault que você planeja usar para hospedar suas chaves de criptografia. Essas propriedades incluem **Soft Delete** e **Não Purgar**. Essas propriedades não são habilitadas por padrão. Você pode habilitá-los usando o PowerShell ou o Azure CLI.

Para saber como habilitar essas propriedades em uma instância existente do Azure Key Vault, consulte as seções "Habilitando a exclusão suave" e "Habilitando a proteção contra expurgo" em um dos seguintes artigos:

- [Como usar soft-delete com powershell](../key-vault/key-vault-soft-delete-powershell.md)
- [Como usar soft-delete com o Azure CLI](../key-vault/key-vault-soft-delete-cli.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Adicione uma política de acesso à sua instância do Azure Key Vault

1. A partir do portal Azure, vá até a instância do Azure Key Vault que você planeja usar para hospedar suas chaves de criptografia. Selecione Políticas de **acesso** no menu esquerdo:

   !["Políticas de acesso" no menu esquerdo](./media/how-to-setup-cmk/portal-akv-ap.png)

1. Selecione **+ Adicione a Política de Acesso**.

1. No menu **suspenso das permissões de tecla,** **selecione Obter,** **Desembrulhar a chave**e encerrar as permissões da **chave:**

   ![Selecionando as permissões certas](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

1. Em **Select principal,** selecione **Nenhum selecionado**. Em seguida, procure **o diretor do Azure Cosmos DB** e selecione-o (para `a232010e-820c-4083-83bb-3ace5fc29d0b` facilitar a descoberta, você também pode pesquisar por iD principal: para qualquer região do Azure, exceto regiões do Governo Azure onde está `57506a73-e302-42a9-b869-6f12d9ec29e9`o ID principal ). Por fim, escolha **Selecionar** na parte inferior. Se o **principal do Azure Cosmos DB** não estiver na lista, talvez seja necessário reregistrar o provedor de recursos **Microsoft.DocumentDB** conforme descrito na seção [Registrar o provedor](#register-resource-provider) de recursos deste artigo.

   ![Selecione o principal do Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-add-ap.png)

1. Selecione **Adicionar** para adicionar a nova política de acesso.

## <a name="generate-a-key-in-azure-key-vault"></a>Gerar uma chave no Azure Key Vault

1. A partir do portal Azure, vá a instância do Azure Key Vault que você planeja usar para hospedar suas chaves de criptografia. Em seguida, **selecione Chaves** no menu esquerdo:

   ![Entrada "Chaves" no menu esquerdo](./media/how-to-setup-cmk/portal-akv-keys.png)

1. Selecione **Gerar/Importar,** forneça um nome para a nova tecla e selecione um tamanho de chave RSA. Recomenda-se um mínimo de 3072 para obter a melhor segurança. Em seguida, **selecione Criar**:

   ![Criar uma nova chave](./media/how-to-setup-cmk/portal-akv-gen.png)

1. Depois que a chave for criada, selecione a tecla recém-criada e, em seguida, sua versão atual.

1. Copie o **identificador**de chave da chave, exceto a parte após a última barra para frente:

   ![Copiando o identificador de chave da chave](./media/how-to-setup-cmk/portal-akv-keyid.png)

## <a name="create-a-new-azure-cosmos-account"></a>Crie uma nova conta do Azure Cosmos

### <a name="using-the-azure-portal"></a>Usando o portal do Azure

Quando você criar uma nova conta Azure Cosmos DB no portal Azure, escolha a **chave gerenciada pelo cliente** na etapa **Criptografia.** No campo **URI da chave,** cole o identificador URI/key da chave Azure Key Vault que você copiou da etapa anterior:

![Definindo parâmetros CMK no portal Azure](./media/how-to-setup-cmk/portal-cosmos-enc.png)

### <a name="using-azure-powershell"></a>Usando o PowerShell do Azure

Quando você cria uma nova conta do Azure Cosmos DB com o PowerShell:

- Passe o URI da chave do Azure Key Vault copiado anteriormente a **propriedade keyVaultKeyUri** no **PropertyObject**.

- Use **2019-12-12** como a versão da API.

> [!IMPORTANT]
> Você deve `Location` definir o parâmetro explicitamente para que a conta seja criada com sucesso com chaves gerenciadas pelo cliente.

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

Quando você cria uma nova conta do Azure Cosmos através de um modelo do Azure Resource Manager:

- Passe o URI da chave Azure Key Vault que você copiou anteriormente a **propriedade keyVaultKeyUri** no objeto **propriedades.**

- Use **2019-12-12** como a versão da API.

> [!IMPORTANT]
> Você deve `Location` definir o parâmetro explicitamente para que a conta seja criada com sucesso com chaves gerenciadas pelo cliente.

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

Implante o modelo com o seguinte script PowerShell:

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

Quando você criar uma nova conta do Azure Cosmos através da CLI do Azure, passe o URI da chave Do Cofre chave do Azure que você copiou anteriormente o parâmetro **--key-uri.**

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

### <a name="is-there-any-additional-charge-for-using-customer-managed-keys"></a>Há alguma taxa adicional para o uso de chaves gerenciadas pelo cliente?

Sim. Para contabilizar a carga de computação adicional necessária para gerenciar a criptografia de dados e a descriptografia com chaves gerenciadas pelo cliente, todas as operações executadas contra a conta do Azure Cosmos consomem um aumento de 25% nas [Unidades de Solicitação](./request-units.md).

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>Quais dados são criptografados com as chaves gerenciadas pelo cliente?

Todos os dados armazenados em sua conta do Azure Cosmos são criptografados com as chaves gerenciadas pelo cliente, exceto os seguintes metadados:

- Os nomes de suas [contas, bancos](./account-overview.md#elements-in-an-azure-cosmos-account) de dados e contêineres do Azure Cosmos DB

- Os nomes de seus [procedimentos armazenados](./stored-procedures-triggers-udfs.md)

- Os caminhos de propriedade declarados em suas [políticas de indexação](./index-policy.md)

- Os valores das chaves de [partição](./partitioning-overview.md) de seus contêineres

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>As chaves gerenciadas pelo cliente são suportadas para contas existentes do Azure Cosmos?

Este recurso está disponível atualmente apenas para novas contas.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>Existe um plano para suportar granularidade mais fina do que chaves de nível de conta?

Não no momento, mas as chaves do nível de contêiner estão sendo consideradas.

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>Como as chaves gerenciadas pelo cliente afetam um backup?

O Azure Cosmos DB faz [backups regulares e automáticos](../synapse-analytics/sql-data-warehouse/backup-and-restore.md) dos dados armazenados em sua conta. Esta operação faz backup dos dados criptografados. Para usar o backup restaurado, a chave de criptografia usada no momento do backup é necessária. Isso significa que nenhuma revogação foi feita e a versão da chave que foi usada no momento do backup ainda será ativada.

### <a name="how-do-i-revoke-an-encryption-key"></a>Como revogo uma chave de criptografia?

A revogação da chave é feita desativando a versão mais recente da chave:

![Desativar a versão de uma chave](./media/how-to-setup-cmk/portal-akv-rev2.png)

Alternativamente, para revogar todas as chaves de uma instância do Azure Key Vault, você pode excluir a política de acesso concedida ao principal do Azure Cosmos DB:

![Excluindo a política de acesso para o principal do Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Quais operações estão disponíveis depois que uma chave gerenciada pelo cliente é revogada?

A única operação possível quando a chave de criptografia foi revogada é a exclusão da conta.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [criptografia de dados no Azure Cosmos DB](./database-encryption-at-rest.md).
- Obtenha uma visão geral do [acesso seguro aos dados no Cosmos DB](secure-access-to-data.md).
