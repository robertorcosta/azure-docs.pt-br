---
title: Como atualizar o Monitor Do Azure para o agente de dependência de VMs
description: Este artigo descreve como atualizar o monitor azure para agente de dependência de VMs usando linha de comando, assistente de configuração e outros métodos.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: c55bee9880c4134f2e304a7fc5176225477fe5f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480752"
---
# <a name="how-to-upgrade-the-azure-monitor-for-vms-dependency-agent"></a>Como atualizar o Monitor Do Azure para o agente de dependência de VMs

Após a implantação inicial do azure Monitor para o agente Dependency de VMs, são lançadas atualizações que incluem correções de bugs ou suporte a novos recursos ou funcionalidades.  Este artigo ajuda você a entender os métodos disponíveis e como realizar a atualização manualmente ou através da automação.

## <a name="upgrade-options"></a>Opções de upgrade 

O agente Dependency para Windows e Linux pode ser atualizado para a versão mais recente manualmente ou automaticamente, dependendo do cenário de implantação e ambiente em que a máquina está sendo executado. Os seguintes métodos podem ser usados para atualizar o agente.

|Ambiente |Método de instalação |Método de atualização |
|------------|--------------------|---------------|
|VM do Azure | Extensão vm do agente de dependência para [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) e [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) | O agente é automaticamente atualizado por padrão, a menos que você configure o modelo do Azure Resource Manager para desativar definindo a propriedade *autoUpgradeMinorVersion* como **falsa**. A atualização para a versão menor onde a atualização automática é desativada, e uma atualização de versão principal seguem o mesmo método - desinstalar e reinstalar a extensão. |
| Imagens personalizadas do Azure VM | Instalação manual do agente de dependência para Windows/Linux | A atualização das VMs para a versão mais recente do agente precisa ser realizada a partir da linha de comando executando o pacote instalador do Windows ou o pacote de script shell auto-extraindo e instalado do Linux.|
| VMs não-azure | Instalação manual do agente de dependência para Windows/Linux | A atualização das VMs para a versão mais recente do agente precisa ser realizada a partir da linha de comando executando o pacote instalador do Windows ou o pacote de script shell auto-extraindo e instalado do Linux. |

## <a name="upgrade-windows-agent"></a>Atualizar agente do Windows 

Para atualizar o agente em uma VM do Windows para a versão mais recente não instalada usando a extensão VM do agente dependency, você é executado a partir do Prompt de comando, script ou outra solução de automação, ou usando o Assistente de Configuração InstallDependencyAgent-Windows.exe.  

Você pode baixar a versão mais recente do agente windows [daqui](https://aka.ms/dependencyagentwindows).

### <a name="using-the-setup-wizard"></a>Usando o assistente de configuração

1. Faça logon no computador com uma conta que tenha direitos administrativos.

2. Execute **InstallDependencyAgent-Windows.exe** para iniciar o Assistente de configuração.
   
3. Siga o assistente **de configuração do agente de dependência** para desinstalar a versão anterior do agente de dependência e, em seguida, instale a versão mais recente.


### <a name="from-the-command-line"></a>Da linha de comando

1. Faça logon no computador com uma conta que tenha direitos administrativos.

2. Execute o comando a seguir.

    ```dos
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    O `/RebootMode=manual` parâmetro impede que a atualização reinicie automaticamente a máquina se alguns processos estiverem usando arquivos da versão anterior e tiver um bloqueio sobre eles. 

3. Para confirmar se a atualização `install.log` foi bem sucedida, verifique se há informações detalhadas de configuração. O diretório de log é *%Programfiles%\Microsoft Dependency Agent\logs*.

## <a name="upgrade-linux-agent"></a>Atualizar agente Linux 

A atualização das versões anteriores do agente Dependency no Linux é suportada e realizada seguindo o mesmo comando de uma nova instalação.

Você pode baixar a versão mais recente do agente windows [daqui](https://aka.ms/dependencyagentlinux).

1. Faça logon no computador com uma conta que tenha direitos administrativos.

2. Execute o seguinte`sh InstallDependencyAgent-Linux64.bin -s`comando como raiz . 

Se o Agente de Dependência não for iniciado, verifique os logs para obter informações de erro detalhadas. Nos agentes Linux, o diretório de log é */var/opt/microsoft/dependency-agent/log*. 

## <a name="next-steps"></a>Próximas etapas

Se você quiser parar de monitorar suas VMs por um período de tempo ou remover o Monitor Azure para VMs inteiramente, consulte [Desativar o monitoramento de suas VMs no Monitor Azure para VMs](vminsights-optout.md).
