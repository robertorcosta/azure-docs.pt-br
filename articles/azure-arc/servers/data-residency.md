---
title: Residência de dadosResidência de dados
description: Residência de dados e informações sobre servidores habilitados para Arc do Azure.
ms.topic: reference
ms.date: 09/02/2020
ms.custom: references_regions
ms.openlocfilehash: 8b4b8171bd7133e52928a5227c488bd6234ce686
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90908119"
---
# <a name="azure-arc-enabled-servers-data-residency"></a>Servidores habilitados para Arc do Azure: residência de dados

Este artigo explica o conceito de residência de dados e como ele se aplica aos servidores habilitados para Arc do Azure.

Os servidores habilitados para Arc do Azure estão **[disponíveis](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** no **Estados Unidos, na Europa ou Pacífico Asiático**.

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