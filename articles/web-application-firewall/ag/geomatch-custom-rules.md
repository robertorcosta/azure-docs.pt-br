---
title: Regras personalizadas do Azure Web Application Firewall (WAF)
description: Este artigo é uma visão geral das regras personalizadas de geomatch do Web Application Firewall (WAF) no Azure Application Gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 01/31/2020
ms.author: victorh
ms.openlocfilehash: 6725e1de21dbd103850071f7511e2800c6bd7b69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961082"
---
# <a name="geomatch-custom-rules-preview"></a>Regras personalizadas do Geomatch (visualização)

As regras personalizadas permitem que você crie regras personalizadas para atender às necessidades exatas de seus aplicativos e políticas de segurança. Agora, você pode restringir o acesso às suas aplicações web por país/região. Como em todas as regras personalizadas, essa lógica pode ser composta com outras regras para atender às necessidades de sua aplicação.

Para criar uma regra personalizada de filtragem geográfica, basta selecionar *Geo-location* como o Tipo de Correspondência e, em seguida, selecionar o país ou países que deseja permitir/bloquear a partir de sua aplicação. Veja [como criar regras personalizadas no Powershell](configure-waf-custom-rules.md) e mais exemplos de regras personalizadas (create-custom-waf-rules.md) para obter mais informações.

> [!IMPORTANT]
> Essa versão prévia pública é fornecida sem um SLA e não deve ser usada para cargas de trabalho de produção. Determinados recursos podem não ter suporte, podem ter restrição ou podem não estar disponíveis em todos os locais do Azure. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.

## <a name="country-codes"></a>Códigos de país/região

Se você estiver usando o operador Geomatch, os seletores podem ser qualquer um dos seguintes códigos de país de dois dígitos. 

|Código do país | Nome do país |
| ----- | ----- |
| AD | Andorra |
| AE | Emirados Árabes Unidos|
| AF | Afeganistão|
| AG | Antígua e Barbuda|
| AL | Albânia|
| AM | Armênia|
| AO | Angola|
| AR | Argentina|
| AS | Samoa Americana|
| AT | Áustria|
| AU | Austrália|
| AZ | Azerbaijão|
| BA | Bósnia e Herzegovina|
| BB | Barbados|
| BD | Bangladesh|
| BE | Bélgica|
| BF | Burkina Faso|
| BG | Bulgária|
| BH | Bahrein|
| BI | Burundi|
| BJ | Benin|
| BL | São Bartolomeu|
| BN | Brunei Darussalam|
| BO | Bolívia|
| BR | Brasil|
| BS | Bahamas|
| BT | Butão|
| BW | Botsuana|
| BY | Belarus|
| BZ | Belize|
| CA | Canada|
| CD | República Democrática do Congo|
| CF | República Centro-Africana|
| CH | Suíça|
| CI | Costa do Marfim|
| CL | Chile|
| CM | Camarões|
| CN | China|
| CO | Colômbia|
| CR | Costa Rica|
| CU | Cuba|
| CV | Cabo Verde|
| CY | Chipre|
| CZ | República Tcheca|
| DE | Alemanha|
| DK | Dinamarca|
| DO | República Dominicana|
| DZ | Argélia|
| EC | Equador|
| EE | Estônia|
| EG | Egito|
| ES | Espanha|
| ET | Etiópia|
| FI | Finlândia|
| FJ | Fiji|
| FM | Micronésia, Estados Federados da|
| FR | França|
| GB | United Kingdom|
| GE | Geórgia|
| GF | Guiana Francesa|
| GH | Gana|
| GN | Guiné|
| GP | Guadalupe|
| GR | Grécia|
| GT | Guatemala|
| GY | Guiana|
| HK | RAE de Hong Kong|
| HN | Honduras|
| HR | Croácia|
| HT | Haiti|
| HU | Hungria|
| ID | Indonésia|
| IE | Irlanda|
| IL | Israel|
| IN | Índia|
| IQ | Iraque|
| IR | Irã, República Islâmica do|
| IS | Islândia|
| IT | Itália|
| JM | Jamaica|
| JO | Jordânia|
| JP | Japão|
| KE | Quênia|
| KG | Quirguistão|
| KH | Camboja|
| KI | Kiribati|
| KN | São Cristóvão e Névis|
| KP | Coreia, República Democrática Popular da|
| KR | Coreia, República da|
| KW | Kuwait|
| KY | Ilhas Cayman|
| KZ | Cazaquistão|
| LA | República Democrática do Laos|
| LB | Líbano|
| LI | Liechtenstein|
| LK | Sri Lanka|
| LR | Libéria|
| LS | Lesoto|
| LT | Lituânia|
| LU | Luxemburgo|
| LV | Letônia|
| LY | Líbia |
| MA | Marrocos|
| MD | Moldávia, República da|
| MG | Madagascar|
| MK | Macedônia do Norte|
| ML | Mali|
| MM | Myanmar|
| MN | Mongólia|
| MO | RAE de Macau|
| MQ | Martinica|
| MR | Mauritânia|
| MT | Malta|
| MV | Maldivas|
| MW | Malaui|
| MX | México|
| MY | Malásia|
| MZ | Moçambique|
| NA | Namíbia|
| NE | Níger|
| NG | Nigéria|
| NI | Nicarágua|
| NL | Países Baixos|
| Não | Noruega|
| NP | Nepal|
| NR | Nauru|
| NZ | Nova Zelândia|
| OM | Omã|
| PA | Panamá|
| PE | Peru|
| PH | Filipinas|
| PK | Paquistão|
| PL | Polônia|
| PR | Porto Rico|
| PT | Portugal|
| PW | Palau|
| PY | Paraguai|
| QA | Catar|
| RE | Reunião|
| RO | Romênia|
| RS | Sérvia|
| RU | Federação Russa|
| RW | Ruanda|
| SA | Arábia Saudita|
| SD | Sudão|
| SE | Suécia|
| SG | Singapura|
| SI | Eslovênia|
| SK | Eslováquia|
| SN | Senegal|
| SO | Somália|
| SR | Suriname|
| SS | Sudão do Sul|
| SV | El Salvador|
| SY | República Árabe Síria|
| SZ | Suazilândia|
| TC | Ilhas Turcas e Caicos|
| TG | Togo|
| TH | Tailândia|
| TN | Tunísia|
| TR | Turquia|
| TT | Trinidad e Tobago|
| TW | Taiwan|
| TZ | Tanzânia, República Unida da|
| UA | Ucrânia|
| UG | Uganda|
| EUA | Estados Unidos|
| UY | Uruguai|
| UZ | Uzbequistão|
| VC | São Vincent e Granadinas|
| VE | Venezuela|
| VG | Ilhas Virgens Britânicas|
| VI | Ilhas Virgens Americanas|
| VN | Vietnã|
| ZA | África do Sul|
| ZM | Zâmbia|
| ZW | Zimbábue|

## <a name="next-steps"></a>Próximas etapas

Depois de aprender sobre regras personalizadas, [crie suas próprias regras personalizadas.](create-custom-waf-rules.md)
