---
title: Entidades nomeadas para saúde
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/28/2020
ms.author: aahi
ms.openlocfilehash: 6880391fb54791fe5f597de2305d24f8c0e47ec6
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88122520"
---
## <a name="health-entity-categories"></a>Categorias de entidades de integridade:

As categorias de entidade a seguir são retornadas pelo [análise de texto para integridade](../../how-tos/text-analytics-for-health.md).  Observe que somente o texto em inglês tem suporte nesta visualização de contêiner e apenas uma única versão de modelo é fornecida em cada imagem de contêiner.

### <a name="named-entity-recognition"></a>Reconhecimento de Entidade Nomeada

|Categoria  |Descrição   |
|----------|--------------|
| Idade | Paralisa. Por exemplo, *30 anos de idade*. |
| AdministrativeEvent | Um evento administrativo. |
| BodyStructure | Partes do corpo humano, incluindo sensoriais e outras estruturas. Por exemplo, *ARM*ou *coração*. | 
| CareEnvironment | O ambiente no qual o cuidado ou o tratamento é administrado. Por exemplo, *sala de emergência* | 
| ConditionQualifier | Níveis de condição. Por exemplo *leve*, *estendido*ou *difuso*. | 
| Diagnóstico | Condições médicas. Por exemplo, a *hipertensão*. | 
| Direção | Instruções. Por exemplo, *Left* ou *anterior*. | 
| Dosage | Tamanho ou quantidade de um medicamentos. Por exemplo, *25mg*.  | 
| Examename | Um método ou procedimento de exame. Por exemplo *, X-Ray*. | 
| RelationalOperator | Um operador que define uma relação entre duas entidades. Por exemplo, *menor que*ou `>=` .  | 
| MeasurementUnit | Uma unidade de medida (como uma porcentagem). | 
| Medidavalue | O valor numérico de uma unidade de medida. | 
| FamilyRelation | Uma relação familial. Por exemplo, *irmã*.  | 
| Frequência | Frequências.   | 
| Sexo | Gêneros. | 
| Gene | Uma entidade de Gene, como *TP53*.   | 
| HealthcareProfession | Método de administração de medicamentos. Por exemplo, *administração oral*. | 
| MedicationClass | Classes medicamentos. Por exemplo, *antibióticos*.  | 
| MedicationForm | Uma forma de medicamentos. Por exemplo, *cápsula*. | 
| MedicationName  | Genérico e marca nomeada medicamentos. Por exemplo, *Ibuprofen*. | 
| MedicationRoute | Método de administração de medicamentos. Por exemplo, *administração oral*. | 
| SymptomOrSign  | Sintomas de doença. Por exemplo, *Throat*. | 
| Hora | Maior. Por exemplo, *8 anos* ou *2: às 9h30 esta manhã* |
| Tratamentos  | Nomes de tratamento. Por exemplo, *terapia*. | 
| Variante | Uma variante genética da entidade Gene. | 

### <a name="relation-extraction"></a>Extração de relações

A extração de relações identifica conexões significativas entre os conceitos mencionados no texto. Por exemplo, uma relação "hora da condição" é encontrada pela Associação de um nome de condição com um horário. Análise de Texto para integridade pode identificar as seguintes relações:

|Categoria  |Descrição   |
|----------|--------------|
| DirectionOfBodyStructure | Direção de uma estrutura de corpo. |
| DirectionOfCondition | Direção de uma condição. |
| DirectionOfExamination | Direção de um exame. |
| DirectionOfTreatment | Direção de um tratamento. |
| TimeOfCondition | O tempo associado ao início de uma condição. |
| QualifierOfCondition | O qualificador associado para uma condição. |
| DosageOfMedication | Um dosage de medicamentos. |
| FormOfMedication | Uma forma de medicamentos. |
| RouteOfMedication | Uma rota ou modo de consumo de uma medicina. Por exemplo, *verbal*. |
| FrequencyOfMedication | A frequência com que um medicamentos é consumido. | 
| ValueOfCondition | Um valor numérico associado a uma condição. |
| UnitOfCondition | Uma unidade (como hora) associada a uma condição. |
| TimeOfMedication | A hora em que um medicamentos foi consumido. |
| TimeOfTreatment | A hora em que um tratamento foi administrado. | 
| FrequencyOfTreatment | A frequência com que um tratamento é administrado. |
| ValueOfExamination | Um valor numérico associado a um exame. | 
| UnitOfExamination | Uma unidade (como uma porcentagem) associada a um exame. |
| RelationOfExamination | Uma relação entre uma entidade e um exame. | 
| TimeOfExamination | O tempo associado a um exame. |
| Abreviação | Uma abreviação.  | 
