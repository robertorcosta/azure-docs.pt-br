---
title: Configure sua própria chave para criptografar dados do Azure Event Hubs em repouso
description: Este artigo fornece informações sobre como configurar sua própria chave para criptografar o descanso de dados do Azure Event Hubs.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: spelluru
ms.openlocfilehash: 43e626355feaf1e51fc840f82506c559a1859b84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77622002"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Configure chaves gerenciadas pelo cliente para criptografar dados do Azure Event Hubs em repouso usando o portal Azure
O Azure Event Hubs fornece criptografia de dados em repouso com o Azure Storage Service Encryption (Azure SSE). O Event Hubs conta com o Azure Storage para armazenar os dados e, por padrão, todos os dados armazenados com o Azure Storage são criptografados usando chaves gerenciadas pela Microsoft. 

## <a name="overview"></a>Visão geral
O Azure Event Hubs agora suporta a opção de criptografar dados em repouso com chaves gerenciadas pela Microsoft ou chaves gerenciadas pelo cliente (Bring Your Own Key – BYOK). Esse recurso permite que você crie, gire, desative e revogue o acesso às chaves gerenciadas pelo cliente que são usadas para criptografar dados do Azure Event Hubs em repouso.

Ativar o recurso BYOK é um processo de configuração única no seu namespace.

> [!NOTE]
> O recurso BYOK é suportado por [clusters dedicados a um único inquilino do Event Hubs.](event-hubs-dedicated-overview.md) Ele não pode ser habilitado para espaços de nomes padrão do Event Hubs.

Você pode usar o Azure Key Vault para gerenciar suas chaves e auditar seu uso de chaves. Você pode criar suas próprias chaves e armazená-las em um cofre de chaves, ou pode usar as APIs do Azure Key Vault para gerar chaves. Para obter mais informações sobre o Azure Key Vault, consulte [O que é o Azure Key Vault?](../key-vault/key-vault-overview.md)

Este artigo mostra como configurar um cofre de chaves com chaves gerenciadas pelo cliente usando o portal Azure. Para aprender como criar um cofre de chaves usando o portal Azure, consulte [Quickstart: Configure e recupere um segredo do Azure Key Vault usando o portal Azure](../key-vault/quick-create-portal.md).

> [!IMPORTANT]
> O uso de chaves gerenciadas pelo cliente com o Azure Event Hubs requer que o cofre-chave tenha duas propriedades necessárias configuradas. São eles: **Soft Delete** e **Não Purga .** Essas propriedades são habilitadas por padrão quando você cria um novo cofre de chaves no portal Azure. No entanto, se você precisar habilitar essas propriedades em um cofre de chaves existente, você deve usar o PowerShell ou o Azure CLI.

## <a name="enable-customer-managed-keys"></a>Habilitar chaves gerenciadas pelo cliente
Para habilitar as chaves gerenciadas pelo cliente no portal Azure, siga estas etapas:

1. Navegue até o cluster dedicado do Event Hubs.
1. Selecione o namespace no qual deseja ativar byok.
1. Na página **Configurações** do namespace do Event Hubs, selecione **Criptografia**. 
1. Selecione a **criptografia de chave gerenciada pelo cliente em repouso,** conforme mostrado na imagem a seguir. 

    ![Habilitar a chave gerenciada pelo cliente](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Configure um cofre de chaves com chaves
Depois de habilitar as chaves gerenciadas pelo cliente, você precisa associar a chave gerenciada pelo cliente com o namespace do Azure Event Hubs. Event Hubs suporta apenas o Azure Key Vault. Se você habilitar a criptografia com a opção **de chave gerenciada pelo cliente** na seção anterior, você precisará ter a chave importada para o Azure Key Vault. Além disso, as teclas devem ter **Soft Delete** e **Não Purgar** configurados para a chave. Essas configurações podem ser configuradas usando [PowerShell](../key-vault/key-vault-soft-delete-powershell.md) ou [CLI](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection).

1. Para criar um novo cofre de chaves, siga o Azure Key Vault [Quickstart](../key-vault/key-vault-overview.md). Para obter mais informações sobre a importação de chaves existentes, consulte [Sobre chaves, segredos e certificados](../key-vault/about-keys-secrets-and-certificates.md).
1. Para ativar a proteção de exclusão suave e purga ao criar um cofre, use o comando [az keyvault create.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Para adicionar proteção contra purga a um cofre existente (que já tem o soft delete ativado), use o comando [az keyvault update.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update)

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Crie chaves seguindo estas etapas:
    1. Para criar uma nova chave, selecione **Gerar/Importar** do menu **Chaves** em **Configurações**.
        
        ![Selecione o botão Gerar/Importar](./media/configure-customer-managed-key/select-generate-import.png)
    1. Defina **Opções** para **Gerar** e forneça um nome à chave.

        ![Criar uma chave](./media/configure-customer-managed-key/create-key.png) 
    1. Agora você pode selecionar essa chave para associar-se ao namespace do Event Hubs para criptografar na lista de itens abaixo. 

        ![Selecione a chave do cofre de chaves](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. Preencha os detalhes da tecla e clique **em Selecionar**. Isso permitirá a criptografia de dados em repouso no namespace com uma chave gerenciada pelo cliente. 


## <a name="rotate-your-encryption-keys"></a>Gire suas chaves de criptografia
Você pode girar sua chave no cofre da chave usando o mecanismo de rotação azure Key Vaults. Para obter mais informações, consulte [Configurar rotação e auditoria de chaves](../key-vault/key-vault-key-rotation-log-monitoring.md). As datas de ativação e expiração também podem ser definidas para automatizar a rotação de chaves. O serviço Event Hubs detectará novas versões-chave e começará a usá-las automaticamente.

## <a name="revoke-access-to-keys"></a>Revogar o acesso às chaves
A revogação do acesso às chaves de criptografia não limpará os dados dos Hubs de eventos. No entanto, os dados não podem ser acessados a partir do namespace do Event Hubs. Você pode revogar a chave de criptografia através da política de acesso ou excluindo a chave. Saiba mais sobre políticas de acesso e proteção do cofre de chaves do [acesso seguro a um cofre-chave](../key-vault/key-vault-secure-your-key-vault.md).

Uma vez que a chave de criptografia seja revogada, o serviço Event Hubs no namespace criptografado se tornará inoperável. Se o acesso à chave estiver ativado ou a chave de exclusão for restaurada, o serviço Event Hubs escolherá a chave para que você possa acessar os dados do namespace do Event Hubs criptografado.

## <a name="set-up-diagnostic-logs"></a>Configuração dos logs de diagnóstico 
A definição de registros de diagnóstico para namespaces habilitados para BYOK fornece as informações necessárias sobre as operações quando um namespace é criptografado com chaves gerenciadas pelo cliente. Esses logs podem ser habilitados e posteriormente transmitidos para um hub de eventos ou analisados através de análises de log ou transmitidos para armazenamento para executar análises personalizadas. Para saber mais sobre os registros de diagnóstico, consulte [Visão geral dos registros de diagnóstico do Azure](../azure-monitor/platform/platform-logs-overview.md).

## <a name="enable-user-logs"></a>Habilite os logs de usuário
Siga essas etapas para habilitar logs para chaves gerenciadas pelo cliente.

1. No portal Azure, navegue até o namespace que tem BYOK ativado.
1. Selecione **as configurações de diagnóstico** em **Monitoramento**.

    ![Selecione as configurações de diagnóstico](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. Selecione **+Adicionar configuração de diagnóstico**. 

    ![Selecione adicionar configuração de diagnóstico](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. Forneça um **nome** e selecione para onde deseja transmitir os logs.
1. Selecione **O ClienteGerenciado-ChaveUsuárioLogs** e **Salvar**. Esta ação permite os logs de BYOK no namespace.

    ![Selecione a opção de logs de usuário-chave gerenciados pelo cliente](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>Esquema do log 
Todos os logs são armazenados no formato JSON (JavaScript Object Notation). Cada entrada tem campos de string que usam o formato descrito na tabela a seguir. 

| Nome | Descrição |
| ---- | ----------- | 
| TaskName | Descrição da tarefa que falhou. |
| ActivityId | ID interno que é usado para rastreamento. |
| category | Define a classificação da tarefa. Por exemplo, se a chave do cofre da chave estiver sendo desativada, então seria uma categoria de informações ou se uma chave não pode ser desembrulhada, ela pode cair em erro. |
| resourceId | ID de recurso do Azure Resource Manager |
| keyVault | Nome completo do cofre das chaves. |
| chave | O nome-chave usado para criptografar o namespace do Event Hubs. |
| version | A versão da chave que está sendo usada. |
| operação | A operação que é realizada na chave do seu cofre. Por exemplo, desativar/ativar a chave, embrulhar ou desembrulhar |
| código | O código que está associado com a operação. Exemplo: Código de erro, 404 significa que a chave não foi encontrada. |
| message | Qualquer mensagem de erro associada à operação |

Aqui está um exemplo do registro de uma chave gerenciada pelo cliente:

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

## <a name="use-resource-manager-template-to-enable-encryption"></a>Use o modelo do Gerenciador de recursos para habilitar a criptografia
Esta seção mostra como fazer as seguintes tarefas usando **modelos do Azure Resource Manager**. 

1. Crie um **namespace do Event Hubs** com uma identidade de serviço gerenciada.
2. Crie um **cofre chave** e conceda ao usuário de identidade de serviço acesso ao cofre principal. 
3. Atualize o namespace do Event Hubs com as informações do cofre chave (chave/valor). 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>Crie um cluster de hubs de eventos e namespace com identidade de serviço gerenciada
Esta seção mostra como criar um namespace do Azure Event Hubs com identidade de serviço gerenciada usando um modelo do Azure Resource Manager e o PowerShell. 

1. Crie um modelo de Gerenciador de recursos do Azure para criar um namespace do Event Hubs com uma identidade de serviço gerenciada. Nomeie o arquivo: **CreateEventHubClusterAndNamespace.json**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/clusters",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('clusterName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Dedicated",
                "capacity":1
             }
          },
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             },
             "dependsOn":[
                "[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             ]
          }
       ],
       "outputs":{
          "EventHubNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.EventHub/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Crie um arquivo de parâmetro de modelo chamado: **CreateEventHubClusterAndNamespaceParams.json**. 

    > [!NOTE]
    > Substitua os seguintes valores: 
    > - `<EventHubsClusterName>`- Nome do cluster Event Hubs    
    > - `<EventHubsNamespaceName>`- Nome do seu espaço de nome do Event Hubs
    > - `<Location>`- Localização do namespace do seu Event Hubs

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    
    ```
3. Execute o seguinte comando PowerShell para implantar o modelo para criar um namespace do Event Hubs. Em seguida, recupere o ID do namespace do Event Hubs para usá-lo mais tarde. Substitua pelo `{MyRG}` nome do grupo de recursos antes de executar o comando.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>Grant Event Hubs namespace acesso à identidade do cofre-chave

1. Execute o comando a seguir para criar um cofre de chaves com **proteção de purga** e **exclusão suave** ativada. 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    (OU)    
    
    Execute o seguinte comando para atualizar um **cofre de chaves existente**. Especifique valores para nomes de grupo de recursos e cofre de chaves antes de executar o comando. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Defina a política de acesso do cofre chave para que a identidade gerenciada do namespace do Event Hubs possa acessar o valor-chave no cofre principal. Use o ID do namespace do Event Hubs da seção anterior. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>Criptografar dados no namespace do Event Hubs com a chave gerenciada pelo cliente do cofre de chaves
Você fez as seguintes etapas até agora: 

1. Criei um namespace premium com uma identidade gerenciada.
2. Crie um cofre de chaves e conceda o acesso de identidade gerenciado ao cofre de chaves. 

Nesta etapa, você atualizará o namespace do Event Hubs com informações do cofre chave. 

1. Crie um arquivo JSON chamado **CreateEventHubClusterAndNamespace.json** com o seguinte conteúdo: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          },
          "keyVaultUri":{
             "type":"string",
             "metadata":{
                "description":"URI of the KeyVault."
             }
          },
          "keyName":{
             "type":"string",
             "metadata":{
                "description":"KeyName."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]",
                "encryption":{
                   "keySource":"Microsoft.KeyVault",
                   "keyVaultProperties":[
                      {
                         "keyName":"[parameters('keyName')]",
                         "keyVaultUri":"[parameters('keyVaultUri')]"
                      }
                   ]
                }
             }
          }
       ]
    }
    ``` 

2. Criar um arquivo de parâmetro de modelo: **UpdateEventHubClusterAndNamespaceParams.json**. 

    > [!NOTE]
    > Substitua os seguintes valores: 
    > - `<EventHubsClusterName>`- Nome do cluster Event Hubs.        
    > - `<EventHubsNamespaceName>`- Nome do seu espaço de nome do Event Hubs
    > - `<Location>`- Localização do namespace do seu Event Hubs
    > - `<KeyVaultName>`- Nome do seu cofre de chaves
    > - `<KeyName>`- Nome da chave no cofre da chave

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          }
       }
    }
    ```             
3. Execute o seguinte comando PowerShell para implantar o modelo Gerenciador de recursos. Substitua pelo `{MyRG}` nome do seu grupo de recursos antes de executar o comando. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="troubleshoot"></a>Solução de problemas
Como uma prática recomendada, sempre habilite logs como mostrado na seção anterior. Ele ajuda no rastreamento das atividades quando a criptografia BYOK é ativada. Também ajuda a diminuir os problemas.

A seguir estão os códigos de erros comuns a serem procurar quando a criptografia BYOK estiver ativada.

| Ação | Código do erro | Estado resultante dos dados |
| ------ | ---------- | ----------------------- | 
| Remova a permissão de embrulho/desembrulho de um cofre de chaves | 403 |    Inaccessible |
| Remova a adesão à função AAD de um diretor AAD que concedeu a permissão de embrulho/desembrulhado | 403 |  Inaccessible |
| Exclua uma chave de criptografia do cofre de chaves | 404 | Inaccessible |
| Exclua o cofre da chave | 404 | Inacessível (assume que a exclusão suave está ativada, que é uma configuração necessária.) |
| Alterando o período de expiração na chave de criptografia de tal forma que ela já está expirada | 403 |   Inaccessible  |
| Alterando o NBF (não antes) de tal forma que a chave de criptografia da chave não esteja ativa | 403 | Inaccessible  |
| Selecionando a opção **Permitir serviços MSFT** para o firewall do cofre chave ou bloqueando o acesso da rede ao cofre de chaves que tem a chave de criptografia | 403 | Inaccessible |
| Movendo o cofre da chave para um inquilino diferente | 404 | Inaccessible |  
| Problema de rede intermitente ou paralisação de DNS/AAD/MSI |  | Acessível usando a chave de criptografia de dados em cache |

> [!IMPORTANT]
> Para habilitar o Geo-DR em um namespace que esteja usando a criptografia BYOK, o namespace secundário para emparelhamento deve estar em um cluster dedicado e deve ter uma identidade gerenciada atribuída ao sistema ativada nele. Para saber mais, consulte [Identidades Gerenciadas para Recursos Do Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:
- [Visão geral de Hubs de Evento](event-hubs-about.md)
- [Visão geral do Cofre chave](../key-vault/key-vault-overview.md)




