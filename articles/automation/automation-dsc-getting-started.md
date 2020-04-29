---
title: Introdução à configuração de estado da automação do Azure
description: Explicação e exemplos das tarefas mais comuns na Configuração de Estado da Automação do Azure (DSC)
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 04/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 43268d5b48b44fffefa222f566c40151c85a5895
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81392165"
---
# <a name="get-started-with-azure-automation-state-configuration"></a>Introdução à configuração de estado da automação do Azure

Este artigo explica como executar as tarefas mais comuns com a Configuração de Estado da Automação do Azure como criar, importar e compilar configurações, integrar computadores para gerenciar e exibir relatórios. Para obter uma visão geral do que é a Configuração de Estado da Automação do Azure, consulte [Visão geral da Configuração de Estado da Automação do Azure](automation-dsc-overview.md). Para a documentação da DSC (Desired State Configuration), consulte [Visão geral da Desired State Configuration do Windows PowerShell](/powershell/scripting/dsc/overview/overview).

Este artigo fornece um guia passo a passo sobre o uso da Configuração de Estado da Automação do Azure. Se quiser um ambiente de exemplo que já esteja configurado sem seguir as etapas descritas neste artigo, você poderá usar o seguinte modelo do Resource Manager: [Modelo de Nó Gerenciado da Automação do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration). Este modelo configura um ambiente de Configuração de Estado da Automação do Azure concluído, incluindo uma VM do Azure gerenciada pela Configuração de Estado da Automação do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir os exemplos neste artigo, são necessários:

- Uma conta de Automação do Azure. Para obter instruções sobre como criar uma conta Executar Como de Automação do Azure, consulte [Conta Executar Como do Azure](automation-sec-configure-azure-runas-account.md).
- Uma VM Azure Resource Manager (não clássica) executando um [sistema operacional com suporte](automation-dsc-overview.md#operating-system-requirements). Para obter instruções sobre a criação de uma VM, consulte [Criar sua primeira máquina virtual do Windows no portal do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="creating-a-dsc-configuration"></a>Criando uma configuração de DSC

Crie uma [configuração de DSC](/powershell/scripting/dsc/configurations/configurations) simples que garante a presença ou a ausência do WindowsFeature do **Servidor Web** (IIS), dependendo de como os nós são atribuídos.

1. Inicie o [VSCode](https://code.visualstudio.com/docs) (ou qualquer editor de texto).
1. Digite o seguinte texto:

    ```powershell
    configuration TestConfig
    {
        Node IsWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Present'
                Name                 = 'Web-Server'
                IncludeAllSubFeature = $true
            }
        }

        Node NotWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Absent'
                Name                 = 'Web-Server'
            }
        }
    }
    ```
1. Salve o arquivo como **TestConfig. ps1**.

Essa configuração chama um recurso em cada bloco de nó, o [recurso WindowsFeature](/powershell/scripting/dsc/reference/resources/windows/windowsfeatureresource). Esse recurso garante a presença ou a ausência do recurso de **servidor Web** .

## <a name="importing-a-configuration-into-azure-automation"></a>Importando uma configuração na Automação do Azure

Em seguida, importe a configuração para a conta de Automação.

1. Entre no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, no nome de sua conta de Automação.
1. Na página conta de automação, selecione **configuração de estado (DSC)** em **Gerenciamento de configuração**.
1. Na página configuração de estado (DSC), clique na guia **configurações** e, em seguida, clique em **Adicionar**.
1. No painel de configuração de importação, navegue até `TestConfig.ps1` o arquivo no computador.

   ![Captura de tela da folha **Importar Configuração**](./media/automation-dsc-getting-started/AddConfig.png)

1. Clique em **OK**.

## <a name="viewing-a-configuration-in-azure-automation"></a>Exibindo uma configuração na Automação do Azure

Depois de importar uma configuração, você pode vê-la no Portal do Azure.

1. Entre no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, no nome de sua conta de Automação.
1. Na página Conta de Automação, selecione **Configuração de estado (DSC)** em **Gerenciamento de Configuração**.
1. Na página configuração de estado (DSC), clique na guia **configurações** e, em seguida, clique em **TestConfig**. Esse é o nome da configuração que você importou no procedimento anterior.
1. No painel configuração do TestConfig, clique em **Exibir fonte de configuração**.

   ![Captura de tela da folha TestConfig configuration (Configuração do TestConfig)](./media/automation-dsc-getting-started/ViewConfigSource.png)

   Um painel de origem de configuração do TestConfig é aberto, exibindo o código do PowerShell para a configuração.

## <a name="compiling-a-configuration-in-azure-automation"></a>Compilando uma configuração na Automação do Azure

Antes de aplicar um estado desejado a um nó, uma configuração DSC definindo esse estado deve ser compilada em uma ou mais configurações de nó (documento MOF) e colocada no servidor de pull do DSC de Automação. Para obter uma descrição mais detalhada da compilação de configurações na Configuração do Estado da Automação do Azure, consulte [Compilar configurações na Configuração de Estado da Automação do Azure](automation-dsc-compile.md).
Para obter mais informações sobre a compilação das configurações, consulte [Configurações da DSC](/powershell/scripting/dsc/configurations/configurations).

1. Entre no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, no nome de sua conta de Automação.
1. Na página Conta de Automação, clique em **Configuração de estado (DSC)** em **Gerenciamento de Configuração**.
1. Na página configuração de estado (DSC), clique na guia **configurações** e, em seguida, clique em **TestConfig**. Este é o nome da configuração importada anteriormente.
1. No painel configuração do TestConfig, clique em **Compilar**e, em seguida, clique em **Sim**. Isso inicia um trabalho de compilação.

   ![Captura de tela da página de configuração de TestConfig realçando o botão de compilação](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Quando você compila uma configuração na automação do Azure, ela implanta automaticamente quaisquer arquivos MOF de configuração de nó criados no servidor de pull.

## <a name="viewing-a-compilation-job"></a>Exibindo um trabalho de compilação

Depois de iniciar uma compilação, você poderá exibi-la no bloco **trabalhos de compilação** na página **configuração** . O bloco **Trabalhos de Compilação** mostra os trabalhos atualmente em execução, concluídos e com falha. Quando você abre um painel de trabalho de compilação, ele mostra informações sobre esse trabalho, incluindo erros ou avisos encontrados, parâmetros de entrada usados na configuração e logs de compilação.

1. Entre no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, no nome de sua conta de Automação.
1. Na página Conta de Automação, clique em **Configuração de estado (DSC)** em **Gerenciamento de Configuração**.
1. Na página configuração de estado (DSC), clique na guia **configurações** e, em seguida, clique em **TestConfig**. Este é o nome da configuração importada anteriormente.
1. Em **trabalhos de compilação**, selecione o trabalho de compilação a ser exibido. Um painel trabalho de compilação é aberto, rotulado com a data em que o trabalho de compilação foi iniciado.

   ![Captura de tela da página Trabalho de Compilação](./media/automation-dsc-getting-started/CompilationJob.png)

1. Clique em qualquer bloco no painel trabalho de compilação para ver mais detalhes sobre o trabalho.

## <a name="viewing-node-configurations"></a>Exibindo configurações de nó

A conclusão com êxito de um trabalho de compilação cria uma ou mais novas configurações de nó. Uma configuração de nó é um documento MOF que é implantado no servidor de pull e está pronto para ser submetido ao pull e aplicado por um ou mais nós. Você pode exibir as configurações de nó em sua conta de automação na página de configuração de estado (DSC). Uma configuração de nó tem um nome com o `ConfigurationName.NodeName`formulário.

1. Entre no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, no nome de sua conta de Automação.
1. Na página Conta de Automação, clique em **Configuração de estado (DSC)** em **Gerenciamento de Configuração**.
1. Na página Configuração de estado (DSC), clique na guia **Configurações compiladas**.

   ![Captura de tela da guia Configurações Compiladas](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration"></a>Integrar uma VM do Azure para gerenciamento com Configuração de Estado da Automação do Azure

Você pode usar a configuração de estado da automação do Azure para gerenciar VMs do Azure (clássicas e Resource Manager), VMs locais, máquinas Linux, VMs AWS e máquinas físicas locais. Neste artigo, você aprenderá como integrar somente VMs do Azure Resource Manager. Para obter mais informações sobre como integrar outros tipos de computadores, consulte [Integrar computadores para gerenciamento por Configuração de Estado da Automação do Azure](automation-dsc-onboarding.md).

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-state-configuration"></a>Para integrar uma VM do Azure Resource Manager para gerenciamento por Configuração de Estado da Automação do Azure

1. Entre no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, no nome de sua conta de Automação.
1. Na página Conta de Automação, clique em **Configuração de estado (DSC)** em **Gerenciamento de Configuração**.
1. Na página configuração de estado (DSC), selecione a guia **nós** e clique em **+ Adicionar**.

   ![Captura de tela da página Nós DSC realçando o botão Adicionar VM do Azure](./media/automation-dsc-getting-started/OnboardVM.png)

1. No painel máquinas virtuais, selecione sua VM.
1. No painel de detalhes da máquina virtual, clique em **+ conectar**.

   > [!IMPORTANT]
   > A VM deve ser uma VM Azure Resource Manager executando um [sistema operacional com suporte](automation-dsc-overview.md#operating-system-requirements).

2. Na página registro, selecione o nome da configuração do nó a ser aplicado à VM no campo **nome da configuração do nó** . Fornecer um nome neste ponto é opcional. Você pode alterar a configuração de nó atribuída após integrar o nó.

3. Marque **Reinicialize o nó, se necessário** e clique em **OK**.

   ![Captura de tela da folha Registro](./media/automation-dsc-getting-started/RegisterVM.png)

   A configuração de nó especificada é aplicada à VM em intervalos especificados pelo valor fornecido para a **frequência do modo de configuração**. A VM verifica se há atualizações para a configuração de nó em intervalos especificados pelo valor de **frequência de atualização** . Para obter mais informações sobre como esses valores são usados, consulte [Configurando o Gerenciador de Configuração Local](/powershell/scripting/dsc/managing-nodes/metaConfig).

O Azure inicia o processo de integração da VM. Quando estiver concluída, a VM aparecerá na guia **Nós** da página Configuração de estado (DSC) na conta de Automação.

## <a name="viewing-the-list-of-managed-nodes"></a>Exibição da lista dos nós gerenciados

É possível exibir a lista de todos os computadores que foram integrados para gerenciamento na conta de Automação na guia **Nós** da página Configuração de estado (DSC).

1. Entre no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, no nome de sua conta de Automação.
1. Na página Conta de Automação, clique em **Configuração de estado (DSC)** em **Gerenciamento de Configuração**.
1. Na página Configuração de estado (DSC), clique na guia **Nós**.

## <a name="viewing-reports-for-managed-nodes"></a>Exibição dos relatórios para nós gerenciados

Sempre que a Configuração de Estado da Automação do Azure executar uma verificação de consistência em um nó gerenciado, o nó enviará um relatório de status de volta ao servidor de pull. Exiba esses relatórios na página do nó.

1. Entre no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, no nome de sua conta de Automação.
1. Na página Conta de Automação, clique em **Configuração de estado (DSC)** em **Gerenciamento de Configuração**.
1. Na página configuração de estado (DSC), clique na guia **nós** . Aqui, você pode ver a visão geral do estado de configuração e os detalhes de cada nó.

   ![Captura de tela da página de Nó](./media/automation-dsc-getting-started/NodesTab.png)

1. Na guia **Nós**, clique no registro do nó para abrir o relatório. Clique no relatório que você quer exibir os detalhes de relatórios adicionais.

   ![Captura de tela da folha Relatório](./media/automation-dsc-getting-started/NodeReport.png)

Na folha de um relatório individual, você pode ver as seguintes informações de status para a verificação de consistência correspondente:

- O status do relatório. Os valores possíveis são:
    * Compatível-o nó está em conformidade com a verificação.
   * Falha-falha na verificação da configuração.
   * Não compatível-o nó está no `ApplyandMonitor` modo e o computador não está no estado desejado.
- A hora de início para a verificação de consistência.
- O runtime total para a verificação de consistência.
- O tipo de verificação de consistência.
- Quaisquer erros, incluindo o código de erro e a mensagem de erro.
- Todos os recursos de DSC usados na configuração e o estado de cada recurso (se o nó estiver no estado desejado para esse recurso). Você pode clicar em cada recurso para obter informações mais detalhadas sobre esse recurso.
- O nome, o endereço IP e o modo de configuração do nó.

Você também pode clicar em **Exibir relatório bruto** para ver os dados reais que o nó envia para o servidor.
Para obter mais informações sobre como usar esses dados, consulte [Usando um servidor de relatório da DSC](/powershell/scripting/dsc/pull-server/reportserver).

Pode levar algum tempo depois de um nó ser integrado até que o primeiro relatório esteja disponível. Talvez seja necessário aguardar até 30 minutos para o primeiro relatório após você integrar um nó.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>Reatribuindo um nó a uma configuração de nó diferente

Você pode atribuir um nó para usar uma configuração de nó diferente daquela que inicialmente atribuída.

1. Entre no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, no nome de sua conta de Automação.
1. Na página Conta de Automação, clique em **Configuração de estado (DSC)** em **Gerenciamento de Configuração**.
1. Na página Configuração de estado (DSC), clique na guia **Nós**.
1. Na guia **Nós** tab, clique no nome do nó que você deseja atribuir.
1. Na página desse nó, clique em **Atribuir configuração de nó**.

    ![Captura de tela da página de detalhes do Nó, realçando o botão Atribuir configuração de nó](./media/automation-dsc-getting-started/AssignNode.png)

1. Na página Atribuir Configuração de Nó, selecione a configuração de nó à qual você deseja atribuir o nó e, em seguida, clique em **OK**.

    ![Captura de tela da página Atribuir Configuração de Nó](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>Cancelando o registro de um nó

Se você não desejar mais que um nó seja gerenciado pelo DSC de Automação do Azure, você poderá cancelar o registro dele.

1. Entre no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, no nome de sua conta de Automação.
1. Na página Conta de Automação, clique em **Configuração de estado (DSC)** em **Gerenciamento de Configuração**.
1. Na página Configuração de estado (DSC), clique na guia **Nós**.
1. Na guia **Nós**, clique no nome do nó que você deseja desatribuir.
1. No painel desse nó, clique em **Cancelar registro**.

    ![Captura de tela da página de detalhes do Nó, realçando o botão Cancelar registro](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>Artigos relacionados

- [Visão geral da Configuração de Estado da Automação do Azure](automation-dsc-overview.md)
- [Integrar computadores para gerenciamento por Configuração de Estado da Automação do Azure](automation-dsc-onboarding.md)
- [Visão Geral da Configuração de Estado Desejado do Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Preço da Configuração de Estado da Automação do Azure](https://azure.microsoft.com/pricing/details/automation/)
- [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation)