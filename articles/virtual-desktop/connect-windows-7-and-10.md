---
title: Conecte-se ao Windows Virtual Desktop Windows 10 ou 7 - Azure
description: Como se conectar ao Windows Virtual Desktop usando o cliente Windows Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b6436059cc8b3637edfc2a146d0aab3e2beae6a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154331"
---
# <a name="connect-with-the-windows-desktop-client"></a>Conectar-se ao Cliente de Área de Trabalho do Windows

> Aplica-se a: Windows 7, Windows 10 e Windows 10 IoT Enterprise

Você pode acessar os recursos do Windows Virtual Desktop em dispositivos com Windows 7, Windows 10 e Windows 10 IoT Enterprise usando o cliente Windows Desktop.

> [!IMPORTANT]
> O Windows Virtual Desktop não suporta o cliente RemoteApp and Desktop Connections (RADC) ou o cliente MSTSC (Remote Desktop Connection, conexão de desktop remota).

> [!IMPORTANT]
> O Windows Virtual Desktop não suporta atualmente o cliente de área de trabalho remota da Windows Store. O suporte para este cliente será adicionado em um lançamento futuro.

## <a name="install-the-windows-desktop-client"></a>Instale o cliente do Windows Desktop

Escolha o cliente que corresponda à sua versão do Windows:

- [Windows 64 bits](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32 bits](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

É possível instalar o cliente do usuário atual, que não requer direitos de administrador, ou seu administrador pode instalar e configurar o cliente para que todos os usuários no dispositivo possam acessá-lo.

Depois de instalado, o cliente pode ser iniciado no menu Iniciar pesquisando **Área de Trabalho Remota**.

## <a name="subscribe-to-a-feed"></a>Assinar um feed

Obtenha a lista de recursos gerenciados disponíveis para você, assinando o feed fornecido pelo seu admin. A inscrição disponibiliza os recursos no seu PC local.

Para assinar um feed:

1. Abra o cliente do Windows Desktop.
2. Selecione **Inscreva-se** na página principal para se conectar ao serviço e recuperar seus recursos.
3. Entre com sua conta quando solicitado.

Depois de fazer login com sucesso, você deve ver uma lista dos recursos que você pode acessar.

Você pode lançar recursos por um dos dois métodos.

- Na página principal do cliente, clique duas vezes em um recurso para lançá-lo.
- Inicie um recurso como você normalmente faria com outros aplicativos do Menu Iniciar.
  - Você também pode procurar os aplicativos na barra de pesquisa.

Uma vez subscrito em um feed, o conteúdo do feed é atualizado automaticamente regularmente. Os recursos podem ser adicionados, alterados ou removidos com base nas alterações feitas pelo administrador.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar o cliente windows desktop, confira [Comece com o cliente Windows Desktop](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/).
