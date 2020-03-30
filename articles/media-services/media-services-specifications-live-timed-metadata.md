---
title: Azure Media Services - Sinalização de metadados cronometrados em transmissão ao vivo
description: Esta especificação descreve métodos para sinalizar metadados cronometrados ao ingerir e transmitir para o Azure Media Services. Isso inclui suporte para sinais genéricos de metadados cronometrados (ID3), bem como sinalização SCTE-35 para inserção de anúncios e sinalização de condição de emenda.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: johndeu
ms.assetid: 265b94b1-0fb8-493a-90ec-a4244f51ce85
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: johndeu
ms.openlocfilehash: 551fb0cb9f3745a62d5d84f2c4878bbbbe5ad9a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137315"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>A sinalização atingiu o tempo de metadados na transmissão ao vivo 

Última atualização: 2019-08-22

### <a name="conformance-notation"></a>Notação de conformidade

As palavras-chave “DEVE”, “NÃO DEVE”, “OBRIGATÓRIO”, “RECOMENDADO”, “PODE” E “OPCIONAL” neste documento devem ser interpretadas conforme descritas na RFC 2119

## <a name="1-introduction"></a>1. Introdução 

A fim de sinalizar a inserção de anúncios ou eventos de metadados personalizados em um player cliente, as emissoras geralmente fazem uso de metadados cronometrados incorporados no vídeo. Para habilitar esses cenários, o Media Services fornece suporte para o transporte de metadados cronometrados do ponto de ingestão do canal de transmissão ao vivo para o aplicativo do cliente.
Esta especificação descreve vários modos que são suportados pelos Serviços de Mídia para metadados cronometrados em sinais de transmissão ao vivo.

1. [SCTE-35] sinalização que está em conformidade com as normas descritas por [SCTE-35], [SCTE-214-1], [SCTE-214-3] e [RFC8216]

2. [SCTE-35] sinalização que está em conformidade com a especificação legado [Adobe-Primetime] para sinalização de anúnciortmp.
   
3. Um modo de sinalização de metadados cronometrado genérico, para mensagens que **NÃO** são [SCTE-35] e poderiam transportar [ID3v2] ou outros esquemas personalizados definidos pelo desenvolvedor do aplicativo.

## <a name="11-terms-used"></a>1.1 Termos Utilizados

| Termo                | Definição                                                                                                                                                                                                                                    |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Quebra de anúncios            | Um local ou ponto no tempo onde um ou mais anúncios podem ser agendados para entrega; mesmo que aproveitar e oportunidade de colocação.                                                                                                                     |
| Serviço de decisão de anúncios | serviço externo que decide quais anúncios e durações serão mostrados ao usuário. Os serviços são normalmente prestados por um parceiro e estão fora de escopo para este documento.                                                                    |
| Cue                 | Indicação de tempo e parâmetros da próxima quebra de anúncio. Observe que as pistas podem indicar um switch pendente para uma quebra de anúncio, aguardando a troca para o próximo anúncio dentro de uma pausa de anúncio e aguardando a troca de uma quebra de anúncio para o conteúdo principal.           |
| Empacotador            | O Azure Media Services "Streaming Endpoint" fornece recursos dinâmicos de embalagem para DASH e HLS e é referido como um "Packager" no setor de mídia.                                                                              |
| Tempo da apresentação   | O horário em que um evento é apresentado em um espectador. O horário representa o momento na linha do tempo da mídia em que um espectador vê o evento. Por exemplo, a hora de apresentação de uma mensagem de comando splice_info() SCTE-35 é o splice_time(). |
| Horário de chegada        | O horário em que uma mensagem de evento chega. O horário é normalmente diferente do horário da apresentação do evento, pois mensagens de evento são enviadas antes do horário da apresentação do evento.                                                    |
| Faixa esparsa        | Faixa de mídia que não é contínua e é sincronizada por horário com um pai ou faixa de controle.                                                                                                                                                  |
| Origem              | O serviço de Streaming de Mídia do Azure                                                                                                                                                                                                             |
| Coletor do canal        | O serviço de Live Streaming de Mídia do Azure                                                                                                                                                                                                        |
| HLS                 | Protocolo Apple HTTP Live Streaming                                                                                                                                                                                                            |
| DASH                | Dynamic Adaptive Streaming Over HTTP                                                                                                                                                                                                          |
| Smooth              | Protocolo Smooth Streaming                                                                                                                                                                                                                     |
| MPEG2-TS            | Fluxos de Transporte de MPEG 2                                                                                                                                                                                                                      |
| RTMP                | Protocolo de multimídia em tempo real                                                                                                                                                                                                                 |
| uimsbf              | Inteiro sem sinal, bit mais significativo primeiro.                                                                                                                                                                                                 |

---

## <a name="12-normative-references"></a>1.2 Referências Normativas

Os documentos a seguir contêm disposições que, por meio de referência neste texto, constituem disposições deste documento. Todos os documentos estão sujeitos à revisão pelos órgãos de normas, e os leitores são encorajados a investigar a possibilidade de aplicar as edições mais recentes dos documentos listados abaixo. Os leitores também são lembrados de que as edições mais recentes dos documentos referenciados podem não ser compatíveis com esta versão da especificação de metadados cronometrados para o Azure Media Services.


| Standard          | Definição                                                                                                                                                                                                     |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Horário nobre da Adobe] | [Especificação de sinalização de inserção de programa digital em horário nobre 1.2](https://www.adobe.com/content/dam/acom/en/devnet/primetime/PrimetimeDigitalProgramInsertionSignalingSpecification.pdf)                       |
| [Adobe-Flash-AS]  | [Referência de linguagem FLASH ActionScript](https://help.adobe.com/archive/en_US/as2/flashlite_2.x_3.x_aslr.pdf)                                                                                                   |
| [AMF0]            | ["Formato de mensagem de ação AMF0"](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)                                                                                                              |
| [DASH-IF-IOP]     | Dash Industry Forum Interop Guidance v 4.2[https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html)    |
| [HLS-TMD]         | Metadados cronometrados para HTTP Live Streaming -[https://developer.apple.com/streaming](https://developer.apple.com/streaming)                                                                                        |
| [CMAF-ID3]        | [Metadados cronometrados no formato de aplicação de mídia comum (CMAF)](https://github.com/AOMediaCodec/id3-emsg)                                                                                                        |
| [ID3v2]           | ID3 Tag versão 2.4.0[http://id3.org/id3v2.4.0-structure](http://id3.org/id3v2.4.0-structure)                                                                                                                |
| [ISO-14496-12]    | ISO/IEC 14496-12: Parte 12 ISO formato de arquivo de mídia base, QuartaEdição 2012-07-15                                                                                                                                 |
| [MPEGDASH]        | Tecnologia da informação - Fluxo adaptativo dinâmico através de HTTP (DASH) - Parte 1: Descrição de apresentação de mídia e formatos de segmento. Maio de 2014. Publicado. URL: https://www.iso.org/standard/65274.html         |
| [MPEGCMAF]        | Tecnologia da informação -- Formato de aplicação multimídia (MPEG-A) -- Parte 19: Formato de aplicação de mídia comum (CMAF) para mídia segmentada. Janeiro de 2018. Publicado. URL: https://www.iso.org/standard/71975.html |
| [MPEGCENC]        | Tecnologia da informação - Tecnologias de sistemas MPEG - Parte 7: Criptografia comum em arquivos de formato de mídia base ISO. Fevereiro de 2016. Publicado. URL: https://www.iso.org/standard/68042.html                   |
| [MS-SSTR]         | ["Microsoft Smooth Streaming Protocol", 15 de maio de 2014](https://docs.microsoft.com/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251)                                                     |
| [MS-SSTR-Ingest]  | [Azure Media Services Fragmentado MP4 Live Ingest Specification](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview)                                                      |
| [RFC8216]         | R. Pantos, Ed.; W. May. HTTP Transmissão ao vivo. Agosto de 2017. Informativo. [https://tools.ietf.org/html/rfc8216](https://tools.ietf.org/html/rfc8216)                                                            |
| [RFC4648]         | As codificações de dados Base16, Base32 e Base64 -[https://tools.ietf.org/html/rfc4648](https://tools.ietf.org/html/rfc4648)                                                                                     |
| [RTMP]            | ["Protocolo de Mensagens em Tempo Real da Adobe", 21 de dezembro de 2012](https://www.adobe.com/devnet/rtmp.html)                                                                                                            |
| [SCTE-35-2019]    | SCTE 35: 2019 - Mensagem de inserção de inserção de programa digital para cabo -https://www.scte.org/SCTEDocs/Standards/ANSI_SCTE%2035%202019r1.pdf                                                                       |
| [SCTE-214-1]      | SCTE 214-1 2016 – MPEG DASH para serviços de cabo baseados em IP Parte 1: Restrições e Extensões mpd                                                                                                                 |
| [SCTE-214-3]      | SCTE 214-3 2015 MPEG DASH para serviços de cabo baseados em IP Parte 3: Perfil DASH/FF                                                                                                                                  |
| [SCTE-224]        | SCTE 224 2018r1 – Agendamento de Eventos e Interface de Notificação                                                                                                                                                  |
| [SCTE-250]        | API de Gerenciamento de Eventos e Sinalização (ESAM)                                                                                                                                                                      |

---


## <a name="2-timed-metadata-ingest"></a>2. Metadados cronometrados ingeridos

O Azure Media Services suporta metadados em banda em tempo real para protocolos [RTMP] e Smooth Streaming [MS-SSTR-Ingest]. Metadados em tempo real podem ser usados para definir eventos personalizados, com seus próprios esquemas personalizados exclusivos (JSON, Binary, XML), bem como formatos definidos pela indústria, como ID3, ou SCTE-35 para sinalização de anúncios em uma transmissão. 

Este artigo fornece os detalhes de como enviar sinais de metadados personalizados usando os protocolos de ingestão suportados do Azure Media Services. O artigo também explica como os manifestos para HLS, DASH e Smooth Streaming são decorados com os sinais de metadados cronometrados, bem como como ele é transportado em banda quando o conteúdo é entregue usando os segmentos CMAF (MP4 fragmentos) ou Transport Stream (TS) para HLS. 

Os cenários comuns de casos de uso para metadados cronometrados incluem:

 - Sinais de anúncio SCTE-35 para desencadear quebras de anúncios em um evento ao vivo ou transmissão linear
 - Metadados personalizados do ID3 que podem desencadear eventos em um aplicativo cliente (navegador, iOS ou Android)
 - Metadados JSON, Binário ou XML definidos medida para desencadear eventos em um aplicativo cliente
 - Telemetria de um codificador ao vivo, câmera IP ou drone
 - Eventos de uma câmera IP como Motion, detecção facial, etc.
 - Informações de posição geográfica de uma câmera de ação, drone ou dispositivo móvel
 - Letras de música
 - Limites do programa em uma transmissão ao vivo linear
 - Imagens ou metadados aumentados a serem exibidos em uma transmissão ao vivo
 - Pontuações esportivas ou informações sobre o relógio do jogo
 - Pacotes de publicidade interativos a serem exibidos ao lado do vídeo no navegador
 - Quizzes ou pesquisas
  
Os Eventos ao Vivo e o Packager do Azure Media Services são capazes de receber esses sinais de metadados cronometrados e convertê-los em um fluxo de metadados que podem chegar a aplicativos clientes usando protocolos baseados em padrões como HLS e DASH.


## <a name="21-rtmp-timed-metadata"></a>Metadados cronometrados de 2.1 RTMP

O protocolo [RTMP] permite que sinais de metadados cronometrados sejam enviados para vários cenários, incluindo metadados personalizados e sinais de anúncios SCTE-35. 

Os sinais de publicidade (mensagens de sinalização) são enviados como mensagens de sinalização [AMF0] incorporadas no fluxo [RTMP]. As mensagens de sinalização podem ser enviadas algum tempo antes do evento real ou [SCTE35] sinal de emenda de anúncio precisa ocorrer. Para apoiar esse cenário, o carimbo de apresentação real do evento é enviado dentro da mensagem de sinalização. Para obter mais informações, consulte [AMF0].

Os seguintes comandos [AMF0] são suportados pelo Azure Media Services para ingestão de RTMP:

- **onUserDataEvent** - usado para metadados personalizados ou metadados cronometrados [ID3v2]
- **onAdCue** - usado principalmente para sinalizar uma oportunidade de colocação de anúncios na transmissão ao vivo. Duas formas da deixa são suportadas, um modo simples e um modo "SCTE-35". 
- **onCuePoint** - suportado por certos codificadores de hardware no local, como o codificador Elemental Live, para sinalizar mensagens [SCTE35]. 
  

A tabela a seguir descreve o formato da carga de mensagem AMF que os Serviços de Mídia ingerirão para os modos de mensagem "simples" e [SCTE35].

O nome da mensagem [AMF0] pode ser usado para diferenciar fluxos de eventos múltiplos do mesmo tipo.  Para ambas as mensagens [SCTE-35] e o modo "simples", o nome da mensagem AMF DEVE ser "onAdCue" conforme exigido na especificação [Adobe-Primetime].  Quaisquer campos não listados abaixo serão ignorados pelos Serviços de Mídia do Azure.

## <a name="211-rtmp-with-custom-metadata-using-onuserdataevent"></a>2.1.1 RTMP com metadados personalizados usando "onUserDataEvent"

Se você quiser fornecer feeds de metadados personalizados do seu codificador upstream, câmera IP, drone ou dispositivo usando o protocolo RTMP, use o tipo de comando de mensagem de dados "onUserDataEvent" [AMF0].

O comando de mensagem de dados **"onUserDataEvent"** deve levar uma carga de mensagens com a seguinte definição a ser capturada pelos Serviços de Mídia e embalada no formato de arquivo em banda, bem como os manifestos para HLS, DASH e Smooth Streaming.
Recomenda-se enviar mensagens de metadados cronometrados não com mais freqüência do que uma vez a cada 0,5 segundos (500ms) ou problemas de estabilidade com a transmissão ao vivo podem ocorrer. Cada mensagem pode agregar metadados de vários quadros se você precisar fornecer metadados em nível de quadro. Se você estiver enviando fluxos multibitrate, é recomendável que você também forneça os metadados em uma única taxa de bits apenas para reduzir a largura de banda e evitar interferências no processamento de vídeo/áudio. 

A carga para o **"onUserDataEvent"** deve ser uma mensagem de formato EventStream XML [MPEGDASH]. Isso facilita a passagem em esquemas personalizados definidos que podem ser transportados em cargas 'emsg' em banda para o conteúdo CMAF [MPEGCMAF] que é fornecido sobre protocolos HLS ou DASH. Cada mensagem DASH Event Stream contém um esquemaIdUri que funciona como um identificador de esquema de mensagem URN e define a carga da mensagem. Alguns esquemashttps://aomedia.org/emsg/ID3como " para [ID3v2], ou **urna:scte:scte35:2013:bin** para [SCTE-35] são padronizados pelos consórcios industriais para interoperabilidade. Qualquer provedor de aplicativo pode definir seu próprio esquema personalizado usando uma URL que eles controlam (domínio próprio) e pode fornecer uma especificação nessa URL se quiser. Se um jogador tem um manipulador para o esquema definido, então esse é o único componente que precisa entender a carga e o protocolo.

O esquema para a carga de pagamento [MPEG-DASH] EventStream XML é definido como (trecho de DASH ISO-IEC-23009-1-3rd Edition). Observe que apenas um "EventType" por "EventStream" é suportado neste momento. Apenas o primeiro elemento **Evento** será processado se vários eventos forem fornecidos no **EventStream**.

```xml
  <!-- Event Stream -->
  <xs:complexType name="EventStreamType">
    <xs:sequence>
      <xs:element name="Event" type="EventType" minOccurs="0" maxOccurs="unbounded"/>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute ref="xlink:href"/>
    <xs:attribute ref="xlink:actuate" default="onRequest"/>
    <xs:attribute name="schemeIdUri" type="xs:anyURI" use="required"/>
    <xs:attribute name="value" type="xs:string"/>
    <xs:attribute name="timescale" type="xs:unsignedInt"/>
  </xs:complexType>
  <!-- Event  -->
  <xs:complexType name="EventType">
    <xs:sequence>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute name="presentationTime" type="xs:unsignedLong" default="0"/>
    <xs:attribute name="duration" type="xs:unsignedLong"/>
    <xs:attribute name="id" type="xs:unsignedInt"/>
    <xs:attribute name="contentEncoding" type="ContentEncodingType"/>
    <xs:attribute name="messageData" type="xs:string"/>
    <xs:anyAttribute namespace="##other" processContents="lax"/>
  </xs:complexType>
```


### <a name="example-xml-event-stream-with-id3-schema-id-and-base64-encoded-data-payload"></a>Exemplo XML Event Stream com ID esquema ID iD e carga de dados codificada base64.  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="https://aomedia.org/emsg/ID3">
         <Event contentEncoding="Base64">
          -- base64 encoded ID3v2 full payload here per [CMAF-TMD] --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-base64-encoded-binary-data"></a>Exemplo de Fluxo de Eventos com ID de esquema personalizado e dados binários codificados com base64  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:binary">
         <Event contentEncoding="Base64">
          -- base64 encoded custom binary data message --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-custom-json"></a>Exemplo de Fluxo de Eventos com ID de esquema personalizado e JSON personalizado  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:JSON">
         <Event>
          [
            {"key1" : "value1"},
            {"key2" : "value2"}
          ]
         </Event>
   <EventStream>
```

### <a name="built-in-supported-scheme-id-uris"></a>URIs de ID de esquema incorporado susterizados
| Esquema ID URI                 | Descrição                                                                                                                                                                                                                                          |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| https:\//aomedia.org/emsg/ID3 | Descreve como os metadados [ID3v2] podem ser transportados como metadados cronometrados em um MP4 fragmentado compatível com CMAF [MPEGCMAF]. Para obter mais informações, consulte os [Metadados cronometrados no formato de aplicação de mídia comum (CMAF)](https://github.com/AOMediaCodec/id3-emsg) |

### <a name="event-processing-and-manifest-signaling"></a>Processamento de eventos e sinalização de manifesto

No recebimento de um evento **"onUserDataEvent"** válido, o Azure Media Services procurará uma carga XML válida que corresponda ao EventStreamType (definido em [MPEGDASH] ), analise a carga XML e converta-a em uma caixa de fragmento mp4 'emsg' de fragmento [MPEGCMAF] para armazenamento no arquivo ao vivo e transmissão ao Media Services Packager.   O Empacoter detectará a caixa 'emsg' na transmissão ao vivo e:

- a "pacote dinâmico" em segmentos TS para entrega a clientes HLS em conformidade com a especificação de metadados cronometrado hls [HLS-TMD], ou
- b Passá-lo para entrega em fragmentos CMAF via HLS ou DASH, ou 
- c Convertê-lo em um sinal de faixa esparso para entrega via Smooth Streaming [MS-SSTR].

Além dos pacotes CMAF ou Pes de formato em banda para HLS, os manifestos para DASH (MPD) e Smooth Streaming conterão uma referência aos fluxos de eventos em banda (também conhecidos como faixa de fluxo esparsa no Smooth Streaming). 

Eventos individuais ou suas cargas de dados NÃO são saída diretamente nos manifestos HLS, DASH ou Smooth. 

### <a name="additional-informational-constraints-and-defaults-for-onuserdataevent-events"></a>Restrições e padrões informativos adicionais para eventos onUserDataEvent

- Se a escala de tempo não estiver definida no elemento EventStream, a escala de tempo RTMP de 1 kHz será usada por padrão
- A entrega de uma mensagem onUserDataEvent é limitada a uma vez a cada 500m no máximo. Se você enviar eventos com mais freqüência, pode afetar a largura de banda e a estabilidade da transmissão ao vivo

## <a name="212-rtmp-ad-cue-signaling-with-onadcue"></a>Sinalização de anúncio rtmp 2.1.2 com "onAdCue"

O Azure Media Services pode ouvir e responder a vários tipos de mensagens [AMF0] que podem ser usadas para sinalizar vários metadados sincronizados em tempo real na transmissão ao vivo.  A especificação [Adobe-Primetime] define dois tipos de sinalização chamados de modo "simples" e "SCTE-35". Para o modo "simples", os Serviços de Mídia suportam uma única mensagem de sinalização AMF chamada "onAdCue" usando uma carga que corresponde à tabela abaixo definida para o sinal "Modo Simples".  

A seção a seguir mostra a carga útil do modo RTMP "simples", que pode ser usado para sinalizar um sinal de anúncio básico "spliceOut" que será levado ao manifesto do cliente para HLS, DASH e Microsoft Smooth Streaming. Isso é muito útil para cenários em que o cliente não tem um sistema de sinalização de anúncio scte-35 complexo baseado em SCTE-35, e está usando um codificador básico no local para enviar a mensagem de sinalização através de uma API. Normalmente, o codificador local suportará uma API baseada em REST para acionar esse sinal, que também "condicionará" o fluxo de vídeo, inserindo um quadro IDR no vídeo e iniciando um novo GOP.

## <a name="213--rtmp-ad-cue-signaling-with-onadcue---simple-mode"></a>Sinalização de anúncio rtmp 2.1.3 com "onAdCue" - Modo simples

| Nome do campo | Tipo de campo | Obrigatório? | Descrições                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| type       | String     | Obrigatório  | A mensagem do evento.  Deve ser "SpliceOut" para designar uma junção de modo simples.                                                                                                                                                                                                         |
| id         | String     | Obrigatório  | Um identificador exclusivo que descreve a junção ou o segmento. Identifica esta instância da mensagem                                                                                                                                                                                       |
| duration   | Número     | Obrigatório  | A duração da junção. As unidades são frações de segundo.                                                                                                                                                                                                                           |
| elapsed    | Número     | Opcional  | Quando o sinal está sendo repetido para dar suporte a ajuste, este campo deve ser a quantidade de horas de apresentação decorrida desde que a junção começou. As unidades são frações de segundo. Ao usar o modo simples, esse valor não deve exceder a duração original da junção. |
| time       | Número     | Obrigatório  | Deverá ser o horário da junção, no horário da apresentação. As unidades são frações de segundo.                                                                                                                                                                                                |

---
 
#### <a name="example-mpeg-dash-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Exemplo de saída do manifesto MPEG DASH ao usar o modo simples DoABE RTMP

Veja o exemplo [3.3.2.1 MPEG DASH .mpd EventStream usando o modo simples da Adobe](#3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode)

Veja o exemplo [3.3.3.1 DASH manifesto com um único período e modo simples Adobe](#3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals)

#### <a name="example-hls-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Exemplo de saída de manifesto HLS ao usar o modo simples Adobe RTMP

Veja o exemplo [do manifesto 3.2.2 HLS usando o modo simples da Adobe e a tag EXT-X-CUE](#322-apple-hls-with-adobe-primetime-ext-x-cue-legacy)

## <a name="214-rtmp-ad-cue-signaling-with-onadcue---scte-35-mode"></a>Sinalização de anúncio rtmp 2.1.4 com "onAdCue" - Modo SCTE-35

Quando você está trabalhando com um fluxo de trabalho de produção de transmissão mais avançado que requer que a mensagem de carga completa SCTE-35 seja levada para o manifesto HLS ou DASH, é melhor usar o "Modo SCTE-35" da especificação [Adobe-Primetime].  Este modo suporta sinais SCTE-35 em banda sendo enviados diretamente para um codificador ao vivo no local, que codifica os sinais no fluxo RTMP usando o "Modo SCTE-35" especificado na especificação [Adobe-Primetime]. 

Normalmente, as mensagens SCTE-35 só podem aparecer em entradas TS (Transport Stream, fluxo de transporte MPEG-2) em um codificador no local. Verifique com o fabricante do codificador detalhes sobre como configurar uma entrada de fluxo de transporte que contenha SCTE-35 e habilite-o para passagem para RTMP no modo Adobe SCTE-35.

Neste cenário, a seguinte carga deve ser enviada do codificador local usando o tipo de mensagem **"onAdCue"** [AMF0].

| Nome do campo | Tipo de campo | Obrigatório? | Descrições                                                                                                                                                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| advertência        | String     | Obrigatório  | A mensagem do evento.  Para as mensagens [SCTE-35], este DEVE ser o splice_info_section binário codificado base64 [RFC4648] para que as mensagens sejam enviadas aos clientes HLS, Smooth e Dash.                                                                                                                                                                                                                               |
| type       | String     | Obrigatório  | Uma URN ou URL que identifica o esquema da mensagem. Para as mensagens [SCTE-35], este **deve** ser **"scte35"** para que as mensagens sejam enviadas aos clientes HLS, Smooth e Dash, em conformidade com [Adobe-Primetime]. Opcionalmente, a URN "urn:scte:scte35:2013:bin" também pode ser usada para sinalizar uma mensagem [SCTE-35].                                                                                                        |
| id         | String     | Obrigatório  | Um identificador exclusivo que descreve a junção ou o segmento. Identifica esta instância da mensagem.  Mensagens com semântica equivalente devem ter o mesmo valor.                                                                                                                                                                                                                                                       |
| duration   | Número     | Obrigatório  | A duração do evento ou junção-segmento ad, se conhecido. Se desconhecido, o **valor DEVE** ser 0.                                                                                                                                                                                                                                                                                                                    |
| elapsed    | Número     | Opcional  | Quando o sinal [SCTE-35] ad está sendo repetido para ajustar, este campo deve ser a quantidade de horas de apresentação decorrida desde que a junção começou. As unidades são frações de segundo. No modo [SCTE-35], esse valor pode exceder a duração especificada original da junção ou segmento.                                                                                                                   |
| time       | Número     | Obrigatório  | O horário da apresentação do evento ou junção ad.  O tempo e a duração da apresentação **devem estar alinhados** com os Pontos de Acesso ao Fluxo (SAP) do tipo 1 ou 2, conforme definido no anexo I da ISO 14496-12. Para saída hls, tempo e duração **DEVEM estar** alinhados com os limites do segmento. A hora de apresentação e a duração de mensagens de eventos diferentes dentro do mesmo fluxo de eventos NÃO DEVEM se sobrepor. As unidades são frações de segundo. |

---

#### <a name="example-mpeg-dash-mpd-manifest-with-scte-35-mode"></a>Exemplo MPEG DASH .mpd manifesto com modo SCTE-35
Veja [a Seção 3.3.3.2 exemplo do manifesto DASH com SCTE-35](#3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling)

#### <a name="example-hls-manifest-m3u8-with-scte-35-mode-signal"></a>Exemplo de manifesto HLS .m3u8 com sinal de modo SCTE-35
Veja [a Seção 3.2.1.1 exemplo do manifesto HLS com SCTE-35](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35)

## <a name="215-rtmp-ad-signaling-with-oncuepoint-for-elemental-live"></a>Sinalização de anúncio rtmp de 2.1.5 rtmp com "onCuePoint" para Elemental Live

O codificador elemental live no local suporta marcadores de anúncios no sinal RTMP. Atualmente, o Azure Media Services só suporta o tipo de marcador de anúncio "onCuePoint" para RTMP.  Isso pode ser ativado nas configurações do grupo Adobe RTMP nas configurações ou API do codificador Elemental Media Live, definindo o "**ad_markers**" como "onCuePoint".  Consulte a documentação elemental live para obter detalhes. Habilitar esse recurso no Grupo RTMP passará sinais SCTE-35 para as saídas Adobe RTMP a serem processadas pelos Serviços de Mídia do Azure.

O tipo de mensagem "onCuePoint" é definido em [Adobe-Flash-AS] e tem a seguinte estrutura de carga útil quando enviado a partir da saída Elemental Live RTMP.


| Propriedade   | Descrição                                                                                                                                                                                                                     |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name       | O nome DEVE ser '**scte35**' por Elemental Live.                                                                                                                                                                              |
| time       | O tempo em segundos em que o ponto de sinalização ocorreu no arquivo de vídeo durante a linha do tempo                                                                                                                                           |
| type       | O tipo de ponto de sinalização DEVE ser definido como "**evento**".                                                                                                                                                                             |
| parâmetros | Uma matriz associativa de strings de par de nomes/valor escancarando as informações da mensagem SCTE-35, incluindo Id e duração. Esses valores são analisados pela Azure Media Services e incluídos na etiqueta de decoração do manifesto. |


Quando este modo de marcador de anúncio é usado, a saída de manifesto HLS é semelhante ao modo Adobe "Simple".


#### <a name="example-mpeg-dash-mpd-single-period-adobe-simple-mode-signals"></a>Exemplo MPEG DASH MPD, período único, sinais de modo Adobe Simple

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T18:58:03Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T17:44:47Z" minBufferTime="PT7S">
    <Period start="PT0S">
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35" timescale="10000000">
            <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
            <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
            <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
            <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
            <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
            <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
            <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
            <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
            <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
            <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
            <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D400C" maxWidth="256" maxHeight="144" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495318000000" d="64000000" r="34"/>
                    <S d="43000000"/>
                    <S d="21000000"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495318000000" type="0" wallClockTime="2020-01-07T17:59:10.957Z" presentationTime="1583495318000000"/>
            <Representation id="1_V_video_3750956353252827751" bandwidth="149952" width="256" height="144"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <Label>ambient</Label>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(ambient=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(ambient=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495254426666" d="64000000" r="35"/>
                    <S d="43093334"/>
                    <S d="20906666"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495254426666" type="0" wallClockTime="2020-01-07T17:59:04.600Z" presentationTime="1583495254426666"/>
            <Representation id="5_A_ambient_9125670592623055209" bandwidth="96000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>
~~~

#### <a name="example-hls-playlist-adobe-simple-mode-signals-using-ext-x-cue-tag-truncated--for-brevity"></a>Exemplo de lista de reprodução HLS, adobe simples sinaliza usando a tag EXT-X-CUE (truncada "..." para a brevidade)

O exemplo a seguir mostra a saída do empacotador dinâmico media services para um fluxo de ingestão de RTMP usando sinais de modo "simples" da Adobe e a tag EXT-X-CUE legado [Adobe-Primetime].  

~~~
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:7
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T17:44:47Z
#EXTINF:6.400000,no-desc
Fragments(video=1583486742000000,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583486806000000,format=m3u8-aapl-v8)
...
#EXTINF:6.166667,no-desc
Fragments(video=1583487638000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667
#EXTINF:0.233333,no-desc
Fragments(video=1583487699666666,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=0.233333
#EXTINF:6.400000,no-desc
Fragments(video=1583487702000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=6.633333
#EXTINF:6.400000,no-desc
Fragments(video=1583487766000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=13.033333
#EXTINF:6.400000,no-desc
Fragments(video=1583487830000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=19.433333
#EXTINF:6.400000,no-desc
Fragments(video=1583487894000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=25.833333
#EXTINF:4.166667,no-desc
Fragments(video=1583487958000000,format=m3u8-aapl-v8)
#EXTINF:2.233333,no-desc
Fragments(video=1583487999666666,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583488022000000,format=m3u8-aapl-v8)
...

~~~

### <a name="216-cancellation-and-updates"></a>2.1.6 Cancelamento e Atualizações

As mensagens podem ser canceladas ou atualizadas enviando-se várias mensagens com a mesma hora e ID de apresentação. A hora e ID de apresentação identificam exclusivamente o evento e a última mensagem recebida por um horário de apresentação específico que atende às restrições de pré-inscrição é a mensagem que é tratada. O evento atualizado substitui qualquer mensagem recebida anteriormente. A restrição de pré-inscrição é de quatro segundos. Mensagens recebidas em pelo menos quatro segundos antes do horário da apresentação serão tratadas.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 Ingestão de MP4 fragmentado (Smooth Streaming)

Consulte [MS-SSTR-Ingest] para obter requisitos sobre a entrada em transmissão ao vivo. As seções a seguir fornecem detalhes com relação à ingestão de metadados de apresentação cronometrados.  Metadados de apresentação cronometrados são ingeridos como uma faixa esparsa, que é definida tanto na Caixa de Manifesto do Servidor Vivo (ver MS-SSTR) quanto na Caixa de Filme ('moov').  

Cada fragmento esparso consiste em uma Caixa de Fragmentos de Filme ('moof') e media data box ('mdat'), onde a caixa 'mdat' é a mensagem binária.

Para obter a inserção precisa de anúncios, o codificador DEVE dividir o fragmento no momento da apresentação em que a sugestão é necessária para ser inserida.  Um novo fragmento DEVE ser criado que começa com um quadro IDR recém-criado, ou Pontos de Acesso de Fluxo (SAP) do tipo 1 ou 2, conforme definido no anexo I [ISO-14496-12]. Isso permite que o Azure Media Packager gere adequadamente um manifesto HLS e um manifesto de vários períodos DASH onde o novo Período começa no tempo de apresentação condicionado à emenda com precisão de quadro.

### <a name="221-live-server-manifest-box"></a>2.2.1 Caixa de manifesto do servidor Live

A faixa esparsa **DEVE** ser declarada na caixa Manifesto do Servidor Vivo com uma **MUST** ** \<\> ** entrada de textstream e deve ter os seguintes atributos definidos:

| **Nome do atributo** | **Tipo de campo** | **Necessário?** | **Descrição**                                                                                                                                                                                                              |
| ------------------ | -------------- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| systemBitrate      | Número         | Obrigatório      | **DEVE** ser "0", indicando uma faixa com bitrate variável e desconhecida.                                                                                                                                                          |
| parentTrackName    | String         | Obrigatório      | **DEVE** ser o nome da faixa pai, à qual os códigos de tempo de faixa esparsos estão alinhados. A faixa pai não pode ser uma faixa esparsa.                                                                             |
| manifestOutput     | Boolean        | Obrigatório      | **DEVE** ser "verdadeiro", para indicar que a faixa esparsa será incorporada no manifesto do cliente Smooth.                                                                                                                        |
| Subtype            | String         | Obrigatório      | **DEVE** ser o código de quatro caracteres "DATA".                                                                                                                                                                                  |
| Esquema             | String         | Obrigatório      | **DEVE** ser uma URN ou URL que identifique o esquema de mensagens. Para as mensagens [SCTE-35], esta **deve** ser "urna:scte:scte:scte35:2013:bin" para que as mensagens sejam enviadas aos clientes HLS, Smooth e Dash em conformidade com [SCTE-35]. |
| trackName          | String         | Obrigatório      | **DEVE** ser o nome da faixa esparsa. O trackName pode ser usado para diferenciar vários fluxos de eventos com o mesmo esquema. Cada fluxo de eventos exclusivo **DEVE** ter um nome de faixa único.                                |
| escala de tempo          | Número         | Opcional      | **DEVE** ser a escala de tempo da faixa dos pais.                                                                                                                                                                               |

---

### <a name="222-movie-box"></a>2.2.2 Caixa de filme

A Caixa de Filme ('moov') segue a Caixa de Manifesto do Servidor Vivo como parte do cabeçalho do fluxo para uma faixa esparsa.

A caixa 'moov' **DEVE** conter uma caixa **TrackHeaderBox ('tkhd')** conforme definido em [ISO-14496-12] com as seguintes restrições:

| **Nome do campo** | **Tipo de campo**          | **Necessário?** | **Descrição**                                                                                                    |
| -------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------ |
| duration       | Inteiro sem sinal de 64 bits | Obrigatório      | **Deve** ser 0, uma vez que a caixa de faixa tem zero amostras e a duração total das amostras na caixa de pista é 0. |

---

A caixa 'moov' **DEVE** conter uma **Caixa de Manipuladores ('hdlr')** conforme definido em [ISO-14496-12] com as seguintes restrições:

| **Nome do campo** | **Tipo de campo**          | **Necessário?** | **Descrição**       |
| -------------- | ----------------------- | ------------- | --------------------- |
| handler_type   | Inteiro sem sinal de 32 bits | Obrigatório      | **DEVE** ser 'meta'. |

---

A caixa 'stsd' **DEVE** conter uma caixa MetaDataSampleEntry com um nome de codificação definido em [ISO-14496-12].  Por exemplo, para mensagens SCTE-35, o nome de codificação **deve** ser 'scte'.

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 Caixa de fragmentos de filme e Data Box de Mídia

Fragmentos de faixa esparsos consistem em uma Caixa de Fragmentos de Filme ('moof') e uma Caixa de Dados de Mídia ('mdat').

> [!NOTE]
> Para obter a inserção precisa de anúncios, o codificador DEVE dividir o fragmento no momento da apresentação em que a sugestão é necessária para ser inserida.  Um novo fragmento deve ser criado que começa com um quadro IDR recém-criado, ou Pontos de Acesso de Fluxo (SAP) do tipo 1 ou 2, conforme definido no anexo I [ISO-14496-12]
> 

A caixa MovieFragmentBox ('moof') **DEVE** conter uma caixa **TrackFragmentExtendedHeaderBox ('uuid')** definida em [MS-SSTR] com os seguintes campos:

| **Nome do campo**         | **Tipo de campo**          | **Necessário?** | **Descrição**                                                                                           |
| ---------------------- | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------- |
| fragment_absolute_time | Inteiro sem sinal de 64 bits | Obrigatório      | **DEVE** ser a hora de chegada do evento. Esse valor alinha a mensagem com a faixa pai.           |
| fragment_duration      | Inteiro sem sinal de 64 bits | Obrigatório      | **Deve** ser a duração do evento. A duração pode ser zero para indicar que a duração é desconhecida. |

---


A caixa MediaDataBox ('mdat') **DEVE** ter o seguinte formato:

| **Nome do campo**          | **Tipo de campo**                   | **Necessário?** | **Descrição**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------- | -------------------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| version                 | Inteiro sem sinal de 32 bits (uimsbf) | Obrigatório      | Determina o formato do conteúdo da caixa 'mdat'. Versões não reconhecidas serão ignoradas. Atualmente, a única versão aceita é 1.                                                                                                                                                                                                                                                                                                                                                                      |
| id                      | Inteiro sem sinal de 32 bits (uimsbf) | Obrigatório      | Identifica esta instância da mensagem. Mensagens com semântica equivalente devem ter o mesmo valor. Ou seja, o processamento de qualquer caixa de mensagem de um evento com a mesma ID é suficiente.                                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | Inteiro sem sinal de 32 bits (uimsbf) | Obrigatório      | A soma do fragment_absolute_time, especificado no TrackFragmentExtendedHeaderBox, e o presentation_time_delta **DEVE** ser o tempo de apresentação do evento. O tempo e a duração da apresentação **devem estar alinhados** com os Pontos de Acesso ao Fluxo (SAP) do tipo 1 ou 2, conforme definido no anexo I da ISO 14496-12. Para saída hls, tempo e duração **DEVEM estar** alinhados com os limites do segmento. O tempo de apresentação e duração de diferentes mensagens de evento dentro do mesmo fluxo de eventos **não deve** se sobrepor. |
| message                 | matriz de bytes                       | Obrigatório      | A mensagem do evento. Para mensagens [SCTE-35], a mensagem é o splice_info_section binário(). Para as mensagens [SCTE-35], este **deve** ser o splice_info_section() para que as mensagens sejam enviadas aos clientes HLS, Smooth e Dash em conformidade com [SCTE-35]. Para mensagens [SCTE-35], o binário splice_info_section() é a carga útil da **NOT** caixa 'mdat', e não é codificado pela base64.                                                                                                                     |

---


### <a name="224-cancellation-and-updates"></a>2.2.4 Cancelamento e atualizações

As mensagens podem ser canceladas ou atualizadas enviando-se várias mensagens com a mesma hora e ID de apresentação.  A hora e ID de apresentação identificam exclusivamente o evento. A última mensagem recebida por um horário de apresentação específico, que atende às restrições de pré-inscrição, é a mensagem que é tratada. A mensagem atualizada substitui qualquer mensagem recebida anteriormente.  A restrição de pré-inscrição é de quatro segundos. Mensagens recebidas em pelo menos quatro segundos antes do horário da apresentação serão tratadas. 


## <a name="3-timed-metadata-delivery"></a>3 Entrega de metadados cronometrados

Dados de fluxo de evento são opacos para Serviços de Mídia. Serviços de Mídia simplesmente passam três partes de informações entre o ponto de extremidade de ingestão e o ponto de extremidade do cliente. As seguintes propriedades são entregues ao cliente, em conformidade com [SCTE-35] e/ou o protocolo de streaming do cliente:

1.  Esquema – uma URN ou URL que identifica o esquema da mensagem.
2.  Horário da apresentação – o horário da apresentação do evento na linha do tempo da mídia.
3.  Duração – a duração do evento.
4.  ID – um identificador exclusivo opcional para o evento.
5.  Mensagem – os dados do evento.

## <a name="31-microsoft-smooth-streaming-manifest"></a>3.1 Microsoft Smooth Streaming Manifesto  

Consulte o controle de faixa sparso [MS-SSTR] para obter detalhes sobre como formatar uma faixa de mensagem esparsa. Para mensagens [SCTE35], o Smooth Streaming produzirá o splice_info_section codificado com base64 em um fragmento esparso.
O StreamIndex **DEVE** ter um subtipo de "DADOS", e os Atributos Personalizados **DEVEM** conter um atributo com Name="Schema" e Value="urn:scte:scte35:2013:bin".

#### <a name="smooth-client-manifest-example-showing-base64-encoded-scte35-splice_info_section"></a>Exemplo de manifesto do cliente suave mostrando base64-codificado [SCTE35] splice_info_section()
~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="0" TimeScale="10000000" IsLive="true" Duration="0"
  LookAheadFragmentCount="2" DVRWindowLength="6000000000">

  <StreamIndex Type="video" Name="video" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(video={start time})">
    <QualityLevel Index="0" Bitrate="230000"
      CodecPrivateData="250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <QualityLevel Index="1" Bitrate="305000"
      CodecPrivateData="250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="audio" Name="audio" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(audio={start time})">
    <QualityLevel Index="0" Bitrate="96000" CodecPrivateData="1000030000000000000000000000E00042C0"
      FourCC="WMAP" AudioTag="354" Channels="2" SamplingRate="44100" BitsPerSample="16" PacketSize="4459"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="text" Name="scte35-sparse-stream" Subtype="DATA" Chunks="0" TimeScale="10000000"
    ParentStreamIndex="video" ManifestOutput="true" 
    Url="QualityLevels({bitrate})/Fragments(captions={start time})">
    <QualityLevel Index="0" Bitrate="0" CodecPrivateData="" FourCC="">
      <CustomAttributes>
        <Attribute Name="Scheme" Value="urn:scte:scte35:2013:bin"/>
      </CustomAttributes>
    </QualityLevel>
    <!-- The following <c> and <f> fragments contains the base64-encoded [SCTE35] splice_info_section() message -->
    <c t="600000000" d="300000000">    <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t="1200000000" d="400000000">      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia>
~~~

## <a name="32-apple-hls-manifest-decoration"></a>3.2 Apple HLS Manifesta decoração

O Azure Media Services suporta as seguintes tags de manifesto HLS para sinalizar informações úteis durante um evento ao vivo ou demanda. 

- EXT-X-DATERANGE conforme definido no Apple HLS [RFC8216]
- EXT-X-CUE conforme definido em [Adobe-Primetime] - este modo é considerado "legado". Os clientes devem adotar a tag EXT-X-DATERANGE quando possível.

A saída de dados para cada tag variará de acordo com o modo de sinal de ingestão usado. Por exemplo, a ingestão de RTMP com o modo Adobe Simple não contém a carga completa codificada SCTE-35 base64.

## <a name="321-apple-hls-with-ext-x-daterange-recommended"></a>3.2.1 Apple HLS com EXT-X-DATERANGE (recomendado)

A especificação Apple HTTP Live Streaming [RFC8216] permite a sinalização de mensagens [SCTE-35]. As mensagens são inseridas na lista de reprodução do segmento em uma tag EXT-X-DATERANGE por [RFC8216] intitulada "Mapping SCTE-35 in EXT-X-DATERANGE".  A camada de aplicativo cliente pode analisar a lista de reprodução M3U e processar tags M3U, ou receber os eventos através da estrutura do player Apple.  

A abordagem **recomendada** no Azure Media Services (versão 3 API) é seguir [RFC8216] e usar a tag EXT-X_DATERANGE para [SCTE35] ad ad ad ad decoração no manifesto.

## <a name="3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35"></a>3.2.1.1 Exemplo de manifesto HLS .m3u8 mostrando sinalização EXT-X-DATERANGE de SCTE-35

O exemplo a seguir a saída do manifesto HLS do empacotador dinâmico media services mostra o uso da tag EXT-X-DATERANGE de [RFC8216] sinalizando os eventos SCTE-35 no fluxo. Além disso, este fluxo contém a tag EXT-X-CUE "legado" para [Adobe-Primetime].

~~~
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:2
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T19:40:50Z
#EXTINF:1.501500,no-desc
Fragments(video=22567545,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22702680,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22837815,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22972950,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=23108085,format=m3u8-aapl-v8)
#EXTINF:1.234567,no-desc
Fragments(video=23243220,format=m3u8-aapl-v8)
#EXTINF:0.016689,no-desc
Fragments(video=23354331,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.000022
#EXTINF:0.250244,no-desc
Fragments(video=23355833,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.250267
#EXTINF:0.850856,no-desc
Fragments(video=23378355,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.101122
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:10.610Z",SCTE35-IN=0xFC30200000000005DD00FFF00F05000003EA7F4FFE0165E4D3000101010000607CE85A
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=0.000000,TIME=260.610344,CUE="/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo="
#EXTINF:0.650644,no-desc
Fragments(video=23454932,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.751767
#EXTINF:0.050044,no-desc
Fragments(video=23513490,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.801811
#EXTINF:1.451456,no-desc
Fragments(video=23517994,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=3.253267
#EXTINF:1.501500,no-desc
Fragments(video=23648625,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=4.754767
#EXTINF:1.501500,no-desc
Fragments(video=23783760,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=6.256267
#EXTINF:1.501500,no-desc
Fragments(video=23918895,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=7.757767
#EXTINF:1.501500,no-desc
Fragments(video=24054030,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=9.259267
#EXTINF:1.501500,no-desc
Fragments(video=24189165,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=10.760767
#EXTINF:1.501500,no-desc
Fragments(video=24324300,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=12.262267
#EXTINF:1.501500,no-desc
Fragments(video=24459435,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=13.763767
#EXTINF:1.501500,no-desc
Fragments(video=24594570,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=15.265267
#EXTINF:1.501500,no-desc
Fragments(video=24729705,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=16.766767
#EXTINF:1.501500,no-desc
Fragments(video=24864840,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=18.268267
#EXTINF:1.501500,no-desc
Fragments(video=24999975,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=19.769767
#EXTINF:1.501500,no-desc
Fragments(video=25135110,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=21.271267
#EXTINF:1.501500,no-desc
Fragments(video=25270245,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=22.772767
#EXTINF:1.501500,no-desc
Fragments(video=25405380,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=24.274267
#EXTINF:1.501500,no-desc
Fragments(video=25540515,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=25.775767
#EXTINF:1.501500,no-desc
Fragments(video=25675650,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=27.277267
#EXTINF:1.501500,no-desc
Fragments(video=25810785,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=28.778767
#EXTINF:1.501500,no-desc
Fragments(video=25945920,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=30.280267
#EXTINF:1.501500,no-desc
Fragments(video=26081055,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=31.781767
#EXTINF:1.501500,no-desc
Fragments(video=26216190,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=33.283267
#EXTINF:1.501500,no-desc
Fragments(video=26351325,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=34.784767
#EXTINF:1.501500,no-desc
Fragments(video=26486460,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=36.286267
#EXTINF:1.501500,no-desc
Fragments(video=26621595,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=37.787767
#EXTINF:1.501500,no-desc
Fragments(video=26756730,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=39.289267
#EXTINF:1.501500,no-desc
Fragments(video=26891865,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=40.790767
#EXTINF:1.501500,no-desc
Fragments(video=27027000,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=42.292267
#EXTINF:1.501500,no-desc
Fragments(video=27162135,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=43.793767
#EXTINF:1.501500,no-desc
Fragments(video=27297270,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=45.295267
#EXTINF:1.501500,no-desc
Fragments(video=27432405,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=46.796767
#EXTINF:1.501500,no-desc
Fragments(video=27567540,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=48.298267
#EXTINF:1.501500,no-desc
Fragments(video=27702675,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=49.799767
#EXTINF:1.501500,no-desc
Fragments(video=27837810,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=51.301267
#EXTINF:1.501500,no-desc
Fragments(video=27972945,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=52.802767
#EXTINF:1.501500,no-desc
Fragments(video=28108080,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=54.304267
#EXTINF:1.501500,no-desc
Fragments(video=28243215,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=55.805767
#EXTINF:1.501500,no-desc
Fragments(video=28378350,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=57.307267
#EXTINF:1.501500,no-desc
Fragments(video=28513485,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=58.808767
#EXTINF:1.501500,no-desc
Fragments(video=28648620,format=m3u8-aapl-v8)

~~~


## <a name="322-apple-hls-with-adobe-primetime-ext-x-cue-legacy"></a>3.2.2 Apple HLS com Adobe Primetime EXT-X-CUE (legado)

Há também uma implementação "legado" fornecida no Azure Media Services (Versão 2 e 3 API) que usa a tag EXT-X-CUE conforme definido no modo SCTE-35 da Adobe-Primetime. Neste modo, o Azure Media Services incorporará splice_info_section de splice_info_section codificados com base64 [SCTE-35] na tag EXT-X-CUE.  

A tag "legado" EXT-X-CUE é definida como abaixo e também pode ser normativa referenciada na especificação [Adobe-Primetime]. Isso só deve ser usado para sinalização SCTE35 legado quando necessário, caso contrário, a tag recomendada é definida em [RFC8216] como EXT-X-DATERANGE. 

| **Nome do atributo** | **Tipo**                      | **Necessário?**                             | **Descrição**                                                                                                                                                                                                                                                                          |
| ------------------ | ----------------------------- | ----------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ADVERTÊNCIA                | cadeia de caracteres entre aspas                 | Obrigatório                                  | A mensagem codificada como uma seqüência de base64 codificada como descrito em [RFC4648]. Para mensagens [SCTE-35], esta é a splice_info_section codificada com base64().                                                                                                                                      |
| TYPE               | cadeia de caracteres entre aspas                 | Obrigatório                                  | Uma URN ou URL que identifica o esquema da mensagem. Para mensagens [SCTE-35], o tipo leva o valor especial "scte35".                                                                                                                                                                          |
| ID                 | cadeia de caracteres entre aspas                 | Obrigatório                                  | Um identificador exclusivo para o evento. Se a ID não for especificada quando a mensagem for ingerida, o Azure Media Services gerará uma ID exclusiva.                                                                                                                                              |
| DURAÇÃO           | um número de ponto flutuante decimal | Obrigatório                                  | A duração do evento. Se desconhecido, o **valor DEVE** ser 0. As unidades são frações de segundos.                                                                                                                                                                                           |
| DECORRIDO            | um número de ponto flutuante decimal | Opcional, mas Necessário para janela deslizante | Quando o sinal está sendo repetido para suportar uma janela de apresentação deslizante, este campo **DEVE** ser a quantidade de tempo de apresentação que se passou desde o início do evento. As unidades são frações de segundo. Esse valor pode exceder a duração especificada original da junção ou segmento. |
| TIME               | um número de ponto flutuante decimal | Obrigatório                                  | O horário da apresentação do evento. As unidades são frações de segundo.                                                                                                                                                                                                                        |


A camada de aplicativo de player HLS usará o TIPO para identificar o formato da mensagem, decodificar a mensagem, aplicar as conversões de tempo necessárias e processar o evento.  Os eventos são sincronizados por horário na lista de reprodução de segmento da faixa pai, de acordo com o carimbo de data/hora do evento.  Eles são inseridos antes do segmento mais próximo (marca #EXTINF).

### <a name="323-hls-m3u8-manifest-example-using-legacy-adobe-primetime-ext-x-cue"></a>3.2.3 HLS .m3u8 exemplo manifesto usando "Legacy" Adobe Primetime EXT-X-CUE

O exemplo a seguir mostra a decoração do manifesto HLS usando a tag Adobe Primetime EXT-X-CUE.  O parâmetro "CUE" contém apenas as propriedades TIPO e Duração, o que significa que esta era uma fonte RTMP usando a sinalização do modo "simples" da Adobe.  Se este fosse um sinal de modo SCTE-35, a tag incluiria a carga binária SCTE-35 codificada base64, como visto no [exemplo 3.2.1.1](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35).

~~~
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-PLAYLIST-TYPE:VOD
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:11
#EXT-X-PROGRAM-DATE-TIME:2019-12-10T09:18:14Z
#EXTINF:10.010000,no-desc
Fragments(video=4011540820,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011550830,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011560840,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000
#EXTINF:8.008000,no-desc
Fragments(video=4011570850,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=0.593000
#EXTINF:4.170000,no-desc
Fragments(video=4011578858,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=4.763000
#EXTINF:9.844000,no-desc
Fragments(video=4011583028,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=14.607000
#EXTINF:10.010000,no-desc
Fragments(video=4011592872,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=24.617000
#EXTINF:10.010000,no-desc
Fragments(video=4011602882,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=34.627000
#EXTINF:10.010000,no-desc
Fragments(video=4011612892,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=44.637000
#EXTINF:10.010000,no-desc
Fragments(video=4011622902,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=54.647000
#EXTINF:10.010000,no-desc
Fragments(video=4011632912,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=64.657000
#EXTINF:10.010000,no-desc
Fragments(video=4011642922,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=74.667000
#EXTINF:10.010000,no-desc
Fragments(video=4011652932,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=84.677000
#EXTINF:10.010000,no-desc
Fragments(video=4011662942,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=94.687000
#EXTINF:10.010000,no-desc
Fragments(video=4011672952,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=104.697000
#EXTINF:10.010000,no-desc
Fragments(video=4011682962,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=114.707000
#EXTINF:10.010000,no-desc
Fragments(video=4011692972,format=m3u8-aapl)
#EXTINF:8.008000,no-desc
Fragments(video=4011702982,format=m3u8-aapl)

~~~

### <a name="324-hls-message-handling-for-legacy-adobe-primetime-ext-x-cue"></a>3.2.4 Manuseio de mensagens HLS para "Legado" Adobe Primetime EXT-X-CUE

Os eventos são sinalizados na lista de reprodução do segmento de cada faixa de vídeo e áudio. A posição da tag EXT-X-CUE **deve** ser sempre imediatamente antes do primeiro segmento HLS (para splice out ou início do segmento) ou imediatamente após o último segmento HLS (para emenda ou final do segmento) ao qual seus atributos TIME e DURAÇÃO se referem, conforme exigido por [Adobe-Primetime].

Quando uma janela de apresentação deslizante estiver ativada, a tag EXT-X-CUE **DEVE** ser repetida com freqüência suficiente para que a emenda ou segmento esteja sempre totalmente descrito na lista de reprodução do segmento, e o atributo ELAPSED **DEVE** ser usado para indicar a quantidade de tempo que a emenda ou segmento esteve ativo, conforme exigido por [Adobe-Primetime].

Quando uma janela de apresentação deslizante está habilitada, as marcas EXT-X-CUE são removidas da lista de reprodução de segmento quando o tempo de mídia a que eles se referem seja revertido para fora da janela deslizante de apresentação.

## <a name="33-dash-manifest-decoration-mpd"></a>3.3 DECORAÇÃO DO MANIFESTO DASH (MPD)

[MPEGDASH] fornece três maneiras de sinalizar eventos:

1.  Eventos sinalizados no MPD EventStream
2.  Eventos sinalizados em banda usando a Caixa de Mensagens de Evento ('emsg')
3.  Uma combinação de 1 e 2

Eventos sinalizados no MPD EventStream são úteis para o streaming de VOD porque os clientes têm acesso a todos os eventos, imediatamente quando o MPD é baixado. Também é útil para a sinalização SSAI, onde o fornecedor SSAI a jusante precisa analisar os sinais de um manifesto MPD de vários períodos e inserir o conteúdo de anúncios dinamicamente.  A solução in-band ('emsg')é útil para transmissão ao vivo onde os clientes não precisam baixar o MPD novamente, ou não há nenhuma manipulação manifesta ssai acontecendo entre o cliente e a origem. 

O comportamento padrão do Azure Media Services para DASH é sinalizar tanto no MPD EventStream quanto na banda usando a Caixa de Mensagens de Evento ('emsg').

As mensagens de sinalização ingeridas sobre [RTMP] ou [MS-SSTR-Ingest] são mapeadas em eventos DASH, usando caixas 'emsg' em banda e/ou in-MPD EventStreams. 

A sinalização SCTE-35 para DASH segue a definição e os requisitos definidos em [SCTE-214-3] e também na seção [DASH-IF-IOP] 13.12.2 ('SCTE35 Events'). 

Para o transporte in-band [SCTE-35], a caixa De mensagem de evento ('emsg') usa o esquemaId = "urna:scte:scte35:2013:bin". Para decoração de manifesto MPD, o esquema EventStreamId usa "urna:scte:scte35:2014:xml+bin".  Este formato é uma representação XML do evento que inclui uma saída binária de base64 codificada da mensagem Completa SCTE-35 que chegou ao ingestão. 

As definições normativas de referência de transporte de mensagens de sinalização [SCTE-35] no DASH estão disponíveis em [SCTE-214-1] seg 6.7.4 (MPD) e [SCTE-214-3] seg 7.3.2 (Transporte de mensagens de sinalização SCTE 35).

### <a name="331-mpeg-dash-mpd-eventstream-signaling"></a>Sinalização eventStream (MPD) 3.3.1 MPEG DASH (MPD)

A decoração manifesto (MPD) dos eventos será sinalizada no MPD usando o elemento EventStream, que aparece dentro do elemento Period. O esquemaId usado é "urn:scte:scte35:2014:xml+bin".

> [!NOTE]
> Para fins de brevidade [SCTE-35] permite o uso da seção codificada base64 no elemento Signal.Binary (em vez do elemento Signal.SpliceInfoSection) como uma alternativa ao transporte de uma mensagem de sinalização completamente parsed.
> O Azure Media Services usa essa abordagem 'xml+bin' para sinalizar no manifesto MPD.
> Este também é o método recomendado usado no [DASH-IF-IOP] - consulte a seção intitulada ['Fluxos de eventos de inserção de anúncios' da diretriz DASH IF IOP](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html#ads-insertion-event-streams)
> 

O elemento EventStream tem os seguintes atributos:

| **Nome do atributo** | **Tipo**                | **Necessário?** | **Descrição**                                                                                                                                                                                                                                                                                                                                                                         |
| ------------------ | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| scheme_id_uri      | string                  | Obrigatório      | Identifica o esquema da mensagem. O esquema é definido como o valor do atributo Esquema na caixa de Manifesto do Servidor Live. O valor **DEVE** ser uma URN ou URL que identifique o esquema de mensagens; O esquema de saída suportadoId deve ser "urna:scte:scte35:2014:xml+bin" por [SCTE-214-1] seg 6.7.4 (MPD), pois o serviço suporta apenas "xml+bin" neste momento para brevidade no MPD. |
| value              | string                  | Opcional      | Um valor de cadeia de caracteres adicional usado pelos proprietários do esquema para personalizar a semântica da mensagem. Para diferenciar vários fluxos de eventos com o mesmo esquema, o valor **DEVE** ser definido como o nome do fluxo de eventos (trackName para [MS-SSTR-Ingest] ou nome de mensagem AMF para ingestão [RTMP]).                                                                         |
| Escala de tempo          | Inteiro sem sinal de 32 bits | Obrigatório      | A escala de tempo, em carrapatos por segundo.                                                                                                                                                                                                                                                                                                                                                     |


### <a name="332-example-event-streams-for-mpeg-dash"></a>3.3.2 Exemplo de Fluxos de Eventos para MPEG DASH

#### <a name="3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode"></a>3.3.2.1 Exemplo MPEG DASH .mpd sinalização manifesto de streaming RTMP usando o modo simples Adobe

O exemplo a seguir mostra um trecho do pacote dinâmico EventStream do Media Services para um fluxo RTMP usando a sinalização do modo "simples" da Adobe.

~~~ xml
<!-- Example EventStream element using "urn:com:adobe:dpi:simple:2015" Adobe simple signaling per [Adobe-Primetime] -->
    <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="10000000">
        <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
        <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
        <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
        <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
        <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
        <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
        <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
        <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
        <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
        <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
        <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
    </EventStream>
~~~

#### <a name="3322-example-mpeg-dash-mpd-manifest-signaling-of-an-rtmp-stream-using-adobe-scte-35-mode"></a>3.3.2.2 Exemplo MPEG DASH .mpd sinalização manifesto de um fluxo RTMP usando o modo Adobe SCTE-35

O exemplo a seguir mostra um trecho eventStream do empacotamento dinâmico media services para um fluxo RTMP usando a sinalização do modo Adobe SCTE-35.

~~~ xml
<!-- Example EventStream element using xml+bin style signaling per [SCTE-214-1] -->

      <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
        <Event presentationTime="2595092444" duration="11011000" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
            </Signal>
        </Event>
        <Event presentationTime="2606103444" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
            </Signal>
        </Event>
      </EventStream>
~~~

> [!IMPORTANT]
> Observe que presentationTime é o tempo de apresentação do evento [SCTE-35] traduzido para ser relativo ao tempo de início do período, não o tempo de chegada da mensagem.
> [MPEGDASH] define Event@presentationTime o como "Especifica o tempo de apresentação do evento em relação ao início do Período.
> O valor do tempo de apresentação em segundos é a divisão EventStream@timescale do valor deste atributo e do valor do atributo.
> Se não estiver presente, o valor do tempo de apresentação é 0.

#### <a name="3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals"></a>3.3.3.1 Exemplo MPEG DASH manifesto (MPD) com um período único, EventStream, usando sinais de modo simples da Adobe

O exemplo a seguir mostra a saída do empacotador dinâmico media services para um fluxo RTMP de origem usando o método de sinal de anúncio do modo Adobe "simples". A saída é um manifesto de período único que mostra um EventStream usando o esquemaId Uri definido como "urn:com:adobe:dpi:simple:2015" e valor de propriedade definido como "simplesignal".
Cada sinal simples é fornecido em @presentationTimeum @durationelemento @id De evento com o , e propriedades povoadas com base nos sinais simples de entrada.

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT28M1.680S" minBufferTime="PT3S">
    <Period>
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="1000">
            <Event presentationTime="4011578265" duration="119987" id="4011578265"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D4028" maxWidth="1920" maxHeight="1080" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460740" type="0" wallClockTime="2020-01-25T19:35:54.740Z" presentationTime="4011460740"/>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460740" d="2002" r="57"/>
                    <S d="1401"/>
                    <S d="601"/>
                    <S d="2002"/>

                     <!--> ... video segments truncated for sample brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="1_V_video_14759481473095519504" bandwidth="6000000" width="1920" height="1080"/>
            <Representation id="1_V_video_1516803357996956148" bandwidth="4000000" codecs="avc1.4D401F" width="1280" height="720"/>
            <Representation id="1_V_video_5430608182379669372" bandwidth="2600000" codecs="avc1.4D401F" width="960" height="540"/>
            <Representation id="1_V_video_3780180650986497347" bandwidth="1000000" codecs="avc1.4D401E" width="640" height="360"/>
            <Representation id="1_V_video_13759117363700265707" bandwidth="699000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_6140004908920393176" bandwidth="400000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.4D400D" width="320" height="180"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460761" type="0" wallClockTime="2020-01-25T19:35:54.761Z" presentationTime="4011460761"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460761" d="1984"/>
                    <S d="2005" r="1"/>
                    <S d="2006"/>

                    <!--> ... audio segments truncated for example brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>

~~~

#### <a name="3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling"></a>3.3.3.2 Exemplo MPEG DASH manifesto (MPD) com multiperíodo, EventStream, usando a sinalização do modo Adobe SCTE35

O exemplo a seguir mostra a saída do empacotador dinâmico media services para um fluxo RTMP de origem usando a sinalização do modo Adobe SCTE35.
Neste caso, o manifesto de saída é um DASH .mpd @schemeIdUri de vários períodos com um elemento EventStream e uma propriedade definida como "urn:scte:scte35:2014:xml+bin" e uma @value propriedade definida como "scte35". Cada elemento de evento no EventStream contém o sinal binário codificado SCTE35 

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T19:42:44Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T19:40:50Z" minBufferTime="PT4S">
    <Period start="PT2M48.168S" id="main-content_0">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="15135120" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="15135120" d="135135" r="59"/>
                    <S d="111111"/>
                    <S d="1502"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="15135120" type="0" wallClockTime="2020-01-07T19:40:50Z" presentationTime="15135120"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="7416208" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="7417856" d="133120"/>
                    <S d="132096" r="1"/>
                    
                    <!--> ... aduio segments truncated for sample brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="7417856" type="0" wallClockTime="2020-01-07T19:40:50.037Z" presentationTime="7417856"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M19.509S" id="scte-35_0">
        <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
            <Event presentationTime="2595092444" duration="11011000" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
                </Signal>
            </Event>
            <Event presentationTime="2606103444" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
                </Signal>
            </Event>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23355832" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23355833" d="22522"/>
                    <S d="76577"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23355833" type="0" wallClockTime="2020-01-07T19:42:21.341Z" presentationTime="23355833"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11444358" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11446272" d="49152"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11446272" type="0" wallClockTime="2020-01-07T19:42:21.384Z" presentationTime="11446272"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M20.610S" id="main-content_1">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23454931" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23454932" d="58558"/>
                    <S d="4504"/>
                    <S d="130631"/>
                    <S d="135135" r="12"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23454932" type="0" wallClockTime="2020-01-07T19:42:22.442Z" presentationTime="23454932"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11492916" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11495424" d="28672"/>
                    <S d="1024"/>
                    <S d="131072"/>
                    <S d="132096"/>
                    <S d="133120"/>
                    <S d="132096" r="1"/>
                    <S d="133120"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11495424" type="0" wallClockTime="2020-01-07T19:42:22.499Z" presentationTime="11495424"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
</MPD>

~~~
### <a name="334-mpeg-dash-in-band-event-message-box-signaling"></a>Sinalização da caixa de mensagens de evento dash in-band 3.3.4 MPEG DASH

Um fluxo de eventos em banda requer que o MPD tenha um elemento InbandEventStream no nível do Conjunto de Adaptação.  Este elemento tem um atributo esquemaobrigatórioIdUri e atributo de escala de tempo opcional, que também aparecem na Caixa de Mensagens de Evento ('emsg').  As caixas de mensagens de evento com identificadores de esquema que não estão definidos no **MPD não devem** estar presentes.

Para o transporte em banda [SCTE-35], os sinais **DEVEM** usar o esquemaId = "urna:scte:scte35:2013:bin".
As definições normativas de transporte de mensagens em banda [SCTE-35] são definidas em [SCTE-214-3] seg 7.3.2 (Transporte de mensagens de sinalização SCTE 35).

Os detalhes a seguir descrevem os valores específicos que o cliente deve esperar no 'emsg' em conformidade com [SCTE-214-3]:

| **Nome do campo**          | **Tipo de campo**          | **Necessário?** | **Descrição**                                                                                                                                                                                                                                                                                        |
| ----------------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| scheme_id_uri           | string                  | Obrigatório      | Identifica o esquema da mensagem. O esquema é definido como o valor do atributo Esquema na caixa de Manifesto do Servidor Live. O valor **DEVE** ser uma URN que identifique o esquema de mensagens. Para as mensagens [SCTE-35], esta **deve** ser "urna:scte:scte35:2013:bin" em conformidade com [SCTE-214-3]          |
| Valor                   | string                  | Obrigatório      | Um valor de cadeia de caracteres adicional usado pelos proprietários do esquema para personalizar a semântica da mensagem. Para diferenciar vários fluxos de eventos com o mesmo esquema, o valor será definido como o nome do fluxo de eventos (trackName para ingestão Smooth ou nome de mensagem AMF para ingestão RTMP). |
| Escala de tempo               | Inteiro sem sinal de 32 bits | Obrigatório      | A escala de tempo, em carrapatos por segundo, dos campos de tempo e duração dentro da caixa 'emsg'.                                                                                                                                                                                                            |
| Presentation_time_delta | Inteiro sem sinal de 32 bits | Obrigatório      | O intervalo de tempo de apresentação de mídia do tempo de apresentação do evento e o primeiro horário nesse segmento. O tempo e a duração da apresentação **devem estar alinhados** com os Pontos de Acesso ao Fluxo (SAP) do tipo 1 ou 2, conforme definido no anexo I da ISO 14496-12.                                  |
| event_duration          | Inteiro sem sinal de 32 bits | Obrigatório      | A duração do evento ou 0xFFFFFFFF para indicar uma duração desconhecida.                                                                                                                                                                                                                              |
| ID                      | Inteiro sem sinal de 32 bits | Obrigatório      | Identifica esta instância da mensagem. Mensagens com semântica equivalente devem ter o mesmo valor. Se a ID não for especificada quando a mensagem for ingerida, o Azure Media Services gerará uma ID exclusiva.                                                                                        |
| Message_data            | matriz de bytes              | Obrigatório      | A mensagem do evento. Para as mensagens [SCTE-35], os dados da mensagem são os splice_info_section binários em conformidade com [SCTE-214-3]                                                                                                                                                                        |


#### <a name="example-inbandevenstream-entity-for-adobe-simple-mode"></a>Exemplo inBandEvenStream entidade para o modo Adobe Simple
~~~ xml

      <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
~~~

### <a name="335-dash-message-handling"></a>3.3.5 Tratamento de mensagens DASH

Os eventos são sinalizados em banda, dentro da caixa 'emsg', tanto para faixas de vídeo quanto de áudio.  A sinalização ocorre para todas as solicitações de segmento para as quais o presentation_time_delta é de 15 segundos ou menos. 

Quando uma janela de apresentação deslizante está habilitada, as mensagens de evento são removidas do MPD quando a soma do tempo e da duração da mensagem de evento é menor que o tempo dos dados de mídia no manifesto.  Em outras palavras, as mensagens de evento são removidas do manifesto quando o tempo de mídia ao qual elas se referem foi transferida para fora da janela de apresentação deslizante.

## <a name="4-scte-35-ingest-implementation-guidance-for-encoder-vendors"></a>4. Orientação de implementação de ingestão scte-35 para fornecedores de codificadores

As seguintes diretrizes são problemas comuns que podem impactar a implementação dessa especificação por um fornecedor de codificador.  As diretrizes abaixo foram coletadas com base no feedback de parceiros do mundo real para facilitar a implementação dessa especificação para outros. 

[SCTE-35] as mensagens são ingeridas em formato binário usando o esquema **"urn:scte:scte35:2013:bin"** para [MS-SSTR-Ingest] e o tipo **"scte35"** para ingestão [RTMP]. Para facilitar a conversão de intervalo [SCTE-35], que se baseia em carimbos de data/hora de apresentação de fluxo de transporte MPEG-2 (pt), um mapeamento entre PTS (pts_time + pts_adjustment da splice_time()) e a linha do tempo de mídia é fornecida pelo tempo de apresentação do evento (o campo fragment_absolute_time para ingestão Smooth e o campo de hora para ingestão RTMP). O mapeamento é necessário porque o valor de PTS de 33 bits passa aproximadamente a cada 26,5 horas.

A ingestão de fluxo suave [MS-SSTR-Ingest] requer que a Caixa de Dados de Mídia ('mdat') **deve** conter o **splice_info_section()** definido em [SCTE-35]. 

Para ingestão de RTMP, o atributo de sinalização da mensagem AMF é definido para o **splice_info_section()** codificado base64 definido em [SCTE-35].  

Quando as mensagens têm o formato descrito acima, elas são enviadas para clientes HLS, Smooth e DASH, conforme definido acima.  

Ao testar sua implementação com a plataforma Azure Media Services, inicie os testes com um LiveEvent "pass-through" primeiro, antes de passar para o teste em um LiveEvent codificado.

---

## <a name="change-history"></a>Histórico de alterações

| Data     | Alterações                                                                                                             |
| -------- | ------------------------------------------------------------------------------------------------------------------- |
| 07/2/19  | Ingerido RTMP revisado para suporte ao SCTE35, adicionado RTMP "onCuePoint" para Elemental Live                                  |
| 08/22/19 | Atualizado para adicionar OnUserDataEvent ao RTMP para metadados personalizados                                                          |
| 1/08/20  | Corrigimos erro no modo RTMP Simple e RTMP SCTE35. Mudou de "onCuePoint" para "onAdCue". Tabela de modo simples atualizada. |

## <a name="next-steps"></a>Próximas etapas
Exibir os roteiros de aprendizagem dos Serviços de Mídia.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
