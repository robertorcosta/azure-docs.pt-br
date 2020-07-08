---
title: Configuração pós-implantação usando extensões
description: Saiba como usar extensões de modelo do Azure Resource Manager para fornecer configurações de pós-implantação.
author: mumian
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: be55138a2aa6dc0552c7556438ffd43705687c87
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86055030"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>Fornecer configurações de pós-implantação usando extensões

As extensões de modelo são pequenos aplicativos que fornecem tarefas de configuração e automação pós-implantação e tarefas de automação nos recursos do Azure. Mais popular são as extensões de máquina virtual. Consulte [Recursos e extensões de máquina Virtual para Windows](../../virtual-machines/extensions/features-windows.md), e [recursos e extensões de máquina Virtual para Linux](../../virtual-machines/extensions/features-linux.md).

## <a name="extensions"></a>Extensões

As extensões existentes são:

- [Microsoft. Compute/virtualMachines/Extensions](/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft.Compute virtualMachineScaleSets/extensions](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Microsoft.HDInsight clusters/extensions](/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters)
- [Microsoft.Sql servers/databases/extensions](/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions) 
- [Microsoft.Web/sites/siteextensions](/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

Para descobrir as extensões disponíveis, navegue até a [referência de modelo](/azure/templates/). Em **Filtrar por título**, insira **extensão**.

Para saber como usar essas extensões, consulte:

- [Tutorial: implantar extensões de máquina virtual com modelos de Azure Resource Manager](template-tutorial-deploy-vm-extensions.md).
- [Tutorial: Importar arquivos BACPAC do SQL com modelos do Azure Resource Manager](template-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Implantar extensões de máquina virtual com modelos do Azure Resource Manager](template-tutorial-deploy-vm-extensions.md)
