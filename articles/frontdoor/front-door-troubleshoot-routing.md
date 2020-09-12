---
title: Solucionar problemas de configuração de porta frontal do Azure
description: Neste tutorial, você aprenderá como solucionar você mesmo alguns dos problemas comuns que pode enfrentar com o Front Door.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/22/2018
ms.author: duau
ms.openlocfilehash: babe24d0c934cffac00a5100d1da7ee252d147da
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399048"
---
# <a name="troubleshooting-common-routing-issues"></a>Solução de problemas comuns de roteamentos

Este artigo descreve como solucionar alguns dos problemas de roteamento comuns que você pode enfrentar para sua configuração de porta frontal do Azure.

## <a name="503-response-from-front-door-after-a-few-seconds"></a>503 resposta da porta frontal após alguns segundos

### <a name="symptom"></a>Sintoma

- As solicitações regulares enviadas ao seu back-end sem passar pela porta frontal estão com sucesso, mas passar por meio da porta frontal resulta em respostas de erro 503.

- A falha da porta frontal é mostrada após alguns segundos (normalmente em cerca de 30 segundos)

### <a name="cause"></a>Causa

Esse sintoma acontece quando o back-end leva além da configuração de tempo limite (o padrão é 30 segundos) para receber a solicitação da porta frontal ou se ela levar além desse valor de tempo limite para enviar uma resposta para a solicitação da porta frontal. 

### <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

- Envie a solicitação ao seu back-end diretamente (sem passar pela porta frontal) e veja qual é o tempo normal que leva para o back-end responder.
- Envie a solicitação por meio da porta frontal e veja se você está vendo quaisquer respostas de 503. Caso contrário, isso pode não ser um problema de tempo limite. Contate o suporte.
- Se passar por meio da porta frontal resultar no código de resposta de erro 503, configure o campo sendReceiveTimeout para sua porta frontal para estender o tempo limite padrão de até 4 minutos (240 segundos). A configuração está sob a `backendPoolSettings` e é chamada `sendRecvTimeoutSeconds` . 

## <a name="requests-sent-to-the-custom-domain-returns-400-status-code"></a>As solicitações enviadas para o domínio personalizado retorna o código de status 400

### <a name="symptom"></a>Sintoma

- Você criou uma porta frontal, mas uma solicitação ao host de domínio ou front-end está retornando um código de status HTTP 400.

- Você criou um mapeamento DNS de um domínio personalizado para o host de front-end configurado. No entanto, enviar uma solicitação para o nome de host do domínio personalizado retorna um código de status HTTP 400 e não aparece rotear para os back-ends que você configurou.

### <a name="cause"></a>Causa

Esse sintoma poderá acontecer se você não tiver configurado uma regra de roteamento para o domínio personalizado que você adicionou como um host de front-end. Uma regra de roteamento deve ser adicionada explicitamente àquele host de front-end, mesmo que já tenha sido configurada uma para o host de front-end no subdomínio do Front Door (*.azurefd.net) para o qual o seu domínio personalizado tem um mapeamento de DNS.

### <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

Adicione uma regra de roteamento do domínio personalizado para o pool de back-end desejado.

## <a name="front-door-is-not-redirecting-http-to-https"></a>A porta frontal não está redirecionando HTTP para HTTPS

### <a name="symptom"></a>Sintoma

Sua porta frontal tem uma regra de roteamento que diz redirecionar HTTP para HTTPS, mas o acesso ao domínio ainda mantém o HTTP como o protocolo.

### <a name="cause"></a>Causa

Esse comportamento pode acontecer se você não tiver configurado corretamente as regras de roteamento para sua porta frontal. Basicamente, a configuração atual não é específica e pode ter regras conflitantes.

### <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>A solicitação ao nome do host do Front-End Retorna o Código de Status 404

### <a name="symptom"></a>Sintoma

- Você criou um Front Door e configurou um host de front-end, um pool de back-end com pelo menos um back-end e uma regra de roteamento que conecta o host de front-end ao pool de back-end. Seu conteúdo não parece estar disponível ao enviar uma solicitação para o host de front-end configurado, uma vez que o código de status HTTP 404 é retornado.

### <a name="cause"></a>Causa

Há várias causas possíveis para esse sintoma:

- O back-end não é um back-end voltado para o público e não é visível para a porta da frente.
- O back-end está configurado incorretamente, o que está fazendo com que a porta frontal envie a solicitação incorreta (ou seja, seu back-end só aceita HTTP, mas você não selecionou a permissão HTTPS, portanto a porta frontal está tentando encaminhar solicitações HTTPS).
- O back-end está rejeitando o cabeçalho de host que foi enviado com a solicitação ao back-end.
- A configuração para o back-end ainda não foi totalmente implantada.

### <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

1. Tempo de implantação
   - Verifique se você já esperou cerca de 10 minutos para a configuração ser implantada.

2. Verificar as configurações de back-end
    - Navegue até o pool de back-end para o qual a solicitação deve estar roteando (depende de como você configurou a regra de roteamento) e verifique se que o _tipo de host de back-end_ e o nome de host de back-end estão corretos. Se o back-end for um host personalizado, verifique se você o escreveu corretamente. 

    - Verifique suas portas HTTP e HTTPS. Na maioria dos casos, 80 e 443 (respectivamente) estão corretas e nenhuma alteração é necessária. No entanto, há uma chance de que o back-end não esteja configurado assim e esteja escutando uma porta diferente.

        - Verifique o _Cabeçalho de host de back-end_ configurado para os back-ends para o qual o host de Front-end deve ser roteamento. Na maioria dos casos, esse cabeçalho deve ser igual a _Nome de host de back-end_. No entanto, um valor incorreto poderá causar vários códigos de status HTTP 4xx se o back-end esperar algo diferente. Se você inserir o endereço IP do seu back-end, poderá precisar definir o _Cabeçalho de host de back-end_ para o nome do host de back-end.


3. Verificar as configurações de regra de roteamento
    - Navegue até a regra de roteamento que deve fazer o roteamento do nome do host do Front-end em questão para um pool de back-end. Verifique se os protocolos aceitos estão configurados corretamente; se não estiverem, verifique se o protocolo que o Front Door usará ao encaminhar a solicitação está configurado corretamente. O campo _protocolos aceitos_ determina quais solicitações a porta frontal deve aceitar e o _protocolo de encaminhamento_ determina qual porta frontal do protocolo deve usar para encaminhar a solicitação para o back-end.
         - Por exemplo, se o back-end só aceitar solicitações HTTP, as seguintes configurações serão válidas:
            - Os _Protocolos aceitos_ são HTTP e HTTPS. O _Protocolo de encaminhamento_ é HTTP. Solicitação de correspondência não funcionará, já que o HTTPS é um protocolo permitido e, se uma solicitação tiver chegado como HTTPS, o Front Door tentará encaminhá-la usando HTTPS.

            - Os _Protocolos aceitos_ são HTTP. O _protocolo de encaminhamento_ é uma solicitação de correspondência ou http.

    - _Reescrita de URL_ é desabilitada por padrão e você só deverá usar este campo se quiser restringir o escopo dos recursos hospedados pelo back-end que deseja disponibilizar. Quando desabilitado, o Front Door encaminhará o mesmo caminho de solicitação que ele recebe. É possível que esse campo esteja configurado incorretamente e o Front Door esteja solicitando um recurso do back-end que não está disponível, retornando, assim, um código de status HTTP 404.

