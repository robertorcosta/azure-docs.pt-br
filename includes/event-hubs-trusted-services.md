---
title: incluir arquivo
description: incluir arquivo
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 11/09/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d828a0c3eb2582a833ee8ad07bdf4f18002c9dca
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015563"
---
## <a name="trusted-microsoft-services"></a>Serviços Microsoft confiáveis
Quando você habilita a opção **permitir que os serviços confiáveis da Microsoft ignorem essa** configuração de firewall, os serviços a seguir recebem acesso aos seus recursos de hubs de eventos.

| Serviço confiável | Cenários de uso com suporte | 
| --------------- | ------------------------- | 
| Grade de Eventos do Azure | Permite que a grade de eventos do Azure envie eventos para os hubs de eventos em seu namespace de hubs de eventos. Você também precisa executar as seguintes etapas: <ul><li>Habilitar a identidade atribuída pelo sistema para um tópico ou um domínio</li><li>Adicionar a identidade à função de remetente de dados dos hubs de eventos do Azure no namespace de hubs de eventos</li><li>Em seguida, configure a assinatura de evento que usa um hub de eventos como um ponto de extremidade para usar a identidade atribuída pelo sistema.</li></ul> <p>Para obter mais informações, consulte [entrega de eventos com uma identidade gerenciada](../articles/event-grid/managed-service-identity.md)</p>|
| Azure Monitor (configurações de diagnóstico) | Permite que Azure Monitor envie informações de diagnóstico para os hubs de eventos em seu namespace de hubs de eventos. |
| Stream Analytics do Azure | Permite que um trabalho de Azure Stream Analytics leia dados de ([entrada](../articles/stream-analytics/stream-analytics-add-inputs.md)) ou grave dados em hubs de eventos ([saída](../articles/stream-analytics/event-hubs-output.md)) em seu namespace de hubs de eventos. |
| Hub IoT do Azure | Permite que o Hub IoT envie mensagens para os hubs de eventos em seu namespace do hub de eventos. Você também precisa executar as seguintes etapas: <ul><li>Habilitar a identidade atribuída pelo sistema para o Hub IoT</li><li>Adicione a identidade à função de remetente de dados dos hubs de eventos do Azure no namespace de hubs de eventos.</li><li>Em seguida, configure o Hub IoT que usa um hub de eventos como um ponto de extremidade personalizado para usar a autenticação baseada em identidade.</li></ul>
