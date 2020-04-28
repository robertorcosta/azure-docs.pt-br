---
title: incluir arquivo
description: incluir arquivo
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 11/06/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: d32beb2d799a60cb9c5be061c39e4ec834da8dcf
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2020
ms.locfileid: "66814874"
---
Esse recurso está em visualização. Para usá-lo, você deve instalar uma extensão ou módulo de visualização.

### <a name="install-extension-for-azure-cli"></a>Instalar extensão para CLI do Azure

Para Azure CLI, é necessária a [extensão da Grade de Eventos](/cli/azure/azure-cli-extensions-list).

No [CloudShell](/azure/cloud-shell/quickstart):

* Se você instalou a extensão anteriormente, atualize-a `az extension update -n eventgrid`
* Se você não instalou a extensão anteriormente, instale-a `az extension add -n eventgrid`

Para uma instalação local:

1. [Instale o CLI do Azure](/cli/azure/install-azure-cli). Verifique se você tem a versão mais recente, verificando com `az --version`.
1. Desinstalar as versões anteriores da extensão `az extension remove -n eventgrid`
1. Instalar a `eventgrid` extensão com`az extension add -n eventgrid`

### <a name="install-module-for-powershell"></a>Instale o módulo para PowerShell

Para PowerShell, é necessário o [módulo AzureRM.EventGrid](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview).

No [CloudShell](/azure/cloud-shell/quickstart-powershell):

* Instale o módulo `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Para uma instalação local:

1. Abra o console do PowerShell como administrador
1. Instale o módulo `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Se o parâmetro `-AllowPrerelease` não estiver disponível, use as seguintes etapas:

1. Execute `Install-Module PowerShellGet -Force`
1. Execute `Update-Module PowerShellGet`
1. Feche o console do PowerShell
1. Reinicie o PowerShell como administrador
1. Instale o módulo `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`
