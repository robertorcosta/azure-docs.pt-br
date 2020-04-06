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
ms.openlocfilehash: aacf1c287dca01b5993d35b311eed22e88ef7e87
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421150"
---
Esses exemplos mostram como usar o Azure Monitor para criar alertas para assinaturas que foram integradas para o gerenciamento de recursos delegados do Azure.

| **Modelo** | **Descrição** |
|---------|---------|
| [monitor-delegation-changes](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/tools/monitor-delegation-changes) | Consulta o último dia de atividade em um locatário de gerenciamento e [relatórios sobre as delegações adicionadas ou removidas](../articles/lighthouse/how-to/monitor-delegation-changes.md) (ou as tentativas que não foram bem-sucedidas).|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/alert-using-actiongroup) | Este modelo cria um alerta do Azure e se conecta a um Grupo de Ações existente.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/multiple-loganalytics-alerts) | Cria vários alertas de log com base em consultas Kusto.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegation-alert-for-customer) | Implanta um alerta em um locatário quando um usuário delega uma assinatura a um locatário gerenciador.|
