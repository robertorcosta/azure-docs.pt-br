---
title: Monitor do Azure para mapeamentos da região de contêineres
description: Este artigo descreve os mapeamentos de região suportados entre o Azure Monitor para contêineres, o Log Analytics Workspace e métricas personalizadas.
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: a058f9cac987bb5c7130019f50370c6a176b09ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75403417"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Mapeamentos da região suportados pelo Azure Monitor para contêineres

 Ao habilitar o Monitor Azure para contêineres, apenas certas regiões são suportadas para vincular um espaço de trabalho do Log Analytics e um cluster AKS e coletar métricas personalizadas enviadas ao Azure Monitor.

## <a name="log-analytics-workspace-supported-mappings"></a>Mapeamentos suportados pelo espaço de trabalho do Log Analytics

Os recursos de cluster AKS ou o espaço de trabalho do Log Analytics podem residir em outras regiões, e a tabela a seguir mostra nossos mapeamentos.

|**Região do Cluster AKS** | **Região do espaço de trabalho do Log Analytics** |
|-----------------------|------------------------------------|
|**África** | |
|África do Sul |WestEurope |
|África do Sul |WestEurope |
|**Austrália** | |
|AustráliaLeste |AustráliaLeste |
|AustráliaCentral |AustráliaCentral |
|AustráliaCentral2 |AustráliaCentral |
|AustráliaLeste |AustráliaLeste |
|**Pacífico Asiático** | |
|EastAsia |EastAsia |
|SoutheastAsia |SoutheastAsia |
|**Brasil** | |
|BrasilSul | Centro-Sul |
|**Canada** ||
|Canadá Central |Canadá Central |
|CanadaEast |Canadá Central |
|**Europa** | |
|FrançaCentral |FrançaCentral |
|FrançaSul |FrançaCentral |
|Norte da Europa |Norte da Europa |
|UKSouth |UKSouth |
|UKWest |UKSouth |
|WestEurope |WestEurope |
|**Índia** | |
|CentralIndia |CentralIndia |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**Japão** | |
|JapanEast |JapanEast |
|JapãoOeste |JapanEast |
|**Coréia** | |
|KoreaCentral |KoreaCentral |
|KoreaSouth |KoreaCentral |
|**Nos** | |
|Centralus |Centralus|
|EastUS |EastUS |
|EastUS2 |EastUS2 |
|Westus |Westus |
|WestUS2 |WestUS2 |
|WestCentralUS<sup>1</sup>|EastUS<sup>1</sup>|
|Gov. dos EUA – Virgínia |Gov. dos EUA – Virgínia |

<sup>1</sup> Devido às restrições de capacidade, a região não está disponível na criação de novos recursos. Isso inclui um espaço de trabalho do Log Analytics. No entanto, os recursos vinculados pré-existentes na região devem continuar funcionando.

## <a name="custom-metrics-supported-regions"></a>Regiões personalizadas suportadas

A coleta de métricas dos nós e pods de clusters Azure Kubernetes Services (AKS) são suportados para publicação como métricas personalizadas apenas nas [seguintes regiões do Azure](../platform/metrics-custom-overview.md#supported-regions).

## <a name="next-steps"></a>Próximas etapas

Para começar a monitorar seu cluster AKS, [revise como habilitar o Monitor Azure para contêineres](container-insights-onboard.md) para entender os requisitos e métodos disponíveis para permitir o monitoramento.  
