---
title: Modos de implantação
description: Descreve como especificar se um modo de implantação completo ou incremental deve ser usado com o Azure Resource Manager.
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 1077d92f076797fb03c4fe750b353e2306f9b6de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460238"
---
# <a name="azure-resource-manager-deployment-modes"></a>Modos de implantação do Azure Resource Manager

Ao implantar os recursos, especifique que a implantação é uma atualização incremental ou uma atualização completa. A diferença entre esses dois modos é como o Resource Manager lida com recursos existentes no grupo de recursos que não estão no modelo.

Para ambos os modos, o Resource Manager tenta criar todos os recursos especificados no modelo. Se o recurso já existe no grupo de recursos e suas configurações são as mesmas, nenhuma operação é realizada para esse recurso. Se você alterar os valores de propriedade de um recurso, o recurso será atualizado com os novos valores. Se você tentar atualizar o local ou o tipo de um recurso existente, a implantação falhará com um erro. Em vez disso, implante um novo recurso com o local ou o tipo de que você precisa.

O modo padrão é incremental.

## <a name="complete-mode"></a>Modo completo

No modo completo, o Gerenciador de recursos **exclui** recursos existentes no grupo de recursos, mas que não são especificados no modelo.

Se o seu modelo incluir um recurso que não seja implantado porque a [condição](conditional-resource-deployment.md) avalia como falso, o resultado depende de qual versão da API REST você usa para implantar o modelo. Se você usar uma versão anterior a 2019-05-10, o recurso **não será excluído**. Com 2019-05-10 ou posterior, o recurso **é excluído**. As versões mais recentes do Azure PowerShell e do Azure CLI excluem o recurso.

Tenha cuidado usando o modo completo com [loops de cópia](copy-resources.md). Todos os recursos que não estão especificados no modelo após a resolução do loop de cópia são excluídos.

Se você implantar [em mais de um grupo de recursos em um modelo,](cross-resource-group-deployment.md)os recursos no grupo de recursos especificados na operação de implantação serão elegíveis para serem excluídos. Os recursos nos grupos de recursos secundários não são excluídos.

Existem algumas diferenças na forma como os tipos de recursos lidam com exclusões completas do modo. Os recursos pai serão excluídos automaticamente quando não estiverem em um modelo que é implantado no modo completo. Alguns recursos filho não são excluídos automaticamente quando não estão no modelo. No entanto, esses recursos filho são excluídos se o recurso pai for excluído.

Por exemplo, se seu grupo de recursos contém uma zona DNS (tipo de recurso Microsoft.Network/dnsZones) e um registro CNAME (tipo de recurso Microsoft.Network/dnsZones/CNAME), a zona DNS é o recurso pai para o registro CNAME. Se você implantar com o modo completo e não incluir a zona DNS em seu modelo, a zona DNS e o registro CNAME são ambos excluídos. Se você incluir a região DNS em seu modelo, mas não incluir o registro CNAME, o CNAME não será excluído.

Para obter uma lista de como os tipos de recursos tratam a exclusão, confira [Exclusão de recursos do Azure para implantações no modo completo](complete-mode-deletion.md).

Se o grupo de recursos estiver [bloqueado,](../management/lock-resources.md)o modo completo não excluirá os recursos.

> [!NOTE]
> Somente modelos de nível raiz suporte ao modo de implantação completa. Para ver os [modelos vinculados ou aninhados](linked-templates.md), você deve usar o modo incremental.
>
> [As implantações](deploy-to-subscription.md) de nível de assinatura não suportam o modo completo.
>
> Atualmente, o portal não suporta o modo completo.
>

## <a name="incremental-mode"></a>Modo incremental

No modo incremental, o Gerenciador de recursos **deixa inalterados** recursos existentes no grupo de recursos, mas que não são especificados no modelo. Os recursos no modelo **são adicionados** ao grupo de recursos.

> [!NOTE]
> Ao reimplantar um recurso existente no modo incremental, todas as propriedades são reaplicadas. As **propriedades não são adicionadas incrementalmente.** Um mal-entendido comum é pensar que as propriedades que não estão especificadas no modelo são deixadas inalteradas. Se você não especificar certas propriedades, o Gerenciador de recursos interpretará a implantação como a substituição desses valores. As propriedades que não estão incluídas no modelo são redefinidas para os valores padrão. Especifique todos os valores não padrão para o recurso, não apenas os que você está atualizando. A definição de recurso no modelo sempre contém o estado final do recurso. Não pode representar uma atualização parcial de um recurso existente.

## <a name="example-result"></a>Resultados de exemplo

Para ilustrar a diferença entre os modos incrementais e completos, considere o cenário a seguir.

O **Grupo de recursos** contém:

* Recurso A
* Recurso B
* Recurso C

O **Modelo** contém:

* Recurso A
* Recurso B
* Recurso D

Quando implantada no **incremental** modo, o grupo de recursos tem:

* Recurso A
* Recurso B
* Recurso C
* Recurso D

Quando implantado no modo **completo**, o Recurso C é excluído. O grupo de recursos tem:

* Recurso A
* Recurso B
* Recurso D

## <a name="set-deployment-mode"></a>Definir o modo de implantação

Para definir o modo de implantação ao implantar com o PowerShell, use o `Mode` parâmetro.

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

Para definir o modo de implantação ao implantar com a CLI do Azure, use o `mode` parâmetro.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

O exemplo a seguir mostra um conjunto de modelos vinculados para o modo de implantação incremental:

```json
"resources": [
  {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

## <a name="next-steps"></a>Próximas etapas

* Para saber como criar modelos do Gerenciador de recursos, consulte [Os modelos do Azure Resource Manager](template-syntax.md).
* Para saber mais sobre como implantar recursos, confira [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](deploy-powershell.md).
* Para exibir as operações para um provedor de recursos, consulte [API REST do Azure](/rest/api/).
