---
title: Conectar-se à área de trabalho virtual do Windows Windows 10 ou 7-Azure
description: Como se conectar à área de trabalho virtual do Windows usando o cliente de área de trabalho do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5d333b2f8bbb660e9fd71339159d942c0520f4f4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85201541"
---
# <a name="connect-with-the-windows-desktop-client"></a>Conectar-se ao Cliente de Área de Trabalho do Windows

> Aplica-se a: Windows 7, Windows 10 e Windows 10 IoT Enterprise

Você pode acessar os recursos da área de trabalho virtual do Windows em dispositivos com Windows 7, Windows 10 e Windows 10 IoT Enterprise usando o cliente de área de trabalho do Windows.

>[!NOTE]
>O cliente do Windows padroniza automaticamente para a versão de área de trabalho virtual do Windows no outono 2019. No entanto, se o cliente detectar que o usuário também tem recursos de Azure Resource Manager, ele adicionará automaticamente os recursos ou notificará o usuário de que eles estão disponíveis.

> [!IMPORTANT]
> A Área de Trabalho Virtual do Windows não dá suporte ao cliente de RADC (Conexões de RemoteApp e Área de Trabalho) nem ao cliente de Conexão de Área de Trabalho Remota (MSTSC).

> [!IMPORTANT]
> No momento, a Área de Trabalho Virtual do Windows não dá suporte ao cliente da Área de Trabalho Remota da Microsoft Store. O suporte para esse cliente será adicionado em uma versão futura.

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

Após a assinatura de um feed, o conteúdo do feed é atualizado automaticamente regularmente. Os recursos podem ser adicionados, alterados ou removidos com base nas alterações realizadas pelo seu administrador.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar o cliente de área de trabalho do Windows, confira [introdução ao cliente de desktop do Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/).
