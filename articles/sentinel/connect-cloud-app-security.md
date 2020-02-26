---
title: Conectar dados de Cloud App Security ao Azure Sentinel | Microsoft Docs
description: Saiba como conectar Cloud App Security dados ao Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/23/2019
ms.author: yelevin
ms.openlocfilehash: 348576fbbdd1037f9e2e792218b96bbbecf36668
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588358"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Conectar dados de Microsoft Cloud App Security 



Você pode transmitir logs de [Cloud app Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) para o Azure Sentinel com um único clique. Essa conexão permite transmitir os alertas de Cloud App Security para o Azure Sentinel. 

## <a name="prerequisites"></a>Prerequisites

- Usuário com permissões de administrador global ou administrador de segurança
- Para transmitir Cloud Discovery logs para o Azure Sentinel, [habilite o Azure Sentinel como seu Siem no Microsoft Cloud app Security](https://aka.ms/AzureSentinelMCAS).

> [!IMPORTANT]
> A ingestão de logs de Cloud Discovery está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="connect-to-cloud-app-security"></a>Conectar-se ao Cloud App Security

Se você já tiver Cloud App Security, verifique se ele está [habilitado em sua rede](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Se Cloud App Security for implantado e ingerir seus dados, os dados de alerta poderão ser facilmente transmitidos para o Azure Sentinel.


1. No Azure Sentinel, selecione **conectores de dados**, clique no bloco **Cloud app Security** e selecione a **página abrir conector**.

1. Selecione os logs que você deseja transmitir para o Azure Sentinel, você pode escolher **alertas** e **Cloud Discovery logs** (versão prévia). 

1. Clique em **Conectar**.

1. Para usar o esquema relevante em Log Analytics para os alertas de Cloud App Security, procure **SecurityAlert**.




## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a conectar Microsoft Cloud App Security ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).
