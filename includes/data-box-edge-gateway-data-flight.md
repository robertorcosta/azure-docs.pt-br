---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67172559"
---
Para dados em vôo:

- O TLS 1.2 padrão é usado para dados que viajam entre o dispositivo e o Azure. Não há retorno para o TLS 1.1 e anteriormente. A comunicação do agente será bloqueada se o TLS 1.2 não for suportado. O TLS 1.2 também é necessário para o gerenciamento de portal e SDK.
- Quando os clientes acessam seu dispositivo através da interface web local de um navegador, o TLS 1.2 padrão é usado como o protocolo seguro padrão.

    - A melhor prática é configurar seu navegador para usar o TLS 1.2.
    - Se o navegador não suportar o TLS 1.2, você pode usar o TLS 1.1 ou o TLS 1.0.
- Recomendamos que você use SMB 3.0 com criptografia para proteger os dados quando você os copiar de seus servidores de dados.
