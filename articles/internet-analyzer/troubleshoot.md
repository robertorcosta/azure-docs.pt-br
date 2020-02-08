---
title: Solução de problemas do Azure Internet Analyzer | Microsoft Docs
description: A referência de solução de problemas do Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: a265278652c16b4682707470d183a02a55b9a0ec
ms.sourcegitcommit: a460fdc19d6d7af6d2b5a4527e1b5c4e0c49942f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77069210"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Solução de problemas do Azure Internet Analyzer

Este artigo contém etapas para solução de problemas comuns do Internet Analyzer.

## <a name="things-to-keep-in-mind"></a>Algumas coisas que se deve manter em mente
- O script do cliente deve ser inserido em um site **https** . As medidas não serão coletadas se o script for executado em um site de texto sem formatação (**http://** ) ou local (**file://** ).
- Os dados de medição só serão coletados se o script de cliente do perfil do Internet Analyzer tiver sido inserido em um aplicativo que esteja recebendo tráfego de usuário real. O tráfego sintético (por exemplo, testes de desempenho do Azure WebApp) normalmente não executa código JavaScript inserido, portanto, nenhuma medida será gerada por esse tipo de tráfego.

## <a name="azure-portal"></a>Portal do Azure
**"Um scorecard não foi gerado para a combinação de filtro selecionada" na seção scorecards**
- Os scorecards são gerados diariamente (no final de cada dia, hora UTC).
- Os scorecards serão gerados somente se mais de 100 medidas forem coletadas para a combinação de filtro selecionada (teste, período de tempo, país, etc.).

**A "contagem total de medições" é zero para um ou ambos os pontos de extremidade em um teste**
- As contagens de séries temporais e de medida são computadas uma vez por hora, portanto, você precisará aguardar pelo menos esse período de tempo para que os novos dados de medição sejam exibidos.
- O Internet Analyzer conta apenas com medidas bem-sucedidas (ou seja, respostas HTTP 200) para sua análise. Se um ou ambos os pontos de extremidade em um teste estiverem inacessíveis ou retornando um código HTTP diferente de 200, eles aparecerão com total de medidas zero.

## <a name="next-steps"></a>Próximas etapas
Leia as [Perguntas frequentes do Analisador de Internet](internet-analyzer-faq.md)
