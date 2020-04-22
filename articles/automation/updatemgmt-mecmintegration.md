---
title: Use o gerenciamento de atualização de automação do Azure com clientes do Gerenciador de Configuração
description: Este artigo destina-se a ajudá-lo a configurar o Microsoft Endpoint Configuration Manager com esta solução para implantar atualizações de software para clientes ConfigMgr.
services: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 32a077c476d9669c3f32bd4040fdc8ff90156c19
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678741"
---
# <a name="deploy-updates-to-microsoft-endpoint-configuration-manager-clients-with-update-management"></a>Implantar atualizações para clientes do Microsoft Endpoint Configuration Manager com gerenciamento de atualizações

Os clientes que investiram no Microsoft Endpoint Configuration Manager para gerenciar PCs, servidores e dispositivos móveis também confiam em sua força e maturidade no gerenciamento de atualizações de software como parte de seu ciclo de gerenciamento de atualizações de software (SUM).

Você pode relatar e atualizar servidores Gerenciados do Windows criando e pré-encenando implantações de atualização de software no Gerenciador de Configuração e obter o status detalhado das implantações de atualização concluídas usando [o Update Management](automation-update-management.md). Se você usar o Gerenciador de Configuração para atualizar relatórios de conformidade, mas não para gerenciar implantações de atualização com seus servidores Windows, você poderá continuar reportando ao Gerenciador de Configuração enquanto as atualizações de segurança são gerenciadas com a solução Update Management.

## <a name="prerequisites"></a>Pré-requisitos

* É necessário ter a [solução de Gerenciamento de Atualizações](automation-update-management.md) adicionada à sua conta de Automação.
* Os servidores Windows atualmente gerenciados pelo ambiente do Gerenciador de Configuração também precisam reportar ao espaço de trabalho do Log Analytics, que também tem a solução Update Management ativada.
* Esse recurso está habilitado na versão atual do Configuration Manager 1606 ou superior. Para integrar o site de administração central do Gerenciador de Configuração ou um site principal autônomo com registros e coleções de importação do Monitor do Azure, revise [o Connect Configuration Manager para logs do Monitor do Azure](../azure-monitor/platform/collect-sccm.md).  
* Os agentes do Windows deverão ser configurados para se comunicar com um servidor WSUS (Windows Server Update Services) ou ter acesso ao Microsoft Update se eles não receberem atualizações de segurança do Configuration Manager.   

Como gerenciar clientes hospedados no IaaS do Azure com seu ambiente existente do Configuration Manager é algo que depende da conexão que você tem entre os datacenters do Azure e sua infraestrutura. Essa conexão afeta as alterações de design que você talvez precise fazer à sua infraestrutura do Configuration Manager, bem como o custo relacionado para dar suporte a essas alterações necessárias. Para entender quais considerações de planejamento você precisa avaliar antes de continuar, examine [Configuration Manager no Azure – Perguntas Frequentes](https://docs.microsoft.com/configmgr/core/understand/configuration-manager-on-azure#networking).

## <a name="configuration"></a>Configuração

### <a name="manage-software-updates-from-configuration-manager"></a>Gerenciar atualizações de software do Configuration Manager 

Se você pretende continuar gerenciando implantações de atualização do Configuration Manager, execute as etapas a seguir. A Automação do Azure conecta-se ao Gerenciador de Configurações para aplicar atualizações aos computadores clientes conectados ao espaço de trabalho do Log Analytics. O conteúdo de atualização está disponível do cache do computador cliente, como se a implantação fosse gerenciada pelo Configuration Manager.

1. Crie uma implantação de atualização de software a partir do site de nível superior em sua hierarquia do Gerenciador de Configuração usando o processo descrito em [Atualizações de software Deploy](https://docs.microsoft.com/configmgr/sum/deploy-use/deploy-software-updates). A única configuração que deve ser realizada de forma diferente de uma implantação padrão é a opção **Não instalar atualizações de software** para controlar o comportamento de download do pacote de implantação. Esse comportamento é gerenciado pela solução de Gerenciamento de Atualizações por meio da criação de uma implantação de atualização agendada na próxima etapa.

1. Na Automação do Azure, selecione **Gerenciamento de Atualizações**. Crie uma nova implantação seguindo as etapas descritas em [Criar uma implantação de atualização](automation-tutorial-update-management.md#schedule-an-update-deployment) e selecione grupos **importados** na parada **tipo** para selecionar a coleção de gerenciador de configuração apropriada. Tenha em mente os seguintes pontos importantes: a. Se uma janela de manutenção for definida na coleção de dispositivos do Gerenciador de Configurações selecionado, os membros da coleção irão respeitá-la, em vez da configuração **Duração** definida na implantação agendada.
    b. Os membros da coleção de destino devem ter uma conexão com a Internet (direta, através de um servidor proxy ou por meio do gateway do Log Analytics).

Após concluir a implantação de atualização através da Automação do Azure, os computadores de destino membros do grupo de computadores selecionado irão instalar as atualizações no horário agendado a partir do cache local do cliente. Você pode [exibir o status de implantação de atualização](automation-tutorial-update-management.md#view-results-of-an-update-deployment) para monitorar os resultados de sua implantação.

### <a name="manage-software-updates-from-azure-automation"></a>Gerenciar atualizações de software da Automação do Azure

Para gerenciar atualizações para as VMs do Windows Server que são clientes do Configuration Manager, você precisa configurar a política de cliente para desabilitar o recurso de Gerenciamento de Atualizações de Software para todos os clientes gerenciados por essa solução. Por padrão, as configurações do cliente são direcionadas a todos os dispositivos na hierarquia. Para obter mais informações sobre essa configuração de diretiva e como configurá-la, [revise como configurar as configurações do cliente no Gerenciador de configuração](https://docs.microsoft.com/configmgr/core/clients/deploy/configure-client-settings).

Após essa alteração de configuração, crie uma nova implantação seguindo as etapas descritas em [Criar uma Implantação de Atualização](automation-tutorial-update-management.md#schedule-an-update-deployment) e selecione **Grupos importados** no menu suspenso **Tipo** para selecionar a coleção adequada do Gerenciador de Configurações.

## <a name="next-steps"></a>Próximas etapas

