---
title: Criar uma definição de aplicativo gerenciado
description: Mostra como criar um aplicativo gerenciado do Azure destinado aos membros de sua organização.
author: tfitzmac
ms.topic: quickstart
ms.date: 09/13/2019
ms.author: tomfitz
ms.openlocfilehash: 11d4eb396d332327addcd85ed7badd36ef8f1d10
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647937"
---
# <a name="publish-an-azure-managed-application-definition"></a>Publicar uma definição de aplicativo gerenciado do Azure

Este início rápido fornece uma introdução ao trabalho com aplicativos gerenciados. Você pode adicionar uma definição de aplicativo gerenciado a um catálogo interno para usuários em sua organização. Para simplificar a introdução, já compilamos os arquivos do seu aplicativo gerenciado. Esses arquivos estão disponíveis por meio do GitHub. Você aprenderá a compilar esses arquivos no tutorial [Criar aplicativo de catálogo de serviço](publish-service-catalog-app.md).

Ao terminar, você terá um grupo de recursos chamado **appDefinitionGroup** que terá a definição de aplicativo gerenciado.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group-for-definition"></a>Criar um grupo de recursos para definição

Sua definição de aplicativo gerenciado existe em um grupo de recursos. O grupo de recursos do Azure é uma coleção lógica na qual os recursos do Azure são implantados e gerenciados.

Para criar um grupo de recursos, use o seguinte comando:

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

## <a name="create-the-managed-application-definition"></a>Criar a definição de aplicativo gerenciado

Ao definir o aplicativo gerenciado, você deve selecionar um usuário, grupo ou aplicativo que gerencie os recursos para o consumidor. Essa identidade tem permissões no grupo de recursos gerenciado de acordo com a função atribuída. Geralmente, você cria um grupo do Azure Active Directory para gerenciar os recursos. No entanto, para este artigo, use sua própria identidade.

Para obter a ID de objeto de sua identidade, forneça seu nome de usuário principal no comando a seguir:

```azurecli-interactive
userid=$(az ad user show --id example@contoso.org --query objectId --output tsv)
```

Em seguida, é necessária a ID de definição de função da função RBAC interna para conceder acesso ao usuário. O comando a seguir mostra como obter a ID de definição de função para a função Proprietário:

```azurecli-interactive
roleid=$(az role definition list --name Owner --query [].name --output tsv)
```

Agora, crie o recurso de definição de aplicativo gerenciado. O aplicativo gerenciado contém somente uma conta de armazenamento.

```azurecli-interactive
az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "https://github.com/Azure/azure-managedapp-samples/raw/master/Managed%20Application%20Sample%20Packages/201-managed-storage-account/managedstorage.zip"
```

Quando o comando for concluído, você tem uma definição de aplicativo gerenciado no seu grupo de recursos. 

Alguns dos parâmetros usados no exemplo anterior são:

* **resource-group**: o nome do grupo de recursos no qual a definição de aplicativo gerenciado é criada.
* **lock-level**: o tipo de bloqueio colocado no grupo de recursos gerenciados. Ela impede que o cliente execute operações indesejáveis no grupo de recursos. Atualmente, ReadOnly é o único nível de bloqueio com suporte. Quando ReadOnly é especificado, o cliente pode ler somente os recursos presentes no grupo de recursos gerenciados. As identidades do publicador que concedem acesso ao grupo de recursos gerenciado são isentas de bloqueio.
* **authorizations**: descreve a ID da entidade e a ID de definição de função que são usadas para conceder permissão ao grupo de recursos gerenciados. Ele é especificado no formato `<principalId>:<roleDefinitionId>`. Se for necessário mais de um valor, especifique-os no formato `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>`. Os valores são separados por um espaço.
* **package-file-uri**: o local de um pacote .zip que contém os arquivos necessários. O pacote deve ter os arquivos **mainTemplate.json** e **createUiDefinition.json**. O **mainTemplate.json** define os recursos do Azure que são criados como parte do aplicativo gerenciado. O modelo não é diferente de um modelo normal do Resource Manager. O **createUiDefinition.json** gera a interface do usuário para os usuários que criam o aplicativo gerenciado por meio do portal.

## <a name="next-steps"></a>Próximas etapas

Você publicou a definição de aplicativo gerenciado. Agora, saiba como implantar uma instância dessa definição.

> [!div class="nextstepaction"]
> [Início Rápido: Implantar aplicativo do catálogo de serviços](deploy-service-catalog-quickstart.md)