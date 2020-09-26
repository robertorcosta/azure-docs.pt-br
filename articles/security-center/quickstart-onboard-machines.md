---
title: Conectar seus computadores não Azure à central de segurança do Azure
description: Saiba como conectar seus computadores não Azure à central de segurança
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 82b8161f92f337002e9d8bbdc45cd53d5921fc00
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91280667"
---
#  <a name="connect-your-non-azure-machines-to-security-center"></a>Conectar seus computadores não Azure à central de segurança

A Central de Segurança pode monitorar a postura de segurança dos computadores não Azure, mas, antes, é necessário que você integre esses recursos. Você pode adicionar computadores não Azure da página de **introdução** ou do **inventário** , conforme descrito abaixo.

## <a name="add-non-azure-computers"></a>Adicionar computadores não Azure 

1. No menu da central de segurança, abra a página **Guia de introdução** .
1. Selecione a guia **Introdução**.

    :::image type="content" source="./media/security-center-onboarding/onboarding-get-started-tab.png" alt-text="Guia introdução na página Introdução" lightbox="./media/security-center-onboarding/onboarding-get-started-tab.png":::

1. Abaixo de **adicionar servidores não Azure**, selecione **Configurar** .

    > [!TIP]
    > Você também pode abrir Adicionar computadores no botão **adicionar servidores não Azure** da página de **inventário** .

    É mostrada uma lista dos workspaces do Log Analytics. A lista inclui, se aplicável, o workspace padrão criado para você pela Central de Segurança quando o provisionamento automático foi habilitado. Selecione esse workspace ou outro que você queira usar.

    Você pode adicionar computadores a um workspace existente ou criar um novo workspace. 

1. Opcionalmente, para criar um novo espaço de trabalho, selecione  **criar novo espaço de trabalho**.

1. Na lista de espaços de trabalho, selecione **adicionar servidores** para o espaço de trabalho relevante.
    A página **Gerenciamento de agentes** é exibida.

    Aqui, escolha o procedimento relevante abaixo, dependendo do tipo de computador que você está integrando:

    - [Integre suas VMs Azure Stack](#onboard-your-azure-stack-vms)
    - [Integre seus computadores Linux](#onboard-your-linux-machines)
    - [Integre suas máquinas Windows](#onboard-your-windows-machines)


### <a name="onboard-your-azure-stack-vms"></a>Integre suas VMs Azure Stack
Para adicionar Azure Stack VMs, você precisará das informações na página **Gerenciamento de agentes** e configurar a extensão de **Azure monitor, atualização e gerenciamento de configuração** de máquina virtual nas máquinas virtuais em execução no seu Azure Stack.
1. Na página **Gerenciamento de agentes** , copie a **ID do espaço de trabalho** e a **chave primária** no bloco de notas.
1. Faça logon em seu portal de **Azure Stack** e abra a página **máquinas virtuais** .
1. Selecione a máquina virtual que você deseja proteger com a central de segurança.
    >[!TIP]
    > Para saber mais sobre como criar uma máquina virtual no Azure Stack, confira [este início rápido para Máquinas Virtuais do Windows](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) ou [este início rápido para Máquinas Virtuais do Linux](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
1. Selecione **Extensões**. A lista de extensões da máquina virtual instaladas nesta máquina virtual é mostrada.
1. Selecione a guia **Adicionar** . O menu **novo recurso** mostra a lista de extensões de máquina virtual disponíveis.
1. Selecione a **Azure monitor, atualização e** extensão de gerenciamento de configuração e selecione **criar**. A página instalar configuração de **extensão** é aberta.
    >[!NOTE]
    > Caso você não veja a extensão **Gerenciamento de Configuração, de Atualização e do Azure Monitor** listada no marketplace, contate seu operador do Azure Stack para disponibilizá-lo.
1. Na página **instalar** configuração de extensão, Cole a **ID do espaço de trabalho** e a **chave do espaço de trabalho (chave primária)** que você copiou no bloco de notas na etapa anterior.
1. Quando você concluir a configuração, selecione **OK**. O status da extensão será exibido conforme o **provisionamento for bem-sucedido**. Pode levar até uma hora para que a máquina virtual apareça na central de segurança.


### <a name="onboard-your-linux-machines"></a>Integre seus computadores Linux
Para adicionar computadores Linux, você precisa do comando WGET na página de **Gerenciamento de agentes** .
1. Na página de **Gerenciamento de agentes** , copie o comando **wget** no bloco de notas. Salve esse arquivo em um local que possa ser acessado pelo computador Linux.
1. No computador Linux, abra o arquivo com o comando WGET. Selecione todo o conteúdo e copie e cole-o em um console de terminal.
1. Quando a instalação for concluída, você poderá validar se o *omsagent* está instalado executando o comando *pgrep* . O comando retornará o PID *omsagent* .
    Os logs do agente podem ser encontrados em: */var/opt/Microsoft/omsagent/ \<workspace id> /log/* pode levar até 30 minutos para que o novo computador Linux apareça na central de segurança.


### <a name="onboard-your-windows-machines"></a>Integre suas máquinas Windows
Para adicionar computadores Windows, você precisará das informações na página de **Gerenciamento de agentes** e baixar o arquivo de agente apropriado (32/64 bits).
1. Selecione o link **Baixar Agente do Windows** aplicável a seu tipo de processador do computador para baixar o arquivo de configuração.
1. Na página **Gerenciamento de agentes** , copie a **ID do espaço de trabalho** e a **chave primária** no bloco de notas.
1. Copie o arquivo de instalação baixado para o computador de destino e execute-o.
1. Siga o assistente de instalação (**em seguida**, **concordo**, avançar **).** **Next**
    1. Na página **log Analytics do Azure** , Cole a **ID do espaço de trabalho** e a **chave do espaço de trabalho (chave primária)** que você copiou no bloco de notas.
    1. Se o computador deve se reportar a um espaço de trabalho Log Analytics na nuvem do Azure governamental, selecione **Azure EUA governamental** na lista suspensa de **nuvem do Azure** .
    1. Caso o computador precise se comunicar por meio de um servidor proxy ao serviço Log Analytics, selecione **Avançado** e forneça a URL e o número da porta do servidor proxy.
    1. Quando você tiver inserido todas as definições de configuração, selecione **Avançar**.
    1. Na página **pronto para instalar** , examine as configurações a serem aplicadas e selecione **instalar**.
    1. Na página **Configuração concluída com êxito**, selecione **Concluir**.

Após a conclusão, o **agente do Log Analytics** será exibido no **Painel de Controle**. Você pode revisar sua configuração e verificar se o agente está conectado.

Para obter mais informações sobre como instalar e configurar o agente, consulte [conectar computadores Windows](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard).


## <a name="verifying"></a>Verificando
Parabéns! Agora você pode ver seus computadores Azure e não Azure juntos em um só lugar. Abra a [página de inventário de ativos](asset-inventory.md) e filtre os tipos de recursos relevantes. Esses dois ícones distinguem os tipos:

  ![icon1](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) Computador não Azure

  ![icon2](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) VM do Azure


## <a name="next-steps"></a>Próximas etapas

Esta página mostrou como adicionar seus computadores não Azure à central de segurança do Azure. Para monitorar seu status, use as ferramentas de inventário, conforme explicado na página a seguir:

- [Explore e gerencie seus recursos com o inventário de ativos e as ferramentas de gerenciamento](asset-inventory.md)