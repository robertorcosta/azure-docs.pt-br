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
ms.openlocfilehash: 7dc05c88416bb2a23221029bc04c506271a86652
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108340"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Conectar Data Factory ao Azure alcance (versão prévia)
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo explicará como se conectar data factory ao Azure alcance e como relatar a linhagem de dados de atividades do ADF copiar dados, fluxo de dados e executar pacote SSIS.

## <a name="connect-data-factory-to-azure-purview"></a>Conectar data factory ao Azure alcance
O Azure alcance é um novo serviço de nuvem para uso por usuários de dados centralmente gerenciam a governança de dados em seu espaço de dados abrangendo ambientes locais e de nuvem. Você pode conectar seu data factory ao Azure alcance e a conexão permite que você aproveite o alcance do Azure para capturar dados de linhagem de cópia, fluxo de dados e executar pacote SSIS. Para saber como registrar data factory no Azure alcance, consulte [como conectar Azure data Factory e o Azure alcance](https://docs.microsoft.com/azure/purview/how-to-link-azure-data-factory). 

## <a name="report-lineage-data-to-azure-purview"></a>Relatar dados de linhagem para o Azure alcance
Quando os clientes executam a atividade copiar, fluxo de dados ou executar pacote SSIS no Azure Data Factory, os clientes podem obter a relação de dependência e ter uma visão geral de alto nível de todo o processo de fluxo de trabalho entre fontes de dados e destino.
Para saber como coletar a linhagem de Azure Data Factory, consulte [Data Factory linhagem](https://docs.microsoft.com/azure/purview/how-to-link-azure-data-factory#supported-azure-data-factory-activities).

## <a name="next-steps"></a>Próximas etapas
[Guia do usuário de linhagem de catálogo](https://docs.microsoft.com/azure/purview/catalog-lineage-user-guide)

[Tutorial: enviar Data Factory dados de linhagem por push para o Azure alcance](turorial-push-lineage-to-purview.md)
