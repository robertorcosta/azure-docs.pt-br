---
title: Integrar computadores Linux à Central de Segurança do Azure | Microsoft Docs
description: Este guia de início rápido mostra como integrar seus computadores Linux à Central de Segurança.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2018
ms.author: memildin
ms.openlocfilehash: 9f49b858a23d0a1f58505d9f9971a31e8c9167e9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "73664489"
---
# <a name="quickstart-onboard-linux-computers-to-azure-security-center"></a>Início Rápido: Integrar computadores Linux à Central de Segurança do Azure
Depois de integrar suas assinaturas do Azure, é possível habilitar a Central de Segurança para recursos Linux que estão sendo executados fora do Azure, por exemplo, localmente ou em outras nuvens, por meio do provisionamento de um agente. O agente é chamado de MMA (Microsoft Monitoring Agent), mas também é conhecido como agente do OMS.

Este início rápido mostra como instalar o agente em um computador Linux.

## <a name="prerequisites"></a>Pré-requisitos
Para começar a usar a Central de Segurança, você deve ter uma assinatura do Microsoft Azure. Se você não tiver uma assinatura, pode se inscrever em uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

Você deverá estar no tipo de preço Standard da Central de Segurança antes de começar este guia de início rápido. Consulte [Integrar sua assinatura do Azure ao Centro de Segurança Standard](security-center-get-started.md) para obter instruções de atualização. Você pode experimentar a Central de Segurança Standard sem nenhum custo. Para saber mais, consulte a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="add-new-linux-computer"></a>Adicionar novo computador Linux

1. Faça logon no [Portal do Azure](https://azure.microsoft.com/features/azure-portal/).
2. No menu **Microsoft Azure**, selecione **Central de Segurança**. **Central de Segurança - Visão geral** é aberto.

   ![Visão geral da Central de Segurança][2]

3. No menu principal da Central de Segurança, selecione **Introdução**.
4. Selecione a guia **Introdução**. ![Introdução][3]

5. Clique em **Configurar** em **Adicionar novos computadores não Azure**, é mostrada uma lista dos espaços de trabalho do Log Analytics. A lista inclui, se aplicável, o workspace padrão criado para você pela Central de Segurança quando o provisionamento automático foi habilitado. Selecione esse workspace ou outro que você queira usar.

    ![Adicionar computador não Azure](./media/quick-onboard-linux-computer/non-azure.png)

6. Na página **Agente Direto** em **FAZER O DOWNLOAD E INTEGRAR AGENTE PARA LINUX**, selecione o botão **copiar** para copiar o comando *wget*.

7. Abra o Bloco de notas e cole esse comando. Salve esse arquivo em um local que possa ser acessado pelo computador Linux.

## <a name="install-the-agent"></a>Instalar o agente

1. No seu computador Linux, abra o arquivo salvo anteriormente. Selecione todo o conteúdo, copie, abra um console do terminal e cole o comando.
2. Depois que a instalação for concluída, você poderá confirmar se o *omsagent* está instalado executando o comando *pgrep*. O comando retornará a PID (ID do Processo) *omsagent* conforme mostrado abaixo:

   ![Instalar o agente][5]

Os logs para o agente podem ser encontrados em: */var/opt/microsoft/omsagent/\<ID do workspace>/log/*

  ![Logs para agente][6]

Depois de algum tempo, podendo levar até 30 minutos, o novo computador Linux aparecerá na Central de Segurança.

Agora você pode monitorar suas VMs do Azure e computadores não Azure em um único lugar. Em **Computação**, você tem uma visão geral de todas as VMs e computadores juntamente com recomendações. Cada coluna representa um conjunto de recomendações. A cor representa o estado de segurança atual da VM ou do computador para essa recomendação. A Central de Segurança também revela quaisquer detecções para esses computadores em alertas de segurança.

  ![Folha Computação][7] Há dois tipos de ícones representados na folha **Computação**:

  ![icon1](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) Computador não Azure

  ![icon2](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) VM do Azure

## <a name="clean-up-resources"></a>Limpar os recursos
Quando não for mais necessário, você poderá remover o agente do computador com Linux.

Para remover o agente:

1. Faça o download do [script universal](https://github.com/Microsoft/OMS-Agent-for-Linux/releases) do agente do Linux para o computador.
2. Execute o arquivo .sh do pacote com o argumento *--purge* no computador, que remove completamente o agente e sua configuração.

    `sudo sh ./omsagent-<version>.universal.x64.sh --purge`

## <a name="next-steps"></a>Próximas etapas
Nesse início rápido, você provisionou o agente em um computador Linux. Para saber mais sobre como usar a Central de Segurança, prossiga para o tutorial para configurar uma política de segurança e avaliar a segurança de seus recursos.

> [!div class="nextstepaction"]
> [Tutorial: Definir e avaliar as políticas de segurança](tutorial-security-policy.md)

<!--Image references-->
[1]: ./media/quick-onboard-linux-computer/portal.png
[2]: ./media/quick-onboard-linux-computer/overview.png
[3]: ./media/quick-onboard-linux-computer/get-started.png
[4]: ./media/quick-onboard-linux-computer/add-computer.png
[5]: ./media/quick-onboard-linux-computer/pgrep-command.png
[6]: ./media/quick-onboard-linux-computer/logs-for-agent.png
[7]: ./media/quick-onboard-linux-computer/compute.png
