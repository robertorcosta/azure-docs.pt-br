---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: b4234acb2ce32980a268e389cb31de9a57ed18e6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82132145"
---
Quando você clona a configuração de outro slot de implantação, a configuração clonada é editável. Alguns elementos de configuração seguem o conteúdo em uma permuta (não específico do slot), enquanto outros elementos de configuração permanecem no mesmo slot após uma permuta (específica do slot). A lista a seguir mostra as configurações que serão alterada com a troca de slots.

**Configurações que são permutadas**:

* Configurações gerais, como versão do Framework, 32/64 bits, Web Sockets
* Configurações do aplicativo (podem ser configuradas para fixarem-se a um slot)
* Cadeias de conexão (podem ser configuradas para fixarem-se a um slot)
* Mapeamentos de manipulador
* Certificados públicos
* Conteúdo de Trabalhos Web
* Conexões híbridas *
* Integração de rede virtual *
* Pontos de extremidade de serviço *
* Rede de distribuição de conteúdo do Azure *

Os recursos marcados com um asterisco (*) estão planejados para serem desalternados. 

**Configurações que não são alternadas**:

* Pontos de extremidade de publicação
* Nomes de domínio personalizados
* Certificados não públicos e configurações de TLS/SSL
* Configurações de dimensionamento
* Agendadores de Trabalhos Web
* Restrições de IP
* Always On
* Configurações de Diagnóstico
* CORS (compartilhamento de recursos entre origens)

> [!NOTE]
> Determinadas configurações de aplicativo que se aplicam a configurações não alternadas também são trocadas. Por exemplo, como as configurações de diagnóstico não são trocadas, as configurações de aplicativo relacionadas, como `WEBSITE_HTTPLOGGING_RETENTION_DAYS` e `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` também não são trocadas, mesmo que não apareçam como configurações de slot.
>
