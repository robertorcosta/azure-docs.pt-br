---
title: incluir arquivo
description: incluir arquivo
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 4c50ca2449787d681d8b9571083a92077d95dcd8
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986781"
---
Esses exemplos ilustram várias tarefas que podem ser executadas em cenários de gerenciamento entre locatários.

| **Modelo** | **Descrição** |
|---------|---------|
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-rg-deployment) | Implanta contas de armazenamento em dois grupos de recursos diferentes.|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-mgmt-services) | Cria serviços de gerenciamento do Azure, vincula-os e implanta soluções adicionais. Para uma implantação de ponta a ponta, use o modelo **rgWithAzureMgmt.json**. |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-security-center) | Habilita e configura a Central de Segurança do Azure na assinatura de destino do Azure. |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-sentinel) | Implanta e habilita o Azure Sentinel em um workspace do Log Analytics existente em uma assinatura delegada. |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-log-analytics-vm-extensions) | Esses modelos permitem que você implante extensões de VM do Log Analytics em suas VMs Windows e Linux, conectando-as ao workspace do Log Analytics desejado |
