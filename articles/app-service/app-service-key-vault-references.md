---
title: Use referências do Key Vault
description: Saiba como configurar o Azure App Service e as funções do Azure para usar as referências do Azure Key Vault. Disponibilize os segredos do Key Vault para o código do seu aplicativo.
author: mattchenderson
ms.topic: article
ms.date: 10/09/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 7fdb7c980a278e2dcd4b64a4b70de50721d0b72a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280333"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions"></a>Use referências do Key Vault para serviço de aplicativo e funções do Azure

Este tópico mostra como trabalhar com segredos do Cofre de Chaves do Azure no seu aplicativo Serviço de Aplicativo ou no Aplicativo de Funções do Azure sem exigir alterações de código. [Azure Key Vault](../key-vault/key-vault-overview.md) é um serviço que fornece gerenciamento centralizado de segredos, com controle total sobre políticas de acesso e histórico de auditoria.

## <a name="granting-your-app-access-to-key-vault"></a>Concedendo o seu acesso ao aplicativo para o Google Key Vault

Para ler os segredos do Key Vault, você precisa criar um vault e conceder permissão ao aplicativo para acessá-lo.

1. Crie um cofre de chaves seguindo o início rápido do [ Key Vault ](../key-vault/quick-create-cli.md).

1. Crie uma [Identidade gerenciada designada pelo sistema](overview-managed-identity.md) para seu aplicativo.

   > [!NOTE] 
   > No momento, as referências do Key Vault suportam apenas identidades gerenciadas atribuídas pelo sistema. Identidades atribuídas pelo usuário não podem ser usadas.

1. Crie uma política de [acesso no Key Vault](../key-vault/key-vault-secure-your-key-vault.md#key-vault-access-policies) para a identidade do aplicativo que você criou anteriormente. Ative a permissão secreta "Obter" nesta política. Não defina o "aplicativo autorizado" ou as configurações `applicationId`, pois isso não é compatível com uma identidade gerenciada.

    > [!NOTE]
    > As referências do Key Vault não são atualmente capazes de resolver segredos armazenados em um cofre de chaves com [restrições de rede](../key-vault/key-vault-overview-vnet-service-endpoints.md).

## <a name="reference-syntax"></a>Sintaxe de referência

Uma referência do Key Vault é da forma `@Microsoft.KeyVault({referenceString})`, em que `{referenceString}` é substituído por uma das seguintes opções:

> [!div class="mx-tdBreakAll"]
> | Cadeia de caracteres de referência                                                            | Descrição                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri = _secretUri_                                                       | O **SegredoUri** deve ser o URI do plano de dados completo de um segredo no Key Vault, incluindo uma versão, por exemplo, https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931  |
> | VaultName = _vaultName_; SecretName = _secretName_; SecretVersion = _secretVersion_ | O **VaultName** deve ser o nome do seu recurso Key Vault. O **SecretName** deve ser o nome do segredo de destino. O **SecretVersion** deve ser a versão do segredo a ser usado. |

Por exemplo, uma referência completa com a Versão seria parecida com a seguinte:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```
Como alternativa:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>Configurações de aplicativos de origem do cofre de chaves

As referências do Key Vault podem ser usadas como valores para [configurações de aplicativos,](configure-common.md#configure-app-settings)permitindo que você mantenha segredos no Key Vault em vez da configuração do site. As configurações do aplicativo são criptografadas com segurança em repouso, mas se você precisar de recursos de gerenciamento secreto, eles devem ir para o Key Vault.

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

## <a name="troubleshooting-key-vault-references"></a>Solucionando referências do cofre de chaves

Se uma referência não for resolvida corretamente, o valor de referência será usado em vez disso. Isso significa que, para as configurações do aplicativo, `@Microsoft.KeyVault(...)` seria criada uma variável de ambiente cujo valor tenha a sintaxe. Isso pode fazer com que a aplicação jogue erros, pois esperava um segredo de uma certa estrutura.

Mais comumente, isso é devido a uma configuração errada da política de [acesso do Key Vault](#granting-your-app-access-to-key-vault). No entanto, também pode ser devido a um segredo que não existe mais ou a um erro de sintaxe na própria referência.

Se a sintaxe estiver correta, você pode visualizar outras causas de erro verificando o status de resolução atual no portal. Navegue até configurações de aplicativo e selecione "Editar" para a referência em questão. Abaixo da configuração de configuração, você deve ver informações de status, incluindo quaisquer erros. A ausência destes implica que a sintaxe de referência é inválida.

Você também pode usar um dos detectores embutidos para obter informações adicionais.

### <a name="using-the-detector-for-app-service"></a>Usando o detector para serviço de aplicativo

1. No portal, navegue até o seu aplicativo.
2. Selecione **Diagnóstico e solução de problemas**.
3. Escolha **Disponibilidade e Desempenho** e selecione o aplicativo da Web para **baixo.**
4. Encontre **o Diagnóstico de Configurações do Aplicativo do Cofre-Chave** e clique em Mais **informações**.


### <a name="using-the-detector-for-azure-functions"></a>Usando o detector para funções azure

1. No portal, navegue até o seu aplicativo.
2. Navegue até **os recursos da Plataforma.**
3. Selecione **Diagnóstico e solução de problemas**.
4. Escolha **Disponibilidade e Desempenho** e selecione **Função aplicativo para baixo ou emissão de erros de emissão de relatórios.**
5. Clique em **Diagnósticos de configurações do aplicativo do cofre-chave.**
