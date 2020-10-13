---
title: arquivo de inclusão
description: arquivo de inclusão
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 08/07/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7cef6252a99430f0d62d8f976510f0a723badc1f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91654432"
---
## <a name="trusted-microsoft-services"></a>Serviços Microsoft confiáveis
Quando você habilita a opção **permitir que os serviços confiáveis da Microsoft ignorem essa** configuração de firewall, os serviços a seguir recebem acesso aos seus recursos de hubs de eventos.

| Serviço confiável | Cenários de uso com suporte | 
| --------------- | ------------------------- | 
| Grade de Eventos do Azure | Permite que a grade de eventos do Azure envie eventos para os hubs de eventos em seu namespace de hubs de eventos. Você também precisa executar as seguintes etapas: <ul><li>Habilitar a identidade atribuída pelo sistema para um tópico ou um domínio</li><li>Adicionar a identidade à função de remetente de dados dos hubs de eventos do Azure no namespace de hubs de eventos</li><li>Em seguida, configure a assinatura de evento que usa um hub de eventos como um ponto de extremidade para usar a identidade atribuída pelo sistema.</li></ul> <p>Para obter mais informações, consulte [entrega de eventos com uma identidade gerenciada](../articles/event-grid/managed-service-identity.md)</p>|
| Azure Monitor (configurações de diagnóstico) | Permite que Azure Monitor envie informações de diagnóstico para os hubs de eventos em seu namespace de hubs de eventos. |