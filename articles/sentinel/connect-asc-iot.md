---
title: Conectar o Azure defender para IoT ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar o Azure defender (anteriormente a central de segurança do Azure) para dados de IoT para o Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: yelevin
ms.openlocfilehash: 822d0c742bbd54b5bab0c69e82652743584a0696
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89659615"
---
# <a name="connect-your-data-from-azure-defender-formerly-azure-security-center-for-iot-to-azure-sentinel"></a>Conectar seus dados do Azure defender (anteriormente a central de segurança do Azure) para IoT ao Azure Sentinel 


> [!IMPORTANT]
> O conector de dados do Azure defender para IoT está atualmente em visualização pública. Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Use o conector do Azure defender para IoT para transmitir todos os seus eventos do Azure defender para IoT para o Azure Sentinel. 

## <a name="prerequisites"></a>Pré-requisitos

- Permissões de **leitura** e **gravação** no espaço de trabalho no qual o Azure Sentinel é implantado
- O **Azure defender para IOT** deve ser **habilitado** em seus hubs IOT relevantes
- Permissões de **leitura** e **gravação** no **Hub IOT do Azure** que você deseja conectar
- Permissões de **leitura** e **gravação** no **grupo de recursos do Hub IOT do Azure**

## <a name="connect-to-azure-defender-for-iot"></a>Conectar-se ao Azure defender para IoT

1. No Azure Sentinel, selecione **conectores de dados** e, em seguida, selecione **Azure defender para IOT** (ainda pode ser chamado de central de segurança do Azure para IOT) da galeria.
1. No painel inferior direito, clique na **página abrir conector**. 
1. Clique em **conectar**, ao lado de cada assinatura do Hub IOT cujos alertas e alertas de dispositivo você deseja transmitir para o Azure Sentinel. 
    - Se o Azure defender para IoT não estiver habilitado nesse Hub, você verá uma mensagem de aviso de **habilitação** . Clique no link **habilitar** para iniciar o serviço. 
1. Você pode decidir se deseja que os alertas do Azure defender para IoT gerem incidentes automaticamente no Azure Sentinel. Em **criar incidentes**, selecione **habilitar** para habilitar a regra analítica padrão para criar incidentes automaticamente a partir de alertas gerados no serviço de segurança conectado. Essa regra pode ser alterada ou editada **Analytics**em  >  regras**ativas** de análise.

> [!NOTE]
> Pode levar algum tempo para a lista de Hub ser atualizada depois de fazer alterações na conexão. 

## <a name="log-analytics-alert-display"></a>Log Analytics exibição de alertas

Para usar o esquema relevante no Log Analytics para exibir os alertas do Azure defender para IoT:

1. Abra **logs**  >  **SecurityInsights**  >  **SecurityAlert**ou pesquise por **SecurityAlert**. 
2. Filtre para ver somente os alertas gerados pelo Azure defender para IoT usando o seguinte filtro kql:

```kusto
SecurityAlert | where ProductName == "Azure Defender for IoT"
``` 

### <a name="service-notes"></a>Notas de serviço

Depois de conectar um hub IoT, os dados do Hub estarão disponíveis no Azure Sentinel aproximadamente 15 minutos mais tarde.


## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar os dados do Azure defender para IoT ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.
