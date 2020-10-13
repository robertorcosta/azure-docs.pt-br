---
title: Configurar o Azure Sentinel para defender para IoT (versão prévia)
description: Explica como configurar o Azure Sentinel para receber dados de sua solução defender for IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 71147352c5b75195ed0dff2b05acc5315f3183cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90933677"
---
# <a name="connect-your-data-from-defender-for-iot-to-azure-sentinel-preview"></a>Conectar seus dados do defender para IoT ao Azure Sentinel (versão prévia)

A central de segurança do Azure para o conector de dados do IoT no Azure Sentinel está atualmente em visualização pública. Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/en-us/support/legal/preview-supplemental-terms/).

Neste guia, saiba como conectar seus dados do defender para IoT ao Azure Sentinel.

> [!div class="checklist"]
> * Pré-requisitos
> * Configurações de conexão
> * Log Analytics exibição de alerta

Conecte alertas do defender para IoT e transmita-os diretamente para o Azure Sentinel.

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter permissões de **leitura** e **gravação** do espaço de trabalho.
- O **defender para IOT** deve estar **habilitado** em seus hubs IOT relevantes.
- Você deve ter permissões de **leitura** e **gravação** no **Hub IOT do Azure** que deseja conectar.
- Você também deve ter permissões de **leitura** e **gravação** no **grupo de recursos do Hub IOT do Azure**.

> [!NOTE]
> Você deve ter o licenciamento da camada Standard da central de segurança do Azure em execução na sua assinatura para enviar alertas gerais de recursos do Azure. Com o licenciamento de camada gratuita necessário para o defender para IoT, somente os alertas relacionados ao defender para IoT serão encaminhados para o Azure Sentinel.

## <a name="connect-to-defender-for-iot"></a>Conectar-se ao defender para IoT

1. No Azure Sentinel, selecione **conectores de dados** e clique no bloco **defender para IOT** .
1. Na parte inferior do painel direito, clique em **abrir página do conector**.
1. Clique em **conectar**, ao lado de cada assinatura do Hub IOT cujos alertas e alertas de dispositivo você deseja transmitir para o Azure Sentinel.
    - Se o defender para IoT não estiver habilitado nesse Hub, você verá uma mensagem de aviso de habilitação. Clique no link **habilitar** para iniciar e habilitar o serviço.
1. Você pode decidir se deseja que os alertas do defender para IoT gerem incidentes automaticamente no Azure Sentinel. Em **criar incidentes**, selecione **habilitar** para habilitar a regra para criar automaticamente incidentes a partir dos alertas gerados.  Essa regra pode ser alterada ou editada **Analytics**em  >  regras**ativas** de análise.

> [!NOTE]
>Pode levar 10 segundos ou mais para atualizar a lista de hubs depois de fazer alterações de conexão.

## <a name="log-analytics-alert-display"></a>Log Analytics exibição de alertas

Para usar o esquema relevante no Log Analytics para exibir os alertas do defender para IoT:

1. Abra **logs**  >  **SecurityInsights**  >  **SecurityAlert**ou pesquise por **SecurityAlert**.
1. Filtre para ver somente os alertas gerados pelo defender for IoT usando o seguinte filtro kql:

```kusto
SecurityAlert | where ProductName == "Defender for IoT"
```

### <a name="service-notes"></a>Notas de serviço

Depois de conectar um hub IoT, os dados do Hub estarão disponíveis no Azure Sentinel aproximadamente 15 minutos mais tarde.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar o defender para IoT ao Azure Sentinel. Para saber mais sobre a detecção de ameaças e o acesso a dados de segurança, consulte os seguintes artigos:

- Saiba como usar o Azure Sentinel para [obter visibilidade de seus dados e ameaças em potencial](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility).

- Saiba como [acessar seus dados de segurança de IOT](how-to-security-data-access.md)
