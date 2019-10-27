---
title: Cobertura de renderização nos Mapas do Azure | Microsoft Docs
description: Saiba mais sobre a cobertura de renderização nos Mapas do Azure
author: jingjing-z
ms.author: jinzh
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5affc9952cfe13ebfeede126051f2236499d1343
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934285"
---
# <a name="azure-maps-render-coverage"></a>Cobertura de renderização do Mapas do Azure

O Mapas do Azure usa blocos de varredura e blocos de vetor para criar mapas. Em sua resolução mais baixa, o mundo inteiro se ajusta a um único bloco. Em sua resolução mais alta, um único bloco representa 38 metros quadrados. Ao ampliar um mapa, portanto, você pode ver cada vez mais detalhes de continentes, regiões, cidades e ruas individuais. Para obter mais informações, consulte [Níveis de Zoom e grade de blocos](zoom-levels-and-tile-grid.md).

No entanto, o Mapas não têm o mesmo nível de informações e precisão para todas as regiões. As tabelas a seguir fornecem informações sobre o nível de detalhe renderizado que você pode esperar de cada região.

## <a name="legend"></a>Legenda

| Símbolo | Significado |
|--------|---------|
| ✓ | A região é representada com dados detalhados.   |
| Ø | A região é representada com dados simplificados. |


## <a name="africa"></a>África 


| País/Região | Blocos de Rastreamento Unificados | Blocos de Vetores Unificados |
| ------ | :------------------: | :------------------: |
| Argélia                          | ✓ | ✓ |
| Angola                           | ✓ | ✓ |
| Benin                            | ✓ | ✓ |
| Botsuana                         | ✓ | ✓ |
| Burquina Faso                     | ✓ | ✓ |
| Burundi                          | ✓ | ✓ |
| Cabo Verde                       | ✓ | ✓ |
| República dos Camarões                         | ✓ | ✓ |
| República Centro-Africana         | ✓ | Ø |
| Chad                             | ✓ | Ø |
| Ilhas Comores                          | ✓ | Ø |
| República Democrática do Congo | ✓ | ✓ |
| Côte d’Ivoire                    | ✓ | Ø |
| Djibuti                         | ✓ | Ø |
| Egito                            | ✓ | ✓ |
| Guiné Equatorial                | ✓ | Ø |
| Eritreia                          | ✓ | Ø |
| Etiópia                         | ✓ | Ø |
| Gabão                            | ✓ | ✓ |
| Gâmbia                           | ✓ | Ø |
| Gana                            | ✓ | ✓ |
| Guiné                           | ✓ | Ø |
| Guiné-Bissau                    | ✓ | Ø |
| Quênia                            | ✓ | ✓ |
| Lesoto                          | ✓ | ✓ |
| Libéria                          | ✓ | Ø |
| Líbia                            | ✓ | Ø |
| Madagascar                       | ✓ | Ø |
| Malaui                           | ✓ | ✓ |
| Mali                             | ✓ | ✓ |
| Mauritânia                       | ✓ | ✓ |
| Maurício                        | ✓ | ✓ |
| Mayotte                          | ✓ | ✓ |
| Marrocos                          | ✓ | ✓ |
| Moçambique                       | ✓ | ✓ |
| Namíbia                          | ✓ | ✓ |
| Níger                            | ✓ | ✓ |
| Nigéria                          | ✓ | ✓ |
| Reunião                          | ✓ | ✓ |
| Ruanda                           | ✓ | ✓ |
| Santa Helena, Ascensão e Tristão da Cunha | ✓ | Ø |
| São Tomé e Príncipe            | ✓ | Ø |
| Senegal                          | ✓ | ✓ |
| Serra Leoa                     | ✓ | ✓ |
| Somália                          | ✓ | ✓ |
| África do Sul                     | ✓ | ✓ |
| Sudão do Sul                      | ✓ | ✓ |
| Sudão                            | ✓ | ✓ |
| Suazilândia                        | ✓ | ✓ |
| República Unida da Tanzânia      | ✓ | ✓ |
| Togo                             | ✓ | ✓ |
| Tunísia                          | ✓ | ✓ |
| Uganda                           | ✓ | ✓ |
| Zâmbia                           | ✓ | ✓ |
| Zimbábue                         | ✓ | ✓ |

## <a name="americas"></a>Américas

| País/Região | Blocos de Rastreamento Unificados | Blocos de Vetores Unificados |
| ------ | :------------------: | :------------------: |
| Anguila                  | ✓ | ✓ |
| Antígua e Barbuda       | ✓ | ✓ |
| Argentina                 | ✓ | ✓ |
| Aruba                     | ✓ | ✓ |
| Bahamas                   | ✓ | ✓ |
| Barbados                  | ✓ | ✓ |
| Belize                    | ✓ | ✓ |
| Bermuda                   | ✓ | ✓ |
| Estado Plurinacional da Bolívia | ✓ | ✓ |
| Bonaire, Santo Eustáquio e Saba | ✓ | ✓ |
| Brasil                    | ✓ | ✓ |
| Canadá                    | ✓ | ✓ |
| Ilhas Cayman            | ✓ | ✓ |
| Chile                     | ✓ | ✓ |
| Colômbia                  | ✓ | ✓ |
| Costa Rica                | ✓ | ✓ |
| Cuba                      | ✓ | ✓ |
| Curaçao                   | ✓ | ✓ |
| Dominica                  | ✓ | ✓ |
| República Dominicana        | ✓ | ✓ |
| Equador                   | ✓ | ✓ |
| Ilhas Falkland (Malvinas) | ✓ | ✓ |
| Guiana Francesa             | ✓ | ✓ |
| Groenlândia                 | ✓ | Ø |
| Granada                   | ✓ | ✓ |
| Guadalupe                | ✓ | ✓ |
| Guatemala                 | ✓ | ✓ |
| Guiana                    | ✓ | ✓ |
| Haiti                     | ✓ | ✓ |
| Honduras                  | ✓ | ✓ |
| Jamaica                   | ✓ | ✓ |
| Martinica                | ✓ | ✓ |
| México                    | ✓ | ✓ |
| Montserrat                | ✓ | ✓ |
| Nicarágua                 | ✓ | ✓ |
| Ilhas Marianas do Norte  | ✓ | ✓ |
| Panamá                    | ✓ | ✓ | 
| Paraguai                  | ✓ | ✓ |
| Peru                      | ✓ | ✓ |
| Porto Rico               | ✓ | ✓ |
| Quebec (Canadá)           | ✓ | ✓ |
| São Bartolomeu          | ✓ | ✓ |
| São Cristóvão e Nevis     | ✓ | ✓ |
| Santa Lúcia               | ✓ | ✓ |
| Saint Martin (francesa)     | ✓ | ✓ |
| São Pierre e Miquelon | ✓ | ✓ |
| São Vicente e Granadinas | ✓ | ✓ |
| Sint Maarten (holandesa)      | ✓ | ✓ |
| Ilhas Geórgia do Sul e Sandwich do Sul | ✓ | ✓ |
| Suriname                  | ✓ | ✓ |
| Trinidad e Tobago       | ✓ | ✓ |
| Ilhas Turcos e Caicos  | ✓ | ✓ |
| Estados Unidos             | ✓ | ✓ |
| Uruguai                   | ✓ | ✓ |
| Venezuela                 | ✓ | ✓ |
| Ilhas Virgens Britânicas   | ✓ | ✓ |
| Ilhas Virgens Americanas      | ✓ | ✓ |

## <a name="asia"></a>Ásia 

| País/Região | Blocos de Rastreamento Unificados | Blocos de Vetores Unificados |
| ------ | :------------------: | :------------------: |
| Afeganistão               |   | Ø |
| Bahrein                   | ✓ | ✓ |
| Bangladesh                |   | Ø |
| Butão                    |   | Ø |
| Território Britânico do Oceano Índico |   | Ø |
| Brunei                    | ✓ | ✓ |
| Camboja                  |   | Ø |
| China                     |   | Ø |
| Ilhas Cocos (Keeling)   |   | Ø |
| República Democrática Popular da Coreia |   | Ø |
| RAE de Hong Kong             | ✓ | ✓ |
| Índia                     | Ø | ✓ | 
| Indonésia                 | ✓ | ✓ |
| Irã                      |   | Ø |
| Iraque                      | ✓ | ✓ |
| Israel                    |   | ✓ |
| Japão                     |   | Ø |
| Jordânia                    | ✓ | ✓ |
| Cazaquistão                |   | ✓ |
| Kuwait                    | ✓ | ✓ |
| Quirguistão                |   | Ø |
| República Democrática Popular do Laos |   | Ø |
| Líbano                   | ✓ | ✓ |
| RAE de Macau                 | ✓ | ✓ |
| Malásia                  | ✓ | ✓ |
| Maldivas                  |   | Ø |
| Mongólia                  |   | Ø |
| Myanmar                   |   | Ø |
| Nepal                     |   | Ø |
| Omã                      | ✓ | ✓ |
| Paquistão                  |   | Ø |
| Filipinas               | ✓ | ✓ |
| Catar                     | ✓ | ✓ |
| República da Coreia         | ✓ | Ø |
| Arábia Saudita              | ✓ | ✓ |
| Ilhas Senkaku           |   | ✓ |
| Cingapura                 | ✓ | ✓|
| Sri Lanka                 |   | Ø |
| República Árabe da Síria      |   | Ø |
| Taiwan                    | ✓ | ✓ |
| Tadjiquistão                |   | Ø |
| Tailândia                  | ✓ | ✓ |
| Timor-Leste               |   | Ø |
| Turcomenistão              |   | Ø |
| Emirados Árabes Unidos      | ✓ | ✓ |
| Territórios Insulares dos Estados Unidos |   | Ø |
| Uzbequistão                |   | Ø |
| Vietnã                   | ✓ | ✓ |
| Iêmen                     | ✓ | ✓ |

## <a name="oceania"></a>Oceania

| País/Região | Blocos de Rastreamento Unificados | Blocos de Vetores Unificados |
| ------ | :------------------: | :------------------: |
| Samoa Americana            |   | ✓ |
| Austrália                 | ✓ | ✓ |
| Ilhas Cook              |   | Ø |
| Fiji                      |   | Ø |
| Polinésia Francesa          |   | Ø |
| Guam                      | ✓ | ✓ |
| Kiribati                  |   | Ø |
| Ilhas Marshall          |   | Ø |
| Micronésia                |   | Ø |
| Nauru                     |   | Ø |
| Nova Caledônia             |   | Ø |
| Nova Zelândia               | ✓ | ✓ |
| Niue                      |   | Ø |
| Ilha Norfolk            |   | Ø |
| Palau                     |   | Ø |
| Papua-Nova Guiné          |   | Ø |
| Pitcairn                  |   | Ø |
| Samoa                     |   | Ø |
| Ilhas Salomão           |   | Ø|
| Tokelau                   |   | Ø |
| Tonganês                     |   | Ø |
| Tuvalu                    |   | Ø |
| Vanuatu                   |   | Ø |
| Wallis e Futuna         |   | Ø |


## <a name="europe"></a>Europa

| País/Região | Blocos de Rastreamento Unificados | Blocos de Vetores Unificados |
| ------ | :------------------: | :------------------: |
| Albânia                   | ✓ | ✓ |
| Andorra                   | ✓ | ✓ |
| Armênia                   | ✓ | Ø |
| Áustria                   | ✓ | ✓ |
| Azerbaijão                | ✓ | Ø |
| Belarus                   | Ø | ✓ |
| Bélgica                   | ✓ | ✓ |
| Bósnia e Herzegovina        | ✓ | ✓ |
| Bulgária                  | ✓ | ✓ |
| Croácia                   | ✓ | ✓ |
| Chipre                    | ✓ | ✓ |
| República Tcheca            | ✓ | ✓ |
| Dinamarca                   | ✓ | ✓ |
| Estônia                   | ✓ | ✓ |
| Ilhas Faroe             | ✓ | Ø |
| Finlândia                   | ✓ | ✓ |
| França                    | ✓ | ✓ |
| Geórgia                   | ✓ | Ø |
| Alemanha                   | ✓ | ✓ |
| Gibraltar                 | ✓ | ✓ |
| Grécia                    | ✓ | ✓ |
| Guernesey                  | ✓ | ✓ |
| Hungria                   | ✓ | ✓ |
| Islândia                   | ✓ | ✓ |
| Irlanda                   | ✓ | ✓ |
| Ilha de Man               | ✓ | ✓ |
| Itália                     | ✓ | ✓ |
| Svalbard                 | ✓ | ✓ |
| Jersey                    | ✓ | ✓ |
| Letônia                    | ✓ | ✓ |
| Liechtenstein             | ✓ | ✓ |
| Lituânia                 | ✓ | ✓ |
| Luxemburgo                | ✓ | ✓ |
| Macedônia do Norte           | ✓ | ✓ |
| Malta                     | ✓ | ✓ |
| Moldova                   | ✓ | ✓ |
| Mônaco                    | ✓ | ✓ |
| Montenegro                | ✓ | ✓ |
| Holanda               | ✓ | ✓ |
| Noruega                    | ✓ | ✓ |
| Polônia                    | ✓ | ✓ |
| Portugal                  | ✓ | ✓ |
| Romênia                   | ✓ | ✓ |
| Federação Russa        | ✓ | ✓ |
| San Marino                | ✓ | ✓ |
| Sérvia                    | ✓ | ✓ |
| Eslováquia                  | ✓ | ✓ |
| Eslovênia                  | ✓ | ✓ |
| Ilhas Curilas do Sul           | ✓ | ✓ |
| Espanha                     | ✓ | ✓ |
| Svalbard                  | ✓ | ✓ |
| Suécia                    | ✓ | ✓ |
| Suíça               | ✓ | ✓ |
| Turquia                    | ✓ | ✓ |
| Ucrânia                   | ✓ | ✓ |
| Reino Unido            | ✓ | ✓ |
| Cidade do Vaticano              | ✓ | ✓ |

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre a renderização de Mapas do Azure, consulte [Níveis de Zoom e grade de blocos](zoom-levels-and-tile-grid.md).

Saiba mais sobre [áreas de cobertura para serviço de tráfego do Mapas](routing-coverage.md). 