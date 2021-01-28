---
title: Conectar um Data Factory ao Azure Purview
description: Saiba mais sobre como conectar um Data Factory ao Azure alcance
services: data-factory
ms.author: lle
author: lrtoyou1223
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: d1a07a3733bddc1516a4d2e19f3cac31a7702954
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944944"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Conectar Data Factory ao Azure alcance (versão prévia)
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo explicará como se conectar data factory ao Azure alcance e como relatar a linhagem de dados de atividades do ADF copiar dados, fluxo de dados e executar pacote SSIS.

## <a name="connect-data-factory-to-azure-purview"></a>Conectar data factory ao Azure alcance
O Azure alcance é um novo serviço de nuvem para uso por usuários de dados centralmente gerenciam a governança de dados em seu espaço de dados abrangendo ambientes locais e de nuvem. Você pode conectar seu data factory ao Azure alcance e a conexão permite que você aproveite o alcance do Azure para capturar dados de linhagem de cópia, fluxo de dados e executar pacote SSIS. Para saber como registrar data factory no Azure alcance, consulte [como conectar Azure data Factory e o Azure alcance](../purview/how-to-link-azure-data-factory.md). 

## <a name="report-lineage-data-to-azure-purview"></a>Relatar dados de linhagem para o Azure alcance
Quando os clientes executam a atividade copiar, fluxo de dados ou executar pacote SSIS no Azure Data Factory, os clientes podem obter a relação de dependência e ter uma visão geral de alto nível de todo o processo de fluxo de trabalho entre fontes de dados e destino.
Para saber como coletar a linhagem de Azure Data Factory, consulte [Data Factory linhagem](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities).

## <a name="next-steps"></a>Próximas etapas
[Guia do usuário de linhagem de catálogo](../purview/catalog-lineage-user-guide.md)

[Tutorial: enviar Data Factory dados de linhagem por push para o Azure alcance](turorial-push-lineage-to-purview.md)