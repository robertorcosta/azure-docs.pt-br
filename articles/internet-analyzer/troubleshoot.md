---
title: Solução de problemas do Azure Internet Analyzer | Microsoft Docs
description: A referência de solução de problemas para o Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: a265278652c16b4682707470d183a02a55b9a0ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77069210"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Solução de problemas do Azure Internet Analyzer

Este artigo contém etapas de solução de problemas para problemas comuns do Analisador da Internet.

## <a name="things-to-keep-in-mind"></a>Algumas coisas que se deve manter em mente
- O script do cliente deve ser incorporado em um site **HTTPS.** As medidas não serão coletadas se o script for executado em um site de texto simples **(http://)** ou local **(file://).**
- Os dados de medição só serão coletados se o script de cliente do perfil do Analisador de Internet tiver sido incorporado em um aplicativo que está recebendo tráfego real do usuário. O tráfego sintético (por exemplo, Testes de Desempenho do WebApp do Azure) normalmente não executa código Javascript incorporado, portanto, nenhuma medição será gerada por esse tipo de tráfego.

## <a name="azure-portal"></a>Portal do Azure
**"Um scorecard não foi gerado para a combinação de filtro selecionado" na seção Scorecards**
- Os scorecards são gerados diariamente (no final de cada dia, horário UTC).
- Os scorecards só são gerados se mais de 100 medições forem coletadas para a combinação de filtros selecionada (Teste, Período de Tempo, País, etc.).

**"Contagem total de medição" é zero para um ou ambos os pontos finais em um teste**
- As séries temporiais e as contagens de medição são calculadas uma vez por hora, então você precisará esperar pelo menos esse tempo para que novos dados de medição apareçam.
- O Analisador de Internet só conta medições bem sucedidas (ou seja, respostas HTTP 200) para sua análise. Se um ou ambos os pontos finais em um teste forem inalcançáveis ou retornarem um código HTTP não-200, eles aparecerão com zero de medições totais.

## <a name="next-steps"></a>Próximas etapas
Leia a [FAQ do Analisador de Internet](internet-analyzer-faq.md)
