---
title: Guia da central de segurança do Azure para IoT para configuração com o Azure Sentinel (versão prévia) | Microsoft Docs
description: Este guia de instruções explica como configurar o Azure Sentinel para receber dados de sua central de segurança do Azure para a solução de IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: mlottner
ms.openlocfilehash: f6e7eddd6ddbcec61c3d8d173891cbc9abaaf08f
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77463174"
---
> [!IMPORTANT]
> A central de segurança do Azure para o conector de dados do IoT no Azure Sentinel está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel-preview"></a>Conectar seus dados da central de segurança do Azure para IoT ao Azure Sentinel (versão prévia) 

Neste guia, saiba como conectar sua central de segurança do Azure para dados de IoT ao Azure Sentinel.  

> [!div class="checklist"]
> * Prerequisites 
> * Configurações de conexão
> * Log Analytics exibição de alerta 

Conecte os alertas da central de segurança do Azure para IoT e transmita-os diretamente para o Azure Sentinel.

## <a name="prerequisites"></a>Prerequisites

- Você deve ter permissões de **leitura** e **gravação** do espaço de trabalho.
- A **central de segurança do Azure para IOT** deve ser **habilitada** em seus hubs (s) IOT relevantes.
- Você deve ter permissões de **leitura** e **gravação** no **Hub IOT do Azure** que deseja conectar.
- Você também deve ter permissões de **leitura** e **gravação** no **grupo de recursos do Hub IOT do Azure**.

> [!NOTE]
> Você deve ter o licenciamento da camada Standard da central de segurança do Azure em execução na sua assinatura para enviar alertas gerais de recursos do Azure. Com o licenciamento de camada gratuita necessário para a central de segurança do Azure para IoT, somente a central de segurança do Azure para alertas relacionados a IoT será encaminhada para o Azure Sentinel. 

## <a name="connect-to-azure-security-center-for-iot"></a>Conectar-se à central de segurança do Azure para IoT

1. No Azure Sentinel, selecione **conectores de dados** e clique no bloco **central de segurança do Azure para IOT** .
1. No painel inferior direito, clique na **página abrir conector**. 
1. Clique em **conectar**, ao lado de cada assinatura do Hub IOT cujos alertas e alertas de dispositivo você deseja transmitir para o Azure Sentinel. 
    - Se a central de segurança do Azure para IoT não estiver habilitada nesse Hub, você verá uma mensagem de aviso de habilitação. Clique no link **habilitar** para iniciar o serviço. 
1. Você pode decidir se deseja que os alertas da central de segurança do Azure para IoT gerem incidentes automaticamente no Azure Sentinel. Em **criar incidentes**, selecione **habilitar** para habilitar a regra analítica padrão para criar incidentes automaticamente a partir de alertas gerados no serviço de segurança conectado. Essa regra pode ser alterada ou editada em **análise** > regras **ativas** .

> [!NOTE]
>Pode levar 10 segundos ou mais para atualizar a lista de hubs depois de fazer alterações de conexão. 

## <a name="log-analytics-alert-display"></a>Log Analytics exibição de alertas

Para usar o esquema relevante no Log Analytics para exibir a central de segurança do Azure para alertas de IoT:

1. Abra **Logs** > **SecurityInsights** > **SecurityAlert**ou pesquise por **SecurityAlert**. 
2. Filtre para ver apenas a central de segurança do Azure para alertas gerados por IoT usando o seguinte filtro kql:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>Notas de serviço

Depois de conectar um hub IoT, os dados do Hub estarão disponíveis no Azure Sentinel aproximadamente 15 minutos mais tarde.


## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a conectar a central de segurança do Azure para IoT ao Azure Sentinel. Para saber mais sobre a detecção de ameaças e o acesso a dados de segurança, consulte os seguintes artigos:

- Saiba como usar o Azure Sentinel para [obter visibilidade de seus dados e ameaças em potencial](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility).

- Saiba como [acessar seus dados de segurança de IOT](how-to-security-data-access.md)