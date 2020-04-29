---
title: Configurar a análise de dependência sem agente na avaliação do servidor de migrações para Azure
description: Configure a análise de dependência sem agente na avaliação do servidor de migrações para Azure.
ms.topic: how-to
ms.date: 2/24/2020
ms.openlocfilehash: af767bf73a3b9a6f2a91298987f11974499fd694
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79455699"
---
# <a name="set-up-agentless-dependency-visualization"></a>Configurar a visualização de dependência sem agente 

Este artigo descreve como configurar a análise de dependência sem agente em migrações para Azure: avaliação de servidor. A [análise de dependência](concepts-dependency-visualization.md) ajuda a identificar e compreender as dependências entre as máquinas que você deseja avaliar e migrar para o Azure.


> [!IMPORTANT]
> A visualização de dependência sem agente está atualmente em versão prévia para VMs VMware apenas, descoberta com a ferramenta migrações para Azure: Server Assessment.
> Os recursos podem ser limitados ou incompletos.
> Essa visualização é coberta pelo atendimento ao cliente e pode ser usada para cargas de trabalho de produção.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



## <a name="before-you-start"></a>Antes de começar

- [Saiba mais sobre](concepts-dependency-visualization.md#agentless-analysis) a análise de dependência sem agente.
- [Examine](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) os pré-requisitos e os requisitos de suporte para configurar a visualização de dependência sem agente para VMs VMware
- Certifique-se de ter [criado](how-to-add-tool-first-time.md) um projeto de migrações para Azure.
- Se você já tiver criado um projeto, certifique-se de ter [adicionado](how-to-assess.md) a ferramenta migrações do Azure: Server Assessment.
- Verifique se você configurou um [dispositivo de migrações para Azure](migrate-appliance.md) para descobrir suas máquinas locais. Saiba como configurar um dispositivo para VMs [VMware](how-to-set-up-appliance-vmware.md) . O dispositivo descobre computadores locais e envia metadados e dados de desempenho para migrações para Azure: avaliação do servidor.


## <a name="current-limitations"></a>Limitações atuais

- Agora você não pode adicionar ou remover um servidor de um grupo, na exibição de análise de dependência.
- Um mapa de dependências para um grupo de servidores não está disponível no momento.
- No momento, os dados de dependência não podem ser baixados no formato tabular.

## <a name="create-a-user-account-for-discovery"></a>Criar uma conta de usuário para descoberta

Configure uma conta de usuário para que a avaliação do servidor possa acessar a VM para descoberta. [Saiba mais](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) sobre os requisitos da conta.


## <a name="add-the-user-account-to-the-appliance"></a>Adicionar a conta de usuário ao dispositivo

Adicione a conta de usuário ao dispositivo.

1. Abra o aplicativo de gerenciamento de dispositivo. 
2. Navegue até o painel **fornecer detalhes do vCenter** .
3. Em **descobrir aplicativo e dependências em VMs**, clique em **Adicionar credenciais**
3. Escolha o **sistema operacional**, forneça um nome amigável para a conta e a**senha** do **nome**/de usuário
6. Clique em **Salvar**.
7. Clique em **salvar e iniciar descoberta**.

    ![Adicionar conta de usuário da VM](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Iniciar descoberta de dependência

Escolha os computadores nos quais você deseja habilitar a descoberta de dependência.

1. Em **migrações para Azure: avaliação de servidor**, clique em **servidores descobertos**.
2. Clique no ícone **análise de dependência** .
3. Clique em **adicionar servidores**.
3. Na página **adicionar servidores** , escolha o dispositivo que está descobrindo os computadores relevantes.
4. Na lista computador, selecione os computadores.
5. Clique em **adicionar servidores**.

    ![Iniciar descoberta de dependência](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

Você pode visualizar dependências em cerca de seis horas após iniciar a descoberta de dependência.

## <a name="visualize-dependencies"></a>Visualizar dependências

1. Em **migrações para Azure: avaliação de servidor**, clique em **servidores descobertos**.
2. Pesquise o computador que você deseja exibir.
3. Na coluna **dependências** , clique em **Exibir dependências**
4. Altere o período de tempo para o qual você deseja exibir o mapa usando a lista suspensa **duração de tempo** .
5. Expanda o grupo de **clientes** para listar os computadores com uma dependência na máquina selecionada.
6. Expanda o grupo de **portas** para listar os computadores que têm uma dependência do computador selecionado.
7. Para navegar até o modo de exibição de mapa de qualquer um dos computadores dependentes, clique no nome do computador > **carregar o mapa do servidor**

    ![Expandir o grupo de portas do servidor e carregar o mapa do servidor](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![Expandir grupo de clientes ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. Expanda a máquina selecionada para exibir detalhes de nível de processo para cada dependência.

    ![Expanda servidor para mostrar processos](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> As informações do processo para uma dependência não estão sempre disponíveis. Se não estiver disponível, a dependência será representada com o processo marcado como "processo desconhecido".

## <a name="stop-dependency-discovery"></a>Parar descoberta de dependência

Escolha os computadores nos quais você deseja parar a descoberta de dependência.

1. Em **migrações para Azure: avaliação de servidor**, clique em **servidores descobertos**.
2. Clique no ícone **análise de dependência** .
3. Clique em **remover servidores**.
3. Na página **remover servidores** , escolha o **dispositivo** que está descobrindo as VMs nas quais você procura parar a descoberta de dependência.
4. Na lista computador, selecione os computadores.
5. Clique em **remover servidores**.


## <a name="next-steps"></a>Próximas etapas

[Agrupe os computadores](how-to-create-a-group.md) para avaliação.
