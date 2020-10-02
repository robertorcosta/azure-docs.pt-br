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
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: dbce9019e33c07dd4faa91ffd490eba4d313c675
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91630603"
---
# <a name="troubleshooting-common-routing-issues"></a>Solução de problemas comuns de roteamentos

Este artigo descreve como solucionar alguns dos problemas de roteamento comuns que você pode enfrentar para sua configuração de porta frontal do Azure.

## <a name="503-response-from-front-door-after-a-few-seconds"></a>503 resposta da porta frontal após alguns segundos

### <a name="symptom"></a>Sintoma

* As solicitações regulares enviadas ao seu back-end sem passar pela porta frontal estão com sucesso, mas passar por meio da porta frontal resulta em respostas de erro 503.
* A falha da porta frontal é mostrada após alguns segundos (normalmente em cerca de 30 segundos)

### <a name="cause"></a>Causa

A causa desse problema pode ser uma das duas coisas:
 
* O back-end está demorando mais do que o tempo limite configurado (o padrão é 30 segundos) para receber a solicitação da porta frontal.
* O tempo necessário para enviar uma resposta para a solicitação da porta frontal está demorando mais do que o valor de tempo limite. 

### <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

* Envie a solicitação ao seu back-end diretamente (sem passar pela porta frontal) e veja qual é o tempo normal que leva para o back-end responder.
* Envie a solicitação por meio da porta frontal e veja se você está recebendo qualquer resposta de 503. Caso contrário, o problema pode não ser um problema de tempo limite. Entre em contato com o suporte.
* Se passar pela porta de frente resultar no código de resposta de erro 503, configure o `sendReceiveTimeout` campo para sua porta frontal. Você pode estender o tempo limite padrão de até 4 minutos (240 segundos). A configuração está sob a `backendPoolSettings` e é chamada `sendRecvTimeoutSeconds` . 

## <a name="requests-sent-to-the-custom-domain-returns-400-status-code"></a>As solicitações enviadas para o domínio personalizado retorna o código de status 400

### <a name="symptom"></a>Sintoma

* Você criou uma porta frontal, mas uma solicitação ao host de domínio ou front-end está retornando um código de status HTTP 400.
* Você criou um mapeamento DNS para um domínio personalizado para o host front-end configurado. No entanto, o envio de uma solicitação para o nome de host do domínio personalizado retorna um código de status HTTP 400. Que não parece rotear para o back-end configurado.

### <a name="cause"></a>Causa

O problema ocorre se você não configurou uma regra de roteamento para o domínio personalizado que foi adicionado como o host de front-end. Uma regra de roteamento precisa ser adicionada explicitamente para esse host de front-end. Mesmo que um já tenha sido configurado para o host de front-end no subdomínio da porta de frente (*. azurefd.net).

### <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

Adicione uma regra de roteamento para o domínio personalizado para direcionar o tráfego para o pool de back-end selecionado.

## <a name="front-door-doesnt-redirect-http-to-https"></a>A porta frontal não redireciona HTTP para HTTPS

### <a name="symptom"></a>Sintoma

Sua porta frontal tem uma regra de roteamento que diz redirecionar HTTP para HTTPS, mas o acesso ao domínio ainda mantém o HTTP como o protocolo.

### <a name="cause"></a>Causa

Esse comportamento pode acontecer se você não configurou as regras de roteamento corretamente para sua porta frontal. Basicamente, a configuração atual não é específica e pode ter regras conflitantes.

### <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>A solicitação ao nome do host do Front-End Retorna o Código de Status 404

### <a name="symptom"></a>Sintoma

 Você criou uma porta frontal Configurando um host de front-end, um pool de back-ends com pelo menos um back-end e uma regra de roteamento que conecta o host de front-end ao pool de back-ends. Seu conteúdo não está disponível quando você faz uma solicitação para o host de front-end configurado como resultado, um código de status HTTP 404 é retornado.

### <a name="cause"></a>Causa

Há várias causas possíveis para esse sintoma:

* O back-end não é um back-end voltado para o público e não é visível para a porta frontal.
* O back-end está configurado incorretamente, fazendo com que a porta frontal envie a solicitação errada. Em outras palavras, o back-end só aceita HTTP e você não selecionou a verificação, permitindo HTTPS. Portanto, a porta frontal está tentando encaminhar solicitações HTTPS.
* O back-end está rejeitando o cabeçalho de host que foi enviado com a solicitação ao back-end.
* A configuração para o back-end ainda não foi totalmente implantada.

### <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

1. Tempo de implantação
   * Verifique se você aguardou cerca de 10 minutos para que a configuração seja implantada.

2. Verificar as configurações de back-end
    * Navegue até o pool de back-end para o qual a solicitação deve ser roteada (depende de como você tem a regra de roteamento configurada). Verifique se o *tipo de host back-end* e o nome do host de back-end estão corretos Se o back-end for um host personalizado, verifique se você o escreveu corretamente. 

    * Verifique suas portas HTTP e HTTPS. Na maioria dos casos, 80 e 443 (respectivamente) estão corretos e nenhuma alteração será necessária. No entanto, há uma chance de que o back-end não seja configurado dessa maneira e esteja ouvindo em uma porta diferente.

        * Verifique o _Cabeçalho de host de back-end_ configurado para os back-ends para o qual o host de Front-end deve ser roteamento. Na maioria dos casos, esse cabeçalho deve ser igual a *Nome de host de back-end*. No entanto, um valor incorreto poderá causar vários códigos de status HTTP 4xx se o back-end esperar algo diferente. Se você inserir o endereço IP do seu back-end, poderá precisar definir o *Cabeçalho de host de back-end* para o nome do host de back-end.

3. Verifique as configurações da regra de roteamento:
    * Navegue até a regra de roteamento que deve fazer o roteamento do nome do host do Front-end em questão para um pool de back-end. Verifique se os protocolos aceitos estão configurados corretamente ao encaminhar a solicitação. O campo *protocolos aceitos* determina quais solicitações a porta frontal deve aceitar. O *protocolo de encaminhamento* determina qual porta frontal do protocolo deve usar para encaminhar a solicitação para o back-end.
         * Por exemplo, se o back-end só aceitar solicitações HTTP, as seguintes configurações serão válidas:
            * Os *Protocolos aceitos* são HTTP e HTTPS. O *Protocolo de encaminhamento* é HTTP. A solicitação de correspondência não funcionará, pois HTTPS é um protocolo permitido e, se uma solicitação for enviada como HTTPS, a porta frontal tentará encaminhá-la usando HTTPS.

            * Os *Protocolos aceitos* são HTTP. O *protocolo de encaminhamento* é uma solicitação de correspondência ou http.

    - A *regravação de URL* é desabilitada por padrão. Esse campo só será usado se você quiser restringir o escopo de recursos hospedados por back-end que deseja disponibilizar. Quando desabilitado, o Front Door encaminhará o mesmo caminho de solicitação que ele recebe. É possível configurar incorretamente esse campo. Assim, quando a porta da frente estiver solicitando um recurso do back-end que não está disponível, ela retornará um código de status HTTP 404.
