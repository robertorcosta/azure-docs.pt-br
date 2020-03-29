---
title: Perguntas frequentes sobre networking em Funções Azure
description: Respostas para algumas das perguntas e cenários mais comuns para networking com funções do Azure.
author: alexkarcher-msft
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: acb1e942c1f342ce6fee7d8aeacafcc1d7b6fd91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75409538"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Perguntas frequentes sobre networking em Funções Azure

Este artigo lista perguntas freqüentes sobre networking em Funções Azure. Para obter uma visão geral mais abrangente, consulte [opções de rede Funções](functions-networking-options.md).

## <a name="how-do-i-set-a-static-ip-in-functions"></a>Como definir um IP estático em Funções?

A implantação de uma função em um ambiente de serviço de aplicativo é atualmente a única maneira de ter um IP estático de entrada e saída para sua função. Para obter detalhes sobre como usar um Ambiente de Serviço de Aplicativo, comece com o artigo [Criar e use um balanceador de carga interna com um Ambiente de Serviço de Aplicativo](../app-service/environment/create-ilb-ase.md).

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>Como restringir o acesso à internet à minha função?

Você pode restringir o acesso à Internet de algumas maneiras:

* [Restrições de IP](../app-service/app-service-ip-restrictions.md): Restringir o tráfego de entrada ao seu aplicativo de função por faixa IP.
    * restrições de IP, você também é capaz de configurar [pontos finais de serviço,](../virtual-network/virtual-network-service-endpoints-overview.md)que restringem sua Função a aceitar apenas tráfego de entrada de uma rede virtual específica.
* Remoção de todos os gatilhos HTTP. Para alguns aplicativos, basta simplesmente evitar gatilhos HTTP e usar qualquer outra fonte de evento para acionar sua função.

Tenha em mente que o editor do portal Azure requer acesso direto à sua função de execução. Qualquer alteração de código através do portal Azure exigirá que o dispositivo que você está usando navegue pelo portal para ter seu IP listado na lista branca. Mas você ainda pode usar qualquer coisa a guia de recursos da plataforma com restrições de rede no local.

## <a name="how-do-i-restrict-my-function-app-to-a-virtual-network"></a>Como restringir meu aplicativo de função a uma rede virtual?

Você é capaz de restringir o tráfego **de entrada** de um aplicativo de função a uma rede virtual usando pontos finais [de serviço](./functions-networking-options.md#private-site-access). Essa configuração ainda permite que o aplicativo de função faça chamadas de saída para a internet.

A única maneira de restringir totalmente uma função de modo que todo o tráfego flua através de uma rede virtual é usar um ambiente de serviço de aplicativo balanceado internamente. Essa opção implanta seu site em uma infra-estrutura dedicada dentro de uma rede virtual e envia todos os gatilhos e tráfego através da rede virtual. 

Para obter detalhes sobre como usar um Ambiente de Serviço de Aplicativo, comece com o artigo [Criar e use um balanceador de carga interna com um Ambiente de Serviço de Aplicativo](../app-service/environment/create-ilb-ase.md).

## <a name="how-can-i-access-resources-in-a-virtual-network-from-a-function-app"></a>Como posso acessar recursos em uma rede virtual a partir de um aplicativo de função?

Você pode acessar recursos em uma rede virtual a partir de uma função em execução usando a integração de rede virtual. Para obter mais informações, consulte [integração de rede virtual](functions-networking-options.md#virtual-network-integration).

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>Como acessar recursos protegidos por pontos finais de serviço?

Usando a integração de rede virtual, você pode acessar recursos protegidos por ponto final de serviço a partir de uma função em execução. Para obter mais informações, consulte [integração de rede virtual](functions-networking-options.md#virtual-network-integration).

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-virtual-network"></a>Como posso ativar uma função a partir de um recurso em uma rede virtual?

Você é capaz de permitir que os gatilhos HTTP sejam chamados de uma rede virtual usando [pontos finais de serviço](./functions-networking-options.md#private-site-access). 

Você também pode ativar uma função de todos os outros recursos em uma rede virtual, implantando seu aplicativo de função em um plano Premium, plano de serviço de aplicativo ou ambiente de serviço de aplicativo. Consulte [gatilhos de rede virtual não-HTTP](./functions-networking-options.md#virtual-network-triggers-non-http) para obter mais informações

## <a name="how-can-i-deploy-my-function-app-in-a-virtual-network"></a>Como posso implantar meu aplicativo de função em uma rede virtual?

Implantar em um ambiente de serviço de aplicativo é a única maneira de criar um aplicativo de função que esteja totalmente dentro de uma rede virtual. Para obter detalhes sobre como usar um balanceador de carga interna com um ambiente de serviço de aplicativo, comece com o artigo [Criar e use um balanceador de carga interna com um Ambiente de Serviço de Aplicativo](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase).

Para cenários em que você precisa apenas de acesso unidirecional aos recursos de rede virtual ou isolamento de rede menos abrangente, consulte a visão geral da [rede Funções](functions-networking-options.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre networking e funções: 

* [Siga o tutorial sobre como começar com a integração de rede virtual](./functions-create-vnet.md)
* [Saiba mais sobre as opções de rede em Funções Do Azure](./functions-networking-options.md)
* [Saiba mais sobre a integração de rede virtual com o App Service and Functions](../app-service/web-sites-integrate-with-vnet.md)
* [Saiba mais sobre redes virtuais no Azure](../virtual-network/virtual-networks-overview.md)
* [Habilite mais recursos de rede e controle com ambientes de serviço de aplicativos](../app-service/environment/intro.md)
