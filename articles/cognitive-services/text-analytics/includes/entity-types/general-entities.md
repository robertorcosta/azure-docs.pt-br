---
title: Entidades nomeadas gerais
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: 630f04bf2cc9e7de6331f9d25754a20fe2327d76
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779267"
---
As categorias de entidade a seguir são retornadas ao enviar solicitações para o `/entities/recognition/general` ponto de extremidade.

| Categoria   | Subcategoria | Descrição                          | Iniciando versão do modelo                                                    | Observações |
|------------|-------------|--------------------------------------|-------------------------------------------------------------|--------------------------------------|
| Pessoa     | N/D         | Nomes de pessoas.  | `2019-10-01`  | Também retornado pelo NER v 2.1 |
| Persontype | N/D         | Tipos de trabalho ou funções mantidas por uma pessoa. | `2020-02-01` | |
|Local    | N/D         | Pontos de referência naturais e humanos, estruturas, recursos geográficos e entidades geopolítica     |  `2019-10-01` | Também retornado pelo NER v 2.1 |
|Local     | Entidade geopolítica (GPE)        | Cidades, países/regiões, Estados.      | `2020-02-01` | |
|Local     | Visualização                       | Estruturas artificiais. | `2020-04-01` | |
|Local     | Geográfico       | Recursos geográficos e naturais, como rios, oceanos e desertos. |  `2020-04-01` | |
|Organização  | N/D | Empresas, grupos políticos, faixas musicais, clubes esporte, órgãos governamentais e organizações públicas.  | `2019-10-01` | As nacionalidades e Religions não são incluídas nesse tipo de entidade. Também retornado pelo NER v 2.1 |
|Organização | Médicos | Empresas e grupos médicos. | `2020-04-01` |  |
|Organização | Troca de estoque | Grupos de troca de estoque. | `2020-04-01` | |
| Organização | Esportes | Organizações relacionadas a esportes. | `2020-04-01` |  |
| Evento  | N/D | Eventos históricos, sociais e naturalmente ocorridos. | `2020-02-01` |  |
| Evento  | Cultural | Eventos culturais e feriados. | `2020-04-01` | |
| Evento  | Natural | Eventos que ocorrem naturalmente. | `2020-04-01` |  |
| Evento  | Esportes | Eventos esportivos.  | `2020-04-01` | |
| Produto | N/D | Objetos físicos de várias categorias. | `2020-02-01` | |
| Produto | Produtos de computação | Computando produtos. |  `2020-02-01 ` | |
| Habilidade | N/D | Capacidade, habilidade ou experiência. | `2020-02-01` |  |
| Endereço | N/D | Endereços de endereçamento completos.  | `2020-04-01` |  |
| PhoneNumber | N/D | Números de telefone (somente números de telefone dos EUA e da UE). | `2019-10-01` | Também retornado pelo NER v 2.1 |
| Email | N/D | Endereços de email. | `2019-10-01` | Também retornado pelo NER v 2.1 |
| URL | N/D | URLs para sites. | `2019-10-01` | Também retornado pelo NER v 2.1  |
| IP | N/D | Endereços IP de rede. | `2019-10-01` | Também retornado pelo NER v 2.1 |
| Datetime | N/D | Datas e horas do dia. | `2019-10-01` | Também retornado pelo NER v 2.1 | 
| DateTime | Data | Datas do calendário. | `2019-10-01` | Também retornado pelo NER v 2.1 |
| DateTime | Hora | Horas do dia | `2019-10-01` | Também retornado pelo NER v 2.1 |
| DateTime | DateRange | Intervalos de datas. | `2019-10-01` | Também retornado pelo NER v 2.1 |
| DateTime | TimeRange | Intervalos de tempo. | `2019-10-01` | Também retornado pelo NER v 2.1 |
| DateTime | Duração | Durações. | `2019-10-01` | Também retornado pelo NER v 2.1 |
| DateTime | Definição | Definido, horários repetidos. |  `2019-10-01` | Também retornado pelo NER v 2.1 |
| Quantidade | N/D | Números e quantidades numéricas. | `2019-10-01` | Também retornado pelo NER v 2.1  |
| Quantidade | Número | Números. | `2019-10-01` | Também retornado pelo NER v 2.1 |
| Quantidade | Percentual | Porcentagens.| `2019-10-01` | Também retornado pelo NER v 2.1 |
| Quantidade | Ordinal | Números ordinais. | `2019-10-01` | Também retornado pelo NER v 2.1 |
| Quantidade | Idade | Paralisa. | `2019-10-01` |  Também retornado pelo NER v 2.1 |
| Quantidade | Moeda | Moedas. | `2019-10-01` | Também retornado pelo NER v 2.1 |
| Quantidade | Dimensão | Dimensões e medidas. | `2019-10-01` | Também retornado pelo NER v 2.1 |
| Quantidade | Temperatura | Temperatura. | `2019-10-01` | Também retornado pelo NER v 2.1 |
