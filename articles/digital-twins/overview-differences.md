---
title: Diferenças da versão anterior
titleSuffix: Azure Digital Twins
description: Entenda o que mudou na nova versão dos Gêmeos Digitais do Azure
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: overview
ms.service: digital-twins
ms.openlocfilehash: 8a69e0bfe172d2cc7a291ee51ffa73ae7b484731
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84613289"
---
# <a name="how-is-the-new-azure-digital-twins-different-from-the-previous-version-2018"></a>Como os novos Gêmeos Digitais do Azure são diferentes da versão anterior (2018)?

A primeira versão prévia pública dos Gêmeos Digitais do Azure foi lançada em outubro de 2018. Embora os principais conceitos dessa versão anterior tenham passado para o novo serviço na versão prévia pública agora, muitas interfaces e detalhes de implementação foram alterados para tornar o serviço mais flexível e acessível. Essas alterações foram motivadas por comentários do cliente.

> [!IMPORTANT]
> À luz das funcionalidades expandidas do novo serviço, o serviço de Gêmeos Digitais do Azure anterior será desativado até o fim do ano 2020.

Se você usou a primeira versão dos Gêmeos Digitais do Azure durante a versão prévia pública anterior, use as informações e as melhores práticas neste artigo para aprender a trabalhar com o novo serviço e aproveitar os recursos dele.

## <a name="differences-by-topic"></a>Diferenças por tópico

O gráfico a seguir fornece uma exibição lado a lado dos conceitos que foram alterados entre a versão anterior do serviço e o novo serviço (atual).

| Tópico | Na versão anterior | Na nova versão |
| --- | --- | --- | --- |
| **Modelagem**<br>*Mais flexível* | A versão anterior foi criada em torno de espaços inteligentes, por isso vem com um vocabulário interno para edifícios. | Os novos Gêmeos Digitais do Azure são independentes de domínio. Você pode definir seu próprio vocabulário personalizado e modelos personalizados para sua solução, a fim de representar mais tipos de ambientes de maneiras mais flexíveis.<br><br>Saiba mais em [Conceitos: modelos personalizados](concepts-models.md). |
| **Topologia**<br>*Mais flexível*| A versão anterior dava suporte a uma estrutura de dados de árvore, personalizada para espaços inteligentes. Os gêmeos digitais foram conectados com relações hierárquicas. | Com a nova versão, seus gêmeos digital pode ser conectado a topologias de grafo arbitrárias, organizadas da forma como você desejar. Isso proporciona mais flexibilidade para expressar as relações complexas do mundo real.<br><br>Saiba mais em [Conceitos: gêmeos digitais e o grafo de gêmeos](concepts-twins-graph.md). |
| **Computação**<br>*Mais avançada e mais flexível* | Na versão anterior, a lógica para processar eventos e telemetria foi definida em UDFs (funções definidas pelo usuário) JavaScript. A depuração com UDFs era limitada. | A nova versão tem um modelo de computação aberta: você fornece lógica personalizada anexando recursos de computação externos, como o [Azure Functions](../azure-functions/functions-overview.md). Isso permite que você use uma linguagem de programação de sua escolha, acesse bibliotecas de código personalizadas sem restrição e aproveite os recursos de desenvolvimento e depuração que o serviço externo pode ter.<br><br>Saiba mais em [Como configurar uma função do Azure para processar dados](how-to-create-azure-function.md). |
| **Gerenciamento de dispositivo com o Hub IoT**<br>*Mais acessível* | A versão anterior tinha dispositivos gerenciados com uma instância do [Hub IoT](../iot-hub/about-iot-hub.md) que era interna ao serviço de Gêmeos Digitais do Azure. Esse hub integrado não estava totalmente acessível para os desenvolvedores. | Na nova versão, você “traz seu próprio” hub IoT, anexando uma instância do Hub IoT criada independentemente (junto com dispositivos que ela já gerencia). Isso dá a você acesso completo aos recursos do Hub IoT e coloca você no controle do gerenciamento de dispositivos.<br><br>Saiba mais em [Como ingerir telemetria do Hub IoT](how-to-ingest-iot-hub-data.md). |
| **Segurança**<br>*Mais padrão* | A versão anterior tinha funções predefinidas que você poderia usar para gerenciar o acesso à sua instância. | A nova versão se integra com o mesmo serviço de back-end de [RBAC (controle de acesso baseado em função)](../role-based-access-control/overview.md) do Azure que outros serviços do Azure usam. Isso pode simplificar a autenticação entre outros serviços do Azure em sua solução, como o Hub IoT, o Azure Functions, a Grade de Eventos e muito mais.<br>Com o RBAC, você ainda pode usar funções predefinidas ou pode criar e configurar funções personalizadas.<br><br>Saiba mais em [Conceitos: segurança para soluções dos Gêmeos Digitais do Azure](concepts-security.md). |
| **Escalabilidade**<br>*Maior* | A versão anterior tinha limitações de escala para dispositivos, mensagens, grafos e unidades de escala. Havia suporte apenas para uma instância dos Gêmeos Digitais do Azure por assinatura.  | A nova versão se baseia em uma nova arquitetura com escalabilidade aprimorada e tem maior capacidade de computação. Ela também dá suporte a 10 instâncias por região, por assinatura.<br><br>Confira [Referência: limites de serviço da versão prévia pública](reference-service-limits.md) para obter detalhes dos limites em versão prévia pública agora. |

## <a name="service-limits-in-public-preview"></a>Limites de serviço em versão prévia pública

Para obter uma lista de limites de Gêmeos Digitais do Azure durante essa versão prévia pública, confira [Referência: limites de serviço da versão prévia pública](reference-service-limits.md).

## <a name="next-steps"></a>Próximas etapas

Em seguida, aprofunde-se no trabalho com os Gêmeos Digitais do Azure com o primeiro tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Codificar um aplicativo cliente](tutorial-code.md)