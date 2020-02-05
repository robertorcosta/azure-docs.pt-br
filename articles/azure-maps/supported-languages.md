---
title: Suporte à localização | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá sobre os idiomas com suporte para os serviços no Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: eec8e47e75a6c92be8f893af893761a9ccddc650
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025428"
---
# <a name="localization-support-in-azure-maps"></a>Suporte à localização no Azure Maps

O Azure Maps dá suporte a várias linguagens e exibições baseadas em país/região. Este artigo fornece os idiomas e as exibições com suporte para ajudar a orientar sua implementação do Azure Maps.


## <a name="azure-maps-supported-languages"></a>Idiomas com suporte nos Mapas do Azure

Os Mapas do Azure foram traduzidos para variedade de idiomas nos serviços. A tabela a seguir fornece os códigos de idioma com suporte para cada serviço.  
  

| ID         | Nome                   |  Mapas | Pesquisa | Roteamento | Clima | Incidentes de tráfego | Controle de mapa JS |
|------------|------------------------|:-----:|:------:|:-------:|:--------:|:-----------------:|:--------------:|
| af-ZA      | Africâner              |       |    ✓   |    ✓    |         |                   |                |
| ar-SA      | Árabe                 |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| bilhão-BD      | Bangla (Bangladesh)    |       |       |         |     ✓    |                   |                |
| bilhão-IN      | Bengali (Índia)         |       |       |         |     ✓    |                   |                |
| BS-BA      | Bósnio                 |       |       |         |     ✓    |                   |                |
| eu-ES      | Basco                 |       |    ✓   |         |         |                   |                |
| bg-BG      | Búlgaro              |   ✓   |    ✓   |    ✓    |     ✓     |                   |        ✓       |
| ca-ES      | Catalão                |       |    ✓   |         |    ✓      |                   |                |
| zh-HanS    | Chinês (simplificado)   |       |  zh-CN |         |     zh-CN   |                   |                |
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
| Fil-PH     | Filipino               |       |       |         |     ✓    |                   |                |
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
| IS-IS      | Islandês              |       |       |         |     ✓    |                   |                |
| id-ID      | Indonésio             |   ✓   |    ✓    |    ✓    |     ✓    |         ✓         |        ✓       |
| it-IT      | Italiano                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| ja-JP      | Japonês               |       |        |         |     ✓    |                   |                |
| kN-IN      | canarim                |       |       |         |     ✓    |                   |                |
| kk-KZ      | Cazaque                 |       |    ✓   |         |     ✓    |                   |                |
| ko-KR      | Coreano                 |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| es-419     | Espanhol (América Latina) |       |    ✓   |         |         |                   |                |
| lv-LV      | Letão                |       |    ✓   |         |     ✓    |         ✓         |                |
| lt-LT      | Lituano             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| MK-MK      | Macedônio             |       |       |         |     ✓    |                   |                |
| ms-MY      | Malaio (latino)          |   ✓   |    ✓   |    ✓    |    ✓   |                   |        ✓       |
| Mr-IN      | Marati                 |       |       |         |     ✓    |                   |                |
| nb-NO      | Norueguês bokmål       |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| NGT        | Neutral Ground Truth - idiomas oficiais para todas as regiões em scripts locais, se disponível |   ✓     |        |         |       |        |      ✓          |
| NGT-Latn   | Neutro verdade - exônimos latinos. Script latino será usado se disponível |   ✓     |        |         |         |                |        ✓         |
| pl-PL      | Polonês                 |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| pt-BR      | Português (Brasil)    |   ✓   |    ✓   |    ✓    |      ✓   |                   |        ✓       |
| pt-PT      | Português (Portugal)  |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| PA-IN      | Panjabi                 |       |       |         |     ✓    |                   |                |
| ro-RO      | Romeno               |       |    ✓    |         |     ✓    |         ✓         |                |
| ru-RU      | Russo                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| sr-Cyrl-RS | Sérvio (cirílico)     |       |   Sr-RS  |         |    Sr-RS     |                   |                |
| sr-Latn-RS | Sérvio (latino)        |       |       |         |     Sr-Latn    |                   |                |
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
| Uz-Latn-UZ | Uzbek                 |       |       |         |     ✓    |                   |                |
| vi-VN      | Vietnamita             |       |    ✓   |         |      ✓    |                  |                |


## <a name="azure-maps-supported-views"></a>Exibições com suporte do Azure Maps

> [!Note]
> Em 1º de agosto de 2019, o mapas do Azure foi lançado nos seguintes países/regiões:
>  * Argentina
>  * Índia
>  * Marrocos
>  * Paquistão
>
> Após 1º de agosto de 2019, o parâmetro de **exibição** definirá o conteúdo do mapa retornado para as novas regiões/países listados acima. O parâmetro de **exibição** do Azure Maps (também conhecido como "parâmetro de região do usuário") é um código de país ISO-3166 de duas letras que mostrará os mapas corretos para esse país/região especificando qual conjunto de conteúdo geoestruturado geoestruturado será retornado por meio dos serviços do Azure Maps, incluindo bordas e rótulos exibidos no mapa. 

Verifique se você configurou o parâmetro de **exibição** conforme necessário para as APIs REST e SDKs que seus serviços estão usando.
>  
>
>  **APIs REST:**
>  
>  Verifique se você configurou o parâmetro de exibição conforme necessário. O parâmetro View especifica qual conjunto de conteúdo de litígio geopolíticos é retornado por meio dos serviços do Azure Maps. 
>
>  Serviços REST do Azure Maps afetados:
>    
>    * Obter bloco de mapa
>    * Obter imagem do mapa 
>    * Obter difusa de pesquisa
>    * Obter POI de pesquisa
>    * Obter categoria de POI de pesquisa
>    * Obter pesquisa próxima
>    * Obter endereço de pesquisa
>    * Obter o endereço de pesquisa estruturado
>    * Obter reversão de endereço de pesquisa
>    * Obter endereço de cruz inverso da pesquisa
>    * Postar pesquisa dentro da geometria
>    * Versão prévia do lote de endereços de pesquisa
>    * Pós pesquisa endereço versão prévia do lote reverso
>    * Postar pesquisa ao longo do roteiro
>    * Visualização do lote difusa da pesquisa posterior
>
>    
>  **SDK**
>
>  Verifique se você configurou o parâmetro de **exibição** conforme necessário e se tem a versão mais recente do SDK Web e SDK do Android. SDKs afetados:
>
>    * SDK da Web do Azure Mapas
>    * SDK do Android do Azure Mapas

Por padrão, o parâmetro View é definido como **Unified**, mesmo que você não o tenha definido na solicitação. Determine o local dos usuários e, em seguida, defina o parâmetro de **exibição** corretamente para esse local. Como alternativa, você pode definir ' View = auto ', que retornará os dados do mapa com base no endereço IP da solicitação.  O parâmetro **View** no Azure Maps deve ser usado em conformidade com as leis aplicáveis, incluindo as leis sobre o mapeamento do país em que mapas, imagens e outros dados e conteúdo de terceiros que você está autorizado a acessar por meio do mapas do Azure são disponibilizados.


A tabela a seguir fornece exibições com suporte.

| Exibir         | Description                            |  Mapas | Pesquisa | JS Controle de Mapeamento |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | Emirados Árabes Unidos (modo de exibição árabe)    |   ✓   |        |     ✓          |
| AR           | Argentina (exibição de Argentinian)           |   ✓   |    ✓   |     ✓          |
| BH           | Bahrein (exibição em árabe)                 |   ✓   |        |     ✓          |
| IN           | Índia (exibição indiana)                    |   ✓   |   ✓     |     ✓          |
| IQ           | Iraque (exibição em árabe)                    |   ✓   |        |     ✓          |
| JO           | Jordânia (exibição em árabe)                  |   ✓   |        |     ✓          |
| KW           | Kuwait (exibição em árabe)                  |   ✓   |        |     ✓          |
| LB           | Líbano (exibição em árabe)                 |   ✓   |        |     ✓          |
| MA           | Marrocos (exibição de Tamazight)                |   ✓   |   ✓     |     ✓          |
| OM           | Omã (exibição em árabe)                    |   ✓   |        |     ✓          |
| PK           | Paquistão (exibição Rúpia)              |   ✓   |    ✓    |     ✓          |
| PS           | Autoridade Palestina (exibição em árabe)    |   ✓   |        |     ✓          |
| Garantia de qualidade           | Catar (exibição em árabe)                   |   ✓   |        |     ✓          |
| SA           | Arábia Saudita (exibição em árabe)            |   ✓   |        |     ✓          |
| SY           | Síria (exibição em árabe)                   |   ✓   |        |     ✓          |
| YE           | Iêmen (exibição em árabe)                   |   ✓   |        |     ✓          |
| Auto         | Retornar os dados do mapa com base no endereço IP da solicitação.|   ✓   |    ✓   |     ✓          |
| Unificado      | Exibição unificada (outras)                  |   ✓   |   ✓     |     ✓          |
