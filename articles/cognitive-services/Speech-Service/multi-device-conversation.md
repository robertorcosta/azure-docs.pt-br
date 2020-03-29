---
title: Conversa ção multi-dispositivos (Visualização) - Serviço de fala
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: dapine
ms.openlocfilehash: b3802e66b0ba5a68c898e69ec64b01edce1541c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371351"
---
# <a name="what-is-multi-device-conversation-preview"></a>O que é Conversação multi-dispositivo (Preview)?

**A conversa com vários dispositivos** facilita a criação de uma conversa de fala ou texto entre vários clientes e a coordenação das mensagens enviadas entre eles.

Com **conversação com vários dispositivos,** você pode:

- Conecte vários clientes à mesma conversa e gerencie o envio e recebimento de mensagens entre eles.
- Transtransccere facilmente o áudio de cada cliente e envie a transcrição para os outros, com tradução opcional.
- Envie facilmente mensagens de texto entre clientes, com tradução opcional.

Você pode construir um recurso ou solução que funcione em uma matriz de dispositivos. Cada dispositivo pode enviar mensagens independentemente (transcrições de áudio ou mensagens instantâneas) para todos os outros dispositivos.

Considerando que [**a Transcrição de Conversação**](conversation-transcription.md) funciona em um único dispositivo com um conjunto de microfones multicanal, **o Multi-device Conversation** é adequado para cenários com vários dispositivos, cada um com um único microfone.

>[!IMPORTANT]
> A conversa com vários dispositivos **não** suporta o envio de arquivos de áudio entre clientes: apenas a transcrição e/ou tradução.

## <a name="key-features"></a>Principais recursos

- **Transcrição** em tempo real – Todos receberão uma transcrição da conversa, para que possam acompanhar o texto em tempo real ou guardá-lo para depois.
- **Tradução em tempo real** – Com mais de 60 [idiomas suportados](language-support.md#text-languages) para tradução de texto, os usuários podem traduzir a conversa para seus idiomas preferidos.
- **Transcrições legíveis** – A transcrição e a tradução são fáceis de seguir, com pontuação e quebras de sentenças.
- **Entrada de voz ou texto** – Cada usuário pode falar ou digitar em seu próprio dispositivo, dependendo dos recursos de suporte ao idioma habilitados para o idioma escolhido pelo participante. Consulte o suporte ao [idioma](language-support.md#speech-to-text).
- **Relé de mensagens** - O serviço de conversação multidispositivos distribuirá mensagens enviadas por um cliente para todos os outros, no idioma de sua escolha.
- **Identificação da mensagem** – Todas as mensagens que os usuários receberem na conversa serão marcadas com o apelido do usuário que a enviou.

## <a name="use-cases"></a>Casos de uso

### <a name="lightweight-conversations"></a>Conversas leves

Criar e participar de uma conversa é fácil. Um usuário agirá como o 'host' e criará uma conversa, que gera um código de conversa de cinco letras aleatório e um código QR. Todos os outros usuários podem participar da conversa digitando o código de conversação ou digitalizando o código QR. 

Uma vez que os usuários entram através do código de conversação e não são obrigados a compartilhar informações de contato, é fácil criar conversas rápidas e in loco.

### <a name="inclusive-meetings"></a>Reuniões inclusivas

A transcrição e a tradução em tempo real podem ajudar a tornar as conversas acessíveis para pessoas que falam diferentes idiomas e/ou são surdas ou difíceis de ouvir. Cada pessoa também pode participar ativamente da conversa, falando seu idioma preferido ou enviando mensagens instantâneas.

### <a name="presentations"></a>Apresentações

Você também pode fornecer legendas para apresentações e palestras tanto na tela quanto nos próprios dispositivos dos membros da platéia. Depois que o público se junta ao código de conversação, eles podem ver a transcrição em seu idioma preferido, em seu próprio dispositivo.

> [!NOTE]
> Para ver um exemplo, confira [O Tradutor](https://www.microsoft.com/translator/apps/presentation-translator/)de Apresentação , um complemento do PowerPoint que usa o serviço de conversação de vários dispositivos. Você pode baixá-lo [aqui](https://www.microsoft.com/download/details.aspx?id=55024).

## <a name="how-it-works"></a>Como ele funciona

Todos os clientes usarão o Speech SDK para criar ou participar de uma conversa. O Speech SDK interage com o serviço de conversação multidispositivos, que gerencia a vida útil de uma conversa, incluindo a lista de participantes, os idiomas escolhidos de cada cliente e as mensagens enviadas.  

Cada cliente pode enviar áudio ou mensagens instantâneas. O serviço usará o reconhecimento de voz para converter áudio em texto e enviar mensagens instantâneas como está. Se os clientes escolherem idiomas diferentes, o serviço traduzirá todas as mensagens para os idiomas especificados de cada cliente.

![Diagrama de visão geral de conversação de vários dispositivos](media/scenarios/multi-device-conversation.png)

## <a name="overview-of-conversation-host-and-participant"></a>Visão geral da conversação, do host e do participante

Uma **conversa** é uma sessão que um usuário inicia para que os outros usuários participantes participem. Todos os clientes se conectam à conversa usando o código de **conversa de**cinco letras .

Cada conversa cria metadados que incluem:
-    Carimbos de quando a conversa começou e terminou
-    Lista de todos os participantes da conversa, que inclui o apelido escolhido de cada usuário e a linguagem primária para a entrada de fala ou texto.


Existem dois tipos de usuários em uma conversa: **host** e **participante**.

O **host** é o usuário que inicia uma conversa, e que age como administrador dessa conversa.
- Cada conversa só pode ter um hospedeiro
- O anfitrião deve estar conectado à conversa durante a conversa. Se o apresentador deixar a conversa, a conversa terminará para todos os outros participantes.
- O host tem alguns controles extras para gerenciar a conversa: 
    - Bloquear a conversa - impedir que participantes adicionais participem
    - Silenciar todos os participantes - impedir que outros participantes enviem mensagens para a conversa, seja transcrita da fala ou mensagens instantâneas
    - Participantes individuais mudos
    - Desomute todos os participantes
    - Participantes individuais unmute

Um **participante** é um usuário que participa de uma conversa.
- O participante pode sair e voltar a participar da mesma conversa a qualquer momento, sem terminar a conversa para outros participantes.
- Os participantes não podem bloquear a conversa ou silenciar/desomutar outros

> [!NOTE]
> Cada conversa pode ter até 100 participantes, dos quais 10 podem falar simultaneamente a qualquer momento.

## <a name="language-support"></a>Suporte ao idioma

Ao criar ou participar de uma conversa, cada usuário deve escolher um **idioma principal**: o idioma em que falará e enviará mensagens instantâneas, e também o idioma em que verá as mensagens de outros usuários.

Existem dois tipos de idiomas: **fala-para-texto** e **apenas texto:**
- Se o usuário escolher um idioma **de fala para texto** como seu idioma principal, então ele poderá usar a entrada de fala e texto na conversa.

- Se o usuário escolher um idioma **somente texto,** então ele só poderá usar a entrada de texto e enviar mensagens instantâneas na conversa. Idiomas somente texto são os idiomas que são suportados para tradução de texto, mas não de fala para texto. Você pode ver idiomas disponíveis na página de suporte ao [idioma.](supported-languages.md)

Além de seu idioma principal, cada participante também pode especificar idiomas adicionais para traduzir a conversa.

Abaixo está um resumo do que o usuário será capaz de fazer em uma conversa com vários dispositivos, com base na linguagem primária escolhida.


| O que o usuário pode fazer na conversa | Conversão de fala em texto | Somente texto |
|-----------------------------------|----------------|------|
| Use a entrada de fala | ✔️ | ❌ |
| Enviar mensagens instantâneas | ✔️ | ✔️ |
| Traduza a conversa | ✔️ | ✔️ |

> [!NOTE]
> Para obter listas de idiomas de tradução de texto e texto disponíveis, consulte [idiomas suportados](supported-languages.md).



## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Traduzir conversas em tempo real](quickstarts/multi-device-conversation.md)
