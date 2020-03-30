---
title: Conecte-se ao Windows Virtual Desktop do iOS - Azure
description: Como se conectar ao Windows Virtual Desktop usando o cliente iOS.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3bf9ea6c68e4cbbe721705639e6c6416c0e93c9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128262"
---
# <a name="connect-with-the-ios-client"></a>Conectar-se ao cliente iOS

> Aplica-se a: iOS 13.0 ou posterior. Compatível com iPhone, iPad e iPod touch.

Você pode acessar os recursos do Windows Virtual Desktop a partir do seu dispositivo iOS com o nosso cliente para download. Este guia lhe dirá como configurar o cliente iOS.

## <a name="install-the-ios-client"></a>Instale o cliente iOS

Para começar, [baixe](https://aka.ms/rdios) e instale o cliente em seu dispositivo iOS.

## <a name="subscribe-to-a-feed"></a>Assinar um feed

Inscreva-se no feed fornecido pelo seu admin para obter a lista de recursos gerenciados que você pode acessar em seu dispositivo iOS.

Para assinar um feed:

1. No Centro de **+** Conexões, toque e toque em **Adicionar espaço de trabalho**.
2. Insira a URL de alimentação no campo **feed URL.** A URL do feed pode ser uma URL ou um endereço de e-mail.
   - Se você usar uma URL, use a que seu admin lhe deu. Normalmente, a <https://rdweb.wvd.microsoft.com>URL é .
   - Para usar o e-mail, insira seu endereço de e-mail. Isso diz ao cliente para procurar uma URL associada ao seu endereço de e-mail se o seu admin configurou o servidor dessa forma.
3. Toque **em Next**.
4. Forneça suas credenciais quando solicitado.
   - Para **nome de usuário,** dê ao nome de usuário permissão para acessar recursos.
   - Para **Senha,** forneça a senha associada ao nome de usuário.
   - Você também pode ser solicitado a fornecer fatores adicionais se o seu admin configurou a autenticação dessa forma.
5. Toque em **Salvar**.

Depois disso, o Centro de Conexão deve exibir os recursos remotos.

Uma vez subscrito em um feed, o conteúdo do feed será atualizado automaticamente regularmente. Os recursos podem ser adicionados, alterados ou removidos com base nas alterações feitas pelo administrador.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar o cliente iOS, confira o Get started com a documentação [do cliente do iOS.](/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios/)
