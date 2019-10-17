---
title: Executar sua primeira consulta usando a CLI do Azure
description: Este artigo orienta você pelas etapas para habilitar a extensão do Resource Graph para a CLI do Azure e executar a primeira consulta.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/18/2019
ms.topic: quickstart
ms.service: resource-graph
ms.openlocfilehash: b50a21f9c1a4bae7ced8afd9a49c33cc936983ac
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389731"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-cli"></a>Início Rápido: Execute a primeira consulta ao Resource Graph usando a CLI do Azure

A primeira etapa para usar o Azure Resource Graph é verificar se a extensão para a [CLI do Azure](/cli/azure/) está instalada. Este início rápido orienta você pelo processo de adição da extensão à instalação da CLI do Azure. Você pode usar a extensão com a CLI do Azure instalada localmente ou por meio do [Azure Cloud Shell](https://shell.azure.com).

No final deste processo, você terá adicionado a extensão à sua instalação da CLI do Azure de preferência e executado sua primeira consulta ao Resource Graph.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="add-the-resource-graph-extension"></a>Adicionar a extensão do Resource Graph

Para habilitar a CLI do Azure para consultar o Azure Resource Graph, é necessário adicionar a extensão. Essa extensão funcionará sempre que a CLI do Azure puder ser usada, incluindo no [bash no Windows 10](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com) (tanto autônomo quanto dentro do portal), na [imagem do Docker da CLI do Azure](https://hub.docker.com/r/microsoft/azure-cli/) ou instalada localmente.

1. Verifique se a CLI do Azure mais recente está instalada (pelo menos a **2.0.45**). Se ainda não estiver instalado, siga [estas instruções](/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

1. Em seu ambiente da CLI do Azure preferido, importe-a com o seguinte comando:

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. Verifique se a extensão foi instalada e se é a versão esperada (pelo menos **1.0.0**):

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="run-your-first-resource-graph-query"></a>Executar a primeira consulta ao Resource Graph

Com a extensão da CLI do Azure adicionada ao seu ambiente de preferência, é hora de experimentar uma consulta simples ao Resource Graph. A consulta retornará os cinco primeiros recursos do Azure com o **Nome** e o **Tipo de recurso** de cada recurso.

1. Execute a primeira consulta ao Azure Resource Graph usando a extensão `graph` e o comando `query`:

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Run Azure Resource Graph query
   az graph query -q 'Resources | project name, type | limit 5'
   ```

   > [!NOTE]
   > Devido a essa consulta de exemplo não fornecer um modificador de classificação, tal como `order by`, executar essa consulta várias vezes provavelmente produzirá um conjunto diferente de recursos por solicitação.

1. Atualizar a consulta para `order by` a propriedade **Name**:

   ```azurecli-interactive
   # Run Azure Resource Graph query with 'order by'
   az graph query -q 'Resources | project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > Assim como ocorre com a primeira consulta, executar esta consulta várias vezes provavelmente produzirá um conjunto diferente de recursos por solicitação. A ordem dos comandos de consulta é importante. Neste exemplo, o `order by` vem após o `limit`. Isso primeiro limitará os resultados da consulta e, em seguida, os ordenará.

1. Atualize a consulta para, primeiro, `order by` a propriedade **Name** e depois `limit` conforme os primeiros cinco resultados:

   ```azurecli-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   az graph query -q 'Resources | project name, type | order by name asc | limit 5'
   ```

Quando a consulta final for executada várias vezes, supondo que nada em seu ambiente está sendo alterado, os resultados retornados são consistentes e conforme o esperado – ordenados pela propriedade **Name**, mas ainda limitados aos cinco primeiros resultados.

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser remover a extensão do Resource Graph do seu ambiente da CLI do Azure, faça isso usando o comando a seguir:

```azurecli-interactive
# Remove the Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

## <a name="next-steps"></a>Próximas etapas

- Obter mais informações sobre a [linguagem da consulta](./concepts/query-language.md)
- Saiba [explorar recursos](./concepts/explore-resources.md)
- Executar sua primeira consulta com o [Azure PowerShell](first-query-powershell.md)
- Ver exemplos de [Consultas iniciais](./samples/starter.md)
- Veja exemplos de [Consultas avançadas](./samples/advanced.md)
- Fornecer comentários sobre o [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)