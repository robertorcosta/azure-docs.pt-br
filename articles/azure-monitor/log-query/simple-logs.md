---
title: Experiência de Logs Simples no Monitor Azure (Preview) | Microsoft Docs
description: A experiência Simple Logs permite criar consultas básicas no Azure Monitor sem interagir diretamente com o KQL.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/12/2019
ms.openlocfilehash: 59bcb42edaf7d46498a3514b4f1c919c6e8cc0c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77660250"
---
# <a name="simple-logs-experience-in-azure-monitor-preview"></a>Experiência de logs simples no Azure Monitor (Preview)
O Azure Monitor oferece uma [experiência rica](get-started-portal.md) para criar consultas [de log](log-query-overview.md) usando o idioma KQL. Você pode não exigir toda a potência do KQL e preferir uma experiência simplificada para os requisitos básicos de consulta. A experiência Simple Logs permite criar consultas básicas sem interagir diretamente com o KQL. Você também pode usar o Simple Logs como uma ferramenta de aprendizado para kql, pois você exige consultas mais sofisticadas.

> [!NOTE]
> O Simple Logs é atualmente implementado como um teste apenas para O Cosmos DB e Key Vaults. Por favor, compartilhe sua experiência com a Microsoft através [do User Voice](https://feedback.azure.com/forums/913690-azure-monitor) para nos ajudar a determinar se vamos expandir e liberar esse recurso.


## <a name="scope"></a>Escopo
A experiência Do Simples Logs recupera dados da tabela *AzureDiagnostics,* *AzureMetrics*e *AzureActivity* para o recurso selecionado. 

## <a name="using-simple-logs"></a>Usando registros simples
Navegue até qualquer Bios DB ou Key Vault em sua assinatura do Azure com [configurações de diagnóstico configuradas para coletar logs em um espaço de trabalho do Log Analytics](../platform/resource-logs-collect-storage.md). Clique em **Logs** no menu **Monitoramento** para abrir a experiência Registros Simples.

![Menu](media/simple-logs/menu.png)

Selecione **um campo** e um **operador** e especifique um **Valor** para comparação. Clique **+** e especifique **E/Ou** para adicionar critérios adicionais.

![Critérios](media/simple-logs/criteria.png)

Clique **em Executar** para exibir os resultados da consulta.

## <a name="view-and-edit-kql"></a>Ver e editar KQL
Selecione **o editor de consulta** para abrir o KQL gerado pela consulta Registros Simples na experiência completa do Log Analytics. 

![Editor de consultas](media/simple-logs/query-editor.png)

Você pode editar diretamente o KQL e usar outros recursos no Log Analytics, como filtros para refinar ainda mais seus resultados.

![Editar KQL](media/simple-logs/edit-kql.png)


## <a name="next-steps"></a>Próximas etapas

- Complete um tutorial sobre [como usar o Log Analytics no portal Azure](get-started-portal.md).
- Complete um tutorial sobre [como escrever consultas de log](get-started-portal.md).
