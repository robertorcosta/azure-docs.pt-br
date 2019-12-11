---
title: Conectar-se à área de trabalho virtual do Windows Windows 10 ou 7-Azure
description: Como se conectar à área de trabalho virtual do Windows usando o cliente de área de trabalho do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: helohr
ms.openlocfilehash: 745b33efe46c82e3b9358c9c5a2ed13292242db1
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997336"
---
# <a name="connect-with-the-windows-desktop-client"></a>Conectar-se ao Cliente de Área de Trabalho do Windows

> Aplica-se a: Windows 7, Windows 10 e Windows 10 IoT Enterprise

Você pode acessar os recursos da área de trabalho virtual do Windows em dispositivos com Windows 7, Windows 10 e Windows 10 IoT Enterprise usando o cliente de área de trabalho do Windows.

> [!IMPORTANT]
> A área de trabalho virtual do Windows não dá suporte ao cliente RADC (RemoteApp e área de trabalho) ou ao cliente do Conexão de Área de Trabalho Remota (MSTSC).

## <a name="install-the-windows-desktop-client"></a>Instalar o cliente de área de trabalho do Windows

Escolha o cliente que corresponda à sua versão do Windows:

- [Windows 64 bits](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32 bits](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

É possível instalar o cliente do usuário atual, que não requer direitos de administrador, ou seu administrador pode instalar e configurar o cliente para que todos os usuários no dispositivo possam acessá-lo.

Depois de instalado, o cliente pode ser iniciado no menu Iniciar pesquisando **Área de Trabalho Remota**.

## <a name="subscribe-to-a-feed"></a>Assinar um feed

Obtenha a lista de recursos gerenciados disponíveis para você assinando o feed fornecido pelo administrador. A assinatura disponibiliza os recursos no seu PC local.

Para assinar um feed:

1. Abra o cliente de área de trabalho do Windows.
2. Selecione **assinar** na página principal para se conectar ao serviço e recuperar seus recursos.
3. Entre com sua conta quando solicitado.

Depois de entrar com êxito, você verá uma lista dos recursos que você pode acessar.

Você pode iniciar recursos por um dos dois métodos.

- Na página principal do cliente, clique duas vezes em um recurso para iniciá-lo.
- Inicie um recurso como faria normalmente com outros aplicativos no menu iniciar.
  - Você também pode pesquisar os aplicativos na barra de pesquisa.

Após a assinatura de um feed, o conteúdo do feed é atualizado automaticamente regularmente. Os recursos podem ser adicionados, alterados ou removidos com base nas alterações feitas pelo administrador.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre como usar o cliente de área de trabalho do Windows, confira [introdução ao cliente de desktop do Windows](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/windowsdesktop).
