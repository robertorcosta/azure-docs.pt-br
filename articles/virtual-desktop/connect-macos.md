---
title: Conectar-se à área de trabalho virtual do Windows do macOS – Azure
description: Como se conectar à área de trabalho virtual do Windows usando o cliente macOS.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 80d75357baeb3f8afd910e22abca8b11af2d80b3
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87285147"
---
# <a name="connect-to-windows-virtual-desktop-with-the-macos-client"></a>Conectar-se à área de trabalho virtual do Windows com o cliente macOS

> Aplica-se a: macOS 10,12 ou posterior

>[!IMPORTANT]
>Esse conteúdo se aplica à área de trabalho virtual do Windows com Azure Resource Manager objetos da área de trabalho virtual do Windows. Se você estiver usando a área de trabalho virtual do Windows (clássico) sem Azure Resource Manager objetos, consulte [Este artigo](./virtual-desktop-fall-2019/connect-macos-2019.md).

Você pode acessar os recursos da área de trabalho virtual do Windows de seus dispositivos macOS com nosso cliente baixável. Este guia lhe dirá como configurar o cliente do.

## <a name="install-the-client"></a>Instalar o cliente

Para começar, [Baixe](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12)   e instale o cliente em seu dispositivo MacOS.

## <a name="subscribe-to-a-feed"></a>Assinar um feed

Assine o feed que o administrador lhe deu para obter a lista de recursos gerenciados disponíveis para você em seu dispositivo macOS.

Para assinar um feed:

1. Selecione **adicionar espaço de trabalho** na página principal para se conectar ao serviço e recuperar seus recursos.
2. Insira a URL do feed. Ela pode ser uma URL ou endereço de email:
   - Se você usar uma URL, use a que o administrador lhe forneceu. Normalmente, a URL é <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery>.
   - Para usar email, insira seu endereço de email. Isso informa ao cliente para pesquisar uma URL associada ao seu endereço de email, caso o administrador tenha configurado o servidor dessa maneira.
3. Selecione **Adicionar**.
4. Entre com sua conta quando solicitado.

Depois de entrar, você deverá ver uma lista de recursos disponíveis.

Após a assinatura de um feed, o conteúdo dele será atualizado automaticamente de maneira regular. Os recursos podem ser adicionados, alterados ou removidos com base nas alterações realizadas pelo seu administrador.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o cliente macOS, confira a documentação [introdução ao cliente MacOS](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/) .
