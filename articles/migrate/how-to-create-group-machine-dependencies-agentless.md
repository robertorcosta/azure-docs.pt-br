---
title: Configure a análise de dependência sem agente na avaliação do servidor Azure Migrate
description: Configure a análise de dependência sem agente na Avaliação do Servidor Migração do Azure.
ms.topic: how-to
ms.date: 2/24/2020
ms.openlocfilehash: af767bf73a3b9a6f2a91298987f11974499fd694
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455699"
---
# <a name="set-up-agentless-dependency-visualization"></a>Configurar visualização de dependência sem agente 

Este artigo descreve como configurar a análise de dependência sem agente no Azure Migrate:Server Assessment. [A análise de dependência](concepts-dependency-visualization.md) ajuda você a identificar e entender as dependências entre as máquinas que deseja avaliar e migrar para o Azure.


> [!IMPORTANT]
> A visualização de dependência sem agente está atualmente em visualização apenas para VMs VMware, descoberta com a ferramenta Azure Migrate:Server Assessment.
> Os recursos podem ser limitados ou incompletos.
> Esta visualização é coberta pelo suporte ao cliente e pode ser usada para cargas de trabalho de produção.
> Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



## <a name="before-you-start"></a>Antes de começar

- [Saiba mais sobre](concepts-dependency-visualization.md#agentless-analysis) análise de dependência sem agente.
- [Revise](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) os pré-requisitos e os requisitos de suporte para configurar a visualização de dependência sem agente para VMs VMware
- Certifique-se de [ter criado](how-to-add-tool-first-time.md) um projeto azure Migrate.
- Se você já criou um projeto, certifique-se de ter [adicionado](how-to-assess.md) a ferramenta Azure Migrate:Server Assessment.
- Certifique-se de configurar um [aparelho Azure Migrate](migrate-appliance.md) para descobrir suas máquinas no local. Saiba como configurar um aparelho para [VMware VMs.](how-to-set-up-appliance-vmware.md) O aparelho descobre máquinas no local e envia dados de metadados e desempenho para o Azure Migrate:Server Assessment.


## <a name="current-limitations"></a>Limitações atuais

- No momento, você não pode adicionar ou remover um servidor de um grupo, na exibição de análise de dependência.
- Um mapa de dependência para um grupo de servidores não está disponível no momento.
- Atualmente, os dados de dependência não podem ser baixados em formato tabular.

## <a name="create-a-user-account-for-discovery"></a>Crie uma conta de usuário para a descoberta

Configure uma conta de usuário para que a Avaliação do Servidor possa acessar a VM para ser descoberta. [Saiba](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) mais sobre os requisitos da conta.


## <a name="add-the-user-account-to-the-appliance"></a>Adicione a conta de usuário ao aparelho

Adicione a conta de usuário ao aparelho.

1. Abra o aplicativo de gerenciamento de aparelhos. 
2. Navegue até o painel **de detalhes Do Provide vCenter.**
3. Em **Discover aplicativo e dependências em VMs,** clique em Adicionar **credenciais**
3. Escolha o **sistema operacional,** forneça um nome amigável para a conta e a**senha** do **nome**/de usuário
6. Clique em **Salvar**.
7. Clique **em Salvar e iniciar a descoberta**.

    ![Adicionar conta de usuário VM](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Iniciar a descoberta da dependência

Escolha as máquinas nas quais deseja ativar a descoberta da dependência.

1. No **Azure Migrate: Server Assessment**, clique em Servidores **descobertos**.
2. Clique no ícone **de análise de dependência.**
3. Clique **em Adicionar servidores**.
3. Na página **Adicionar servidores,** escolha o aparelho que está descobrindo as máquinas relevantes.
4. Na lista de máquinas, selecione as máquinas.
5. Clique **em Adicionar servidores**.

    ![Iniciar a descoberta da dependência](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

Você pode visualizar dependências cerca de seis horas após iniciar a descoberta da dependência.

## <a name="visualize-dependencies"></a>Visualize dependências

1. No **Azure Migrate: Server Assessment**, clique em Servidores **descobertos**.
2. Procure a máquina que você deseja ver.
3. Na coluna **Dependências,** clique **em Exibir dependências**
4. Alterar o período de tempo para o qual deseja visualizar o mapa usando a **isento de duração do tempo.**
5. Expanda o **grupo Cliente** para listar as máquinas com uma dependência da máquina selecionada.
6. Expanda o grupo **Porta** para listar as máquinas que têm uma dependência da máquina selecionada.
7. Para navegar até a visualização do mapa de qualquer uma das máquinas dependentes, clique no nome da máquina > **mapa do servidor de carga**

    ![Expandir o grupo de portas do servidor e o mapa do servidor de carga](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![Expandir o grupo de clientes ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. Expanda a máquina selecionada para visualizar detalhes em nível de processo para cada dependência.

    ![Expandir o servidor para mostrar processos](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> As informações de processo para uma dependência nem sempre estão disponíveis. Se não estiver disponível, a dependência é representada com o processo marcado como "processo desconhecido".

## <a name="stop-dependency-discovery"></a>Parar a descoberta da dependência

Escolha as máquinas nas quais deseja impedir a descoberta da dependência.

1. No **Azure Migrate: Server Assessment**, clique em Servidores **descobertos**.
2. Clique no ícone **de análise de dependência.**
3. Clique **em Remover servidores**.
3. Na página **Remover servidores,** escolha o **aparelho** que está descobrindo as VMs nas quais você procura parar a descoberta da dependência.
4. Na lista de máquinas, selecione as máquinas.
5. Clique **em Remover servidores**.


## <a name="next-steps"></a>Próximas etapas

[Agrupar as máquinas](how-to-create-a-group.md) para avaliação.
