---
title: Conectar os computadores não Azure à Central de Segurança do Azure
description: Aprender a conectar computadores não Azure à Central de Segurança
author: memildin
ms.author: memildin
ms.date: 10/01/2020
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: df780e4e55bb5c119320d4b33502d50a95da1eaf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91612210"
---
#  <a name="connect-your-non-azure-machines-to-security-center"></a>Conectar os computadores não Azure à Central de Segurança

A Central de Segurança pode monitorar a postura de segurança dos computadores não Azure, mas, antes, é necessário que você integre esses recursos. 

É possível adicionar computadores que não sejam do Azure de qualquer uma das seguintes maneiras:

- Usando o Azure Arc (**recomendado**)
- Nas páginas da Central de Segurança no portal do Azure (**Introdução** e **Inventário**)

Veja a descrição das duas maneiras abaixo.

## <a name="add-non-azure-machines-with-azure-arc"></a>Adicionar computadores não Azure com Azure Arc

Usar o Azure Arc é a maneira mais recomendada para adicionar seus computadores não Azure à Central de Segurança do Azure.

Um computador com o Azure Arc habilitado torna-se um recurso do Azure e aparece na Central de Segurança com recomendações, assim como seus outros recursos do Azure. 

Além disso, o Azure Arc fornece recursos aprimorados, como a opção de habilitar políticas no computador, implantar o agente de Análise de Logs como extensão, simplificar a implantação com outros serviços do Azure e muito mais. Para ter uma visão geral dos benefícios, confira [Cenários com suporte](../azure-arc/servers/overview.md#supported-scenarios).

**Para implantar o Azure Arc:**

- Em um computador, siga as instruções em [Início Rápido: Conectar o computador híbrido com os servidores habilitados para Azure Arc](../azure-arc/servers/learn/quick-enable-hybrid-vm.md).
- Para implantar o Azure Arc em grande escala, confira [Conectar computadores híbridos ao Azure em grande escala](../azure-arc/servers/onboard-service-principal.md)

[Saiba mais sobre o Azure Arc](../azure-arc/servers/overview.md).

> [!TIP]
> Se você estiver integrando computadores AWS, o conector da Central de Segurança para AWS lidará com a implantação do Azure Arc para você de forma transparente. Saiba mais em [Conectar as contas do AWS à Central de Segurança do Azure](quickstart-onboard-aws.md).

## <a name="add-non-azure-machines-from-security-centers-portal-pages"></a>Adicionar computadores não Azure pelas páginas do portal da Central de Segurança

1. No menu da Central de Segurança, abra a página **Introdução**.
1. Selecione a guia **Introdução**.

    :::image type="content" source="./media/security-center-onboarding/onboarding-get-started-tab.png" alt-text="Guia de introdução na página Introdução" lightbox="./media/security-center-onboarding/onboarding-get-started-tab.png":::

1. Abaixo de **Adicionar servidores não Azure**, selecione **Configurar**.

    > [!TIP]
    > Você também pode abrir a seção Adicionar computadores por meio da página **Estoque**, pressionando o botão **Adicionar servidores não Azure**.
    > 
    > :::image type="content" source="./media/security-center-onboarding/onboard-inventory.png" alt-text="Guia de introdução na página Introdução":::

    É mostrada uma lista dos workspaces do Log Analytics. A lista inclui, se aplicável, o workspace padrão criado para você pela Central de Segurança quando o provisionamento automático foi habilitado. Selecione esse workspace ou outro que você queira usar.

    Você pode adicionar computadores a um workspace existente ou criar um novo workspace. 

1. Opcionalmente, para criar um workspace, selecione **Criar workspace**.

1. Na lista de workspaces, selecione **Adicionar servidores** para o workspace relevante.
    A página **Gerenciamento de agentes** é exibida.

    Neste ponto, escolha o procedimento relevante abaixo, com base no tipo de computador que você está integrando:

    - [Integrar as VMs do Azure Stack](#onboard-your-azure-stack-vms)
    - [Integrar os computadores Linux](#onboard-your-linux-machines)
    - [Integrar os computadores Windows](#onboard-your-windows-machines)


### <a name="onboard-your-azure-stack-vms"></a>Integrar as VMs do Azure Stack
Para adicionar as VMs do Azure Stack, você precisará de informações da página **Gerenciamento de agentes** e configurar a extensão da máquina virtual **Azure Monitor, atualização e gerenciamento de configuração** nas máquinas virtuais em execução no Azure Stack.
1. Na página **Gerenciamento de agentes**, copie a **ID do Workspace** e a **Chave Primária** no Bloco de notas.
1. Faça logon no portal do **Azure Stack** e abra a página **Máquinas virtuais**.
1. Selecione a máquina virtual que você deseja proteger com a Central de Segurança.
    >[!TIP]
    > Para saber mais sobre como criar uma máquina virtual no Azure Stack, confira [este início rápido para Máquinas Virtuais do Windows](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) ou [este início rápido para Máquinas Virtuais do Linux](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
1. Selecione **Extensões**. A lista de extensões da máquina virtual instaladas nesta máquina virtual é mostrada.
1. Selecione a guia **Adicionar**. O menu **Novo Recurso** mostra a lista de extensões da máquina virtual disponíveis.
1. Selecione a extensão **Azure Monitor, atualização e gerenciamento de configuração** e selecione **Criar**. A página de configuração **Instalar extensão** é aberta.
    >[!NOTE]
    > Caso você não veja a extensão **Gerenciamento de Configuração, de Atualização e do Azure Monitor** listada no marketplace, contate seu operador do Azure Stack para disponibilizá-lo.
1. Na página de configuração **Instalar extensão**, cole a **ID do Workspace** e a **Chave do Workspace (Chave Primária)** que você copiou para o Bloco de Notas na etapa anterior.
1. Ao concluir a configuração, selecione **OK**. O status da extensão será exibido como **Provisionamento bem-sucedido**. Pode levar até uma hora para que a máquina virtual seja exibida na Central de Segurança.


### <a name="onboard-your-linux-machines"></a>Integrar os computadores Linux
Para adicionar computadores Linux, você precisa do comando WGET na página **Gerenciamento de agentes**.
1. Na página **Gerenciamento de agentes**, copie o comando **WGET** no bloco de notas. Salve esse arquivo em um local que possa ser acessado pelo computador Linux.
1. No computador Linux, abra o arquivo com o comando WGET. Selecione o conteúdo todo, copie e cole em um console do terminal.
1. Quando a instalação for concluída, você poderá confirmar se o *omsagent* está instalado executando o comando *pgrep*. O comando retornará a PID do *omsagent*.
    Os logs do agente podem ser encontrados em: */var/opt/microsoft/omsagent/\<workspace id>/log/* Pode levar até 30 minutos para que o novo computador Linux apareça na Central de Segurança.


### <a name="onboard-your-windows-machines"></a>Integrar os computadores Windows
Para adicionar computadores Windows, você precisará das informações contidas na página **Gerenciamento de agentes** e baixar o arquivo de agente apropriado (32/64 bits).
1. Selecione o link **Baixar Agente do Windows** aplicável a seu tipo de processador do computador para baixar o arquivo de configuração.
1. Na página **Gerenciamento de agentes**, copie a **ID do Workspace** e a **Chave Primária** no Bloco de notas.
1. Copie o arquivo de instalação baixado para o computador de destino e execute-o.
1. Siga o assistente de instalação (**Próxima**, **Eu concordo**, **Próximo**, **Próximo**).
    1. Na página **Azure Log Analytics**, cole a **ID do Workspace** e a **Chave do Workspace (Chave Primária)** que você copiou para o Bloco de Notas.
    1. Caso o computador deva se reportar a um workspace do Log Analytics na nuvem do Azure Government, selecione **Azure US Government** na lista suspensa da **Nuvem do Azure**.
    1. Caso o computador precise se comunicar por meio de um servidor proxy ao serviço Log Analytics, selecione **Avançado** e forneça a URL e o número da porta do servidor proxy.
    1. Quando você tiver inserido todas as definições de configuração, selecione **Próximo**.
    1. Na página **Pronto para Instalar**, examine as configurações a serem aplicadas e selecione **Instalar**.
    1. Na página **Configuração concluída com êxito**, selecione **Concluir**.

Após a conclusão, o **agente do Log Analytics** será exibido no **Painel de Controle**. Você pode revisar sua configuração e verificar se o agente está conectado.

Para obter mais informações sobre como instalar e configurar o agente, confira [Conectar computadores Windows](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard).


## <a name="verifying"></a>Verificando
Parabéns! Agora você pode ver seus computadores Azure e não Azure juntos, em um só lugar. Abra a [página Estoque de ativos](asset-inventory.md) e filtre pelos tipos de recursos relevantes. Estes ícones distinguem os tipos:

  ![Ícone ASC para computador não Azure](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) Computador não Azure

  ![Ícone ASC para computador Azure](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) VM do Azure

  ![Ícone ASC para computador Azure Arc](./media/quick-onboard-linux-computer/arc-enabled-machine-icon.png) Computador habilitado para Azure Arc

## <a name="next-steps"></a>Próximas etapas

Essa página mostrou como adicionar seus computadores não Azure à Central de Segurança do Azure. Para monitorar o status, use as ferramentas de estoque, conforme explicado na seguinte página:

- [Explorar e gerenciar os recursos com as ferramentas de gerenciamento e estoque de ativos](asset-inventory.md)