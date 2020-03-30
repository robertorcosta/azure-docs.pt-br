---
title: Suporte à localização | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá sobre idiomas suportados para os serviços no Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3b3733dfc32dae21ddcf4c5f73cddf9ad6b7fc59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334065"
---
# <a name="localization-support-in-azure-maps"></a>Suporte à localização no Azure Maps

O Azure Maps suporta vários idiomas e pontos de vista baseados em país/região. Este artigo fornece os idiomas e visualizações suportados para ajudar a orientar a implementação do Azure Maps.


## <a name="azure-maps-supported-languages"></a>Idiomas com suporte nos Mapas do Azure

O Azure Maps foi localizado em idiomas variados em seus serviços. A tabela a seguir fornece os códigos de idioma com suporte para cada serviço.  
  

| ID         | Nome                   |  Mapas | Search | Roteamento | Clima | Incidentes de tráfego | Controle de mapa JS |
|------------|------------------------|:-----:|:------:|:-------:|:--------:|:-----------------:|:--------------:|
| af-ZA      | Africâner              |       |    ✓   |    ✓    |         |                   |                |
| ar-SA      | Árabe                 |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| bn-BD      | Bangla (Bangladesh)    |       |       |         |     ✓    |                   |                |
| bn-IN      | Bangla (Índia)         |       |       |         |     ✓    |                   |                |
| bs-BA      | Bósnio                 |       |       |         |     ✓    |                   |                |
| eu-ES      | Basco                 |       |    ✓   |         |         |                   |                |
| bg-BG      | Búlgaro              |   ✓   |    ✓   |    ✓    |     ✓     |                   |        ✓       |
| ca-ES      | Catalão                |       |    ✓   |         |    ✓      |                   |                |
| zh-HanS    | Chinês (Simplificado)   |       |  zh-CN |         |     zh-CN   |                   |                |
| zh-HanT    | Chinês (RAE de Hong Kong)  |  |   |    |    zh-HK   |                   |           |
| zh-HanT    | Chinês (Taiwan)  | zh-TW |  zh-TW |  zh-TW  |    zh-TW   |                   |      zh-TW     |
| hr-HR      | Croata               |       |    ✓   |         |    ✓      |                   |                |
| cs-CZ      | Tcheco                  |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| da-DK      | Dinamarquês                 |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| nl-BE      | Holandês (Bélgica)        |       |    ✓   |         |      ✓    |                   |                |
| nl-NL      | Holandês (Países Baixos)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-AU      | Inglês (Austrália)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-NZ      | Inglês (Nova Zelândia)  |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-GB      | Inglês (Grã-Bretanha) |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| pt-BR      | Inglês (EUA)          |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| et-EE      | Estoniano               |       |    ✓   |         |      ✓    |         ✓         |                |
| fil-PH     | Filipino               |       |       |         |     ✓    |                   |                |
| fi-FI      | Finlandês                |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-FR      | Francês                 |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-CA      | Francês (Canadá)      |       |    ✓   |         |     ✓     |                   |                |
| gl-ES      | Galego               |       |    ✓   |         |         |                   |                |
| de-DE      | Alemão                 |   ✓   |    ✓   |    ✓    |   ✓      |         ✓         |        ✓       |
| el-GR      | Grego                  |   ✓   |    ✓   |    ✓    |    ✓     |         ✓         |        ✓       |
| gu-IN      | Guzerate                |       |       |         |     ✓    |                   |                |
| he-IL      | Hebraico                 |       |    ✓   |         |     ✓    |         ✓         |                |
| hi-IN      | Híndi                  |       |        |         |     ✓    |                   |                |
| hu-HU      | Húngaro              |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| is-IS      | Islandês              |       |       |         |     ✓    |                   |                |
| id-ID      | Indonésio             |   ✓   |    ✓    |    ✓    |     ✓    |         ✓         |        ✓       |
| it-IT      | Italiano                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| ja-JP      | Japonês               |       |        |         |     ✓    |                   |                |
| kn-IN      | canarim                |       |       |         |     ✓    |                   |                |
| kk-KZ      | Cazaque                 |       |    ✓   |         |     ✓    |                   |                |
| ko-KR      | Coreano                 |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| es-419     | Espanhol (América Latina) |       |    ✓   |         |         |                   |                |
| lv-LV      | Letão                |       |    ✓   |         |     ✓    |         ✓         |                |
| lt-LT      | Lituano             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| mk-MK      | Macedônio             |       |       |         |     ✓    |                   |                |
| ms-MY      | Malaio (latino)          |   ✓   |    ✓   |    ✓    |    ✓   |                   |        ✓       |
| mr-IN      | Marati                 |       |       |         |     ✓    |                   |                |
| nb-NO      | Norueguês bokmål       |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| NGT        | Neutral Ground Truth - idiomas oficiais para todas as regiões em scripts locais, se disponível |   ✓     |        |         |       |        |      ✓          |
| NGT-Latn   | Neutro verdade - exônimos latinos. Script latino será usado se disponível |   ✓     |        |         |         |                |        ✓         |
| pl-PL      | Polonês                 |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| pt-BR      | Português (Brasil)    |   ✓   |    ✓   |    ✓    |      ✓   |                   |        ✓       |
| pt-PT      | Português (Portugal)  |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| pa-IN      | Panjabi                 |       |       |         |     ✓    |                   |                |
| ro-RO      | Romeno               |       |    ✓    |         |     ✓    |         ✓         |                |
| ru-RU      | Russo                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| sr-Cyrl-RS | Sérvio (cirílico)     |       |   sr-RS  |         |    sr-RS     |                   |                |
| sr-Latn-RS | Sérvio (latino)        |       |       |         |     sr-latn    |                   |                |
| sk-SK      | Eslovaco             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| sl-SL      | Esloveno              |   ✓   |    ✓   |    ✓    |     ✓    |                   |        ✓       |
| es-ES      | Espanhol                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| es-MX      | Espanhol (México)       |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| sv-SE      | Sueco                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| ta-IN      | Tâmil (Índia)                 |       |       |         |     ✓    |                   |                |
| te-IN      | Télugo (Índia)                 |       |       |         |     ✓    |                   |                |
| th-TH      | Tailandês                   |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| tr-TR      | Turco                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| uk-UA      | Ucraniano               |       |    ✓   |         |     ✓    |                   |                |
| ur-PK      | Urdu                 |       |       |         |     ✓    |                   |                |
| uz-Latn-UZ | Uzbek                 |       |       |         |     ✓    |                   |                |
| vi-VN      | Vietnamita             |       |    ✓   |         |      ✓    |                  |                |


## <a name="azure-maps-supported-views"></a>Azure Maps suportado visualizações

> [!Note]
> Em 1º de agosto de 2019, o Azure Maps foi lançado nos seguintes países/regiões:
>  * Argentina
>  * Índia
>  * Marrocos
>  * Paquistão
>
> Após 1º de agosto de 2019, o parâmetro **Exibir** definirá o conteúdo do mapa retornado para as novas regiões/países listados acima. O parâmetro Azure Maps **View** (também conhecido como "parâmetro da região do usuário") é um código de país ISO-3166 de duas letras que mostrará os mapas corretos para aquele país/região especificando qual conjunto de conteúdo politicamente disputado é devolvido através dos serviços do Azure Maps, incluindo bordas e rótulos exibidos no mapa. 

Certifique-se de configurar o parâmetro **Exibir** conforme necessário para as APIs REST e os SDKs, que seus serviços estão usando.
>  
>
>  **APIs de descanso:**
>  
>  Certifique-se de que você configurou o parâmetro 'Exibir' conforme necessário. O parâmetro de exibição especifica qual conjunto de conteúdo disputado geopoliticamente é devolvido através dos serviços do Azure Maps. 
>
>  Azure Maps Serviços DE DESCANSO afetados:
>    
>    * Obter azulejo de mapa
>    * Obter imagem do mapa 
>    * Obter pesquisa fuzzy
>    * Obter poi de pesquisa
>    * Obter a categoria POI de pesquisa
>    * Obter pesquisa nas proximidades
>    * Obter endereço de pesquisa
>    * Obter endereço de pesquisa estruturado
>    * Obter Reversão de Endereço de Pesquisa
>    * Obter endereço de pesquisa reverse cross street
>    * Pesquisa POST dentro da geometria
>    * Visualização do lote do endereço de pesquisa pós
>    * Visualização do lote reverso do endereço de pesquisa pós
>    * Post Search Along Route
>    * Visualização do lote fuzzy da pesquisa pós
>
>    
>  **Sdks:**
>
>  Certifique-se de configurar o parâmetro **View** conforme necessário e que você tenha a versão mais recente do Web SDK e do Android SDK. SDKs afetados:
>
>    * SDK da Web do Azure Mapas
>    * SDK do Android do Azure Mapas

Por padrão, o parâmetro Exibir é definido como **Unificado,** mesmo que você não tenha definido na solicitação. Determine a localização de seus usuários. Em seguida, defina o parâmetro **Exibir** corretamente para esse local. Alternativamente, você pode definir 'View=Auto', que retornará os dados do mapa com base no endereço IP da solicitação.  O parâmetro **Exibir** no Azure Maps deve ser usado em conformidade com as leis aplicáveis, incluindo as leis sobre mapeamento do país onde mapas, imagens e outros dados e conteúdo de terceiros que você está autorizado a acessar via Azure Maps é disponibilizado.


A tabela a seguir fornece visualizações suportadas.

| Visualizar         | Descrição                            |  Mapas | Search | Controle do Mapa JS |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | Emirados Árabes Unidos (Vista Árabe)    |   ✓   |        |     ✓          |
| AR           | Argentina (Vista Argentina)           |   ✓   |    ✓   |     ✓          |
| BH           | Bahrein (Vista Árabe)                 |   ✓   |        |     ✓          |
| IN           | Índia (Visão Indiana)                    |   ✓   |   ✓     |     ✓          |
| IQ           | Iraque (Vista Árabe)                    |   ✓   |        |     ✓          |
| JO           | Jordânia (Vista Árabe)                  |   ✓   |        |     ✓          |
| KW           | Kuwait (Vista Árabe)                  |   ✓   |        |     ✓          |
| LB           | Líbano (Vista Árabe)                 |   ✓   |        |     ✓          |
| MA           | Marrocos (Vista marroquina)                |   ✓   |   ✓     |     ✓          |
| OM           | Omã (Vista Árabe)                    |   ✓   |        |     ✓          |
| PK           | Paquistão (Vista paquistanesa)              |   ✓   |    ✓    |     ✓          |
| PS           | Autoridade Palestina (Vista Árabe)    |   ✓   |        |     ✓          |
| QA           | Catar (Vista Árabe)                   |   ✓   |        |     ✓          |
| SA           | Arábia Saudita (Vista Árabe)            |   ✓   |        |     ✓          |
| SY           | Síria (Vista Árabe)                   |   ✓   |        |     ✓          |
| YE           | Iêmen (Vista Árabe)                   |   ✓   |        |     ✓          |
| Auto         | Retorne os dados do mapa com base no endereço IP da solicitação.|   ✓   |    ✓   |     ✓          |
| Unificada      | Visão Unificada (Outros)                  |   ✓   |   ✓     |     ✓          |
