---
title: Configure sua própria chave para criptografar dados do Ônibus de Serviço do Azure em repouso
description: Este artigo fornece informações sobre como configurar sua própria chave para criptografar o descanso de dados do Ônibus de Serviço do Azure.
services: service-bus-messaging
ms.service: service-bus
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: aschhab
ms.openlocfilehash: aeb9a9730ddc61793e49c9e042906457e0068d9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624081"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>Configure chaves gerenciadas pelo cliente para criptografar os dados do Ônibus de Serviço do Azure em repouso usando o portal Azure
O Azure Service Bus Premium fornece criptografia de dados em repouso com o Azure Storage Service Encryption (Azure SSE). O Service Bus Premium conta com o Azure Storage para armazenar os dados e, por padrão, todos os dados armazenados com o Azure Storage são criptografados usando chaves gerenciadas pela Microsoft. 

## <a name="overview"></a>Visão geral
O Azure Service Bus agora suporta a opção de criptografar dados em repouso com chaves gerenciadas pela Microsoft ou chaves gerenciadas pelo cliente (Traga sua própria chave - BYOK). esse recurso permite que você crie, gire, desative e revogue o acesso às chaves gerenciadas pelo cliente que são usadas para criptografar o Ônibus de Serviço do Azure em repouso.

Ativar o recurso BYOK é um processo de configuração única no seu namespace.

> [!NOTE]
> Existem algumas ressalvas à chave gerenciada pelo cliente para criptografia do lado do serviço. 
>   * Esse recurso é suportado pelo [nível Azure Service Bus Premium.](service-bus-premium-messaging.md) Ele não pode ser habilitado para espaços de nome de ônibus de serviço padrão.
>   * A criptografia só pode ser habilitada para espaços de nomes novos ou vazios. Se o namespace contiver dados, a operação de criptografia falhará.

Você pode usar o Azure Key Vault para gerenciar suas chaves e auditar seu uso de chaves. Você pode criar suas próprias chaves e armazená-las em um cofre de chaves, ou pode usar as APIs do Azure Key Vault para gerar chaves. Para obter mais informações sobre o Azure Key Vault, consulte [O que é o Azure Key Vault?](../key-vault/key-vault-overview.md)

Este artigo mostra como configurar um cofre de chaves com chaves gerenciadas pelo cliente usando o portal Azure. Para aprender como criar um cofre de chaves usando o portal Azure, consulte [Quickstart: Configure e recupere um segredo do Azure Key Vault usando o portal Azure](../key-vault/quick-create-portal.md).

> [!IMPORTANT]
> O uso de chaves gerenciadas pelo cliente com o Azure Service Bus requer que o cofre principal tenha duas propriedades necessárias configuradas. São eles: **Soft Delete** e **Não Purga .** Essas propriedades são habilitadas por padrão quando você cria um novo cofre de chaves no portal Azure. No entanto, se você precisar habilitar essas propriedades em um cofre de chaves existente, você deve usar o PowerShell ou o Azure CLI.

## <a name="enable-customer-managed-keys"></a>Habilitar chaves gerenciadas pelo cliente
Para habilitar as chaves gerenciadas pelo cliente no portal Azure, siga estas etapas:

1. Navegue até o seu espaço de nome Service Bus Premium.
2. Na página **Configurações** do espaço de nome do busto de serviço, selecione **Criptografia**.
3. Selecione a **criptografia de chave gerenciada pelo cliente em repouso,** conforme mostrado na imagem a seguir.

    ![Habilitar a chave gerenciada pelo cliente](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>Configure um cofre de chaves com chaves

Depois de habilitar as chaves gerenciadas pelo cliente, você precisa associar a chave gerenciada pelo cliente com o espaço de nome do Azure Service Bus. Service Bus suporta apenas Azure Key Vault. Se você habilitar a criptografia com a opção **de chave gerenciada pelo cliente** na seção anterior, você precisará ter a chave importada para o Azure Key Vault. Além disso, as teclas devem ter **Soft Delete** e **Não Purgar** configurados para a chave. Essas configurações podem ser configuradas usando [PowerShell](../key-vault/key-vault-soft-delete-powershell.md) ou [CLI](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection).

1. Para criar um novo cofre de chaves, siga o Azure Key Vault [Quickstart](../key-vault/key-vault-overview.md). Para obter mais informações sobre a importação de chaves existentes, consulte [Sobre chaves, segredos e certificados](../key-vault/about-keys-secrets-and-certificates.md).
1. Para ativar a proteção de exclusão suave e purga ao criar um cofre, use o comando [az keyvault create.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Para adicionar proteção contra purga a um cofre existente (que já tem o soft delete ativado), use o comando [az keyvault update.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update)

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Crie chaves seguindo estas etapas:
    1. Para criar uma nova chave, selecione **Gerar/Importar** do menu **Chaves** em **Configurações**.
        
        ![Selecione o botão Gerar/Importar](./media/configure-customer-managed-key/select-generate-import.png)

    1. Defina **Opções** para **Gerar** e forneça um nome à chave.

        ![Criar uma chave](./media/configure-customer-managed-key/create-key.png) 

    1. Agora você pode selecionar essa chave para associar-se ao espaço de nome service bus para criptografar na lista de parada. 

        ![Selecione a chave do cofre de chaves](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > Para redundância, você pode somar até 3 chaves. Caso uma das chaves tenha expirado ou não esteja acessível, as outras chaves serão usadas para criptografia.
        
    1. Preencha os detalhes da tecla e clique **em Selecionar**. Isso permitirá a criptografia de dados em repouso no namespace com uma chave gerenciada pelo cliente. 


    > [!IMPORTANT]
    > Se você está procurando usar a chave gerenciada pelo cliente junto com a recuperação de desastres Geo, por favor, revise o abaixo - 
    >
    > Para habilitar a criptografia em repouso com a chave gerenciada pelo cliente, uma política de [acesso](../key-vault/key-vault-secure-your-key-vault.md) é configurada para a identidade gerenciada do Service Bus no Azure KeyVault especificado. Isso garante o acesso controlado ao Azure KeyVault a partir do espaço de nome do Ônibus de Serviço do Azure.
    >
    > Devido a isso:
    > 
    >   * Se [a recuperação de desastres geo](service-bus-geo-dr.md) já estiver habilitada para o espaço de nome do Ônibus de Serviço e você estiver procurando ativar a chave gerenciada pelo cliente, então 
    >     * Quebre o emparelhamento
    >     * [Configure a política de acesso](../key-vault/managed-identity.md) para a identidade gerenciada para os namespaces primários e secundários para o cofre-chave.
    >     * Configure criptografia no namespace principal.
    >     * Reemparelhe os espaços de nome primário e secundário.
    > 
    >   * Se você está procurando ativar o Geo-DR em um namespace de ônibus de serviço onde a chave gerenciada pelo cliente já está configurada, então -
    >     * [Configure a política de acesso](../key-vault/managed-identity.md) para a identidade gerenciada para o namespace secundário para o cofre de chaves.
    >     * Emparelhe os espaços de nome primário e secundário.


## <a name="rotate-your-encryption-keys"></a>Gire suas chaves de criptografia

Você pode girar sua chave no cofre da chave usando o mecanismo de rotação azure Key Vaults. Para obter mais informações, consulte [Configurar rotação e auditoria de chaves](../key-vault/key-vault-key-rotation-log-monitoring.md). As datas de ativação e expiração também podem ser definidas para automatizar a rotação de chaves. O serviço de ônibus de serviço detectará novas versões-chave e começará a usá-las automaticamente.

## <a name="revoke-access-to-keys"></a>Revogar o acesso às chaves

A revogação do acesso às chaves de criptografia não limpará os dados do Service Bus. No entanto, os dados não podem ser acessados a partir do espaço de nome do Service Bus. Você pode revogar a chave de criptografia através da política de acesso ou excluindo a chave. Saiba mais sobre políticas de acesso e proteção do cofre de chaves do [acesso seguro a um cofre-chave](../key-vault/key-vault-secure-your-key-vault.md).

Uma vez que a chave de criptografia seja revogada, o serviço de Ônibus de Serviço no namespace criptografado ficará inoperável. Se o acesso à chave estiver ativado ou a chave excluída for restaurada, o serviço de ônibus de serviço escolherá a chave para que você possa acessar os dados do espaço de nome do Service Bus criptografado.

## <a name="use-resource-manager-template-to-enable-encryption"></a>Use o modelo do Gerenciador de recursos para habilitar a criptografia
Esta seção mostra como fazer as seguintes tarefas usando **modelos do Azure Resource Manager**. 

1. Crie um **namespace de** barramento de serviço premium com uma **identidade de serviço gerenciada**.
2. Crie um **cofre chave** e conceda ao usuário de identidade de serviço acesso ao cofre principal. 
3. Atualize o espaço de nome do Service Bus com as informações do cofre chave (chave/valor). 


### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>Crie um namespace de barra de serviço premium com identidade de serviço gerenciada
Esta seção mostra como criar um namespace do Azure Service Bus com identidade de serviço gerenciada usando um modelo do Azure Resource Manager e o PowerShell. 

1. Crie um modelo de Gerenciador de recursos do Azure para criar um namespace de nível premium do Service Bus com uma identidade de serviço gerenciada. Nomeie o arquivo: **CreateServiceBusPremiumNamespace.json**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace."
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
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
    
             }
          }
       ],
       "outputs":{
          "ServiceBusNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.ServiceBus/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Crie um arquivo de parâmetro de modelo chamado: **CreateServiceBusPremiumNamespaceParams.json**. 

    > [!NOTE]
    > Substitua os seguintes valores: 
    > - `<ServiceBusNamespaceName>`- Nome do seu espaço de nome do Ônibus de Serviço
    > - `<Location>`- Localização do seu espaço de nome do Ônibus de Serviço

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    ```
3. Execute o seguinte comando PowerShell para implantar o modelo para criar um namespace premium service bus. Em seguida, recupere o ID do espaço de nome do Ônibus de Serviço para usá-lo mais tarde. Substitua pelo `{MyRG}` nome do grupo de recursos antes de executar o comando.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>Grant Service Bus namespace acesso de identidade ao cofre-chave

1. Execute o comando a seguir para criar um cofre de chaves com **proteção de purga** e **exclusão suave** ativada. 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    (OU)
    
    Execute o seguinte comando para atualizar um **cofre de chaves existente**. Especifique valores para nomes de grupo de recursos e cofre de chaves antes de executar o comando. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Defina a política de acesso do cofre chave para que a identidade gerenciada do namespace do Service Bus possa acessar o valor-chave no cofre principal. Use o ID do espaço de nome do Ônibus de Serviço da seção anterior. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>Criptografe dados no espaço de nome do Service Bus com a chave gerenciada pelo cliente do cofre de chaves
Você fez as seguintes etapas até agora: 

1. Criei um namespace premium com uma identidade gerenciada.
2. Crie um cofre de chaves e conceda o acesso de identidade gerenciado ao cofre de chaves. 

Nesta etapa, você atualizará o espaço de nome do Service Bus com informações do cofre chave. 

1. Crie um arquivo JSON chamado **UpdateServiceBusNamespaceWithEncryption.json** com o seguinte conteúdo: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
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
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
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

2. Criar um arquivo de parâmetro de modelo: **UpdateServiceBusNamespaceWithEncryptionParams.json**.

    > [!NOTE]
    > Substitua os seguintes valores: 
    > - `<ServiceBusNamespaceName>`- Nome do seu espaço de nome do Ônibus de Serviço
    > - `<Location>`- Localização do seu espaço de nome do Ônibus de Serviço
    > - `<KeyVaultName>`- Nome do seu cofre de chaves
    > - `<KeyName>`- Nome da chave no cofre da chave  

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
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
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```
    

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:
- [Visão geral do Ônibus de Serviço](service-bus-messaging-overview.md)
- [Visão geral do Cofre chave](../key-vault/key-vault-overview.md)


