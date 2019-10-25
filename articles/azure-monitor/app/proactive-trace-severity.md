---
title: Detecção Inteligente - Degradação na taxa de gravidade de rastreamento, no Azure Application Insights | Microsoft Docs
description: Monitore os rastreamentos de aplicativo com o Azure Application Insights para padrões incomuns na telemetria de rastreamento.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/27/2017
ms.openlocfilehash: 83c1296beabaaae78289a653c6b30f6665f725c2
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820534"
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>Degradação na taxa de gravidade de rastreamento (visualização)

Os rastreamentos são amplamente usados em aplicativos, porque ajudam a contar a história do que acontece nos bastidores. Quando as coisas não dão certo, os rastreamentos fornecem visibilidade crucial da sequência de eventos que acarretam o estado indesejado. Embora os rastreamentos sejam geralmente não estruturados, há algo que pode ser aprendido concretamente com eles – o nível de gravidade. No estado estável de um aplicativo, esperamos que a taxa entre rastreamentos “bons” (*Informações* e *Detalhado*) e “ruins” (*Aviso*, *Erro* e *Crítico*) permaneça estável. A suposição é de que os rastreamentos “ruins” possam ocorrer regularmente até determinado ponto por causa de vários motivos (problemas de rede transitórios, por exemplo). Mas, quando começa a crescer, um problema real geralmente se manifesta como um aumento na proporção relativa de rastreamentos “bons” X “ruins”. O Application Insights Smart Detection analisa automaticamente os rastreamentos registrados pelo aplicativo e pode avisar você sobre padrões incomuns na gravidade da telemetria de rastreamento.

Este recurso não exige configuração especial, além de configurar o registro em log de rastreamento para o aplicativo (veja como configurar um ouvinte de log de rastreamento para [.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-trace-logs) ou [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-trace-logs)). Ele permanece ativo quando o aplicativo gera telemetria de exceção suficiente.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando eu receberia este tipo de notificação de detecção inteligente?
Você pode obter esse tipo de notificação se a taxa entre rastreamentos "bons" (rastreamentos registrados com um nível de *informações* ou *detalhado*) e rastreamentos "incorretos" (rastreamentos registrados com um nível de *aviso*, *erro*ou *fatal*) está degradando em um dia específico, em comparação com uma linha de base calculada nos sete dias anteriores.

## <a name="does-my-app-definitely-have-a-problem"></a>Meu aplicativo definitivamente tem um problema?
Não, uma notificação não significa que seu aplicativo definitivamente tem um problema. Embora uma degradação na taxa entre rastreamentos “bons” e “ruins” possa indicar um problema no aplicativo, essa alteração na taxa pode ser benigna. Por exemplo, o aumento pode ser devido a um fluxo de novo no aplicativo emitindo mais rastreamentos “ruins” do que fluxos existentes).

## <a name="how-do-i-fix-it"></a>Como corrigi-la?
As notificações incluem informações de diagnóstico para oferecer suporte no processo de diagnóstico:
1. **Triagem.** A notificação mostra quantas operações foram afetadas. Isso pode ajudá-lo a atribuir uma prioridade ao problema.
2. **Escopo.** O problema está afetando todo o tráfego ou apenas algumas operações? Essas informações podem ser obtidas na notificação.
3. **Diagnosticar.** Você pode usar os itens e os relatórios relacionados às informações de suporte para ajudar a diagnosticar o problema.


