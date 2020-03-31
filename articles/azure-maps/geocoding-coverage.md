---
title: Cobertura de geocodificação | Mapas do Microsoft Azure
description: O processo de conversão do endereço de localização para coordenadas de latitude e longitude é conhecido como geocodificação. Neste artigo, você aprenderá sobre regiões com cobertura de geocodificação no Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 12/31/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 9836bd35b16c4c308b7c9d096b104c0cec68a34c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335645"
---
# <a name="azure-maps-geocoding-coverage"></a>Cobertura da geocodificação dos Mapas do Azure

Quando você procura um local com o Azure Maps, o serviço de pesquisa, por exemplo, [obter endereço de pesquisa,](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)pega seus termos de pesquisa e retorna as coordenadas de latitude e longitude. Esse processo é chamado de geocodificação. No entanto, o Azure Maps não tem o mesmo nível de informação e precisão para todas as regiões e países. Use este artigo para determinar que tipo de locais você pode procurar de forma confiável em cada região. 

A capacidade de geocodificação em um país/região depende da cobertura de dados rodoviários e da precisão de geocodificação do serviço de geocodificação. As categorizações a seguir são usadas para especificar o nível de suporte à geocodificação em cada país/região.
* **Pontos de endereço** - dados de endereços podem ser resolvidos para uma coordenada de latitude/longitude dentro do pacote de endereço (limite de propriedade). Às vezes chamado de “Telhado” preciso. Esse é o nível mais alto de precisão para endereços disponíveis. 
* **Números residenciais** - endereços são interpolados para uma coordenada de latitude/longitude na rua.
* **Nível da rua** - endereços são resolvidos para a coordenada de latitude/longitude da rua que contém o endereço. O número da casa não pode ser processado.
* **Nível de cidade** - nomes de locais de cidade que são suportados.

## <a name="americas"></a>Américas

| País/Região                                       | Pontos de endereço | Números de casa | Nível da rua | Nível da cidade | Pontos de interesse |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Anguila                                            |                 |                |              |      ✓     |          ✓         |
| Antártica                                          |                 |                |              |      ✓     |          ✓         |
| Antígua e Barbuda                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Argentina                                           |       ✓         |        ✓       |       ✓      |      ✓     |          ✓         |
| Aruba                                               |                 |                |              |      ✓     |          ✓         |
| Bahamas                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Barbados                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Belize                                              |                 |                |              |      ✓     |          ✓         |
| Bermudas                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bolívia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bonaire, Santo Eustáquio e Saba                   |                 |                |              |      ✓     |          ✓         |
| Brasil                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Canada                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Ilhas Cayman                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Chile                                               |       ✓         |        ✓       |       ✓      |      ✓     |          ✓         |
| Colômbia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Costa Rica                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Cuba                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Dominica                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Dominica                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Equador                                             |                 |                |       ✓      |      ✓     |          ✓         |
| El Salvador                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Ilhas Malvinas                                    |                 |                |              |      ✓     |          ✓         |
| Guiana Francesa                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Granada                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Guadalupe                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Guam                                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Guatemala                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Guiana                                              |                |             |           |      ✓     |                 |
| Haiti                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Honduras                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Jamaica                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Martinica                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| México                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Montserrat                                          |                 |                |              |      ✓     |          ✓         |
| Nicarágua                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Panamá                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Paraguai                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Peru                                                |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Porto Rico                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| São Bartolomeu                                    |                 |                |       ✓      |      ✓     |          ✓         |
| São Cristóvão e Névis                               |                 |                |       ✓      |      ✓     |          ✓         |
| Santa Lúcia                                         |                 |                |              |      ✓     |          ✓         |
| Saint Martin                                        |                 |                |       ✓      |      ✓     |          ✓         |
| São Pierre e Miquelon                           |                 |                |       ✓      |      ✓     |          ✓         |
| São Vincent e Granadinas                    |                 |                |              |      ✓     |          ✓         |
| Sint Maarten                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Geórgia do Sul e Ilhas Sandwich do Sul        |                 |                |              |      ✓     |          ✓         |
| Suriname                                            |                 |                |              |      ✓     |          ✓         |
| Trinidad e Tobago                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Territórios Insulares dos Estados Unidos                |                 |                |              |      ✓     |          ✓         |
| Estados Unidos da América                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uruguai                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Venezuela                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Ilhas Virgens Britânicas                              |                 |                |              |      ✓     |          ✓         |
| Ilhas Virgens dos Estados Unidos                                 |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |

## <a name="asia-pacific"></a>Pacífico Asiático

| País/Região                                      | Pontos de endereço |Números de casa | Nível da rua | Nível da cidade | Pontos de interesse |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Samoa Americana                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Austrália                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bangladesh                                          |                 |                |              |      ✓     |          ✓         |
| Butão                                              |                 |                |              |      ✓     |          ✓         |
| Território Britânico do Oceano Índico                      |                 |                |              |      ✓     |          ✓         |
| Brunei                                              |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Camboja                                            |                 |                |              |      ✓     |          ✓         |
| China                                               |                 |                |              |      ✓     |          ✓         |
| Ilha Christmas                                    |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Ilhas Cocos (Keeling)                             |                 |                |              |      ✓     |          ✓         |
| Ilhas Comores                                             |                 |                |              |      ✓     |          ✓         |
| Ilhas Cook                                        |                 |                |              |      ✓     |          ✓         |
| Fiji                                                |                  |                |              |      ✓     |          ✓        |
| Polinésia Francesa                                    |                 |                |              |      ✓     |          ✓         |
| Ilhas Heard e McDonald                   |                 |                |              |      ✓     |          ✓         |
| RAE de Hong Kong                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Indonésia                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Índia                                               |        ✓        |        ✓       |       ✓      |      ✓     |                   |
| Japão                                               |                 |                |              |      ✓     |          ✓         |
| Kiribati                                            |                 |                |              |      ✓     |          ✓         |
| Coreia do Sul                                         |                 |                |              |      ✓     |          ✓         |
| Laos                                                |                 |                |              |      ✓     |          ✓         |
| RAE de Macau                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Malásia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Micronésia                                          |                 |                |              |      ✓     |          ✓         |
| Mongólia                                            |                 |                |              |      ✓     |          ✓         |
| Nauru                                               |                 |                |              |      ✓     |          ✓         |
| Nepal                                               |                 |                |              |      ✓     |          ✓         |
| Nova Caledônia                                       |                 |                |              |      ✓     |          ✓         |
| Nova Zelândia                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Niue                                                |                 |                |              |      ✓     |          ✓         |
| Ilha Norfolk                                      |                 |                |              |      ✓     |          ✓         |
| Coreia do Norte                                         |                 |                |              |      ✓     |          ✓         |
| Ilhas Marianas do Norte                            |                 |                |       ✓      |      ✓     |          ✓         |
| Paquistão                                            |                 |                |              |      ✓     |          ✓         |
| Palau                                               |                 |                |              |      ✓     |          ✓         |
| Papua-Nova Guiné                                    |                 |                |              |      ✓     |          ✓         |
| Filipinas                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Pitcairn                                            |                 |                |              |      ✓     |          ✓         |
| Samoa                                               |                 |                |              |      ✓     |          ✓         |
| Ilhas Senkaku                                     |        ✓        |                |              |      ✓     |          ✓         |
| Singapura                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Ilhas Salomão                                     |                 |                |              |      ✓     |          ✓         |
| Ilhas Curilas do Sul                                     |        ✓        |                |              |      ✓     |          ✓         |
| Sri Lanka                                           |                 |                |              |      ✓     |          ✓         |
| Taiwan                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Tailândia                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Tokelau                                             |                 |                |              |      ✓     |          ✓         |
| Tonganês                                               |                 |                |              |      ✓     |          ✓         |
| Ilhas Turcas e Caicos                            |                 |                |              |      ✓     |          ✓         |
| Tuvalu                                              |                 |                |              |      ✓     |          ✓         |
| Vanuatu                                             |                 |                |              |      ✓     |          ✓         |
| Vietnã                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Wallis e Futuna                                   |                 |                |              |      ✓     |          ✓         |

## <a name="europe"></a>Europa

| País/Região                                      | Pontos de endereço |Números de casa | Nível da rua | Nível da cidade | Pontos de interesse |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Albânia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Andorra                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Armênia                                             |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Áustria                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Azerbaijão                                          |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Bélgica                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bósnia e Herzegovina                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bulgária                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Belarus                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Croácia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Chipre                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| República Tcheca                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Dinamarca                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Estônia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Ilhas Faroe                                       |                 |                |              |      ✓     |          ✓         |
| Finlândia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| França                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Geórgia                                             |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Alemanha                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Gibraltar                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Grécia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Groenlândia                                           |                 |                |              |      ✓     |          ✓         |
| Guernesey                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Hungria                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Islândia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Irlanda                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Ilha de Man                                         |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Itália                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Jan Mayen                                           |        ✓        |                |              |      ✓     |          ✓         |
| Jersey                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Cazaquistão                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kosovo                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Quirguistão                                          |                 |                |              |      ✓     |          ✓         |
| Letônia                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Liechtenstein                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Lituânia                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Luxemburgo                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Macedônia do Norte                                     |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Malta                                               |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Moldova                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Mônaco                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Montenegro                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Países Baixos                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Noruega                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Polônia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Portugal                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| +Açores e Madeira                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Romênia                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Federação Russa                                  |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| San Marino                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Sérvia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Eslováquia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Eslovênia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Espanha                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Svalbard                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Suécia                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Suíça                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Tadjiquistão                                          |                 |                |              |      ✓     |          ✓         |
| Turquia                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Turcomenistão                                        |                 |                |              |      ✓     |          ✓         |
| Ucrânia                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| United Kingdom                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uzbequistão                                          |                 |                |              |      ✓     |          ✓         |
| Cidade do Vaticano                                        |                 |                |       ✓      |      ✓     |          ✓         |


## <a name="middle-east-and-africa"></a>Oriente Médio e África

| País/Região                                      | Pontos de endereço |Números de casa | Nível da rua | Nível da cidade | Pontos de interesse |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Afeganistão                                         |                 |                |              |      ✓     |          ✓         |
| Argélia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Angola                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Bahrein                                             |        ✓        |       ✓        |       ✓      |      ✓     |          ✓         |
| Benin                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Botsuana                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Ilha Bouvet                                       |                 |                |              |      ✓     |          ✓         |
| Burkina Faso                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Burundi                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Camarões                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Cabo Verde                                          |                 |                |       ✓      |      ✓     |          ✓         |
| República Centro-Africana                            |                 |                |       ✓      |      ✓     |          ✓         |
| Chad                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Congo                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Côte d’Ivoire                                       |                 |                |       ✓      |      ✓     |          ✓         |
| República Democrática do Congo                    |                 |                |       ✓      |      ✓     |          ✓         |
| Djibuti                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Egito                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| República da Guiné Equatorial                      |                 |                |       ✓      |      ✓     |          ✓         |
| Eritreia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Etiópia                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Territórios Franceses do Sul|                        |                |              |      ✓     |          ✓         |
| Gabão                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Gâmbia                                              |                 |                |              |      ✓     |          ✓         |
| Gana                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Guiné                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Guiné-Bissau                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Irã                                                |                 |                |              |      ✓     |          ✓         |
| Iraque                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Israel                                              |        ✓        |       ✓        |              |      ✓     |          ✓         |
| Jordânia                                              |        ✓        |       ✓        |       ✓      |      ✓     |          ✓         |
| Quênia                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Kuwait                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Líbano                                             |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Lesoto                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Libéria                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Líbia                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Madagascar                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Malaui                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Maldivas                                            |                 |                |              |      ✓     |          ✓         |
| Mali                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Ilhas Marshall                                    |                 |                |              |      ✓     |          ✓         |
| Mauritânia                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Maurício                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Mayotte                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Marrocos                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Moçambique                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Myanmar                                             |                 |                |              |      ✓     |          ✓         |
| Namíbia                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Níger                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Nigéria                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Omã                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Catar                                               |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Reunião                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Ruanda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Santa Helena                                        |                 |                |              |      ✓     |          ✓         |
| Arábia Saudita                                        |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Senegal                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Seychelles                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Serra Leoa                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Somália                                             |                 |                |              |      ✓     |          ✓         |
| África do Sul                                        |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Sudão do Sul                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Sudão                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Suazilândia                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Síria                                               |                 |                |              |      ✓     |          ✓         |
| São Tomé e Príncipe                               |                 |                |       ✓      |      ✓     |          ✓         |
| Tanzânia                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Togo                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Tunísia                                             |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Uganda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Emirados Árabes Unidos                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Iêmen                                               |                 |                |              |      ✓     |          ✓         |
| Zâmbia                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Zimbábue                                            |                 |                |       ✓      |      ✓     |          ✓         |



## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a geocodificação de Mapas do Azure, consulte as páginas de referência [Pesquisa](https://docs.microsoft.com/rest/api/maps/search).

Saiba mais sobre o [áreas de cobertura para serviço de tráfego do Mapas](traffic-coverage.md). 

