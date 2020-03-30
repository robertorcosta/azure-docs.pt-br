---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: e00db06346b19ef85eb77626eb2ed169d2224b6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "71129687"
---
Quando você clona a configuração de outro slot de implantação, a configuração clonada é editável. Alguns elementos de configuração seguem o conteúdo através de um swap (não específico de slot), enquanto outros elementos de configuração permanecem no mesmo slot após uma troca (slot específico). A lista a seguir mostra as configurações que serão alterada com a troca de slots.

**Configurações que são permutadas**:

* Configurações gerais, como versão framework, 32/64 bits, soquetes web
* Configurações do aplicativo (podem ser configuradas para fixarem-se a um slot)
* Cadeias de conexão (podem ser configuradas para fixarem-se a um slot)
* Mapeamentos de manipulador
* Certificados públicos
* Conteúdo de Trabalhos Web
* Conexões híbridas *
* Integração de rede virtual *
* Pontos finais de serviço *
* Rede de entrega de conteúdo azure *

As características marcadas com um asterisco (*) estão previstas para serem destrocadas. 

**Configurações que não são alternadas**:

* Pontos de extremidade de publicação
* Nomes de domínio personalizados
* Certificados não públicos e configurações TLS/SSL
* Configurações de dimensionamento
* Agendadores de Trabalhos Web
* Restrições de IP
* Always On
* Configurações do registro de diagnóstico
* Compartilhamento de recursos de origem cruzada (CORS)

> [!NOTE]
> Certas configurações de aplicativo que se aplicam a configurações não trocadas também não são trocadas. Por exemplo, como as configurações de registro de `WEBSITE_HTTPLOGGING_RETENTION_DAYS` diagnóstico `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` não são trocadas, as configurações do aplicativo relacionadas também não são trocadas, mesmo que elas não apareçam como configurações de slot.
>
