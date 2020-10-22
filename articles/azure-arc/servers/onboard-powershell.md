---
title: Conectar máquinas híbridas ao Azure usando o PowerShell
description: Neste artigo, você aprende a instalar o agente e a conectar um computador ao Azure usando os servidores habilitados para Arc do Azure usando o PowerShell.
ms.date: 10/21/2020
ms.topic: conceptual
ms.openlocfilehash: d36fd174606b49b28b1d8343bff6ccc1f62e5194
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92374249"
---
# <a name="connect-hybrid-machines-to-azure-using-powershell"></a>Conectar máquinas híbridas ao Azure usando o PowerShell

Você pode habilitar os servidores habilitados para Arc do Azure para um ou um pequeno número de computadores Windows ou Linux em seu ambiente executando um conjunto de etapas manualmente. Ou você pode usar o cmdlet do PowerShell [Connect-AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine). Esse cmdlet executa as seguintes ações:

- Configura o computador host para baixar o agente do Windows do centro de download da Microsoft e o pacote de agente do Linux de packages.microsoft.com.
- Instala o agente do computador conectado.
- Registra o computador com o arco do Azure

Esse método exige que você tenha permissões de administrador no computador para instalar e configurar o agente. No Linux, usando a conta raiz, e no Windows, você é membro do grupo local de administradores.

Antes de começar, examine os [pré-requisitos](agent-overview.md#prerequisites) e verifique se a sua assinatura e os recursos atendem aos requisitos. Para obter informações sobre regiões com suporte e outras considerações relacionadas, consulte [regiões do Azure com suporte](overview.md#supported-regions).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Um computador com o PowerShell do Azure. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure](/powershell/azure/).

Antes de usar Azure PowerShell para gerenciar extensões de VM em seu servidor híbrido gerenciado por servidores habilitados para Arc, você precisa instalar o `Az.ConnectedMachine` módulo. Execute o seguinte comando em seu servidor habilitado para Arc:

```powershell
Install-Module -Name Az.ConnectedMachine
```

Quando a instalação for concluída, a seguinte mensagem será retornada:

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-and-validate-the-agent-on-windows"></a>Instalar e validar o agente no Windows

1. Abra um console do PowerShell como administrador.

2. Entre no Azure executando o comando `Connect-AzAccount` .

3. Para instalar o agente do computador conectado, use `Connect-AzConnectedMachine` com `-Name` os `-ResourceGroupName` parâmetros, `-Location` e. Use o `-SubscriptionId` parâmetro para substituir a assinatura padrão como resultado do contexto do Azure criado após a entrada.

    Para instalar o agente do computador conectado no computador de destino que pode se comunicar diretamente com o Azure, execute o seguinte comando::

    ```azurepowershell
    Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e
    ```
    
    Se o computador de destino se comunicar por meio de um servidor proxy, execute o seguinte comando:
    
    ```azurepowershell
    Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e -proxy http://<proxyURL>:<proxyport>
    ```

    Se o agente não for iniciado após a conclusão da instalação, verifique os logs para obter informações de erro detalhadas. No Windows em *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log*e no Linux em */var/opt/azcmagent/log/himds.log*.

## <a name="verify-the-connection-with-azure-arc"></a>Verificar a conexão com o Azure Arc

Depois de instalar o agente e configurá-lo para se conectar aos servidores habilitados para Azure Arc, acesse o portal do Azure para verificar se o servidor foi conectado com êxito. Veja seus computadores no [portal do Azure](https://portal.azure.com).

![Uma conexão de servidor bem-sucedida](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Próximas etapas

* Informações de solução de problemas podem ser encontradas no [guia solucionar problemas do agente do computador conectado](troubleshoot-agent-onboard.md).

* Saiba como gerenciar seu computador usando o [Azure Policy](../../governance/policy/overview.md) para itens como [configurar convidados](../../governance/policy/concepts/guest-configuration.md) de VM, verificar se o computador está relatando ao workspace do Log Analytics esperado, habilitar o monitoramento com o [Azure Monitor em VMs](../../azure-monitor/insights/vminsights-enable-policy.md) e muito mais.

* Saiba mais sobre o [Agente do Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). O agente Log Analytics para Windows e Linux é necessário quando você deseja coletar dados de monitoramento do sistema operacional e da carga de trabalho, gerenciá-los usando runbooks de automação ou recursos como Gerenciamento de Atualizações ou usar outros serviços do Azure, como a [central de segurança do Azure](../../security-center/security-center-introduction.md).