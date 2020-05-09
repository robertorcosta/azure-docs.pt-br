---
title: Usar o Gerenciamento de Atualizações de automação do Azure com clientes Configuration Manager
description: Este artigo destina-se a ajudá-lo a configurar o Microsoft Endpoint Configuration Manager com essa solução para implantar atualizações de software em clientes do ConfigMgr.
services: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 2dbc33aa56c7e930596ba6806ba1dd2e128e1c82
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780208"
---
# <a name="deploy-updates-to-microsoft-endpoint-configuration-manager-clients-with-update-management"></a>Implantar atualizações para clientes do Microsoft Endpoint Configuration Manager com Gerenciamento de Atualizações

Os clientes que investiram no Microsoft Endpoint Configuration Manager para gerenciar PCs, servidores e dispositivos móveis também dependem de sua força e maturidade no gerenciamento de atualizações de software como parte do ciclo de SUM (gerenciamento de atualização de software).

Você pode relatar e atualizar servidores Windows gerenciados criando e pré-preparando implantações de atualização de software no Configuration Manager e obter o status detalhado das implantações de atualização concluídas usando [Gerenciamento de atualizações](automation-update-management.md). Se você usar Configuration Manager para relatórios de conformidade de atualização, mas não para gerenciar implantações de atualização com seus servidores Windows, poderá continuar relatando para Configuration Manager enquanto as atualizações de segurança são gerenciadas com o Gerenciamento de Atualizações.

## <a name="prerequisites"></a>Pré-requisitos

* Você deve ter [Gerenciamento de atualizações](automation-update-management.md) adicionado à sua conta de automação.
* Os servidores do Windows atualmente gerenciados pelo ambiente de Configuration Manager também precisam relatar para o espaço de trabalho Log Analytics que também tem Gerenciamento de Atualizações habilitado.
* Esse recurso está habilitado no Configuration Manager Branch atual versão 1606 e superior. Para integrar seu site de administração central do Configuration Manager ou um site primário autônomo com Azure Monitor logs e importar coleções, examine [conectar Configuration Manager para Azure monitor logs](../azure-monitor/platform/collect-sccm.md).  
* Os agentes do Windows deverão ser configurados para se comunicar com um servidor WSUS (Windows Server Update Services) ou ter acesso ao Microsoft Update se eles não receberem atualizações de segurança do Configuration Manager.

Como gerenciar clientes hospedados no IaaS do Azure com seu ambiente existente do Configuration Manager é algo que depende da conexão que você tem entre os datacenters do Azure e sua infraestrutura. Essa conexão afeta as alterações de design que você talvez precise fazer à sua infraestrutura do Configuration Manager, bem como o custo relacionado para dar suporte a essas alterações necessárias. Para entender quais considerações de planejamento você precisa avaliar antes de continuar, examine [Configuration Manager no Azure – Perguntas Frequentes](https://docs.microsoft.com/configmgr/core/understand/configuration-manager-on-azure#networking).

## <a name="configuration"></a>Configuração

### <a name="manage-software-updates-from-configuration-manager"></a>Gerenciar atualizações de software do Configuration Manager

Se você pretende continuar gerenciando implantações de atualização do Configuration Manager, execute as etapas a seguir. A Automação do Azure conecta-se ao Gerenciador de Configurações para aplicar atualizações aos computadores clientes conectados ao espaço de trabalho do Log Analytics. O conteúdo de atualização está disponível do cache do computador cliente, como se a implantação fosse gerenciada pelo Configuration Manager.

1. Crie uma implantação de atualização de software do site de nível superior em sua hierarquia de Configuration Manager usando o processo descrito em [implantar atualizações de software](https://docs.microsoft.com/configmgr/sum/deploy-use/deploy-software-updates). A única configuração que deve ser realizada de forma diferente de uma implantação padrão é a opção **Não instalar atualizações de software** para controlar o comportamento de download do pacote de implantação. Esse comportamento é gerenciado em Gerenciamento de Atualizações criando uma implantação de atualização agendada na próxima etapa.

1. Na Automação do Azure, selecione **Gerenciamento de Atualizações**. Crie uma nova implantação seguindo as etapas descritas em [criando uma implantação de atualização](automation-tutorial-update-management.md#schedule-an-update-deployment) e selecione **grupos importados** na lista suspensa **tipo** para selecionar a coleção de Configuration Manager apropriada. Tenha em mente os seguintes pontos importantes: a. Se uma janela de manutenção for definida na coleção de dispositivos do Gerenciador de Configurações selecionado, os membros da coleção irão respeitá-la, em vez da configuração **Duração** definida na implantação agendada.
    b. Os membros da coleção de destino devem ter uma conexão com a Internet (direta, através de um servidor proxy ou por meio do gateway do Log Analytics).

Após concluir a implantação de atualização através da Automação do Azure, os computadores de destino membros do grupo de computadores selecionado irão instalar as atualizações no horário agendado a partir do cache local do cliente. Você pode [exibir o status de implantação de atualização](automation-tutorial-update-management.md#view-results-of-an-update-deployment) para monitorar os resultados de sua implantação.

### <a name="manage-software-updates-from-azure-automation"></a>Gerenciar atualizações de software da Automação do Azure

Para gerenciar atualizações para VMs do Windows Server que são Configuration Manager clientes, você precisa configurar a política do cliente para desabilitar o recurso de Gerenciamento de Atualização de Software para todos os clientes gerenciados pelo Gerenciamento de Atualizações. Por padrão, as configurações do cliente são direcionadas a todos os dispositivos na hierarquia. Para obter mais informações sobre essa configuração de política e como configurá-la, examine [como definir as configurações do cliente no Configuration Manager](https://docs.microsoft.com/configmgr/core/clients/deploy/configure-client-settings).

Após essa alteração de configuração, crie uma nova implantação seguindo as etapas descritas em [Criar uma Implantação de Atualização](automation-tutorial-update-management.md#schedule-an-update-deployment) e selecione **Grupos importados** no menu suspenso **Tipo** para selecionar a coleção adequada do Gerenciador de Configurações.

## <a name="next-steps"></a>Próximas etapas

Crie uma nova implantação seguindo as etapas descritas em [criando uma implantação de atualização](automation-tutorial-update-management.md#schedule-an-update-deployment).