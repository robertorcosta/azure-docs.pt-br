---
title: Criptografia em repouso com uma chave gerenciada pelo cliente
description: Saiba mais sobre a criptografia em repouso do registro de contêiner do Azure e como criptografar seu registro Premium com uma chave gerenciada pelo cliente armazenada no Azure Key Vault
ms.topic: article
ms.date: 05/01/2020
ms.custom: ''
ms.openlocfilehash: 393e51e687e95c1ff4c6a50429dd342005aad296
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84509535"
---
# <a name="encrypt-registry-using-a-customer-managed-key"></a>Criptografar o Registro usando uma chave gerenciada pelo cliente

Quando você armazena imagens e outros artefatos em um Registro de Contêiner do Azure, o Azure criptografa automaticamente o conteúdo do Registro em repouso com as [chaves gerenciadas pelo serviço](../security/fundamentals/encryption-atrest.md#data-encryption-models). Você pode complementar a criptografia padrão com uma camada de criptografia adicional usando uma chave criada e gerenciada por você no Azure Key Vault. Este artigo descreve as etapas necessárias usando a CLI do Azure e o portal do Azure.

Há suporte para a criptografia do servidor com as chaves gerenciadas pelo cliente por meio da integração ao [Azure Key Vault](../key-vault/general/overview.md). Você pode criar chaves de criptografia próprias e armazená-las em um cofre de chaves ou usar as APIs do Azure Key Vault para gerar chaves. Com o Azure Key Vault, você também pode auditar o uso das chaves.

Esse recurso está disponível na camada de serviço **Premium** do registro de contêiner. Para obter informações sobre os limites e as camadas de serviço do Registro, confira [Camadas de serviço do Registro de Contêiner do Azure](container-registry-skus.md).

   
## <a name="things-to-know"></a>Observações importantes

* Atualmente, você pode habilitar uma chave gerenciada pelo cliente somente quando cria um Registro.
* Depois de habilitar uma chave gerenciada pelo cliente em um Registro, você não poderá desabilitá-la.
* Atualmente, não há suporte para a [confiança no conteúdo](container-registry-content-trust.md) em um Registro criptografado com uma chave gerenciada pelo cliente.
* Em um Registro criptografado com uma chave gerenciada pelo cliente, os logs de execução das [Tarefas do ACR](container-registry-tasks-overview.md) são atualmente mantidos por apenas 24 horas. Caso precise manter os logs por um período mais longo, confira as diretrizes para [Exportar e armazenar os logs de execução de tarefas](container-registry-tasks-logs.md#alternative-log-storage).

## <a name="prerequisites"></a>Pré-requisitos

Para usar as etapas da CLI do Azure neste artigo, você precisará ter a CLI do Azure versão 2.2.0 ou posterior. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="enable-customer-managed-key---cli"></a>Habilitar a chave gerenciada pelo cliente – CLI

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Se necessário, execute o comando [az group create][az-group-create] para criar um grupo de recursos para criar o cofre de chaves, o registro de contêiner e outros recursos necessários.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerenciada atribuída ao usuário

Crie uma [identidade gerenciada atribuída ao usuário para os recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) com o comando [az identity create][az-identity-create]. Essa identidade será usada pelo Registro para acessar o serviço Key Vault.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name> 
```

Na saída do comando, anote os seguintes valores: `id` e `principalId`. Você precisará deles nas etapas posteriores para configurar o acesso ao registro ao cofre de chaves.

```JSON
{
  "clientId": "xxxx2bac-xxxx-xxxx-xxxx-192cxxxx6273",
  "clientSecretUrl": "https://control-eastus.identity.azure.net/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myidentityname/credentials?tid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&oid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&aid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myresourcegroup",
  "location": "eastus",
  "name": "myidentityname",
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Para sua conveniência, armazene esses valores em variáveis de ambiente:

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>Criar um cofre de chave

Crie um cofre de chaves com [az keyvault create][az-keyvault-create] para armazenar uma chave gerenciada pelo cliente para a criptografia do Registro. 

A fim de evitar a perda de dados causada por exclusões acidentais de chave ou de cofre de chaves, habilite as seguintes configurações: **Exclusão temporária** e **Proteção contra limpeza**. O seguinte exemplo inclui parâmetros para essas configurações: 

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-soft-delete \
  --enable-purge-protection
```

### <a name="add-key-vault-access-policy"></a>Adicionar uma política de acesso ao cofre de chaves

Configure uma política para o cofre de chaves, de modo que a identidade possa acessá-lo. No comando [az keyvault set-policy][az-keyvault-set-policy] a seguir, passe a ID da entidade de segurança da identidade gerenciada que você criou, armazenada anteriormente em uma variável de ambiente. Defina as permissões de chave como **get**, **unwrapKey** e **wrapKey**.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey 
```

### <a name="create-key-and-get-key-id"></a>Criar uma chave e obter a ID da chave

Execute o comando [az keyvault key create][az-keyvault-key-create] para criar uma chave no cofre de chaves.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

Na saída do comando, anote a ID da chave, `kid`. Você usará essa ID na próxima etapa:

```JSON
[...]
  "key": {
    "crv": null,
    "d": null,
    "dp": null,
    "dq": null,
    "e": "AQAB",
    "k": null,
    "keyOps": [
      "encrypt",
      "decrypt",
      "sign",
      "verify",
      "wrapKey",
      "unwrapKey"
    ],
    "kid": "https://mykeyvault.vault.azure.net/keys/mykey/xxxxxxxxxxxxxxxxxxxxxxxx",
    "kty": "RSA",
[...]
```
Para sua conveniência, armazene esse valor em uma variável de ambiente:

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>Criar um Registro com uma chave gerenciada pelo cliente

Execute o comando [az acr create][az-acr-create] para criar um Registro na camada de serviço Premium e habilitar a chave gerenciada pelo cliente. Passe a ID da entidade de segurança de identidade gerenciada e a ID da chave, armazenadas anteriormente em variáveis de ambiente:

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>Mostrar o status de criptografia

Para mostrar se a criptografia do Registro com uma chave gerenciada pelo cliente está habilitada, execute o comando [az acr encryption show][az-acr-encryption-show]:

```azurecli
az acr encryption show --name <registry-name> 
```

A saída é semelhante a:

```console
{
  "keyVaultProperties": {
    "identity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "keyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
    "versionedKeyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789..."
  },
  "status": "enabled"
}
```

## <a name="enable-customer-managed-key---portal"></a>Habilitar a chave gerenciada pelo cliente – portal

### <a name="create-a-managed-identity"></a>Criar uma identidade gerenciada

Crie uma [identidade gerenciada atribuída ao usuário para os recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) no portal do Azure. Para obter as etapas, confira [Criar uma identidade atribuída ao usuário](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

Você usará o nome da identidade nas etapas posteriores.

![Criar uma identidade gerenciada atribuída ao usuário no portal do Azure](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>Criar um cofre de chave

Para obter as etapas necessárias para criar um cofre de chaves, confira [Início Rápido: Definir e recuperar um segredo do Azure Key Vault usando o portal do Azure](../key-vault/secrets/quick-create-portal.md).

Ao criar um cofre de chaves para uma chave gerenciada pelo cliente, na guia **Informações Básicas**, habilite as seguintes configurações de proteção: **Exclusão temporária** e **Proteção contra limpeza**. Essas configurações ajudam a evitar a perda de dados causada por exclusões acidentais de chave ou de cofre de chaves.

![Criar um cofre de chaves no portal do Azure](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="add-key-vault-access-policy"></a>Adicionar uma política de acesso ao cofre de chaves

Configure uma política para o cofre de chaves, de modo que a identidade possa acessá-lo.

1. Navegue até o cofre de chaves.
1. Selecione **Configurações** > **Políticas de acesso > +Adicionar Política de Acesso**.
1. Selecione **Permissões de chave** e **Obter**, **Desencapsular Chave** e **Encapsular Chave**.
1. Escolha **Selecionar entidade de segurança** e o nome do recurso da identidade gerenciada atribuída ao usuário.  
1. Selecione **Adicionar** e **Salvar**.

![Criar uma política de acesso ao cofre de chaves](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

### <a name="create-key"></a>Chave Create

1. Navegue até o cofre de chaves.
1. Selecione **Configurações** > **Chaves**.
1. Selecione **+Gerar/Importar** e insira um nome exclusivo para a chave.
1. Aceite os valores padrão restantes e selecione **Criar**.
1. Após a criação, selecione a chave e anote a versão da chave atual.

### <a name="create-azure-container-registry"></a>Crie um registro de contêiner do Azure

1. Selecione **Criar um recurso** > **Contêineres** > **Registro de Contêiner**.
1. Na guia **Informações Básicas**, selecione ou crie um grupo de recursos e insira o nome do Registro. Em **SKU**, selecione **Premium**.
1. Na guia **Criptografia**, em **Chave gerenciada pelo cliente**, selecione **Habilitada**.
1. Em **Identidade**, escolha a identidade gerenciada que você criou.
1. Em **Criptografia**, escolha **Selecionar no Key Vault**.
1. Na janela **Selecionar chave no Azure Key Vault**, selecione o cofre de chaves, a chave e a versão que você criou na seção anterior.
1. Na guia **Criptografia**, escolha **Examinar + criar**.
1. Escolha **Criar** para implantar a instância do Registro.

![Criar registro de contêiner no portal do Azure](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

Para ver o status de criptografia do Registro no portal, navegue até o Registro. Em **Configurações**, selecione **Criptografia**.

## <a name="enable-customer-managed-key---template"></a>Habilitar a chave gerenciada pelo cliente – modelo

Use também um modelo do Resource Manager para criar um Registro e habilitar a criptografia com uma chave gerenciada pelo cliente. 

O modelo a seguir cria um registro de contêiner e uma identidade gerenciada atribuída ao usuário. Copie o conteúdo a seguir em um novo arquivo e salve-o usando um nome de arquivo como `CMKtemplate.json`.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vault_name": {
      "defaultValue": "",
      "type": "String"
    },
    "registry_name": {
      "defaultValue": "",
      "type": "String"
    },
    "identity_name": {
      "defaultValue": "",
      "type": "String"
    },
    "kek_id": {
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2019-12-01-preview",
      "name": "[parameters('registry_name')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Premium",
        "tier": "Premium"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]": {}
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "adminUserEnabled": false,
        "encryption": {
          "status": "enabled",
          "keyVaultProperties": {
            "identity": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').clientId]",
            "KeyIdentifier": "[parameters('kek_id')]"
          }
        },
        "networkRuleSet": {
          "defaultAction": "Allow",
          "virtualNetworkRules": [],
          "ipRules": []
        },
        "policies": {
          "quarantinePolicy": {
            "status": "disabled"
          },
          "trustPolicy": {
            "type": "Notary",
            "status": "disabled"
          },
          "retentionPolicy": {
            "days": 7,
            "status": "disabled"
          }
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "apiVersion": "2018-02-14",
      "name": "[concat(parameters('vault_name'), '/add')]",
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').principalId]",
            "permissions": {
              "keys": [
                "get",
                "unwrapKey",
                "wrapKey"
              ]
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "apiVersion": "2018-11-30",
      "name": "[parameters('identity_name')]",
      "location": "[resourceGroup().location]"
    }
  ]
}

```

Siga as etapas das seções anteriores para criar os seguintes recursos:

* Cofre de chaves, identificado pelo nome
* Chave do cofre de chaves, identificada pela ID da chave

Execute o comando [az group deployment create][az-group-deployment-create] a seguir para criar o Registro usando o arquivo de modelo anterior. Quando indicado, forneça um novo nome de Registro e um nome de identidade gerenciada, bem como o nome do cofre de chaves e a ID da chave que você criou. 

```bash
az group deployment create \
  --resource-group <resource-group-name> \
  --template-file CMKtemplate.json \
  --parameters \
    registry_name=<registry-name> \
    identity_name=<managed-identity> \
    vault_name=<key-vault-name> \
    kek_id=<key-vault-key-id>
```

### <a name="show-encryption-status"></a>Mostrar o status de criptografia

Para mostrar o status da criptografia do Registro, execute o comando [az acr encryption show][az-acr-encryption-show]:

```azurecli
az acr encryption show --name <registry-name> 
```

## <a name="use-the-registry"></a>Usar o Registro

Depois de habilitar uma chave gerenciada pelo cliente em um Registro, você pode executar as mesmas operações de Registro que executa em um Registro não criptografado com uma chave gerenciada pelo cliente. Por exemplo, você pode fazer a autenticação no Registro e efetuar push de imagens do Docker. Confira exemplos de comandos em [Efetuar push e pull de uma imagem](container-registry-get-started-docker-cli.md).

## <a name="rotate-key"></a>Girar a chave

Gire uma chave gerenciada pelo cliente usada para criptografia do Registro para as políticas de conformidade. Crie uma chave ou atualize uma versão de chave e atualize o Registro para criptografar os dados usando a chave. Execute essas etapas usando a CLI do Azure ou no portal.

Ao girar uma chave, normalmente, você especifica a mesma identidade usada ao criar o Registro. Opcionalmente, configure uma nova identidade atribuída ao usuário para o acesso à chave ou habilite e especifique a identidade atribuída ao sistema do registro.

> [!NOTE]
> Verifique se a [política de acesso do cofre de chaves](#add-key-vault-access-policy) obrigatória está definida para a identidade que você configurou para o acesso à chave. 

### <a name="azure-cli"></a>CLI do Azure

Use os comandos [az keyvault key][az-keyvault-key] para criar ou gerenciar suas chaves do cofre de chaves. Por exemplo, para criar uma versão da chave ou uma chave, execute o comando [az keyvault key create][az-keyvault-key-create]:

```azurecli
# Create new version of existing key
az keyvault key create \
  –-name <key-name> \
  --vault-name <key-vault-name> 

# Create new key
az keyvault key create \
  –-name <new-key-name> \
  --vault-name <key-vault-name> 
```

Em seguida, execute o comando [az acr encryption rotate-key][az-acr-encryption-rotate-key] passando a nova ID de chave e a identidade que deseja configurar:

```azurecli
# Rotate key and use user-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity <principal-id-user-assigned-identity>

# Rotate key and use system-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity [system]
```

### <a name="portal"></a>Portal

Use as configurações de **Criptografia** do Registro para atualizar a versão da chave, a chave, o cofre de chaves ou as configurações de identidade usadas para a chave gerenciada pelo cliente. 

Por exemplo, para gerar e configurar uma nova versão da chave:

1. No portal, navegue até o Registro. 
1. Em **Configurações**, selecione **Criptografia** > **Alterar chave**.
1. Escolha **Selecionar chave**
    
    ![Girar a chave no portal do Azure](./media/container-registry-customer-managed-keys/rotate-key.png)
1. Na janela **Selecionar chave no Azure Key Vault**, escolha o cofre de chaves e a chave que você configurou anteriormente e, em **Versão**, escolha **Criar**.
1. Na janela **Criar uma chave**, selecione **Gerar** e **Criar**.
1. Conclua a seleção de chave e escolha **Salvar**.

## <a name="revoke-key"></a>Revogar chave

Revogue a chave de criptografia gerenciada pelo cliente alterando a política de acesso no cofre de chaves ou excluindo a chave. Por exemplo, use o comando [az keyvault delete-policy][az-keyvault-delete-policy] para alterar a política de acesso da identidade gerenciada usada pelo Registro:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

A revogação da chave efetivamente bloqueia o acesso a todos os dados do Registro, já que o Registro não pode acessar a chave de criptografia. Se o acesso à chave estiver habilitado ou a chave excluída for restaurada, o registro selecionará a chave para que você possa acessar novamente os dados de registro criptografados.

## <a name="advanced-scenarios"></a>Cenários avançados

### <a name="system-assigned-identity"></a>Identidade atribuída pelo sistema

Configure a identidade gerenciada atribuída ao sistema de um Registro para acessar o cofre de chaves para as chaves de criptografia. Se você não estiver familiarizado com as diferentes identidades gerenciadas para recursos do Azure, confira a [visão geral](../active-directory/managed-identities-azure-resources/overview.md).

Para habilitar a identidade atribuída ao sistema do Registro no portal:

1. No portal, navegue até o Registro. 
1. Selecione **Configurações** >  **Identidade**.
1. Em **Atribuído ao sistema**, defina **Status** como **Ativado**. Clique em **Salvar**.
1. Copie a **ID de Objeto** da identidade.

Para conceder à identidade o acesso ao cofre de chaves:

1. Navegue até o cofre de chaves.
1. Selecione **Configurações** > **Políticas de acesso > +Adicionar Política de Acesso**.
1. Selecione **Permissões de chave** e **Obter**, **Desencapsular Chave** e **Encapsular Chave**.
1. Escolha **Selecionar entidade de segurança** e procure a ID de objeto da identidade gerenciada atribuída ao sistema ou o nome do Registro.  
1. Selecione **Adicionar** e **Salvar**.

Para atualizar as configurações de criptografia do Registro para usar a identidade:

1. No portal, navegue até o Registro. 
1. Em **Configurações**, selecione **Criptografia** > **Alterar chave**.
1. Em **Identidade**, selecione **Atribuído ao sistema** e **Salvar**.

### <a name="key-vault-firewall"></a>Firewall do Key Vault

Se o Azure Key Vault for implantado em uma rede virtual com um firewall do Key Vault, execute as seguintes etapas:

1. Configure a criptografia do Registro para usar a identidade atribuída ao sistema do Registro. Confira a seção anterior.
2. Configure o cofre de chaves para permitir o acesso por qualquer [serviço confiável](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services). 

Para obter etapas detalhadas, confira [Configurar as redes virtuais e os firewalls do Azure Key Vault](../key-vault/general/network-security.md). 

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [a criptografia em repouso no Azure](../security/fundamentals/encryption-atrest.md).
* Saiba mais sobre as políticas de acesso e como [proteger o acesso a um cofre de chaves](../key-vault/general/secure-your-key-vault.md).


<!-- LINKS - external -->

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-show]: /cli/azure/feature#az-feature-show
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[az-keyvault-create]: /cli/azure/keyvault#az-keyvault-create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az-keyvault-key-create
[az-keyvault-key]: /cli/azure/keyvault/key
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
