---
title: Integrar as máquinas virtuais do Azure Stack à Central de Segurança do Azure
description: Este início rápido mostra como provisionar a extensão da máquina virtual Gerenciamento de Configuração, de Atualização e do Azure Monitor em máquinas virtuais do Azure Stack.
services: security-center
documentationcenter: na
author: pipposera
manager: dsavage
ms.assetid: 8982348a-0624-40c7-8a1e-642a523c7f6b
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: fiseraci
ms.openlocfilehash: c186dcb2d7d7d423f5c001bdb4d3f3503beebd9c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686514"
---
# <a name="quickstart-onboard-your-azure-stack-virtual-machines-to-security-center"></a>Início Rápido: Integrar suas máquinas virtuais do Azure Stack à Central de Segurança
Após integrar sua assinatura do Azure, será possível permitir que a Central de Segurança proteja suas máquinas virtuais em execução no Azure Stack adicionando a extensão da máquina virtual **Gerenciamento de Configuração, de Atualização e do Azure Monitor** no marketplace do Azure Stack.

Este início rápido mostra como adicionar a extensão da máquina virtual **Gerenciamento de Configuração, de Atualização e do Azure Monitor** em uma máquina virtual (há suporte para Linux e Windows) em execução no Azure Stack.

## <a name="prerequisites"></a>Prerequisites
Para começar a usar a Central de Segurança, você deve ter uma assinatura do Microsoft Azure. Se você não tiver uma assinatura, pode se inscrever em uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

É necessário ter uma assinatura do Azure na camada Standard da Central de Segurança antes de iniciar este início rápido. Consulte [Integrar sua assinatura do Azure ao Centro de Segurança Standard](security-center-get-started.md) para obter instruções de atualização. É possível experimentar a camada Standard da Central de Segurança sem custo por 30 dias. Para saber mais, consulte a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="select-your-workspace-in-azure-security-center"></a>Selecionar seu workspace na Central de Segurança do Azure

1. Faça logon no [Portal do Azure](https://azure.microsoft.com/features/azure-portal/).
2. No menu **Microsoft Azure**, selecione **Central de Segurança**. **Central de Segurança - Visão geral** é aberto. 

   ![Visão geral da Central de Segurança][2]

3. No menu principal da Central de Segurança, selecione **Introdução**.
4. Selecione a guia **Introdução**.

   ![Introdução][3]

5. Clique em **Configurar** sob **Adicionar novos computadores não Azure**. É mostrada uma lista dos workspaces do Log Analytics. A lista inclui, se aplicável, o workspace padrão criado para você pela Central de Segurança quando o provisionamento automático foi habilitado. Selecione este workspace ou outro workspace ao qual você deseja que a VM do Azure Stack relate os dados de segurança.

    ![Adicionar computador não Azure](./media/quick-onboard-windows-computer/non-azure.png)

   A folha **Agente Direto** é aberta com um link para baixar o agente e as chaves da sua ID do workspace a ser usada na configuração do agente.

   >[!NOTE]
   > NÃO é necessário baixar o agente manualmente. O agente será instalado como uma extensão de VM nas etapas abaixo.

6. À direita da **ID do Workspace**, selecione o ícone copiar e cole a ID no Bloco de Notas.

7. À direita da **Chave Primária**, clique no ícone copiar e cole a chave no Bloco de Notas.

## <a name="add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines"></a>Adicionar a extensão da máquina virtual às máquinas virtuais existentes do Azure Stack
Agora é necessário adicionar a extensão da máquina virtual **Gerenciamento de Configuração, de Atualização e do Azure Monitor** para as máquinas virtuais em execução no Azure Stack.

1. Em uma nova guia do navegador, faça logon no portal do **Azure Stack**.
2. Acesse a página **Máquinas virtuais**, selecione a máquina virtual que você deseja proteger com a Central de Segurança. Para saber mais sobre como criar uma máquina virtual no Azure Stack, confira [este início rápido para Máquinas Virtuais do Windows](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) ou [este início rápido para Máquinas Virtuais do Linux](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
3. Selecione **Extensões**. A lista de extensões da máquina virtual instaladas nesta máquina virtual é mostrada.
4. Clique na guia **Adicionar**. A folha de menu **Novo recurso** é aberta e mostra a lista de extensões da máquina virtual disponíveis. 
5. Selecione a extensão **Gerenciamento de Configuração, de Atualização e do Azure Monitor** e clique em **Criar**. A folha de configuração **Instalar extensão** é aberta.

>[!NOTE]
> Caso você não veja a extensão **Gerenciamento de Configuração, de Atualização e do Azure Monitor** listada no marketplace, contate seu operador do Azure Stack para disponibilizá-lo.

6. Na folha de configuração **Instalar extensão**, cole a **ID do Workspace** e a **Chave do Workspace (Chave Primária)** que você copiou para o Bloco de Notas no procedimento anterior.
7. Quando terminar de fornecer as definições de configuração necessárias, clique em **OK**.
8. Depois que a instalação da extensão for concluída, seu status será mostrado como **Provisionamento bem-sucedido**. Pode levar até uma hora para que a máquina virtual seja exibida no portal da Central de Segurança.

Para saber mais sobre como instalar e configurar o agente para Windows, confira [Connect Windows computers](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard) (Conectar computadores com Windows).

Para solução de problemas de agente do Linux, confira [Troubleshoot Azure Log Analytics Linux Agent](../azure-monitor/platform/agent-linux-troubleshoot.md) (Solucionar problemas do agente do Linux do Azure Log Analytics).

Agora você pode monitorar suas VMs do Azure e computadores não Azure em um único lugar. No portal da Central de Segurança no Azure, em **Computação**, há uma visão geral de todas as VMs e computadores juntamente com recomendações. A Central de Segurança também revela qualquer detecção para esses computadores em alertas de segurança.

  ![Folha Computação][6]

Há dois tipos de ícones representados na folha **Computação**:

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Computador não Azure 

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) VM do Azure (as VMs do Azure Stack serão mostradas neste grupo)

## <a name="clean-up-resources"></a>Limpar recursos
Quando não for mais necessário, será possível remover a extensão da máquina virtual por meio do portal do Azure Stack.

Para remover a extensão:

1. Abra o **portal do Azure Stack**.
2. Acesse a página **Máquinas virtuais**, selecione a máquina virtual da qual você deseja remover a extensão.
3. Selecione **Extensões**, selecione a extensão **Microsoft.EnterpriseCloud.Monitoring**.
4. Clique em **Desinstalar** e confirme a seleção clicando em **Sim**.

## <a name="next-steps"></a>Próximas etapas
Neste início rápido, você provisionou a extensão Gerenciamento de Configuração, de Atualização e do Azure Monitor em uma máquina virtual em execução no Azure Stack. Para saber mais sobre como usar a Central de Segurança, prossiga para o tutorial para configurar uma política de segurança e avaliar a segurança de seus recursos.

> [!div class="nextstepaction"]
> [Tutorial: Definir e avaliar as políticas de segurança](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
