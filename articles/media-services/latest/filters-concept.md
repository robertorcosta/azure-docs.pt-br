---
title: Definir filtros nos Serviços de Mídia do Azure
description: Este tópico descreve como criar filtros para que seu cliente possa usá-los na transmissão de seções específicas de um fluxo. Os Serviços de Mídia criam manifestos dinâmicos para atingir esse streaming seletivo.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 05/23/2019
ms.author: juliako
ms.openlocfilehash: fdf29924da31db0347938df89e698cb258c2336b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251460"
---
# <a name="filters"></a>Filtros

Ao fornecer seu conteúdo aos clientes (eventos de transmissão ao vivo ou vídeo demanda) seu cliente pode precisar de mais flexibilidade do que o descrito no arquivo manifesto do ativo padrão. O Azure Media Services oferece [Manifestos Dinâmicos baseados](filters-dynamic-manifest-overview.md) em filtros pré-definidos. 

Os filtros são regras do lado do servidor que permitem que seus clientes façam coisas como: 

- Reproduza apenas uma seção de um vídeo (em vez de reproduzir o vídeo inteiro). Por exemplo: 
  - Reduzir o manifesto para mostrar um subclipe de um evento ao vivo ("filtragem de subclipe"), ou
  - Corte do início de um vídeo ("corte de um vídeo").
- Entregue apenas as execuções especificadas e / ou faixas de idioma especificadas que são suportadas pelo dispositivo usado para reproduzir o conteúdo ("filtragem de renderização"). 
- Ajuste a Janela de Apresentação (DVR) para fornecer uma duração limitada da janela do DVR no leitor ("ajustar a janela de apresentação").

Os Serviços de Mídia permitem que você crie **filtros de conta** e **filtros de ativos** para o seu conteúdo. Além disso, você pode associar seus filtros pré-criados com um **localizador de streaming**.

## <a name="defining-filters"></a>Definir filtros

Existem dois tipos de filtros: 

* [Filtros de conta](https://docs.microsoft.com/rest/api/media/accountfilters) (global) - podem ser aplicados a qualquer recurso na conta dos Serviços de Mídia do Azure, têm vida útil da conta.
* [Filtros de ativos](https://docs.microsoft.com/rest/api/media/assetfilters) (local) - só podem ser aplicados a um ativo ao qual o filtro foi associado na criação, têm uma vida útil do recurso. 

**Os tipos filtros de** conta e **filtros de ativos** têm exatamente as mesmas propriedades para definir/descrever o filtro. Exceto ao criar o **Filtro de ativos**, você precisa especificar o nome do ativo ao qual deseja associar o filtro.

Dependendo do seu cenário, você decide qual tipo de filtro é mais adequado (Filtro de ativos ou Filtro de conta). Os filtros de conta são adequados para perfis de dispositivos (filtragem de renderização) em que os filtros de recursos podem ser usados para cortar um recurso específico.

Você usa as seguintes propriedades para descrever os filtros. 

|Nome|Descrição|
|---|---|
|firstQuality|A primeira taxa de bits de qualidade do filtro.|
|presentationTimeRange|O intervalo de tempo de apresentação. Esta propriedade é usada para filtrar os pontos de início / fim do manifesto, a duração da janela de apresentação e a posição de início ao vivo. <br/>Para mais informações, consulte [PresentationTimeRange](#presentationtimerange).|
|faixas|As condições de seleção de faixas. Para obter mais informações, consulte [faixas](#tracks)|

### <a name="presentationtimerange"></a>presentationTimeRange

Use essa propriedade com **filtros de ativo**. Não é recomendável definir a propriedade com **filtros de conta**.

|Nome|Descrição|
|---|---|
|**endTimestamp**|Aplica-se ao Video on Demand (VoD).<br/>Para a apresentação de Transmissão ao Vivo, ele é silenciosamente ignorado e aplicado quando a apresentação termina e o fluxo se torna VoD.<br/>Este é um valor longo que representa um ponto final absoluto da apresentação, arredondado para o próximo início gop mais próximo. A unidade é a escala de tempo, então um carimbo final de 18000000000 seria por 3 minutos.<br/>Use startTimestamp e endTimestamp para aparar os fragmentos que estarão na lista de reprodução (manifesto).<br/>Por exemplo, startTimestamp=40000000 e endTimestamp=100000000 usando a escala de tempo padrão gerará uma lista de reprodução que contém fragmentos entre 4 segundos e 10 segundos da apresentação do VoD. Se um fragmento ultrapassar o limite, o fragmento inteiro será incluído no manifesto.|
|**forceEndTimestamp**|Aplica-se apenas ao Live Streaming.<br/>Indica se a propriedade endTimestamp deve estar presente. Se for verdade, endTimestamp deve ser especificado ou um código de solicitação ruim é devolvido.<br/>Valores permitidos: false, true.|
|**liveBackoffDuration**|Aplica-se apenas ao Live Streaming.<br/> Esse valor define a última posição ao vivo que um cliente pode procurar.<br/>Usando essa propriedade, você pode atrasar a posição de reprodução ao vivo e criar um buffer do lado do servidor para jogadores.<br/>A unidade para esta propriedade é a escala de tempo (veja abaixo).<br/>A duração máxima de retorno ao vivo é de 300 segundos (30000000000).<br/>Por exemplo, um valor de 20000000000significa que o conteúdo mais recente disponível está 20 segundos atrasado da borda real ao vivo.|
|**presentationWindowDuration**|Aplica-se apenas ao Live Streaming.<br/>Use presentationWindowDuration para aplicar uma janela deslizante de fragmentos para incluir em uma lista de reprodução.<br/>A unidade para esta propriedade é a escala de tempo (veja abaixo).<br/>Por exemplo, definir presentationWindowDuration = 1200000000 para aplicar uma janela deslizante de dois minutos. Mídia dentro de 2 minutos da borda ao vivo será incluída na lista de reprodução. Se um fragmento ultrapassar o limite, todo o fragmento será incluído na lista de reprodução. A duração mínima da janela de apresentação é de 60 segundos.|
|**startTimestamp**|Aplica-se a Vídeo Demanda (VoD) ou Transmissão ao Vivo.<br/>Este é um valor longo que representa um ponto de partida absoluto do fluxo. O valor é arredondado para o próximo início de GOP mais próximo. A unidade é a escala de tempo, então um startTimede 150000000 seria por 15 segundos.<br/>Use startTimestamp e endTimestampp para aparar os fragmentos que estarão na lista de reprodução (manifesto).<br/>Por exemplo, startTimestamp=40000000 e endTimestamp=100000000 usando a escala de tempo padrão gerará uma lista de reprodução que contém fragmentos entre 4 segundos e 10 segundos da apresentação do VoD. Se um fragmento ultrapassar o limite, o fragmento inteiro será incluído no manifesto.|
|**Calendário**|Aplica-se a todos os carimbos e durações em um intervalo de tempo de apresentação, especificado como o número de incrementos em um segundo.<br/>O padrão é de 10000000 - dez milhões de incrementos em um segundo, onde cada incremento teria 100 nanossegundos de comprimento.<br/>Por exemplo, se você quiser definir um startTimestamp em 30 segundos, você usaria um valor de 300000000ao usar a escala de tempo padrão.|

### <a name="tracks"></a>Faixas

Você especifica uma lista de condições de propriedade de filtragem (FilterTrackPropertyConditions) com base na qual as faixas de sua transmissão (Transmissão ao vivo ou vídeo demanda) devem ser incluídas em manifesto criado dinamicamente. Os filtros são combinados usando uma operação lógica **E** e **OU**.

As condições de propriedade da faixa de filtro descrevem tipos de trilha, valores (descritos na tabela a seguir) e operações (Equal, NotEqual). 

|Nome|Descrição|
|---|---|
|**Bitrate**|Use a taxa de bits da faixa para filtragem.<br/><br/>O valor recomendado é um intervalo de bitrates, em bits por segundo. Por exemplo, "0-2427000".<br/><br/>Nota: embora você possa usar um valor de taxa de bits específico, como 250000 (bits por segundo), essa abordagem não é recomendada, pois as taxas de bits exatas podem variar de um ativo para outro.|
|**FourCC**|Use o valor de FourCC da faixa para filtragem.<br/><br/>O valor é o primeiro elemento do formato de codecs, conforme especificado na [6381 RFC](https://tools.ietf.org/html/rfc6381). Atualmente, há suporte para os seguintes codecs: <br/>Vídeo: "Avc1", "hev1", "hvc1"<br/>Para áudio: "Mp4a", "ec-3"<br/><br/>Para determinar os valores de FourCC para faixas em um ativo obter e examine o arquivo de manifesto.|
|**Linguagem**|Use a linguagem da faixa para filtragem.<br/><br/>O valor é a tag de um idioma que você deseja incluir, conforme especificado no RFC 5646. Por exemplo, “in”|
|**Nome**|Use o nome da faixa para filtragem.|
|**Tipo**|Use o tipo da faixa para filtragem.<br/><br/>Os seguintes valores são permitidos: "video", "áudio" ou "texto".|

### <a name="example"></a>Exemplo

O exemplo a seguir define um filtro de transmissão ao vivo: 

```json
{
  "properties": {
    "presentationTimeRange": {
      "startTimestamp": 0,
      "endTimestamp": 170000000,
      "presentationWindowDuration": 9223372036854776000,
      "liveBackoffDuration": 0,
      "timescale": 10000000,
      "forceEndTimestamp": false
    },
    "firstQuality": {
      "bitrate": 128000
    },
    "tracks": [
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Audio"
          },
          {
            "property": "Language",
            "operation": "NotEqual",
            "value": "en"
          },
          {
            "property": "FourCC",
            "operation": "NotEqual",
            "value": "EC-3"
          }
        ]
      },
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Video"
          },
          {
            "property": "Bitrate",
            "operation": "Equal",
            "value": "3000000-5000000"
          }
        ]
      }
    ]
  }
}
```

## <a name="associating-filters-with-streaming-locator"></a>Associar filtros com localizador de streaming

Você pode especificar uma lista de [filtros](filters-concept.md) de ativos ou contas no [localizador de streaming](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body). O [Dynamic Packager](dynamic-packaging-overview.md) aplica esta lista de filtros juntamente com os que seu cliente especifica na URL. Essa combinação gera um [Manifesto Dinâmico](filters-dynamic-manifest-overview.md), que é baseado em filtros no URL + filtros especificados no Localizador de Streaming. 

Veja os exemplos a seguir:

* [Associar filtros com localizador de streaming - .NET](filters-dynamic-manifest-dotnet-howto.md#associate-filters-with-streaming-locator)
* [Associar filtros com localizador de streaming - CLI](filters-dynamic-manifest-cli-howto.md#associate-filters-with-streaming-locator)

## <a name="updating-filters"></a>Atualização de filtros
 
**Os localizadores de streaming** não são atualizáveis enquanto os filtros podem ser atualizados. 

Não é recomendável atualizar a definição de filtros associados a um **localizador de streaming**publicado ativamente, especialmente quando o CDN está ativado. Servidores de streaming e CDNs podem ter caches internos que podem resultar em dados em cache obsoletos a serem devolvidos. 

Se a definição do filtro precisar ser alterada, considere criar um novo filtro e adicioná-lo à URL do **Localizador de Streaming** ou publicar um novo **Localizador de Streaming** que faça referência diretamente ao filtro.

## <a name="next-steps"></a>Próximas etapas

Os artigos a seguir mostram como criar filtros programaticamente.  

- [Criar filtros com APIs REST](filters-dynamic-manifest-rest-howto.md)
- [Criar filtros com o .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Criar filtros com a CLI](filters-dynamic-manifest-cli-howto.md)

