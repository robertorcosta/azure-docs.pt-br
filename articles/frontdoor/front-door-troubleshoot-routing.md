---
title: Solucionar problemas de configuração do Azure Front Door
description: Neste tutorial, você aprenderá como solucionar você mesmo alguns dos problemas comuns que pode enfrentar com o Front Door.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2018
ms.author: sharadag
ms.openlocfilehash: 962c884eb8adc05e5d50b6b254d5c3f0b18af556
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471499"
---
# <a name="troubleshooting-common-routing-issues"></a>Solução de problemas comuns de roteamentos

Este artigo descreve como solucionar problemas de alguns dos problemas comuns de roteamento que você pode enfrentar para a configuração do Azure Front Door.

## <a name="503-response-from-front-door-after-a-few-seconds"></a>Resposta 503 da Porta da Frente após alguns segundos

### <a name="symptom"></a>Sintoma

- As solicitações regulares enviadas para o seu backend sem passar pela Porta da Frente estão tendo sucesso, mas ir pela Porta da Frente resulta em 503 respostas de erro.

- A falha da Porta da Frente aparece após alguns segundos (normalmente em torno de 30 segundos)

### <a name="cause"></a>Causa

Esse sintoma acontece quando o backend leva além da configuração de tempo de tempo (o padrão é de 30 segundos) para receber a solicitação da Porta da Frente ou se leva além desse valor de tempo para enviar uma resposta à solicitação da Porta da Frente. 

### <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

- Envie a solicitação diretamente para o seu backend (sem passar pela Porta da Frente) e veja qual é o tempo habitual necessário para o seu backend responder.
- Envie a solicitação via Front Door e veja se você está vendo alguma resposta 503. Se não, então isso pode não ser um problema de tempo. Contate o suporte.
- Se ir através do Front Door resultar em código de resposta de erro 503, configure o campo sendReceiveTimeout para sua Porta da Frente para estender o tempo inicial padrão até 4 minutos (240 segundos). A configuração `backendPoolSettings` está sob `sendRecvTimeoutSeconds`o e é chamado . 

## <a name="requests-sent-to-the-custom-domain-returns-400-status-code"></a>Solicitações enviadas ao domínio personalizado retorna 400 Status Code

### <a name="symptom"></a>Sintoma

- Você criou uma Porta da Frente, mas uma solicitação para o host de domínio ou frontend está retornando um código de status HTTP 400.

- Você criou um mapeamento DNS de um domínio personalizado para o host de front-end configurado. No entanto, enviar uma solicitação para o nome de host do domínio personalizado retorna um código de status HTTP 400 e não aparece rotear para os back-ends que você configurou.

### <a name="cause"></a>Causa

Esse sintoma poderá acontecer se você não tiver configurado uma regra de roteamento para o domínio personalizado que você adicionou como um host de front-end. Uma regra de roteamento deve ser adicionada explicitamente àquele host de front-end, mesmo que já tenha sido configurada uma para o host de front-end no subdomínio do Front Door (*.azurefd.net) para o qual o seu domínio personalizado tem um mapeamento de DNS.

### <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

Adicione uma regra de roteamento do domínio personalizado para o pool de back-end desejado.

## <a name="front-door-is-not-redirecting-http-to-https"></a>Front Door não está redirecionando HTTP para HTTPS

### <a name="symptom"></a>Sintoma

Sua Porta frontal tem uma regra de roteamento que diz redirecionar HTTP para HTTPS, mas acessar o domínio ainda mantém HTTP como protocolo.

### <a name="cause"></a>Causa

Esse comportamento pode acontecer se você não tiver configurado corretamente as regras de roteamento para a sua Porta da Frente. Basicamente, sua configuração atual não é específica e pode ter regras conflitantes.

### <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>A solicitação ao nome do host do Front-End Retorna o Código de Status 404

### <a name="symptom"></a>Sintoma

- Você criou um Front Door e configurou um host de front-end, um pool de back-end com pelo menos um back-end e uma regra de roteamento que conecta o host de front-end ao pool de back-end. Seu conteúdo não parece estar disponível ao enviar uma solicitação para o host de front-end configurado, uma vez que o código de status HTTP 404 é retornado.

### <a name="cause"></a>Causa

Há várias causas possíveis para esse sintoma:

- O backend não é um backend público voltado para o público e não é visível para a Porta da Frente.
- O backend está mal configurado, o que está fazendo com que a Porta da Frente envie a solicitação errada (ou seja, seu backend só aceita HTTP, mas você não desverificou permitindo HTTPS então o Front Door está tentando encaminhar solicitações HTTPS).
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
    - Navegue até a regra de roteamento que deve fazer o roteamento do nome do host do Front-end em questão para um pool de back-end. Verifique se os protocolos aceitos estão configurados corretamente; se não estiverem, verifique se o protocolo que o Front Door usará ao encaminhar a solicitação está configurado corretamente. O campo _de protocolos aceitos_ determina quais solicitações a Porta da Frente deve aceitar e o _protocolo de encaminhamento_ determina qual protocolo a Porta da Frente deve usar para encaminhar a solicitação para o backend.
         - Por exemplo, se o back-end só aceitar solicitações HTTP, as seguintes configurações serão válidas:
            - Os _Protocolos aceitos_ são HTTP e HTTPS. O _Protocolo de encaminhamento_ é HTTP. Solicitação de correspondência não funcionará, já que o HTTPS é um protocolo permitido e, se uma solicitação tiver chegado como HTTPS, o Front Door tentará encaminhá-la usando HTTPS.

            - Os _Protocolos aceitos_ são HTTP. O _Protocolo de encaminhamento_ é solicitação de correspondência ou HTTPS.

    - _Reescrita de URL_ é desabilitada por padrão e você só deverá usar este campo se quiser restringir o escopo dos recursos hospedados pelo back-end que deseja disponibilizar. Quando desabilitado, o Front Door encaminhará o mesmo caminho de solicitação que ele recebe. É possível que esse campo esteja configurado incorretamente e o Front Door esteja solicitando um recurso do back-end que não está disponível, retornando, assim, um código de status HTTP 404.

