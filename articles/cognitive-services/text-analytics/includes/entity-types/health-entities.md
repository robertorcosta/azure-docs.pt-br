---
title: Entidades nomeadas para saúde
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/15/2020
ms.author: aahi
ms.openlocfilehash: 906e8e9e6cde80a2151c6b8671f64854cb431250
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108476"
---
## <a name="health-entity-categories"></a>Categorias de entidades de integridade:

As categorias de entidade a seguir são retornadas pelo [análise de texto para integridade](../../how-tos/text-analytics-for-health.md).  Observe que somente o texto em inglês tem suporte nesta visualização de contêiner e apenas uma única versão de modelo é fornecida em cada imagem de contêiner.

### <a name="named-entity-recognition"></a>Reconhecimento de Entidade Nomeada

|Categoria  |Descrição   |
|----------|--------------|
| IDADE | Paralisa. |
| BODY_STRUCTURE | Partes do corpo humano, incluindo sensoriais e outras estruturas. | 
| CONDITION_QUALIFIER | Níveis de condição como *leve*, *estendido*ou *difuso*. | 
| CORRETO | Condições médicas. Por exemplo, a *hipertensão* . | 
| DIREÇÃO | Direções como *esquerda* ou *anterior*. | 
| DOSAGE | Tamanho ou quantidade de um medicamentos.  | 
| EXAMINATION_NAME | Um método ou procedimento de exame. | 
| EXAMINATION_RELATION | uma associação entre uma unidade de medida e um exame.  | 
| EXAMINATION_UNIT | Uma unidade de medida para um exame. | 
| EXAMINATION_VALUE | O valor da unidade de medida de exame. | 
| FAMILY_RELATION | Uma relação familial, como *irmã*.  | 
| FREQUÊNCIA | Frequências.   | 
| SEXO | Gêneros. | 
| GENE | Uma entidade de Gene, como *TP53*.   | 
| MEDICATION_CLASS | Classes medicamentos. Por exemplo, *antibióticos*.  | 
| MEDICATION_NAME  | Genérico e marca nomeada medicamentos.| 
| ROUTE_OR_MODE  | Método de administração de medicamentos. | 
| SYMPTOM_OR_SIGN  | Sintomas de doença. | 
| TIME  | Maior. Por exemplo, "8 anos" ou "2: às 9h30 esta manhã" |
| TREATMENT_NAME  | Nomes de tratamento. | 
| VARIANT  | Uma variante genética da entidade gene | 

### <a name="relation-extraction"></a>Extração de relações

A extração de relações identifica conexões significativas entre os conceitos mencionados no texto. Por exemplo, uma relação "hora da condição" é encontrada pela Associação de um nome de condição com um horário. Análise de Texto para integridade pode identificar as seguintes relações:

* DIRECTION_OF_BODY_STRUCTURE  
* TIME_OF_CONDITION
* QUALIFIER_OF_CONDITION  
* DOSAGE_OF_MEDICATION 
* FORM_OF_MEDICATION  
* ROUTE_OR_MODE_OF_MEDICATION   
* STRENGTH_OF_MEDICATION 
* ADMINISTRATION_RATE_OF_MEDICATION   
* FREQUENCY_OF_MEDICATION 
* TIME_OF_MEDICATION 
* TIME_OF_TREATMENT 
* FREQUENCY_OF_TREATMENT  
* VALUE_OF_EXAMINATION
* UNIT_OF_EXAMINATION 
* RELATION_OF_EXAMINATION 
* TIME_OF_EXAMINATION  
* ABREVIAÇÃO 
