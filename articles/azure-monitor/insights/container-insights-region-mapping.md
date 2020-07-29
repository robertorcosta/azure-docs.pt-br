---
title: Azure Monitor para mapeamentos de região de contêineres
description: Este artigo descreve os mapeamentos de região com suporte entre Azure Monitor para contêineres, espaço de trabalho Log Analytics e métricas personalizadas.
ms.topic: conceptual
ms.date: 06/26/2019
ms.custom: references_regions
ms.openlocfilehash: 3e8ead78c5e0e534e07c1e2ab0e25eb3f5a90c38
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84194980"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Mapeamentos de região com suporte pelo Azure Monitor para contêineres

 Ao habilitar Azure Monitor para contêineres, somente determinadas regiões têm suporte para vincular um espaço de trabalho Log Analytics e um cluster AKS e coletar métricas personalizadas enviadas para Azure Monitor.

## <a name="log-analytics-workspace-supported-mappings"></a>Mapeamentos com suporte do Log Analytics Workspace

Os recursos de cluster AKS ou Log Analytics espaço de trabalho podem residir em outras regiões e a tabela a seguir mostra nossos mapeamentos.

|**Região de cluster AKS** | **Região do espaço de trabalho Log Analytics** |
|-----------------------|------------------------------------|
|**África** | |
|SouthAfricaNorth |WestEurope |
|SouthAfricaWest |WestEurope |
|**Austrália** | |
|AustraliaEast |AustraliaEast |
|AustraliaCentral |AustraliaCentral |
|AustraliaCentral2 |AustraliaCentral |
|AustraliaEast |AustraliaEast |
|**Pacífico Asiático** | |
|EastAsia |EastAsia |
|SoutheastAsia |SoutheastAsia |
|**Brasil** | |
|BrazilSouth | SouthCentralUS |
|**Canadá** ||
|Canadá Central |Canadá Central |
|CanadaEast |Canadá Central |
|**Europa** | |
|FranceCentral |FranceCentral |
|FranceSouth |FranceCentral |
|NorthEurope |NorthEurope |
|UKSouth |UKSouth |
|UKWest |UKSouth |
|WestEurope |WestEurope |
|**Índia** | |
|CentralIndia |CentralIndia |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**Japão** | |
|JapanEast |JapanEast |
|JapanWest |JapanEast |
|**Coreia do Sul** | |
|KoreaCentral |KoreaCentral |
|KoreaSouth |KoreaCentral |
|**EUA** | |
|CentralUS |CentralUS|
|EastUS |EastUS |
|EastUS2 |EastUS2 |
|WestUS |WestUS |
|WestUS2 |WestUS2 |
|WestCentralUS<sup>1</sup>|EastUS<sup>1</sup>|
|Gov. dos EUA – Virgínia |Gov. dos EUA – Virgínia |

<sup>1</sup> devido a restrições de capacidade, a região não está disponível durante a criação de novos recursos. Isso inclui um espaço de trabalho Log Analytics. No entanto, os recursos vinculados preexistentes na região devem continuar funcionando.

## <a name="custom-metrics-supported-regions"></a>Regiões com suporte para métricas personalizadas

A coleta de métricas dos nós de clusters AKS (serviços Kubernetess do Azure) e pods tem suporte para publicação como métricas personalizadas somente nas seguintes [regiões do Azure](../platform/metrics-custom-overview.md#supported-regions).

## <a name="next-steps"></a>Próximas etapas

Para começar a monitorar o cluster do AKS, examine [como habilitar o Azure monitor para contêineres](container-insights-onboard.md) para entender os requisitos e os métodos disponíveis para habilitar o monitoramento.  
