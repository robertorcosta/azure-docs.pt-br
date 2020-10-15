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
ms.openlocfilehash: 12f65d0e7f9c380f77fe4189d26fdeafd426295b
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090787"
---
# <a name="connect-your-data-from-defender-for-iot-to-azure-sentinel-preview"></a>Conectar seus dados do defender para IoT ao Azure Sentinel (versão prévia)

O conector de dados do Azure defender para IoT no Azure Sentinel está atualmente em visualização pública. Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/en-us/support/legal/preview-supplemental-terms/).

Neste guia, saiba como conectar seus dados do defender para IoT ao Azure Sentinel.

> [!div class="checklist"]
> * Pré-requisitos
> * Configurações de conexão
> * Log Analytics exibição de alerta

Conecte alertas do defender para IoT e transmita-os diretamente para o Azure Sentinel.

Ao integrar mais rigidamente o Azure defender para IoT com o Azure Sentinel, o primeiro SIEM nativo de nuvem e o primeiro SIEM com IoT nativo e tem segurança, a Microsoft fornece uma abordagem mais simples para a segurança unificada em redes industriais e de ti. Quando combinado com o aprendizado de máquina do Azure Sentinel, essa integração permite que as organizações detectem rapidamente ataques de várias etapas que geralmente cruzam e ficam em limites. Além disso, a integração do Azure defender para IoT com os recursos de orquestração de segurança, automação e resposta (disparar) do Azure Sentinel permite a resposta e a prevenção automáticas usando guias estratégicos internos de OT. 

## <a name="prerequisites"></a>Pré-requisitos

- Você deve ter permissões de **leitura** e **gravação** do espaço de trabalho.
- O **defender para IOT** deve estar **habilitado** em seus hubs IOT relevantes.
- Você deve ter permissões de **leitura** e **gravação** no **Hub IOT do Azure** que deseja conectar.
- Você também deve ter permissões de **leitura** e **gravação** no **grupo de recursos do Hub IOT do Azure**.


## <a name="connect-to-defender-for-iot"></a>Conectar-se ao defender para IoT

1. No Azure Sentinel, selecione **conectores de dados** e clique no bloco **defender para IOT** .
1. Na parte inferior do painel direito, clique em **abrir página do conector**.
1. Clique em **conectar**, ao lado de cada assinatura do Hub IOT cujos alertas e alertas de dispositivo você deseja transmitir para o Azure Sentinel.
    - Se o defender para IoT não estiver habilitado nesse Hub, você verá uma mensagem de aviso de habilitação. Clique no link **habilitar** para iniciar e habilitar o serviço.
1. Você pode decidir se deseja que os alertas do defender para IoT gerem incidentes automaticamente no Azure Sentinel. Em **criar incidentes**, selecione **habilitar** para habilitar a regra para criar automaticamente incidentes a partir dos alertas gerados.  Essa regra pode ser alterada ou editada **Analytics**em  >  regras**ativas** de análise.

> [!NOTE]
>Pode levar 10 segundos ou mais para atualizar a lista de hubs depois de fazer alterações de conexão.

## <a name="using-log-analytics-for-alert-display"></a>Usando Log Analytics para exibição de alerta

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
