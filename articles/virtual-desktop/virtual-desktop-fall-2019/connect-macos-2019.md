---
title: Conectar-se à área de trabalho virtual do Windows do macOS – Azure
description: Como se conectar à área de trabalho virtual do Windows usando o cliente macOS.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 79eaefb5cd8181bd471618761255bf38f464d735
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614909"
---
# <a name="connect-with-the-macos-client"></a>Conectar-se ao cliente macOS

> Aplica-se a: macOS 10,12 ou posterior

>[!IMPORTANT]
>Esse conteúdo se aplica à versão 2019 do outono que não dá suporte a Azure Resource Manager objetos da área de trabalho virtual do Windows. Se você estiver tentando gerenciar Azure Resource Manager objetos da área de trabalho virtual do Windows introduzidos na atualização do Spring 2020, consulte [Este artigo](../connect-macos.md).

Você pode acessar os recursos da área de trabalho virtual do Windows de seus dispositivos macOS com nosso cliente baixável. Este guia lhe dirá como configurar o cliente do.

## <a name="install-the-client"></a>Instalar o cliente

Para começar, [Baixe](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) e instale o cliente em seu dispositivo MacOS.

## <a name="subscribe-to-a-feed"></a>Assinar um feed

Assine o feed o administrador lhe deu para obter a lista de recursos gerenciados disponíveis para você em seu dispositivo macOS.

Para assinar um feed:

1. Selecione **adicionar espaço de trabalho** na página principal para se conectar ao serviço e recuperar seus recursos.
2. Insira a URL do Feed. Ela pode ser uma URL ou endereço de email:
   - Se você usar uma URL, use aquela que o administrador lhe forneceu. Normalmente, a URL é <https://rdweb.wvd.microsoft.com>.
   - Para usar o email, insira seu endereço de email. Isso instrui o cliente a procurar uma URL associada ao seu endereço de email se o administrador configurou o servidor dessa maneira.
3. Selecione **Adicionar**.
4. Entre com sua conta quando solicitado.

Depois de entrar, você deverá ver uma lista de recursos disponíveis.

Depois que você se inscreveu em um feed, o conteúdo do feed será atualizado automaticamente regularmente. Os recursos podem ser adicionados, alterados ou removidos com base nas alterações feitas pelo administrador.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o cliente macOS, confira a documentação [introdução ao cliente MacOS](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/) .