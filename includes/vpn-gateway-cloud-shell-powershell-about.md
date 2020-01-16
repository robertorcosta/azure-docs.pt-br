---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 684b212ca771af6c336cf6239e18ea367f2da5ce
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045103"
---
Este artigo usa cmdlets do PowerShell. Para executar os cmdlets, é possível usar o Azure Cloud Shell, um ambiente de shell interativo hospedado no Azure e usado por meio do navegador. O Azure Cloud Shell vem com cmdlets do Azure PowerShell pré-instalados.

Para executar qualquer código contido neste artigo no Azure Cloud Shell, abra uma sessão do Cloud Shell, use o botão **Copiar** em um bloco de códigos para copiar o código e colá-lo na sessão do Cloud Shell com __Ctrl+Shift+V__ no Windows e no Linux ou __Cmd+Shift+V__ no macOS. O texto colado não é executado automaticamente; portanto, pressione **Enter** para executar o código.

É possível iniciar o Azure Cloud Shell com:

|  |   |
|-----------------------------------------------|---|
| Selecione **Experimente** no canto superior direito de um bloco de código. Isso __não__ copia automaticamente o texto para o Cloud Shell. | ![Exemplo de “Experimente” no Azure Cloud Shell](./media/cloud-shell-try-it/hdi-azure-cli-try-it.png) |
| Abra [shell.azure.com](https://shell.azure.com) no seu navegador. | [![Botão Iniciar o Azure Cloud Shell](./media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com) |
| Selecione o botão **Cloud Shell** no menu no canto superior direito do [Portal do Azure](https://portal.azure.com). | ![Botão Cloud Shell no portal do Azure](./media/cloud-shell-try-it/hdi-cloud-shell-menu.png) |

**Executando o PowerShell localmente**

Você também pode instalar e executar os cmdlets Azure PowerShell localmente no seu computador. Os cmdlets do PowerShell são atualizados com frequência. Se você não estiver executando a versão mais recente, os valores especificados nas instruções poderão falhar. Para localizar as versões do Azure PowerShell instaladas no seu computador, use o cmdlet `Get-Module -ListAvailable Az`. Para instalar ou atualizar o, consulte [instalar o Azure PowerShell Module](/powershell/azure/install-az-ps).

Se você estiver executando o PowerShell localmente, certifique-se de executar ' Connect-AzAccount ' para criar sua conexão com o Azure.