---
title: Residência de dadosResidência de dados
description: Residência de dados e informações sobre servidores habilitados para Arc do Azure.
ms.topic: reference
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: 73acb07b8caa3c1368c936463f04969c29d37985
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327758"
---
# <a name="azure-arc-enabled-servers-data-residency"></a>Servidores habilitados para Arc do Azure: residência de dados

Este artigo explica o conceito de residência de dados e como ele se aplica aos servidores habilitados para Arc do Azure.

Os servidores habilitados para Arc do Azure estão **[disponíveis](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** nas **Estados Unidos, na Europa, no reino Unido ou Pacífico Asiático**.

## <a name="data-residency"></a>Residência de dadosResidência de dados

Os servidores habilitados para Arc do Azure armazenam definições de configuração de [extensão de VM do Azure](manage-vm-extensions.md) (ou seja, valores de propriedade) a extensão requer a especificação antes de tentar habilitar no computador conectado. Por exemplo, quando você habilita a extensão de VM Log Analytics, ela solicita a **ID do espaço de trabalho** log Analytics e a **chave primária**.

As informações de metadados sobre o computador conectado também são coletadas. Especificamente:

* Nome e versão do sistema operacional
* Nome do computador
* FQDN (nome de domínio totalmente qualificado) do computador
* Versão do agente do computador conectado

Os servidores habilitados para Arc permitem que você especifique a região onde os dados serão armazenados. A Microsoft pode replicar para outras regiões para resiliência de dados, mas a Microsoft não Replica nem move dados fora da geografia. Esses dados são armazenados na região em que o recurso de máquina do arco do Azure está configurado. Por exemplo, se o computador estiver registrado com Arc na região leste dos EUA, esses dados serão armazenados na região dos EUA.

Para obter mais informações sobre a resiliência regional e o suporte de conformidade, consulte [Geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como projetar para [resiliência do Azure](/azure/architecture/reliability/architect).