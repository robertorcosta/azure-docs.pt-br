---
title: Use o Windows PowerShell para conectar e gerenciar o dispositivo Gateway da caixa de dados Azure
description: Descreve como se conectar e, em seguida, gerenciar o Data Box Gateway através da interface Do Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/02/2019
ms.author: alkohli
ms.openlocfilehash: 6c9f3455a07001a8d1b9b8a1e84f2af3392b5690
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260209"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Gerenciar um dispositivo Gateway da caixa de dados do Azure via Windows PowerShell

A solução Azure Data Box Gateway permite enviar dados pela rede para o Azure. Este artigo descreve algumas das tarefas de configuração e gerenciamento do dispositivo Data Box Gateway. Você pode usar o portal Azure, a interface web local ou a interface Windows PowerShell para gerenciar seu dispositivo.

Este artigo se concentra nas tarefas que você faz usando a interface PowerShell.

Este artigo inclui os seguintes procedimentos:

- Conecte-se à interface PowerShell
- Criar um pacote de suporte
- Carregar um certificado
- Inicializar em ambiente não-DHCP
- Exibir informações do dispositivo

## <a name="connect-to-the-powershell-interface"></a>Conecte-se à interface PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Criar um pacote de suporte

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Carregar um certificado

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>Inicializar em ambiente não-DHCP

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>Exibir informações do dispositivo

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>Próximas etapas

- Implante o [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) no portal do Azure.
