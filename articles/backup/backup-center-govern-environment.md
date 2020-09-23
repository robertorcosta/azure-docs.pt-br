---
title: Governar seu espaço de backup usando o centro de backup
description: Saiba como controlar seu ambiente do Azure para garantir que todos os seus recursos estejam em conformidade de uma perspectiva de backup com o centro de backup.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 009ee461b0372a3fb73ffb3b0ee5151b77bd8ef8
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90994331"
---
# <a name="govern-your-backup-estate-using-backup-center"></a>Governar seu espaço de backup usando o centro de backup

O centro de backup ajuda você a governar seu ambiente do Azure para garantir que todos os seus recursos estejam em conformidade a partir de uma perspectiva de backup. Abaixo estão alguns dos recursos de governança do centro de backup:

* Exibir e atribuir políticas do Azure para backup

* Exiba todas as fontes de fonte que não foram configuradas para backup.

## <a name="supported-scenarios"></a>Cenários com suporte

* Consulte a [matriz de suporte](backup-center-support-matrix.md) para obter uma lista detalhada de cenários com e sem suporte.

## <a name="azure-policies-for-backup"></a>Políticas do Azure para backup

Para exibir todas as [políticas do Azure](https://docs.microsoft.com/azure/governance/policy/overview) que estão disponíveis para backup, selecione o item de menu **políticas do Azure para backup** . Isso exibirá todas as definições de política do Azure internas e personalizadas [para backup](policy-reference.md) que estão disponíveis para atribuição para suas assinaturas e grupos de recursos.

A seleção de qualquer uma das definições permite [atribuir a política](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage#assign-a-policy) a um escopo.

![Selecionar definições de Azure Policy](./media/backup-center-govern-environment/azure-policy-definitions.png)

## <a name="protectable-datasources"></a>Fontes de fonte de proteção

A seleção do item de menu de **fontes de fonte de proteção** permite que você exiba todas as fontes de fonte que não foram configuradas para backup. Você pode filtrar a lista por assinatura de DataSource, grupo de recursos, local, tipo e marcas. Depois de identificar uma fonte de fontes que precisa ser submetida a backup, você pode clicar com o botão direito do mouse no item de grade correspondente e selecionar **backup** para configurar o backup do recurso.

![Menu fonte de fontes protegíveis](./media/backup-center-govern-environment/protectable-datasources.png)

## <a name="next-steps"></a>Próximas etapas

* [Monitorar e operar backups](backup-center-monitor-operate.md)
* [Executar ações usando o centro de backup](backup-center-actions.md)
* [Obtenha informações sobre seus backups](backup-center-obtain-insights.md)
