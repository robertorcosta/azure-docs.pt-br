---
title: 'Início Rápido: Criar uma consulta compartilhada com modelos'
description: Neste guia de início rápido, você usa um modelo do Resource Manager para criar uma consulta compartilhada do Resource Graph que conta máquinas virtuais por SO.
ms.date: 04/28/2020
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: 050cf26da2054883fceaa08b11f94c6af4c85a16
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82254237"
---
# <a name="quickstart-create-a-shared-query-by-using-a-resource-manager-template"></a>Início Rápido: Criar uma consulta compartilhada usando um modelo do Resource Manager

As consultas do Resource Graph podem ser salvas como uma _consulta privada_ ou uma _consulta compartilhada_. Uma consulta privada é salva no perfil do portal dos indivíduos e não fica visível a outras pessoas. Uma consulta compartilhada é um objeto do Resource Manager que pode ser compartilhado com outras pessoas por meio de permissões e acesso baseado em função. Uma consulta compartilhada fornece execução consistente e comum da descoberta de recursos. Este guia de início rápido usa um modelo do Resource Manager para criar uma consulta compartilhada.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-a-shared-query"></a>Criar uma consulta compartilhada

Neste guia de início rápido, você cria uma consulta compartilhada chamada _Contar VMs por SO_. Para testar essa consulta no SDK ou no portal com o Resource Graph Explorer, confira [Exemplos – Contar máquinas virtuais por tipo SO](./samples/starter.md#count-os).

### <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/resourcegraph-sharedquery-countos/).

:::code language="json" source="~/quickstart-templates/resourcegraph-sharedquery-countos/azuredeploy.json" highlight="28-37":::

O recurso definido no modelo inclui:

- [Microsoft.ResourceGraph/queries](/azure/templates/microsoft.resourcegraph/queries)

### <a name="deploy-the-template"></a>Implantar o modelo

> [!NOTE]
> O serviço do Azure Resource Graph é gratuito. Para saber mais, confira [Visão geral do Azure Resource Graph](./overview.md).

1. Selecione a imagem a seguir para entrar no portal do Azure e abrir o modelo:

   [![Implantar o modelo de política no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fresourcegraph-sharedquery-countos%2Fazuredeploy.json)

1. Selecione ou insira os valores a seguir:

   | Nome | Valor |
   |------|-------|
   | Subscription | Selecione sua assinatura do Azure. |
   | Resource group | Selecione **Criar**, especifique um nome e, em seguida, selecione **OK**. |
   | Location | Selecione uma região. Por exemplo, **Centro dos EUA**. |
   | Nome da consulta | Deixe o valor padrão **Contar de VMs por SO**. |
   | Código da consulta | Deixar o valor padrão `Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)` |
   | Descrição da consulta | Deixe o valor padrão **Esta consulta compartilhada conta todos os recursos de máquina virtual e os resume pelo tipo de SO.** |
   | Concordo com os termos e condições acima | (Selecionar) |

1. Selecione **Comprar**.

Alguns recursos adicionais:

- para encontrar mais exemplos de modelo, confira [Modelo de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- Para ver a referência de modelo, acesse [Referência de modelo do Azure](/azure/templates/microsoft.resourcegraph/allversions).
- Para saber como desenvolver modelos do Resource Manager, confira a [Documentação do Azure Resource Manager](../../azure-resource-manager/management/overview.md).
- Para conhecer a implantação de nível de assinatura, confira [Create resource groups and resources at the subscription level](../../azure-resource-manager/templates/deploy-to-subscription.md) (Criar grupos de recursos e recursos no nível da assinatura).

## <a name="validate-the-deployment"></a>Validar a implantação

Para executar a nova consulta compartilhada, siga estas etapas:

1. Na barra de pesquisa do portal, pesquise **Consultas do Resource Graph** e selecione.

1. Selecione a consulta compartilhada denominada **Contar VMs por SO** e selecione a guia **Resultados** na página **Visão geral**.

Como alternativa, a consulta compartilhada pode ser aberta no Resource Graph Explorer:

1. Na barra de pesquisa do portal, pesquise **Consultas do Resource Graph Explorer** e selecione.

1. Selecione o botão **Abrir uma consulta**.

1. Altere **Tipo** para _Consultas compartilhadas_. Se você não vir **Contar VMs por SO** na lista, use a caixa de filtro para limitar os resultados. Depois que a consulta compartilhada **Contar VMs por SO** estiver visível, selecione seu nome.

1. Depois que a consulta for carregada, selecione o botão **Executar consulta**. Os resultados são exibidos na guia **Resultados** abaixo.

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover a consulta compartilhada criada, siga estas etapas:

1. Na barra de pesquisa do portal, pesquise **Consultas do Resource Graph** e selecione.

1. Marque a caixa de seleção ao lado da consulta compartilhada chamada **Contar VMs por SO**.

1. Selecione o botão **Excluir** na parte superior da página.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou uma consulta compartilhada do Resource Graph.

Para saber mais sobre consultas compartilhadas, continue no tutorial para:

> [!div class="nextstepaction"]
> [Gerenciar consultas no portal do Azure](./tutorials/create-share-query.md)