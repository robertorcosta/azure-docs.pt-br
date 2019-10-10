---
title: Criar uma atribuição de política com um modelo do Resource Manager
description: Este artigo orienta você quanto às etapas para usar um modelo do Resource Manager para criar uma atribuição de política para identificar recursos sem conformidade.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/13/2019
ms.topic: quickstart
ms.service: azure-policy
ms.openlocfilehash: 72d8fab39e8dd11cf46eb2977a9b9fe288ca2de1
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980809"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-resource-manager-template"></a>Início Rápido: Criar uma atribuição de política para identificar recursos sem conformidade usando um modelo do Resource Manager

A primeira etapa para compreender a conformidade no Azure é identificar o status de seus recursos.
Este guia de início rápido orienta você no processo de criação de uma atribuição de política para identificar máquinas virtuais que não estão usando discos gerenciados.

No final deste processo, você identificará com êxito quais máquinas virtuais não estão usando discos gerenciados. Eles *não estão em conformidade* com a atribuição da política.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste início rápido, você criará uma atribuição de política e atribuirá uma definição de política interna chamada *Auditar VMs que não usam discos gerenciados*. Para ver uma lista parcial das políticas internas disponíveis, confira [Exemplos do Azure Policy](./samples/index.md).

Há vários métodos para criar atribuições de política. Neste início rápido, você usará um [modelo de início rápido](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/).
Veja uma cópia do modelo:

[!code-json[policy-assignment](~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json)]

> [!NOTE]
> o serviço Azure Policy é gratuito.  Para saber mais, confira [Visão geral do Azure Policy](./overview.md).

1. Selecione a imagem a seguir para entrar no portal do Azure e abrir o modelo:

   [![Implantar o modelo de política no Azure](./media/assign-policy-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json)

1. Selecione ou insira os valores a seguir:

   | NOME | Valor |
   |------|-------|
   | Subscription | Selecione sua assinatura do Azure. |
   | Resource group | Selecione **Criar**, especifique um nome e, em seguida, selecione **OK**. Na captura de tela, o nome do grupo de recursos é *mypolicyquickstart\<Data em MMDD>rg*. |
   | Location | Selecione uma região. Por exemplo, **Centro dos EUA**. |
   | Nome de atribuição de política | Especifique um nome de atribuição de política. Será possível usar a exibição de definição de política se você desejar. Por exemplo, **Auditar VMs que não usam discos gerenciados**. |
   | Nome do Rg | Especifique um nome de grupo de recursos ao qual você deseja atribuir a política. Neste início rápido, use o valor padrão **[resourceGroup().name]** . **[resourceGroup()](../../azure-resource-manager/resource-group-template-functions-resource.md#resourcegroup)** é uma função de modelo que recupera o grupo de recursos. |
   | ID de definição de política | Especifique **/providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4c19-b460-a2d36003525a**. |
   | Concordo com os termos e condições acima | (Selecionar) |

1. Selecione **Comprar**.

Alguns recursos adicionais:

- para encontrar mais exemplos de modelo, confira [Modelo de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Para ver a referência de modelo, acesse [Referência de modelo do Azure](/azure/templates/microsoft.authorization/allversions).
- Para saber como desenvolver modelos do Resource Manager, confira a [Documentação do Azure Resource Manager](/azure/azure-resource-manager/).
- Para conhecer a implantação de nível de assinatura, confira [Create resource groups and resources at the subscription level](../../azure-resource-manager/deploy-to-subscription.md) (Criar grupos de recursos e recursos no nível da assinatura).

## <a name="identify-non-compliant-resources"></a>Identificar recursos sem conformidade

Selecione **Conformidade** no lado esquerdo da página. Em seguida, localize as **VMs de auditoria que não usam a atribuição de política de discos gerenciados** que você criou.

![Página de visão geral de conformidade de política](./media/assign-policy-template/policy-compliance.png)

Se houver recursos sem conformidade com essa nova atribuição, eles aparecerão em **Recursos sem conformidade**.

Para saber mais, confira [How compliance works](./how-to/get-compliance-data.md#how-compliance-works) (Como a conformidade funciona).

## <a name="clean-up-resources"></a>Limpar recursos

Para remover a atribuição criada, siga estas etapas:

1. Selecione **Conformidade** (ou **Atribuições**) no lado esquerdo da página e localize as **VMs de auditoria que não usam a atribuição  de política de discos gerenciados** que você criou.

1. Clique com o botão direito do mouse na atribuição de política **Auditar VMs que não usam discos gerenciados** e selecione **Excluir atribuição**.

   ![Excluir uma atribuição da página de visão geral de conformidade](./media/assign-policy-template/delete-assignment.png)

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você atribuiu uma definição de política interna a um escopo e avaliou seu relatório de conformidade. A definição de política valida que todos os recursos no escopo estão em conformidade e identifica quais não estão.

Para saber mais sobre a atribuição de políticas para validar que novos recursos estejam em conformidade, continue com o tutorial para:

> [!div class="nextstepaction"]
> [Criando e gerenciando políticas](./tutorials/create-and-manage.md)