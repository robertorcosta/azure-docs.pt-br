---
title: Configurar o servidor de configuração para recuperação de desastre de servidores físicos para o Azure usando Azure Site Recovery | Microsoft Docs '
description: Este artigo descreve como configurar o servidor de configuração local para recuperação de desastre de servidores físicos locais para o Azure.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: ramamill
ms.openlocfilehash: 902c14211e91a1500c6b50cd790b347e337c4f70
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78367073"
---
# <a name="set-up-the-configuration-server-for-disaster-recovery-of-physical-servers-to-azure"></a>Configurar o servidor de configuração para recuperação de desastre de servidores físicos para o Azure

Este artigo descreve como configurar seu ambiente local para iniciar a replicação de servidores físicos que executam Windows ou Linux no Azure.

## <a name="prerequisites"></a>Pré-requisitos

O artigo pressupõe que você já tem:
- Um cofre dos serviços de recuperação no [portal do Azure](https://portal.azure.com "portal do Azure").
- Um computador físico no qual instalar o servidor de configuração.
- Se você tiver desabilitado o TLS 1,0 no computador no qual está instalando o servidor de configuração, verifique se o TLs 1,2 está habilitado e se o .NET Framework versão 4,6 ou posterior está instalado no computador (com criptografia forte habilitada). [Saiba mais](https://support.microsoft.com/help/4033999/how-to-resolve-azure-site-recovery-agent-issues-after-disabling-tls-1).

### <a name="configuration-server-minimum-requirements"></a>Requisitos mínimos do servidor de configuração
A tabela a seguir lista os requisitos mínimos de hardware, software e rede para um servidor de configuração.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> Os servidores proxy baseados em HTTPS não são suportados pelo servidor de configuração.

## <a name="choose-your-protection-goals"></a>Escolher suas metas de proteção

1. Na portal do Azure, vá para a folha cofres **dos serviços de recuperação** e selecione seu cofre.
2. No menu de **recursos** do cofre, clique em **Introdução** > **site Recovery** > **etapa 1: preparar a infraestrutura** > meta de **proteção**.

    ![Escolher metas](./media/physical-azure-set-up-source/choose-goals.png)
3. Em **meta de proteção**, selecione **para o Azure** e **não virtualizado/outro**e clique em **OK**.

    ![Escolher metas](./media/physical-azure-set-up-source/physical-protection-goal.png)

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

1. Em **preparar origem**, se você não tiver um servidor de configuração, clique em **+ servidor de configuração** para adicionar um.

   ![Configurar origem](./media/physical-azure-set-up-source/plus-config-srv.png)
2. Na folha **Adicionar servidor** , verifique se o **servidor de configuração** aparece em **tipo de servidor**.
4. Baixe o arquivo de instalação do Site Recovery Unified setup.
5. Baixe a chave de registro do cofre. Você precisa da chave de registro ao executar a instalação unificada. A chave é válida por cinco dias depois de você gerá-la.

    ![Configurar origem](./media/physical-azure-set-up-source/set-source2.png)
6. No computador que você está usando como o servidor de configuração, execute **Azure site Recovery configuração unificada** para instalar o servidor de configuração, o servidor de processo e o servidor de destino mestre.

#### <a name="run-azure-site-recovery-unified-setup"></a>Executar Azure Site Recovery configuração unificada

> [!TIP]
> O registro do servidor de configuração falhará se a hora no relógio do sistema do computador tiver mais de cinco minutos fora da hora local. Sincronize o relógio do sistema com um [servidor de horário](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) antes de iniciar a instalação.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> O servidor de configuração pode ser instalado por meio de uma linha de comando. [Saiba mais](physical-manage-configuration-server.md#install-from-the-command-line).


## <a name="common-issues"></a>Problemas comuns

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Próximas etapas

A próxima etapa envolve [a configuração de seu ambiente de destino](physical-azure-set-up-target.md) no Azure.
