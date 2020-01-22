---
title: Publicar um aplicativo gerenciado do catálogo de serviços
description: Mostra como criar um aplicativo gerenciado do Azure destinado aos membros de sua organização.
author: tfitzmac
ms.topic: tutorial
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: e756617a700d258078e84a3fa11c8aceb6f4dd88
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903276"
---
# <a name="create-and-publish-a-managed-application-definition"></a>Criar e publicar uma definição de aplicativo gerenciado

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Crie e publique [aplicativos gerenciados](overview.md) do Azure destinados aos membros de sua organização. Por exemplo, um departamento de TI pode publicar aplicativos gerenciados que atendem aos padrões organizacionais. Esses aplicativos gerenciados estão disponíveis por meio do catálogo de serviços, não pelo Azure Marketplace.

Para publicar um aplicativo gerenciado para o Catálogo de Serviços do Azure, você precisa:

* Crie um modelo que define os recursos para implantar com o aplicativo gerenciado.
* Defina os elementos da interface do usuário para o portal ao implantar o aplicativo gerenciado.
* Criar um pacote .zip que contenha os arquivos de modelo necessários.
* Decidir qual usuário, grupo ou aplicativo precisa de acesso ao grupo de recursos na assinatura do usuário.
* Criar a definição de aplicativo gerenciado que aponta para o pacote .zip e solicita o acesso à identidade.

Para este artigo, seu aplicativo gerenciado contém apenas uma conta de armazenamento. Sua finalidade é ilustrar as etapas da publicação de um aplicativo gerenciado. Para obter exemplos completos, consulte [Projetos de exemplo para aplicativos gerenciados pelo Azure](sample-projects.md).

Os exemplos de PowerShell neste artigo exigem a versão 6.2 ou posterior do Azure PowerShell. Se necessário, [atualize sua versão](/powershell/azure/install-Az-ps).

## <a name="create-the-resource-template"></a>Criar o modelo de recurso

Cada definição de aplicativo gerenciado contém um arquivo chamado **mainTemplate.json**. Nele, você pode definir os recursos do Azure que serão implantados. O modelo não é diferente de um modelo normal do Resource Manager.

Crie um arquivo chamado **mainTemplate.json**. O nome diferencia maiúsculas de minúsculas.

Adicione o seguinte JSON ao seu arquivo. Ele define os parâmetros para a criação de uma conta de armazenamento e especifica as propriedades dela.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        },
        "storageAccountType": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "storageAccountName": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('storageAccountType')]"
            },
            "kind": "Storage",
            "properties": {}
        }
    ],
    "outputs": {
        "storageEndpoint": {
            "type": "string",
            "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
        }
    }
}
```

Salve o arquivo mainTemplate.json.

## <a name="defining-your-create-experience-using-createuidefinitionjson"></a>Definir a experiência de criação usando CreateUiDefinition.json

Como editor, você define a experiência de criação usando o arquivo **createUiDefinition.json**, que gera a interface para usuários que criam aplicativos gerenciados. Você define como os usuários fornecem a entrada para cada parâmetro usando [elementos de controle](create-uidefinition-elements.md), incluindo menus suspensos, caixas de texto e caixas de senha.

Criar um arquivo chamado **createUiDefinition.json** (esse nome diferencia maiúsculas de minúsculas)

Adicione o JSON inicial a seguir ao arquivo e salve-o.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Azure.CreateUIDef",
   "version": "0.1.2-preview",
   "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "storageConfig",
                "label": "Storage settings",
                "subLabel": {
                    "preValidation": "Configure the infrastructure settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Storage settings",
                "elements": [
                    {
                        "name": "storageAccounts",
                        "type": "Microsoft.Storage.MultiStorageAccountCombo",
                        "label": {
                            "prefix": "Storage account name prefix",
                            "type": "Storage account type"
                        },
                        "defaultValue": {
                            "type": "Standard_LRS"
                        },
                        "constraints": {
                            "allowedTypes": [
                                "Premium_LRS",
                                "Standard_LRS",
                                "Standard_GRS"
                            ]
                        }
                    }
                ]
            }
        ],
        "outputs": {
            "storageAccountNamePrefix": "[steps('storageConfig').storageAccounts.prefix]",
            "storageAccountType": "[steps('storageConfig').storageAccounts.type]",
            "location": "[location()]"
        }
    }
}
```

Para saber mais, consulte [Introdução a CreateUiDefinition](create-uidefinition-overview.md).

## <a name="package-the-files"></a>Empacote os arquivos

Adicione os dois arquivos em um arquivo zip chamado app.zip. Os dois arquivos devem estar no nível raiz do arquivo .zip. Se você colocá-los em uma pasta, receberá um erro ao criar a definição de aplicativo gerenciado que indica que os arquivos necessários não estão presentes. 

Carregue o pacote em um local acessível no qual ele pode ser consumido. 

```powershell
New-AzResourceGroup -Name storageGroup -Location eastus
$storageAccount = New-AzStorageAccount -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context

New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzStorageBlobContent -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx 
```

## <a name="create-the-managed-application-definition"></a>Criar a definição de aplicativo gerenciado

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Criar um grupo de usuários ou aplicativo do Azure Active Directory

A próxima etapa é selecionar um grupo de usuários ou um aplicativo para gerenciar os recursos em nome do cliente. Esse grupo de usuários ou aplicativo tem permissões no grupo de recursos gerenciados de acordo com a função atribuída. A função pode ser qualquer função interna de RBAC (Controle de acesso baseado em função) como Proprietário ou Colaborador. Também é possível conceder a um usuário individual permissões para gerenciar os recursos, mas, normalmente, você atribui essa permissão a um grupo de usuários. Para criar um novo grupo de usuários do Active Directory, consulte [Criar um grupo e adicionar membros no Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

É necessário usar a ID de objeto do grupo de usuários para gerenciar os recursos. 

```powershell
$groupID=(Get-AzADGroup -DisplayName mygroup).Id
```

### <a name="get-the-role-definition-id"></a>Obter a ID de definição da função

Em seguida, é necessário o ID de definição de função da função RBAC interna para conceder acesso ao usuário, grupo de usuários ou aplicativo. Normalmente, você usa a função Proprietário, Colaborador ou Leitor. O comando a seguir mostra como obter a ID de definição de função para a função Proprietário:

```powershell
$ownerID=(Get-AzRoleDefinition -Name Owner).Id
```

### <a name="create-the-managed-application-definition"></a>Criar a definição de aplicativo gerenciado

Se você ainda não tiver um grupo de recursos para armazenar a definição de aplicativo gerenciado, crie um agora:

```powershell
New-AzResourceGroup -Name appDefinitionGroup -Location westcentralus
```

Agora, crie o recurso de definição de aplicativo gerenciado.

```powershell
$blob = Get-AzStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "${groupID}:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

## <a name="bring-your-own-storage-for-the-managed-application-definition"></a>Traga seu próprio armazenamento para a definição de aplicativo gerenciado
Você pode optar por armazenar a definição de aplicativo gerenciado em uma conta de armazenamento fornecida por você durante a criação, para que a localização dessa definição e o acesso a ela possam ser totalmente gerenciados por você para suas necessidades regulatórias.

> [!NOTE]
> O modelo "traga seu próprio armazenamento" só é compatível com as implantações de modelo do ARM ou da API REST da definição de aplicativo gerenciado.

### <a name="select-your-storage-account"></a>Selecione sua conta de armazenamento
Você deve [criar uma conta de armazenamento](../../storage/common/storage-account-create.md) para conter a definição de aplicativo gerenciado para uso com o Catálogo de Serviços.

Copie a ID do recurso da conta de armazenamento. Ela será usada posteriormente ao implantar a definição.

### <a name="set-the-role-assignment-for-appliance-resource-provider-in-your-storage-account"></a>Defina a atribuição de função para "Provedor de Recursos de Dispositivo" em sua conta de armazenamento
Antes que a definição de aplicativo gerenciado possa ser implantada em sua conta de armazenamento, você deverá fornecer permissões de colaborador para a função de **Provedor de Recursos do Dispositivo** para que ela possa gravar os arquivos de definição no contêiner da sua conta de armazenamento.

1. No [Portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento.
1. Selecione **Controle de Acesso (IAM)** para exibir as configurações de controle de acesso da conta de armazenamento. Selecione a guia **Atribuições de função** para ver as atribuições de função atuais.
1. Na janela **Adicionar atribuição de função**, selecione a função **Colaborador**. 
1. No campo **Atribuir acesso a**, selecione **usuário, grupo ou entidade de serviço do Azure AD**.
1. Em **Selecionar**, pesquise pela função **Provedor de Recursos do Dispositivo** e selecione-a.
1. Salve a atribuição de função.

### <a name="deploy-the-managed-application-definition-with-an-arm-template"></a>Implantar a definição de aplicativo gerenciado com um modelo de ARM 

Use o modelo de ARM a seguir para implantar o aplicativo gerenciado empacotado como uma nova definição de aplicativo gerenciado no Catálogo de Serviços cujos arquivos de definição são armazenados e mantidos em sua conta de armazenamento:
   
```json
    {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "applicationName": {
            "type": "string",
            "metadata": {
                "description": "Managed Application name"
            }
        },
        "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
        "definitionStorageResourceID": {
            "type": "string",
            "metadata": {
                "description": "Storage account resource ID for where you're storing your definition"
            }
        },
        "_artifactsLocation": {
            "type": "string",
            "metadata": {
                "description": "The base URI where artifacts required by this template are located."
            }
        }
    },
    "variables": {
        "lockLevel": "None",
        "description": "Sample Managed application definition",
        "displayName": "Sample Managed application definition",
        "managedApplicationDefinitionName": "[parameters('applicationName')]",
        "packageFileUri": "[parameters('_artifactsLocation')]",
        "defLocation": "[parameters('definitionStorageResourceID')]",
        "managedResourceGroupId": "[concat(subscription().id,'/resourceGroups/', concat(parameters('applicationName'),'_managed'))]",
        "applicationDefinitionResourceId": "[resourceId('Microsoft.Solutions/applicationDefinitions',variables('managedApplicationDefinitionName'))]"
    },
    "resources": [
        {
            "type": "Microsoft.Solutions/applicationDefinitions",
            "apiVersion": "2019-07-01",
            "name": "[variables('managedApplicationDefinitionName')]",
            "location": "[parameters('location')]",
            "properties": {
                "lockLevel": "[variables('lockLevel')]",
                "description": "[variables('description')]",
                "displayName": "[variables('displayName')]",
                "packageFileUri": "[variables('packageFileUri')]",
                "storageAccountId": "[variables('defLocation')]"
            }
        }
    ],
    "outputs": {}
}
```

Adicionamos uma nova propriedade chamada **storageAccountId** às propriedades de sua applicationDefinition e fornecemos a ID da conta de armazenamento na qual você deseja armazenar sua definição como o valor dessa propriedade:

Você pode verificar se os arquivos de definição de aplicativo são salvos em sua conta de armazenamento fornecida em um contêiner intitulado **applicationdefinitions**.

> [!NOTE]
> Para aumentar a segurança, você pode criar uma definição de aplicativos gerenciados armazenando-a em um [blob de conta de Armazenamento do Azure em que a criptografia está habilitada](../../storage/common/storage-service-encryption.md). O conteúdo da definição é criptografado por meio das opções de criptografia da conta de armazenamento. Somente os usuários com permissões para o arquivo podem ver a definição no Catálogo de Serviços.

### <a name="make-sure-users-can-see-your-definition"></a>Verifique se os usuários podem ver sua definição

Você tem acesso à definição de aplicativo gerenciado, mas você deve certificar-se de que outros usuários na sua organização podem acessá-lo. Conceda a eles pelo menos a função de Leitor para a definição. Eles podem ter herdado esse nível de acesso da assinatura ou grupo de recursos. Para verificar quem tem acesso à definição e adicionar usuários ou grupos, consulte [Usar Controle de acesso baseado em função para gerenciar o acesso aos recursos da sua assinatura do Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Próximas etapas

* Para publicar o aplicativo gerenciado no Azure Marketplace, veja [Aplicativos Gerenciados do Azure no Marketplace](publish-marketplace-app.md).
* Para implantar uma instância de aplicativo gerenciado, veja [Implantar aplicativo do catálogo de serviços por meio do portal do Azure](deploy-service-catalog-quickstart.md).
