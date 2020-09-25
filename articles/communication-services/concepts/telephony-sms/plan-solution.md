---
title: Planejar sua solução de telefonia e SMS dos Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services concept document
description: Saiba como planejar com eficiência o uso de números de telefone e telefonia.
author: stkozak
manager: rampras
services: azure-communication-services
ms.author: stkozak
ms.date: 06/23/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 39f88ab8b735438f60d8e20513ea5cbda43d41ee
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943127"
---
# <a name="plan-your-telephony-and-sms-solution"></a>Planejar sua solução de telefonia e SMS

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Este documento descreve os vários tipos de número e planos de telefonia que os Serviços de Comunicação do Azure têm a oferecer. Vamos percorrer os fluxos de decisão para ajudá-lo a selecionar um provedor de serviços de voz, tipos de números de telefone, planos e recursos disponibilizados por meio dos Serviços de Comunicação.

## <a name="about-phone-numbers-in-azure-communications-services"></a>Sobre números de telefone em Serviços de Comunicação do Azure

Os Serviços de Comunicação do Azure permitem que você use números de telefone para fazer e receber chamadas telefônicas e mensagens SMS. Esses números de telefone podem ser usados para configurar a ID de Chamadas em chamadas de saída feitas pelo seu serviço.
  
Se você não tem um número de telefone para importar para sua solução de Serviços de Comunicação, a maneira mais simples de começar é obter um novo número de telefone dos Serviços de Comunicação do Azure, o que pode ser feito em questão de minutos.

Se você tem um número de telefone que deseja continuar usando em sua solução (por exemplo, 1-800–EMPRESA), pode portar o número de telefone do provedor atual para os Serviços de Comunicação.

O diagrama a seguir ajuda você a navegar pelas opções disponíveis:

:::image type="content" source="../media/plan-solution/decision-tree-basic.png" alt-text="Diagrama que mostra como tomar uma decisão sobre seus números de telefone.":::

Agora, vamos examinar os tipos de número de telefone e os recursos disponíveis por meio dos Serviços de Comunicação. 

## <a name="microsoft-direct-offer-of-phone-numbers-and-capabilities"></a>Oferta direta da Microsoft de funcionalidades e números de telefone

Os Serviços de Comunicação do Azure fornecem excelente flexibilidade para os desenvolvedores. Na maioria dos números de telefone, permitimos que você configure um conjunto de planos "a la carte". Alguns desenvolvedores precisam de apenas um plano de chamada de entrada, enquanto outros podem optar por chamadas de entrada e de saída de planos de SMS. Esses planos podem ser selecionados à medida que você aluga e/ou porta seus números de telefone nos Serviços de Comunicação.

Os planos disponíveis dependem do país e do tipo de número de telefone em que você está operando. O diagrama a seguir representa o fluxo de decisão:    Os planos disponíveis dependem do país e do tipo de número de telefone em que você está operando.

<!-- Tami/team have rejected this multiple times despite updates, says it needs to be higher res - need to work with her to get approval for this image. Commenting out to move our staging forward. :::image type="content" source="../../media/example-decision-flow.png" alt-text="Example for the decision flow"::: -->

Para selecionar um tipo de número de telefone, vamos examinar o plano de numeração de telefone internacional.

### <a name="optional-reading-international-public-telecommunication-numbering-plan-e164"></a>Leitura opcional. Plano de numeração de telecomunicação pública internacional (E.164)

> [!NOTE]
> É recomendável examinar essas informações mesmo que você esteja familiarizado com o plano de numeração de telefone E.164, a fim de entender melhor os tipos de número e as funcionalidades oferecidas pela oferta direta dos Serviços de Comunicação do Azure.

O plano de numeração de telecomunicações pública internacional é definido na recomendação da ITU (União Internacional de Telecomunicações) E.164. Os números em conformidade são limitados a um máximo de 15 dígitos.

O número de telefone consiste em

-   Um prefixo "+"
-   Um prefixo de discagem internacional ou código de país/região (um, dois ou três dígitos) 
-   *(Opcional)* um código de destino nacional ou um plano de numeração, normalmente conhecido como código de área. O comprimento desse código depende do país. No Estados Unidos, ele tem três dígitos. Na Austrália e na Nova Zelândia, ele tem um dígito. Alemanha, Japão, México e outros países têm comprimentos variáveis de códigos de área. Por exemplo, na Alemanha, o código de área pode ter de dois a cinco dígitos, enquanto no Japão, pode ter de um a cinco dígitos.
-   Um número de assinante

> [!NOTE]
> A classificação acima não está totalmente em conformidade com o padrão E.164 da ITU e destina-se a fornecer uma descrição simplificada. Por exemplo, o número do assinante é subdividido dentro do padrão. Se você está interessado em aprender sobre o plano de numeração internacional em mais detalhes, o [padrão E.164 da ITU](https://www.itu.int/rec/T-REC-E.164) é um excelente ponto de partida.  

Aqui estão alguns exemplos que ajudarão você a entender melhor o plano de numeração:

Um número de telefone regional nos EUA:

:::image type="content" source="../media/plan-solution/regional-us.png" alt-text="Exemplo de um número de telefone regional nos EUA":::

Um número de telefone regional no Canadá:

:::image type="content" source="../media/plan-solution/regional-canada.png" alt-text="Exemplo de um número de telefone regional no Canadá":::

Um número de chamada gratuita na região América do Norte:

:::image type="content" source="../media/plan-solution/tollfree-us.png" alt-text="Exemplo de um número gratuito de chamada em América do Norte":::

Um número de telefone celular no Reino Unido:

:::image type="content" source="../media/plan-solution/mobile-uk.png" alt-text="Exemplo de um número de telefone celular no Reino Unido":::

Em seguida, vamos examinar os tipos de números de telefone específicos disponíveis nos Serviços de Comunicação do Azure.

## <a name="phone-number-types-in-azure-communication-services"></a>Tipos de número de telefone nos Serviços de Comunicação do Azure

A Microsoft oferece planos regionais e gratuitos para códigos curtos e números de telefone celular em alguns países, onde aplicável.

A tabela a seguir resume esses tipos de números de telefone: 

| Tipo de número de telefone | Exemplo                              | Disponibilidade do país    | Caso de uso comum                                                                                                     |
| ----------------- | ------------------------------------ | ----------------------- | ------------------------------------------------------------------------------------------------------------------- |
| Regional          | \+ 1 (código de área geográfica) XXX XX XX  | EUA, Canadá, Porto Rico | Como atribuir números de telefone a usuários em aplicativos ou atribuir a sistemas/bots de IVR (resposta por voz interativa) |
| Chamada gratuita         | \+ 1 (*código* de área gratuita) XXX XX XX | EUA, Canadá, Porto Rico | Como atribuir a sistemas IVR (resposta por voz interativa)/bots, aplicativos de SMS                                        |

## <a name="plans"></a>Planos 

Vamos examinar os recursos que você pode habilitar para seus números de telefone. Esses recursos variam por país devido a requisitos regulatórios. Os Serviços de Comunicação do Azure oferecem os seguintes recursos:

- **SMS de saída unidirecional**, útil para cenários de notificação e autenticação de dois fatores.
- **SMS de entrada e saída de duas vias**, um pacote predefinido que permite enviar e receber SMS como parte de um plano.
- **PSTN de chamada**, você pode selecionar uma chamada de entrada e usar a ID de Chamadas para fazer chamadas de saída.

## <a name="next-steps"></a>Próximas etapas

### <a name="quickstarts"></a>Inícios rápidos

- [Obter um número de telefone](../../quickstarts/telephony-sms/get-phone-number.md)
- [Fazer uma chamada](../../quickstarts/voice-video-calling/calling-client-samples.md)
- [Enviar um SMS](../../quickstarts/telephony-sms/send.md)

### <a name="conceptual-documentation"></a>Documentação conceitual

- [Conceitos de voz e vídeo](../voice-video-calling/about-call-types.md)
- [Fluxos de Chamadas e Fluxos de SMS](../call-flows.md)
- [Preços](../pricing.md)
