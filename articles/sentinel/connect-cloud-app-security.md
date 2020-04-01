---
title: Conecte os dados de segurança do aplicativo em nuvem ao Azure Sentinel| Microsoft Docs
description: Saiba como conectar dados de segurança de aplicativos em nuvem ao Azure Sentinel.
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
ms.date: 03/24/2020
ms.author: yelevin
ms.openlocfilehash: 266d97e834247088d40837cbec1436e00d0f4be2
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422154"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Conecte dados da Microsoft Cloud App Security 



O conector [MCAS (Microsoft Cloud App Security, segurança](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) de aplicativos em nuvem) permite transmitir alertas e [logs](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it) de Descoberta de Nuvem do MCAS para o Azure Sentinel. Isso permitirá que você ganhe visibilidade em seus aplicativos em nuvem, obtenha análises sofisticadas para identificar e combater ameaças cibernéticas e controlar como seus dados viajam.

## <a name="prerequisites"></a>Pré-requisitos

- Seu usuário deve ter permissões de leitura e gravação no espaço de trabalho.
- O usuário deve ter permissões de administrador global ou administrador de segurança no inquilino do espaço de trabalho.
- Para transmitir os logs do Cloud Discovery no Azure Sentinel, [habilite o Azure Sentinel como seu SIEM no Microsoft Cloud App Security](https://aka.ms/AzureSentinelMCAS).

> [!IMPORTANT]
> A ingestão de registros do Cloud Discovery está atualmente em visualização pública.
> Este recurso é fornecido sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção.
> Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="connect-to-cloud-app-security"></a>Conectar-se ao Cloud App Security

Se você já tiver o Cloud App Security, certifique-se de que ele está [ativado em sua rede](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Se o Cloud App Security for implantado e ingerindo seus dados, os dados de alerta podem ser facilmente transmitidos para o Azure Sentinel.


1. No menu de navegação do Azure Sentinel, selecione **conectores de dados**. Na lista de conectores, clique no bloco **de segurança do aplicativo Microsoft Cloud E,** em seguida, no botão de página do **conector Aberto** no canto inferior direito.

1. Selecione quais logs deseja transmitir no Azure Sentinel; você pode escolher **Alertas** e **Registros de Descoberta em Nuvem** (visualização). 

1. Clique em **Aplicar Alterações**.

1. Para usar o esquema relevante no Log Analytics para `SecurityAlert` alertas de segurança de aplicativos em nuvem, digite na janela de consulta. Para o esquema de logs `McasShadowItReporting`de registro de cloud discovery, digite .

> [!NOTE]
> O Cloud Discovery ajuda a detectar e identificar tendências, agregando os dados subjacentes às conexões dos usuários com aplicativos em nuvem.
>
> Uma vez que os dados do Cloud Discovery são agregados por dia, esteja ciente de que até 24 horas dos dados mais recentes não serão refletidos no Azure Sentinel. Caso uma investigação de baixo nível exija dados mais imediatos, ele deve ser feito diretamente no aparelho de origem ou serviço onde os dados brutos residem.

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como conectar o Microsoft Cloud App Security ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a detectar ameaças com o Azure Sentinel, usando regras [incorporadas](tutorial-detect-threats.md) ou [personalizadas.](tutorial-detect-threats-custom.md)
