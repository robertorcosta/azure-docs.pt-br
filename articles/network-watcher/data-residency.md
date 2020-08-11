---
title: Residência de dados para o observador de rede do Azure | Microsoft Docs
description: Entender a residência de dados para o serviço do observador de rede
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
ms.openlocfilehash: 14b4d3568c129c77260b00d554db520809dfd670
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88068088"
---
# <a name="data-residency-for-the-azure-network-watcher"></a>Residência de dados para o observador de rede do Azure
O observador de rede do Azure não armazena dados do cliente, exceto o serviço monitor de conexão (versão prévia).


## <a name="connection-monitor-preview-data-residency"></a>Residência de dados do monitor de conexão (visualização)
O serviço *Monitor de conexão (versão prévia)* armazena dados do cliente. Esses dados são armazenados automaticamente pelo observador de rede em uma única região. Portanto, o *Monitor de conexão (versão prévia)* atende automaticamente aos requisitos de residência de dados em região, incluindo o especificado na [central de confiabilidade](https://azuredatacentermap.azurewebsites.net/).

## <a name="singapore-data-residency"></a>Residência de dados de Cingapura

No Azure, o recurso para habilitar o armazenamento de dados do cliente em uma única região está disponível no momento apenas na região do Sudeste Asiático (Cingapura) da área geográfica do Pacífico Asiático. Para todas as outras regiões, os dados do cliente são armazenados em área geográfica. Para obter mais informações, consulte [central de confiabilidade](https://azuredatacentermap.azurewebsites.net/).

> [!Note]
> **Replicação anterior** Os clientes têm a opção de armazenar os endereços IP do usuário final com sua instância do observador de rede para que o observador de rede possa monitorar as alterações de acessibilidade, latência e topologia de rede relacionadas aos endereços IP do usuário final. Esses endereços IP do usuário final podem ser classificados como dados do cliente. A partir de 15 de julho de 2020, o observador de rede armazenou esses dados em uma única região. (Os dados do cliente não são mais replicados para o HK.) Esses dados não são mais replicados para o HK. Esses dados do cliente são e foram criptografados em repouso.
> 
> Se esses dados do cliente fossem acessíveis por terceiros, isso permitiria que o terceiro tivesse conhecimento do endereço IP, mas não concederia a ele o acesso de terceiros ao computador ou ao dispositivo associado ao endereço IP. O observador de rede acredita que nenhum terceiro acessava dados do cliente em HK.

## <a name="next-steps"></a>Próximas etapas

* Leia uma visão geral do [observador de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).
