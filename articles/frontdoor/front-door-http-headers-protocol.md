---
title: Suporte de protocolo para cabeçalhos HTTP na porta da frente do Azure | Microsoft Docs
description: Este artigo descreve protocolos http de cabeçalho que o Front Door suporta.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: bb1de5d51afd01cf0aa519f12aa3665bee804efd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471669"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Suporte de protocolo para cabeçalhos HTTP na porta da frente do Azure
Este artigo descreve o protocolo que a Porta da Frente suporta com partes do caminho de chamada (ver imagem). As seções a seguir fornecem mais informações sobre cabeçalhos HTTP suportados pelo Front Door.

![Protocolo de cabeçalhos HTTP da Porta da Frente do Azure][1]

>[!IMPORTANT]
>A Porta da Frente não certifica nenhum cabeçalho HTTP que não esteja documentado aqui.

## <a name="client-to-front-door"></a>Cliente para o Front Door
Front Door aceita a maioria dos cabeçalhos da solicitação de entrada sem modificá-los. Alguns cabeçalhos reservados são removidos da solicitação de entrada se enviados, incluindo cabeçalhos com o prefixo X-FD-*.

## <a name="front-door-to-backend"></a>Front Door para o back-end

Front Door inclui cabeçalhos de uma solicitação de entrada, a menos que seja removido devido a restrições. Front Door também adiciona os seguintes cabeçalhos:

| Cabeçalho  | Exemplo e descrição |
| ------------- | ------------- |
| Através de |  Via: 1.1 Azure </br> Front Door adiciona a versão HTTP do cliente seguida pelo *Azure* como o valor para o cabeçalho Via. Este cabeçalho indica a versão HTTP do cliente e que o Front Door era um destinatário intermediário para a solicitação entre o cliente e o backend.  |
| X-Azure-ClientIP | X-Azure-ClientIP: 127.0.0.1 </br> Representa o endereço IP do cliente associado à solicitação que está sendo processada. Por exemplo, uma solicitação proveniente de um proxy pode adicionar o cabeçalho X-Forwarded-For para indicar o endereço IP do chamador original. |
| X-Azure-SocketIP |  X-Azure-SocketIP: 127.0.0.1 </br> Representa o endereço IP do soquete associado à conexão TCP da a que a solicitação atual se originou. O endereço IP do cliente de uma solicitação pode não ser igual ao seu endereço IP do soquete, porque pode ser substituído arbitrariamente por um usuário.|
| X-Azure-Ref |  X-Azure-Ref: 0zxV+XAAAAAAAAAAAAAALKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QTMzYwYS00YTU0LTk0YzMtTTZZZMNzA3NjQ3Nzgz </br> Uma seqüência de referência única que identifica uma solicitação atendida pelo Front Door. É usado para pesquisar registros de acesso e crítico para solução de problemas.|
| X-Azure-RequestChain |  X-Azure-RequestChain: hops=1 </br> Um cabeçalho que o Front Door usa para detectar loops de solicitação, e os usuários não devem ter uma dependência dele. |
| X-Forwarded-For | X-Forwarded-For: 127.0.0.1 </br> O campo de cabeçalho HTTP X-Forwarded-For (XFF) geralmente identifica o endereço IP de origem de um cliente conectado a um servidor web através de um proxy HTTP ou balanceador de carga. Se houver um cabeçalho XFF existente, então a Porta frontal anexa o IP do soquete cliente a ele ou adiciona o cabeçalho XFF com o IP do soquete cliente. |
| X-Forwarded-Host | X-Forwarded-Host: contoso.azurefd.net </br> O campo de cabeçalho HTTP do Host-X-Forwarded é um método comum usado para identificar o host original solicitado pelo cliente no cabeçalho de solicitação do Host HTTP. Isso porque o nome host do Front Door pode diferir para o servidor back-end que manuseia a solicitação. |
| X-Forwarded-Proto | X-Forwarded-Proto: http </br> O campo de cabeçalho X-Forwarded-Proto HTTP é frequentemente usado para identificar o protocolo de origem de uma solicitação HTTP porque o Front Door, baseado na configuração, pode se comunicar com o backend usando HTTPS. Isso é verdade mesmo que a solicitação para o proxy reverso seja HTTP. |
| Sonda X-FD-HealthProbe | O campo de cabeçalho X-FD-HealthProbe HTTP é usado para identificar a sonda de saúde da Porta da Frente. Se este cabeçalho definido como 1, a solicitação é sonda de saúde. Você pode usar quando quiser restringir o acesso da porta da frente específica com o campo de cabeçalho X-Forwarded-Host. |

## <a name="front-door-to-client"></a>Front Door para o cliente

Quaisquer cabeçalhos enviados para a porta da frente do backend também são passados para o cliente. A seguir, cabeçalhos enviados da Porta da Frente para os clientes.

| Cabeçalho  | Exemplo |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAAAAAAAAAAALKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QTMzYwYS00YTU0LTk0YzMtTTZZZMNzA3NjQ3Nzgz* </br> Esse é uma cadeia de caracteres de referência exclusiva que identifica uma solicitação atendida pelo Front Door. Isso é fundamental para a solução de problemas, pois é usado para pesquisar logs de acesso.|

## <a name="next-steps"></a>Próximas etapas

- [Crie uma porta da frente](quickstart-create-front-door.md)
- [Como funciona a Porta da Frente](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png
