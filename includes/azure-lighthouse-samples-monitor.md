---
title: incluir arquivo
description: incluir arquivo
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 03/30/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: be8aae6308e712449402b002576974743bc125ef
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986805"
---
Esses exemplos mostram como usar o Azure Monitor para criar alertas para assinaturas que foram integradas para o gerenciamento de recursos delegados do Azure.

| **Modelo** | **Descrição** |
|---------|---------|
| [monitor-delegation-changes](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) | Consulta o último dia de atividade em um locatário de gerenciamento e [relatórios sobre as delegações adicionadas ou removidas](../articles/lighthouse/how-to/monitor-delegation-changes.md) (ou as tentativas que não foram bem-sucedidas).|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/alert-using-actiongroup) | Este modelo cria um alerta do Azure e se conecta a um Grupo de Ações existente.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/multiple-loganalytics-alerts) | Cria vários alertas de log com base em consultas Kusto.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegation-alert-for-customer) | Implanta um alerta em um locatário quando um usuário delega uma assinatura a um locatário gerenciador.|
