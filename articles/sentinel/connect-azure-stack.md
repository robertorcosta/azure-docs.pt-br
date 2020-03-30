---
title: A bordo de suas máquinas virtuais Azure Stack para o Azure Sentinel | Microsoft Docs
description: Este artigo mostra como provisionar a extensão de máquina virtual do Monitor, Atualização e Gerenciamento de Configuração do Azure em máquinas virtuais Do Zure Stack e começar a monitorá-las com o Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: a8213bd57936f95870324950204dbd6c1473739a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588511"
---
# <a name="connect-azure-stack-virtual-machines-to-azure-sentinel"></a>Conecte as máquinas virtuais Do Azure Stack ao Azure Sentinel




Com o Azure Sentinel, você pode monitorar suas VMs rodando no Azure e no Azure Stack em um só lugar. Para embarcar em suas máquinas Azure Stack no Azure Sentinel, primeiro você precisa adicionar a extensão da máquina virtual às suas máquinas virtuais Azure Stack existentes. 

Depois de conectar as máquinas Do Zure Stack, escolha entre uma galeria de dashboards que superem insights com base em seus dados. Esses painéis podem ser facilmente personalizados para suas necessidades.



## <a name="add-the-virtual-machine-extension"></a>Adicione a extensão da máquina virtual 

Adicione a extensão de máquina virtual **do Monitor, atualização e gerenciamento de configuração do Azure** às máquinas virtuais em execução no Azure Stack. 

1. Em uma nova guia do navegador, faça login no [portal Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-use-portal#access-the-portal).
2. Vá para a página **máquinas virtuais,** selecione a máquina virtual que deseja proteger com o Azure Sentinel. Para obter informações sobre como criar uma máquina virtual no Azure Stack, consulte [Criar uma VM de servidor Windows com o portal Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) ou criar uma [VM de servidor Linux usando o portal Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
3. Selecione **Extensões**. A lista de extensões da máquina virtual instaladas nesta máquina virtual é mostrada.
4. Clique na guia **Adicionar.** A lâmina do menu **Novo recurso** é aberta e mostra a lista de extensões de máquina virtual disponíveis. 
5. Selecione a extensão **Azure Monitor, Update e Configuration Management** e clique em **Criar**. A **janela de configuração de extensão Instalar** é aberta.

   ![Configurações de gerenciamento de monitor, atualização e configuração do Azure](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > Se você não ver a extensão de Gerenciamento de **Monitor, Atualização e Configuração do Azure** listada em seu marketplace, entre em contato com o operador do Azure Stack para disponibilizá-lo.

6. No menu Do Azure Sentinel, selecione **as configurações do Espaço de Trabalho seguidas** por **Avançado**e copie o **ID do espaço de trabalho** e a chave do espaço de **trabalho (Chave principal)**. 
1. Na janela de **extensão** Azure Stack Install, cole-as nos campos indicados e clique em **OK**.
1. Após a instalação da extensão ser concluída, seu status será mostrado como **Provisionamento Bem Sucedido**. Pode levar até uma hora para a máquina virtual aparecer no portal Azure Sentinel.

Para obter mais informações sobre a instalação e configuração do agente para Windows, consulte [Conectar computadores Windows](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard).

Para solução de problemas de agente do Linux, confira [Troubleshoot Azure Log Analytics Linux Agent](../azure-monitor/platform/agent-linux-troubleshoot.md) (Solucionar problemas do agente do Linux do Azure Log Analytics).

No portal Azure Sentinel no Azure, em **Máquinas Virtuais,** você tem uma visão geral de todas as VMs e computadores, juntamente com seu status. 

## <a name="clean-up-resources"></a>Limpar recursos
Quando não for mais necessário, será possível remover a extensão da máquina virtual por meio do portal do Azure Stack.

Para remover a extensão:

1. Abra o **portal do Azure Stack**.
2. Acesse a página **Máquinas virtuais**, selecione a máquina virtual da qual você deseja remover a extensão.
3. Selecione **Extensões**, selecione a extensão **Microsoft.EnterpriseCloud.Monitoring**.
4. Clique em **Desinstalar**e confirme sua seleção.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- Transmita dados de [dispositivos com Formato de Erro Comuns](connect-common-event-format.md) para o Azure Sentinel.
