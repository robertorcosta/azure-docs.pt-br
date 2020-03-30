---
title: Conecte os dados do ATP do Azure ao Azure Sentinel| Microsoft Docs
description: Saiba como conectar dados do Azure ATP ao Azure Sentinel.
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
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 387d04cbbb125006efcc4efc53a02015fe3f5919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588579"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Conecte dados do ATP (Azure Advanced Threat Protection, proteção avançada de ameaças) do Azure

> [!IMPORTANT]
> O conector de dados Azure Advanced Threat Protection no Azure Sentinel está atualmente em visualização pública.
> Este recurso é fornecido sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Você pode transmitir logs do [Azure Advanced Threat Protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) para o Azure Sentinel com um único clique.

## <a name="prerequisites"></a>Pré-requisitos

- Usuário com permissões globais de administrador ou administrador de segurança
- Você deve ser um cliente de pré-visualização do Azure ATP e permitir a integração entre o Azure ATP e o Microsoft Cloud App Security. Para obter mais informações, consulte [Azure Advanced Protection Integration](https://docs.microsoft.com/cloud-app-security/aatp-integration).

## <a name="connect-to-azure-atp"></a>Conecte-se ao ATP do Azure

Certifique-se de que a versão de pré-visualização do Azure ATP esteja [ativada em sua rede](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1).
Se o Azure ATP for implantado e ingerindo seus dados, os alertas suspeitos podem ser facilmente transmitidos para o Azure Sentinel. Pode levar até 24 horas para os alertas começarem a transmitir no Azure Sentinel.


1. Para conectar o Azure ATP ao Azure Sentinel, você deve primeiro ativar a integração entre o Azure ATP e o Microsoft Cloud App Security. Para obter informações sobre como fazer isso, consulte [a integração azure Advanced Threat Protection](https://docs.microsoft.com/cloud-app-security/aatp-integration).

1. No Azure Sentinel, selecione **conectores de dados** e clique no bloco **Azure Advanced Threat Protection (Preview).**

1. Você pode selecionar se deseja que os alertas do Azure ATP gerem automaticamente incidentes no Azure Sentinel. Em **Criar incidentes**, selecione **Habilitar** para habilitar a regra analítica padrão que cria incidentes automaticamente com base em alertas gerados no serviço de segurança conectado. É possível editar essa regra em **Análise** e depois em **Regras ativas**.

1. Clique em **Conectar**.

1. Para usar o esquema relevante no Log Analytics para os alertas do Azure ATP, procure o **SecurityAlert**.

> [!NOTE]
> Se os alertas forem maiores que 30 KB, o Azure Sentinel pára de exibir o campo Entidades nos alertas.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar o Azure Advanced Threat Protection ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).

