---
title: Integração o Gerenciamento de Atualizações da Automação do Azure com o ponto de extremidade do Windows Endpoint Configuration Manager
description: Este artigo descreve como configurar o Microsoft Endpoint Configuration Manager com Gerenciamento de Atualizações para implantar atualizações de software em clientes do gerenciador.
services: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 06/16/2020
ms.topic: conceptual
ms.openlocfilehash: 2bcf2518ab7f4e5a3648b508e42868fd5bb1a863
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84817217"
---
# <a name="integrate-update-management-with-windows-endpoint-configuration-manager"></a>Integração do Gerenciamento de Atualizações com o Windows Endpoint Configuration Manager

Os clientes que investiram no Microsoft Endpoint Configuration Manager para gerenciar PCs, servidores e dispositivos móveis também dependem da força e maturidade dele no gerenciamento de atualizações de software como parte do ciclo de gerenciamento de atualizações de software (SUM).

Você pode relatar e atualizar servidores Windows gerenciados criando e preparando previamente implantações de atualização de software no Windows Endpoint Configuration Manager, bem como obter o status detalhado de implantações de atualização concluídas, usando o [Gerenciamento de Atualizações](automation-update-management.md). Se usar o Windows Endpoint Configuration Manager para relatórios de conformidade de atualizações, mas não para gerenciar implantações de atualizações com os servidores Windows, você pode continuar a relatar para o Configuration Manager, enquanto as atualizações de segurança são gerenciadas com o Gerenciamento de Atualizações da Automação do Azure.

>[!NOTE]
>Embora Gerenciamento de Atualizações ofereça suporte à avaliação de atualização e aplicação de patches do Windows Server 2008 R2, ele não dá suporte a clientes gerenciados pelo ponto de extremidade Configuration Manager executar este sistema operacional.

## <a name="prerequisites"></a>Pré-requisitos

* É necessário adicionar o [Gerenciamento de Atualizações da Automação do Azure](automation-update-management.md) à conta de Automação.
* Servidores Windows atualmente gerenciados pelo ambiente do Windows Endpoint Configuration Manager também precisam relatar para o workspace do Log Analytics, que também tem o Gerenciamento de Atualizações habilitado.
* Esse recurso está habilitado no Windows Endpoint Configuration Manager versão do branch atual 1606 e superior. Para integrar o seu site de administração central do Windows Endpoint Configuration Manager ou um site primário autônomo aos logs do Azure Monitor e importar coleções, veja [Conexão do Configuration Manager aos logs do Azure Monitor](../azure-monitor/platform/collect-sccm.md).  
* Os agentes do Windows deverão ser configurados para se comunicar com um servidor Windows Server Update Services (WSUS) ou ter acesso ao Microsoft Update se eles não receberem atualizações de segurança do Windows Endpoint Configuration Manager.

Como gerenciar clientes hospedados no IaaS do Azure com o ambiente existente do Windows Endpoint Configuration Manager é algo que depende da conexão que você tem entre os datacenters do Azure e sua infraestrutura. Essa conexão afeta as alterações de design que você talvez precise fazer à sua infraestrutura do Windows Endpoint Configuration Manager, bem como o custo relacionado para dar suporte a essas alterações necessárias. Para entender quais considerações de planejamento você precisa avaliar antes de continuar, examine [Configuration Manager no Azure – Perguntas Frequentes](https://docs.microsoft.com/configmgr/core/understand/configuration-manager-on-azure#networking).

## <a name="manage-software-updates-from-windows-endpoint-configuration-manager"></a>Gerenciamento de atualizações de software do Windows Endpoint Configuration Manager

Se pretende continuar gerenciando implantações de atualização do Windows Endpoint Configuration Manager, execute as etapas a seguir. A Automação do Azure conecta-se ao Windows Endpoint Configuration Manager para aplicar atualizações aos computadores clientes conectados ao workspace do Log Analytics. O conteúdo de atualização está disponível no cache do computador cliente, como se a implantação fosse gerenciada pelo Windows Endpoint Configuration Manager.

1. Crie uma implantação de atualização de software do site de nível superior na hierarquia do Windows Endpoint Configuration Manager usando o processo descrito em [Implantação de atualizações de software](https://docs.microsoft.com/configmgr/sum/deploy-use/deploy-software-updates). A única configuração que deve ser realizada de forma diferente de uma implantação padrão é a opção **Não instalar atualizações de software** para controlar o comportamento de download do pacote de implantação. Esse comportamento é gerenciado pelo Gerenciamento de Atualizações através da criação de uma implantação de atualização agendada na próxima etapa.

1. Na Automação do Azure, selecione **Gerenciamento de Atualizações**. Crie uma nova implantação seguindo as etapas descritas em [Criação de uma Implantação de Atualização](automation-tutorial-update-management.md#schedule-an-update-deployment) e selecione **Grupos importados** no menu suspenso **Tipo** para selecionar a coleção apropriada do Windows Endpoint Configuration Manager. Tenha em mente os seguintes pontos importantes: a. Se uma janela de manutenção for definida na coleção de dispositivos do Windows Endpoint Configuration Manager selecionada, os membros da coleção irão respeitá-la, em vez da configuração **Duração** definida na implantação agendada.
    b. Os membros da coleção de destino devem ter uma conexão com a Internet (direta, através de um servidor proxy ou por meio do gateway do Log Analytics).

Após concluir a implantação de atualização através da Automação do Azure, os computadores de destino membros do grupo de computadores selecionado irão instalar as atualizações no horário agendado a partir do cache local do cliente. Você pode [exibir o status de implantação de atualização](automation-tutorial-update-management.md#check-deployment-status) para monitorar os resultados de sua implantação.

## <a name="manage-software-updates-from-azure-automation"></a>Gerenciar atualizações de software da Automação do Azure

Para gerenciar atualizações para as VMs do Windows Server que são clientes do Windows Endpoint Configuration Manager, configure a política de cliente para desabilitar o recurso de Gerenciamento de Atualização de Software para todos os clientes gerenciados pelo Gerenciamento de Atualizações. Por padrão, as configurações do cliente são direcionadas a todos os dispositivos na hierarquia. Para obter mais informações sobre essa configuração de política e como configurá-la, veja [Como definir as configurações de cliente no Configuration Manager](https://docs.microsoft.com/configmgr/core/clients/deploy/configure-client-settings).

Após essa alteração de configuração, crie uma nova implantação seguindo as etapas descritas em [Criação de uma Implantação de Atualização](automation-tutorial-update-management.md#schedule-an-update-deployment) e selecione **Grupos importados** no menu suspenso **Tipo** para selecionar a coleção adequada do Windows Endpoint Configuration Manager.

## <a name="next-steps"></a>Próximas etapas

Para configurar um agendamento de integração, veja [Agendamento de uma implantação de atualização](automation-tutorial-update-management.md#schedule-an-update-deployment).