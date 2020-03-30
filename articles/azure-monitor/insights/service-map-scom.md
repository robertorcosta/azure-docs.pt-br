---
title: Integrar o Monitor Azure para VMs Map com o Gerente de Operações | Microsoft Docs
description: O Azure Monitor for VMs descobre automaticamente os componentes do aplicativo nos sistemas Windows e Linux e mapeia a comunicação entre os serviços. Este artigo discute o uso do recurso Mapa para criar automaticamente diagramas de aplicativos distribuídos no Operations Manager.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/12/2019
ms.openlocfilehash: 112413720f969474369555a74bc89846666e2ef9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663446"
---
# <a name="integrate-system-center-operations-manager-with-azure-monitor-for-vms-map-feature"></a>Integrar o System Center Operations Manager com o recurso Azure Monitor for VMs Map

No Monitor Azure para VMs, você pode visualizar componentes de aplicativos descobertos em máquinas virtuais Windows e Linux (VMs) que são executadas no Azure ou no seu ambiente. Com essa integração entre o recurso Mapa e o Gerente de Operações do Centro de Sistema, você pode criar automaticamente diagramas de aplicativos distribuídos no Operations Manager que são baseados nos mapas de dependência dinâmica no Monitor Do Azure para VMs. Este artigo descreve como configurar o grupo de gerenciamento do System Center Operations Manager para dar suporte a esse recurso.

>[!NOTE]
>Se você já implantou o Mapa de Serviços, você pode visualizar seus mapas no Monitor Azure para VMs, que inclui recursos adicionais para monitorar a saúde e o desempenho da VM. O recurso Mapa do Monitor Azure para VMs destina-se a substituir a solução independente do Mapa de Serviço. Para saber mais, consulte [visão geral do Azure Monitor para VMs](vminsights-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um grupo de gerenciamento de gerente de operações do Centro de Sistema (2012 R2 ou posterior).
* Um espaço de trabalho do Log Analytics configurado para suportar o Monitor Azure para VMs.
* Uma ou mais máquinas virtuais Windows e Linux ou computadores físicos que são monitorados pelo Operations Manager e enviam dados para o seu espaço de trabalho log analytics. Os servidores Linux que reportam a um grupo de gerenciamento do Operations Manager precisam ser configurados para se conectar diretamente ao Azure Monitor. Para obter mais informações, revise a visão geral no [Collect log data with the Log Analytics agent](../platform/log-analytics-agent.md).
* Uma entidade de serviço com acesso à assinatura do Azure que está associada ao espaço de trabalho do Log Analytics. Para obter mais informações, acesse [Criar uma entidade de serviço](#create-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>Instalar o pacote de gerenciamento do Mapa do Serviço

Você habilita a integração entre o Gerenciador de Operações e o recurso Mapa importando o pacote de gerenciamento Microsoft.SystemCenter.ServiceMap (Microsoft.SystemCenter.ServiceMap.mpb). Você pode baixar o pacote de gerenciamento no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=55763). O pacote contém os seguintes pacotes de gerenciamento:

* Exibições de Aplicativo do Mapa do Serviço da Microsoft
* Mapa do Serviço Interno do Microsoft System Center
* Substituições do Mapa do Serviço do Microsoft System Center
* Mapa do Serviço do Microsoft System Center

## <a name="configure-integration"></a>Configurar integração

Depois de instalar o pacote de gerenciamento do Mapa de Serviços, um novo nó, **Mapa de Serviço,** é exibido no **Pacote de Gerenciamento de Operações** no painel **Administração** do console Operações do Gerente de Operações.

>[!NOTE]
>[Operations Management Suite era uma coleção de serviços](../terminology.md#april-2018---retirement-of-operations-management-suite-brand) que incluía log analytics, agora faz parte do [Azure Monitor](../overview.md).

Para configurar o Monitor Azure para integração do Mapa de VMs, faça o seguinte:

1. Para abrir o assistente de configuração, no painel **Visão Geral do Mapa do Serviço**, clique em **Adicionar workspace**.  

    ![Painel Visão Geral do Mapa do Serviço](media/service-map-scom/scom-configuration.png)

2. Na janela **Configuração da Conexão**, insira o nome ou a ID do locatário, a ID do aplicativo (também conhecida como o nome de usuário ou a clientID) e a senha da entidade de serviço e, em seguida, clique em **Avançar**. Para obter mais informações, acesse Criar uma entidade de serviço.

    ![A janela Configuração da Conexão](media/service-map-scom/scom-config-spn.png)

3. Na janela **Seleção de Assinatura**, selecione a assinatura do Azure, o grupo de recursos do Azure (aquele que contém o espaço de trabalho do Log Analytics) e o espaço de trabalho do Log Analytics e, em seguida, clique em **Avançar**.

    ![O workspace de configuração do Operations Manager](media/service-map-scom/scom-config-workspace.png)

4. No **seleção de grupo do computador** janela, que você escolha quais grupos de máquina do mapa de serviço que deseja sincronizar com o Operations Manager. Clique em **adicionar ou remover grupos de computadores**, escolha grupos na lista de **grupos de computadores disponíveis**e clique em **adicionar**.  Quando você terminar de selecionar os grupos, clique em **OK** para concluir.

    ![Os grupos de computadores de configuração do Operations Manager](media/service-map-scom/scom-config-machine-groups.png)

5. Na janela **Seleção de servidores,** você configura o Grupo de servidores do mapa de serviço com os servidores que deseja sincronizar entre o Gerenciador de Operações e o recurso Mapa. Clique em **Adicionar/Remover Servidores**.

    Para que a integração crie um diagrama de aplicativo distribuído para um servidor, o servidor deve ser/estar:

   * Monitorado pelo Gerente de Operações
   * Configurado para reportar ao espaço de trabalho log analytics configurado com o Monitor Azure para VMs
   * Listado no Grupo de Servidores do Mapa do Serviço

     ![O grupo de configuração do Operations Manager](media/service-map-scom/scom-config-group.png)

6. Opcional: Selecione o pool de recursos de todos os servidores de gerenciamento para se comunicar com o Log Analytics e, em seguida, clique **em Adicionar espaço de trabalho**.

    ![O pool de recursos de configuração do Operations Manager](media/service-map-scom/scom-config-pool.png)

    Pode levar alguns minutos para configurar e registrar o espaço de trabalho do Log Analytics. Depois de configurado, o Gerente de Operações inicia a primeira sincronização do Mapa.

    ![O pool de recursos de configuração do Operations Manager](media/service-map-scom/scom-config-success.png)

## <a name="monitor-integration"></a>Monitore a integração

Depois que o espaço de trabalho do Log Analytics é conectado, uma nova pasta, Mapa de Serviço, é exibida no painel **De monitoramento** do console Operations Manager Operations.

![O painel Monitoramento do Operations Manager](media/service-map-scom/scom-monitoring.png)

A pasta do Mapa do Serviço tem quatro nós:

* **Alertas Ativos**: Lista todos os alertas ativos sobre a comunicação entre o Gerente de Operações e o Monitor Do Azure.  

  >[!NOTE]
  >Esses alertas não são alertas de Log Analytics sincronizados com o Operations Manager, eles são gerados no grupo de gerenciamento com base nos fluxos de trabalho definidos no pacote de gerenciamento do Mapa de Serviços.

* **Servidores**: Lista os servidores monitorados configurados para sincronizar a partir do recurso Azure Monitor for VMs Map.

    ![O painel Monitorando Servidores do Operations Manager](media/service-map-scom/scom-monitoring-servers.png)

* **Visualizações de dependência de grupos de máquinas**: Lista todos os grupos de máquinas sincronizados a partir do recurso Mapa. É possível clicar em qualquer grupo para exibir seu diagrama de aplicativo distribuído.

    ![O diagrama de aplicativo distribuído do Operations Manager](media/service-map-scom/scom-group-dad.png)

* **Visualizações de dependência do servidor :** Lista todos os servidores sincronizados do recurso Mapa. É possível clicar em qualquer servidor para exibir seu diagrama de aplicativo distribuído.

    ![O diagrama de aplicativo distribuído do Operations Manager](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Editar ou excluir o workspace

É possível editar ou excluir o workspace configurado por meio do painel **Visão Geral do Mapa do Serviço** (painel **Administração** &gt; **Operations Management Suite** > **Mapa do Serviço**).

>[!NOTE]
>[Operations Management Suite foi uma coleção de serviços](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand) que incluiu o Log Analytics, que agora faz parte do [Azure Monitor](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/overview.md).

Você pode configurar apenas um espaço de trabalho do Log Analytics nesta versão atual.

![O painel Editar Workspace do Operations Manager](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Configurar regras e substituições

Uma regra, *Microsoft.SystemCenter.ServiceMapImport.Rule*, busca periodicamente informações do recurso Azure Monitor for VMs Map. Para modificar o intervalo de sincronização, você pode substituir a regra e modificar o valor do parâmetro **IntervalMinutes**.

![A janela de propriedades Substituições do Operations Manager](media/service-map-scom/scom-overrides.png)

* **Enabled**: habilite ou desabilite atualizações automáticas.
* **IntervalMinutes**: Especifica o tempo entre as atualizações. O intervalo padrão é uma hora. Se você quiser sincronizar mapas com mais freqüência, você pode alterar o valor.
* **Tempode intervaloSegundos**: Especifica o tempo antes do tempo de solicitação ser eliminado.
* **TimeWindowMinutes**: Especifica a janela de tempo para consulta de dados. O padrão é de 60 minutos, que é o intervalo máximo permitido.

## <a name="known-issues-and-limitations"></a>Limitações e problemas conhecidos

O design atual apresenta os seguintes problemas e limitações:

* Você só pode se conectar a um único espaço de trabalho do Log Analytics.
* Embora você possa adicionar servidores ao Grupo de Servidores do Mapa do Serviço manualmente por meio do painel **Criação**, os mapas desses servidores não são sincronizados imediatamente. Eles serão sincronizados a partir do recurso Azure Monitor for VMs Map durante o próximo ciclo de sincronização.
* Se você fizer qualquer alteração nos Diagramas de Aplicativos Distribuídos criados pelo pacote de gerenciamento, essas alterações provavelmente serão substituídas na próxima sincronização com o Monitor Azure para VMs.

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço

Para obter a documentação oficial do Azure sobre como criar uma entidade de serviço, consulte:

* [Criar uma entidade de serviço usando o PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Criar uma entidade de serviço usando a CLI do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Criar uma entidade de serviço usando o portal do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>Comentários
Você tem algum feedback para nós sobre a integração com o recurso Azure Monitor for VMs Map ou esta documentação? Visite nossa [página do User Voice](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), onde você pode sugerir recursos ou votar em sugestões existentes.
