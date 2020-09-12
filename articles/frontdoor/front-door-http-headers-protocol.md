---
title: Suporte de protocolo para cabeçalhos HTTP na porta frontal do Azure | Microsoft Docs
description: Este artigo descreve os protocolos de cabeçalho HTTP aos quais a porta frontal dá suporte.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: 6864a854215d899043607b3d01cffbd343ee7751
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399507"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Suporte de protocolo para cabeçalhos HTTP na porta frontal do Azure
Este artigo descreve o protocolo que a porta frontal dá suporte com partes do caminho de chamada (consulte a imagem). As seções a seguir fornecem mais informações sobre os cabeçalhos HTTP com suporte da porta frontal.

![Protocolo de cabeçalhos HTTP de porta frontal do Azure][1]

>[!IMPORTANT]
>A porta frontal não certifica nenhum cabeçalho HTTP que não esteja documentado aqui.

## <a name="client-to-front-door"></a>Cliente para o Front Door
A porta frontal aceita a maioria dos cabeçalhos da solicitação de entrada sem modificá-los. Alguns cabeçalhos reservados são removidos da solicitação de entrada se forem enviados, incluindo cabeçalhos com o prefixo X-FD-*.

## <a name="front-door-to-backend"></a>Front Door para o back-end

A porta frontal inclui cabeçalhos de uma solicitação de entrada, a menos que sejam removidas devido a restrições. A porta frontal também adiciona os seguintes cabeçalhos:

| Cabeçalho  | Exemplo e descrição |
| ------------- | ------------- |
| Através de |  Via: 1,1 Azure </br> Porta frontal adiciona a versão HTTP do cliente seguida pelo *Azure* como o valor para o cabeçalho via. Esse cabeçalho indica a versão HTTP do cliente e essa porta frontal era um destinatário intermediário da solicitação entre o cliente e o back-end.  |
| X-Azure-ClientIP | X-Azure-ClientIP: 127.0.0.1 </br> Representa o endereço IP do cliente associado à solicitação que está sendo processada. Por exemplo, uma solicitação proveniente de um proxy pode adicionar o cabeçalho X-Forwardd-for para indicar o endereço IP do chamador original. |
| X-Azure-SocketIP |  X-Azure-SocketIP: 127.0.0.1 </br> Representa o endereço IP do soquete associado à conexão TCP da qual a solicitação atual foi originada. O endereço IP do cliente de uma solicitação pode não ser igual ao seu endereço IP de soquete porque pode ser substituído arbitrariamente por um usuário.|
| X-Azure-ref |  X-Azure-Ref: 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz </br> Uma cadeia de caracteres de referência exclusiva que identifica uma solicitação servida pela porta da frente. Ele é usado para pesquisar logs de acesso e crítico para solução de problemas.|
| X-Azure-RequestChain |  X-Azure-RequestChain: saltos = 1 </br> Um cabeçalho que o front door usa para detectar loops de solicitação e os usuários não devem assumir uma dependência dele. |
| X-Forwarded-For | X-Forwarded-for: 127.0.0.1 </br> O campo de cabeçalho HTTP X-Forwarded-for (XFF) geralmente identifica o endereço IP de origem de um cliente que se conecta a um servidor Web por meio de um proxy HTTP ou balanceador de carga. Se houver um cabeçalho XFF existente, a porta frontal acrescentará o IP de soquete do cliente a ele ou adicionará o cabeçalho XFF com o IP de soquete do cliente. |
| X-Forwarded-Host | X-Forwarded-host: contoso.azurefd.net </br> O campo de cabeçalho HTTP do host X encaminhado é um método comum usado para identificar o host original solicitado pelo cliente no cabeçalho de solicitação HTTP do host. Isso ocorre porque o nome do host da porta frontal pode ser diferente para o servidor de back-end que manipula a solicitação. |
| X-Forwarded-Proto | X-encaminhar-proto: http </br> O campo de cabeçalho HTTP de IP encaminhada-proto é geralmente usado para identificar o protocolo de origem de uma solicitação HTTP porque a porta frontal, com base na configuração, pode se comunicar com o back-end usando HTTPS. Isso é verdadeiro mesmo que a solicitação para o proxy reverso seja HTTP. |
| X-FD-HealthProbe | O campo de cabeçalho HTTP X-FD-HealthProbe é usado para identificar a investigação de integridade da porta frontal. Se esse cabeçalho for definido como 1, a solicitação será investigação de integridade. Você pode usar quando deseja restringir o acesso de uma porta de frente específica com o campo de cabeçalho de host X/encaminhado. |
|X-Azure-FDID | Cabeçalho X-Azure-FDID: 437c82cd-360A-4A54-94c3-5ff707647783 </br> Este campo contém frontdoorID que pode ser usado para identificar em qual porta frontal a solicitação de entrada é. Esse campo é preenchido pelo serviço de porta frontal. | 

## <a name="front-door-to-client"></a>Front Door para o cliente

Todos os cabeçalhos enviados para a porta de front-end também são passados para o cliente. Os cabeçalhos a seguir são enviados da porta frontal para os clientes.

| Cabeçalho  | Exemplo |
| ------------- | ------------- |
| X-Azure-ref |  *X-Azure-Ref: 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Esse é uma cadeia de caracteres de referência exclusiva que identifica uma solicitação atendida pelo Front Door. Isso é essencial para a solução de problemas, pois é usado para pesquisar logs de acesso.|

## <a name="next-steps"></a>Próximas etapas

- [Criar um Front Door](quickstart-create-front-door.md)
- [Como funciona a porta frontal](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png
