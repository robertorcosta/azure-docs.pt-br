---
layout: LandingPage
description: Saiba como criar a solução de problemas das implantações de máquina virtual.
title: Documentação para solucionar problemas das máquinas virtuais do Azure | Microsoft Docs
services: virtual-machines
author: genlin
manager: dcscontentpm
ms.assetid: ''
ms.service: virtual-machines
ms.tgt_pltfrm: na
ms.topic: landing-page
ms.date: 10/3/2018
ms.author: genli
ms.openlocfilehash: bdb459a69557269a20d36f05acc2da502064efb9
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851342"
---
# <a name="troubleshooting-azure-virtual-machines"></a>Solução de problemas de máquinas virtuais do Azure

## <a name="tools-for-troubleshooting"></a>Ferramentas para solução de problemas

- [Console serial](serial-console-overview.md)
- [Diagnóstico de inicialização](boot-diagnostics.md)
- [VM do Windows: Anexe o disco do SO a outra VM para solucionar problemas](troubleshoot-recovery-disks-portal-windows.md)
- [VM do Linux: Anexe o disco do SO a outra VM para solucionar problemas](troubleshoot-recovery-disks-portal-linux.md)

## <a name="cant-connect-to-the-vm"></a>Não é possível conectar à VM

### <a name="windows"></a>Windows

**Solução comum**

- [Redefinir RDP](reset-rdp.md)
- [Solução de problemas do RDP](troubleshoot-rdp-connection.md)
- [Solução de problemas detalhada do RDP](detailed-troubleshoot-rdp.md)

**Erros do RDP**

- [Nenhum servidor de licença](troubleshoot-rdp-no-license-server.md)
- [Um ](Troubleshoot-rdp-internal-error.md) interno
- [erros de autenticação](troubleshoot-authentication-error-rdp-vm.md)
- [Solucionar erros específicos](troubleshoot-specific-rdp-errors.md)

**Erros de inicialização da VM**

* [Erros de inicialização do BitLocker](troubleshoot-bitlocker-boot-error.md)
* [O Windows mostra "Verificando sistema de arquivos" durante a inicialização](troubleshoot-check-disk-boot-error.md)
* [Erros de tela azul](troubleshoot-common-blue-screen-error.md)
* [A inicialização da VM está parada em "Preparando o Windows"](troubleshoot-vm-boot-configure-update.md)
* [Erro "FALHA DE SERVIÇO CRÍTICO" na tela azul](troubleshoot-critical-service-failed-boot-error.md)
* [Problema de loop de reinicialização](troubleshoot-reboot-loop.md)
* [A inicialização da VM está parada no estágio do Windows Update](troubleshoot-stuck-updating-boot-error.md)
* [VM é inicializada no Modo de Segurança](troubleshoot-rdp-safe-mode.md)

**Outros**
- [Redefinir senha da VM para a VM Windows offline](reset-local-password-without-agent.md)
- [Redefinir NIC após configuração incorreta](reset-network-interface.md)

### <a name="linux"></a>Linux

- [Solução de problemas de SSH](troubleshoot-ssh-connection.md)
- [Solução de problemas detalhada de SSH](detailed-troubleshoot-ssh-connection.md)
- [Mensagens de erro comuns](error-messages.md)
- [Redefinir senha da VM para a VM Linux offline](reset-password.md)

## <a name="vm-deployment-issues"></a>Problemas de implantação da VM

- [Falhas na alocação](allocation-failure.md)
- Reimplantar uma VM
    - [Linux](redeploy-to-new-node-linux.md)
    - [Windows](redeploy-to-new-node-windows.md)
- Solucionar problemas das implantações
    - [Linux](troubleshoot-deploy-vm-linux.md)
    - [Windows](troubleshoot-deploy-vm-windows.md)
- [Os nomes do dispositivo foram alterados](troubleshoot-device-names-problems.md)
- [Instalar o agente de VM do Windows offline](install-vm-agent-offline.md)
- [Reinicializar ou redimensionar uma VM](restart-resize-error-troubleshooting.md)

## <a name="vm-performance-issue"></a>Problema de desempenho da VM
- [Problemas de desempenho com as VMs](performance-diagnostics.md)
- Windows
    - [Como usar o PerfInsights](how-to-use-perfinsights.md)
    - [Extensão Performance Diagnostics](performance-diagnostics-vm-extension.md)
- Linux
    - [Como usar o PerfInsights](how-to-use-perfinsights-linux.md)