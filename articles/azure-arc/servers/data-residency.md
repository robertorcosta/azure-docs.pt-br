---
title: Residência de dadosResidência de dados
description: Residência de dados e informações sobre os servidores habilitados para Arc do Azure (visualização).
ms.topic: reference
ms.date: 08/25/2020
ms.custom: references_regions
ms.openlocfilehash: 028398c27dde9760192d366b9e8a91dca8e429cf
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88860710"
---
# <a name="azure-arc-enabled-servers-preview-data-residency"></a>Servidores habilitados para Arc do Azure (visualização): residência de dados

Este artigo explica o conceito de residência de dados e como ele se aplica aos servidores habilitados para Arc do Azure (versão prévia).

Os servidores habilitados para Arc do Azure (versão prévia) estão **[disponíveis na versão prévia](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** no **Estados Unidos, na Europa ou Pacífico Asiático**.

## <a name="data-residency"></a>Residência de dadosResidência de dados

Os servidores habilitados para Arc do Azure (versão prévia) armazenam definições de configuração de [extensão de VM do Azure](manage-vm-extensions.md) (ou seja, valores de propriedade) a extensão requer especificar antes de tentar habilitar no computador conectado. Por exemplo, quando você habilita a extensão de VM Log Analytics, ela solicita a **ID do espaço de trabalho** log Analytics e a **chave primária**.

As informações de metadados sobre o computador conectado também são coletadas. Especificamente:

* Nome e versão do sistema operacional
* Nome do computador
* FQDN (nome de domínio totalmente qualificado) do computador
* Versão do agente do computador conectado

Os servidores habilitados para Arc (visualização) permitem que você especifique a região onde os dados serão armazenados. A Microsoft pode replicar para outras regiões para resiliência de dados, mas a Microsoft não Replica nem move dados fora da geografia. Esses dados são armazenados na região em que o recurso de máquina do arco do Azure está configurado. Por exemplo, se o computador estiver registrado com Arc na região leste dos EUA, esses dados serão armazenados na região dos EUA.

Para obter mais informações sobre a resiliência regional e o suporte de conformidade, consulte [Geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como projetar para [resiliência do Azure](/architecture/reliability/architect).