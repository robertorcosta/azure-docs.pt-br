---
title: Residência de dados para o observador de rede do Azure | Microsoft Docs
description: Este artigo o ajudará a entender a residência de dados para o serviço observador de rede do Azure.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: damendo
ms.openlocfilehash: a6be3a7fd19b43bd1b18af05d0dbfaf5053fb181
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682954"
---
# <a name="data-residency-for-azure-network-watcher"></a>Residência de dados para o observador de rede do Azure
Com exceção do serviço monitor de conexão (versão prévia), o observador de rede do Azure não armazena dados do cliente.


## <a name="connection-monitor-preview-data-residency"></a>Residência de dados do monitor de conexão (visualização)
O serviço monitor de conexão (versão prévia) armazena dados do cliente. Esses dados são armazenados automaticamente pelo observador de rede em uma única região. Portanto, o monitor de conexão (versão prévia) atende automaticamente aos requisitos de residência de dados em região, incluindo os requisitos especificados na [central de confiabilidade](https://azuredatacentermap.azurewebsites.net/).

## <a name="data-residency"></a>Residência de dadosResidência de dados
No Azure, o recurso que habilita o armazenamento de dados do cliente em uma única região está disponível no momento apenas na região do Sudeste Asiático (Cingapura) da região Pacífico Asiático geográfica e do Sul do Brasil (estado de São Paulo) da geografia do Brasil. Para todas as outras regiões, os dados do cliente são armazenados na Área geográfica. Para obter mais informações, consulte a [central de confiabilidade](https://azuredatacentermap.azurewebsites.net/).

## <a name="next-steps"></a>Próximas etapas

* Leia uma visão geral do [observador de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).
