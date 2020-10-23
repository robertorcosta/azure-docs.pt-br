---
title: Habilitar extensão de VM do portal do Azure
description: Este artigo descreve como implantar extensões de máquina virtual para servidores habilitados para Arc do Azure em execução em ambientes de nuvem híbrida da portal do Azure.
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: dcab7cb441c329a60b2c6fa3256aeedb2bb5b33d
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92462856"
---
# <a name="enable-azure-vm-extensions-from-the-azure-portal"></a>Habilitar extensões de VM do Azure no portal do Azure

Este artigo mostra como implantar e desinstalar extensões de VM do Azure, com suporte dos servidores habilitados para Arc do Azure, para uma máquina híbrida Linux ou Windows por meio do portal do Azure.

## <a name="enable-extensions-from-the-portal"></a>Habilitar extensões do portal

As extensões de VM podem ser aplicadas ao seu arco para o computador gerenciado pelo servidor por meio do portal do Azure.

1. No navegador, acesse o [portal do Azure](https://portal.azure.com).

2. No portal, navegue até **servidores – Azure Arc** e selecione seu computador híbrido na lista.

3. Escolha **extensões**e, em seguida, selecione **Adicionar**. Escolha a extensão desejada na lista de extensões disponíveis e siga as instruções no assistente. Neste exemplo, iremos implantar a extensão de VM Log Analytics.

    ![Selecionar a extensão de VM para o computador selecionado](./media/manage-vm-extensions/add-vm-extensions.png)

    O exemplo a seguir mostra a instalação do Log Analytics extensão de VM do portal do Azure:

    ![Instalar Log Analytics extensão de VM](./media/manage-vm-extensions/mma-extension-config.png)

    Para concluir a instalação, é necessário fornecer a ID do espaço de trabalho e a chave primária. Se você não estiver familiarizado com a localização dessas informações, consulte [obter ID e chave do espaço de trabalho](../../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

4. Depois de confirmar as informações necessárias fornecidas, selecione **criar**. Um resumo da implantação é exibido e você pode revisar o status da implantação.

>[!NOTE]
>Embora várias extensões possam ser agrupadas e processadas em lote, elas são instaladas em série. Depois que a primeira instalação da extensão for concluída, a instalação da próxima extensão será tentada.

## <a name="uninstall-extension"></a>Extensão de desinstalação

Você pode remover uma ou mais extensões de um servidor habilitado para Arc da portal do Azure. Execute as etapas a seguir para remover uma extensão.

1. No navegador, acesse o [portal do Azure](https://portal.azure.com).

2. No portal, navegue até **servidores – Azure Arc** e selecione seu computador híbrido na lista.

3. Escolha **extensões**e, em seguida, selecione uma extensão na lista de extensões instaladas.

4. Selecione **desinstalar** e, quando for solicitado a verificar, selecione **Sim** para continuar.

## <a name="next-steps"></a>Próximas etapas

- Você pode implantar, gerenciar e remover extensões de VM usando os modelos [CLI do Azure](manage-vm-extensions-cli.md), [PowerShell](manage-vm-extensions-powershell.md)ou [Azure Resource Manager](manage-vm-extensions-template.md).

- As informações de solução de problemas podem ser encontradas no [guia solucionar problemas de extensões de VM](troubleshoot-vm-extensions.md).