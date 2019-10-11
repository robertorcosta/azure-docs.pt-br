---
title: Referências do Key Vault – Serviço de Aplicativo do Azure | Microsoft Docs
description: Referência conceitual e guia de configuração para referências do Azure Key Vault no Serviço de Aplicativo do Azure e nas Funções do Azure
services: app-service
author: mattchenderson
manager: jeconnoc
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/09/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 49bf7984efe74edd2a19909509e0c6b9564fc2e9
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274423"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions"></a>Usar referências de Key Vault para o serviço de aplicativo e Azure Functions

> [!NOTE] 
> Key Vault referências não estão disponíveis atualmente nos planos de consumo do Linux.

Este tópico mostra como trabalhar com segredos do Cofre de Chaves do Azure no seu aplicativo Serviço de Aplicativo ou no Aplicativo de Funções do Azure sem exigir alterações de código. [Azure Key Vault](../key-vault/key-vault-overview.md) é um serviço que fornece gerenciamento centralizado de segredos, com controle total sobre políticas de acesso e histórico de auditoria.

## <a name="granting-your-app-access-to-key-vault"></a>Concedendo o seu acesso ao aplicativo para o Google Key Vault

Para ler os segredos do Key Vault, você precisa criar um vault e conceder permissão ao aplicativo para acessá-lo.

1. Crie um cofre de chaves seguindo o início rápido do [ Key Vault ](../key-vault/quick-create-cli.md).

1. Crie uma [Identidade gerenciada designada pelo sistema](overview-managed-identity.md) para seu aplicativo.

   > [!NOTE] 
   > No momento, as referências do Key Vault suportam apenas identidades gerenciadas atribuídas pelo sistema. Identidades atribuídas pelo usuário não podem ser usadas.

1. Crie uma política de [acesso no Key Vault](../key-vault/key-vault-secure-your-key-vault.md#key-vault-access-policies) para a identidade do aplicativo que você criou anteriormente. Ative a permissão secreta "Obter" nesta política. Não defina o "aplicativo autorizado" ou as configurações `applicationId`, pois isso não é compatível com uma identidade gerenciada.

    > [!NOTE]
    > Key Vault referências não estão atualmente capazes de resolver segredos armazenados em um cofre de chaves com [restrições de rede](../key-vault/key-vault-overview-vnet-service-endpoints.md).

## <a name="reference-syntax"></a>Sintaxe de referência

Uma referência do Key Vault é da forma `@Microsoft.KeyVault({referenceString})`, em que `{referenceString}` é substituído por uma das seguintes opções:

> [!div class="mx-tdBreakAll"]
> | Cadeia de caracteres de referência                                                            | DESCRIÇÃO                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri = _secretUri_                                                       | O **SegredoUri** deve ser o URI do plano de dados completo de um segredo no Key Vault, incluindo uma versão, por exemplo, https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931  |
> | VaultName = _vaultName_; SecretName = _secretName_; SecretVersion = _secretVersion_ | O **VaultName** deve ser o nome do seu recurso Key Vault. O **SecretName** deve ser o nome do segredo de destino. O **SecretVersion** deve ser a versão do segredo a ser usado. |

> [!NOTE] 
> Atualmente, as versões são necessárias. Ao girar segredos, você precisará atualizar a versão na configuração do seu aplicativo.

Por exemplo, uma referência completa seria semelhante ao seguinte:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```

Como alternativa:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>Configurações de aplicativos de origem do cofre de chaves

As referências do Key Vault podem ser usadas como valores para [Configurações do aplicativo](configure-common.md#configure-app-settings), permitindo que você mantenha segredos no Key Vault em vez da configuração do site. As configurações do aplicativo são criptografadas com segurança em repouso, mas se você precisar de recursos de gerenciamento secreto, elas devem entrar no Key Vault.

Para usar uma referência do Key Vault para uma configuração de aplicativo, defina a referência como o valor da configuração. Seu aplicativo pode fazer referência ao segredo por meio de sua chave normalmente. Nenhuma alteração de código é necessária.

> [!TIP]
> A maioria das configurações de aplicativos que usam referências do Key Vault deve ser marcada como configurações de slot, pois você deve ter cofres separados para cada ambiente.

### <a name="azure-resource-manager-deployment"></a>Implantação do Azure Resource Manager

Ao automatizar implantações de recursos por meio dos modelos do Azure Resource Manager, talvez seja necessário sequenciar suas dependências em uma ordem específica para que esse recurso funcione. Observe que você precisará definir as configurações do aplicativo como seu próprio recurso, em vez de usar uma propriedade `siteConfig` na definição do site. Isso ocorre porque o site precisa ser definido primeiro para que a identidade atribuída pelo sistema seja criada com ele e possa ser usada na política de acesso.

Um exemplo de psuedo-template para um aplicativo de função pode ser semelhante ao seguinte:

```json
{
    //...
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            //...
        },
        {
            "type": "Microsoft.Insights/components",
            "name": "[variables('appInsightsName')]",
            //...
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[variables('functionAppName')]",
            "identity": {
                "type": "SystemAssigned"
            },
            //...
            "resources": [
                {
                    "type": "config",
                    "name": "appsettings",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('storageConnectionStringName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('appInsightsKeyName'))]"
                    ],
                    "properties": {
                        "AzureWebJobsStorage": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "APPINSIGHTS_INSTRUMENTATIONKEY": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('appInsightsKeyResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_ENABLE_SYNC_UPDATE_SITE": "true"
                        //...
                    }
                },
                {
                    "type": "sourcecontrols",
                    "name": "web",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.Web/sites/config', variables('functionAppName'), 'appsettings')]"
                    ],
                }
            ]
        },
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[variables('keyVaultName')]",
            //...
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]"
            ],
            "properties": {
                //...
                "accessPolicies": [
                    {
                        "tenantId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').tenantId]",
                        "objectId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').principalId]",
                        "permissions": {
                            "secrets": [ "get" ]
                        }
                    }
                ]
            },
            "resources": [
                {
                    "type": "secrets",
                    "name": "[variables('storageConnectionStringName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
                    ],
                    "properties": {
                        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountResourceId'),'2015-05-01-preview').key1)]"
                    }
                },
                {
                    "type": "secrets",
                    "name": "[variables('appInsightsKeyName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
                    ],
                    "properties": {
                        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
                    }
                }
            ]
        }
    ]
}
```

> [!NOTE] 
> Neste exemplo, a implantação do controle de origem depende das configurações do aplicativo. Esse comportamento normalmente é inseguro, pois a atualização da configuração do aplicativo se comporta de maneira assíncrona. No entanto, como incluímos a configuração do aplicativo `WEBSITE_ENABLE_SYNC_UPDATE_SITE`, a atualização é síncrona. Isso significa que a implantação do controle de origem só será iniciada quando as configurações do aplicativo tiverem sido totalmente atualizadas.

## <a name="troubleshooting-key-vault-references"></a>Solucionando problemas de referências de Key Vault

Se uma referência não for resolvida corretamente, o valor de referência será usado em seu lugar. Isso significa que, para as configurações do aplicativo, uma variável de ambiente seria criada cujo valor tem a sintaxe `@Microsoft.KeyVault(...)`. Isso pode fazer com que o aplicativo gere erros, pois estava esperando um segredo de uma determinada estrutura.

Normalmente, isso se deve a uma configuração incorreta da [política de acesso de Key Vault](#granting-your-app-access-to-key-vault). No entanto, também pode ser devido a um segredo não mais existente ou a um erro de sintaxe na própria referência.

Se a sintaxe estiver correta, você poderá exibir outras causas de erro verificando o status atual da resolução no Portal. Navegue até configurações do aplicativo e selecione "Editar" para a referência em questão. Abaixo da configuração de configuração, você deve ver informações de status, incluindo quaisquer erros. A ausência deles implica que a sintaxe de referência é inválida.

Você também pode usar um dos detectores internos para obter informações adicionais.

### <a name="using-the-detector-for-app-service"></a>Usando o detector para o serviço de aplicativo

1. No portal, navegue até seu aplicativo.
2. Selecione **diagnosticar e resolver problemas**.
3. Escolha **disponibilidade e desempenho** e selecione **aplicativo Web inativo.**
4. Encontre **Key Vault diagnóstico de configurações do aplicativo** e clique em **mais informações**.


### <a name="using-the-detector-for-azure-functions"></a>Usando o detector para Azure Functions

1. No portal, navegue até seu aplicativo.
2. Navegue até **recursos da plataforma.**
3. Selecione **diagnosticar e resolver problemas**.
4. Escolha **disponibilidade e desempenho** e selecione **aplicativo de funções ou relatando erros.**
5. Clique em **Key Vault configurações do aplicativo diagnósticos.**
