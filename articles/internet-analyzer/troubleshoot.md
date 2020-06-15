---
title: Solução de problemas do Analisador de Internet do Azure | Microsoft Docs
description: A referência para solução de problemas do Analisador de Internet do Azure.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: 0c9e203d7e8be7b24c711f73e2152a7745a57dac
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745477"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Solução de problemas do Analisador de Internet do Azure

Este artigo contém etapas para a solução dos problemas mais comuns do Analisador de Internet.

## <a name="things-to-keep-in-mind"></a>Algumas coisas que se deve manter em mente
- O script do cliente deve ser inserido em um site **HTTPS**. As medidas não serão coletadas se o script for executado em um site de texto não criptografado (**http://** ) ou local (**file://** ).
- Os dados de medição serão coletados apenas se o script de cliente do perfil do Analisador de Internet tiver sido inserido em um aplicativo que esteja recebendo tráfego de um usuário real. O tráfego sintético (por exemplo, Testes de Desempenho do Azure WebApp) normalmente não executa código JavaScript integrado, portanto, nenhuma medida será gerada por esse tipo de tráfego.

## <a name="azure-portal"></a>Portal do Azure
**“Um scorecard não foi gerado para a combinação de filtros selecionada” na seção Scorecards**
- Os scorecards são gerados diariamente (no final de cada dia, hora UTC).
- Os scorecards são gerados somente se mais de 100 medidas forem coletadas para a combinação de filtros selecionada (Teste, Período de Tempo, País/Região, etc.).

**A “Contagem Total de Medições” é zero para um ou ambos os pontos de extremidade em um teste**
- As contagens de série temporal e de medida são computadas uma vez por hora, portanto, você precisará aguardar pelo menos esse período de tempo para que os novos dados de medição sejam exibidos.
- O Analisador de Internet conta apenas medidas bem-sucedidas (ou seja, respostas HTTP 200) para a análise. Se um ou ambos os pontos de extremidade em um teste estiverem inacessíveis ou retornando um código HTTP diferente de 200, eles aparecerão com um total de medidas igual a zero.

## <a name="next-steps"></a>Próximas etapas
Leia as [Perguntas frequentes do Analisador de Internet](internet-analyzer-faq.md)
