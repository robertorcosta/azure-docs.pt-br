---
title: Conecte-se ao Windows Virtual Desktop do Android - Azure
description: Como se conectar ao Windows Virtual Desktop usando o cliente Android.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d2990c82efbcdac7d453f920301787b8c83db1e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295373"
---
# <a name="connect-with-the-android-client"></a>Conectar-se ao cliente Android

> Aplica-se a: Android 4.1 e posterior, Chromebooks com ChromeOS 53 e posteriores.

>[!NOTE]
> A capacidade de acessar os recursos do Windows Virtual Desktop do cliente Android está atualmente disponível na pré-visualização.

Você pode acessar os recursos do Windows Virtual Desktop a partir do seu dispositivo Android com o nosso cliente para download. Você também pode usar o cliente Android em dispositivos Chromebook que suportam a Google Play Store. Este guia lhe dirá como configurar o cliente Android.

## <a name="install-the-android-client"></a>Instale o cliente Android

Para começar, [baixe](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) e instale o cliente em seu dispositivo Android.

## <a name="subscribe-to-a-feed"></a>Assinar um feed

Inscreva-se no feed fornecido pelo seu admin para obter a lista de recursos gerenciados que você pode acessar em seu dispositivo Android.

Para assinar um feed:

1. No Centro de **+** Conexões, toque e toque em **Feed de recursos remotos**.
2. Insira a URL de alimentação no campo **feed URL.** A URL do feed pode ser uma URL ou um endereço de e-mail.
   - Se você usar uma URL, use a que seu <https://rdweb.wvd.microsoft.com>admin lhe deu, normalmente .
   - Para usar o e-mail, insira seu endereço de e-mail. O cliente procurará por uma URL associada ao seu endereço de e-mail se o seu admin configurou o servidor dessa forma.
3. Toque **em NEXT**.
4. Forneça suas credenciais quando solicitado.
   - Para **nome de usuário,** dê ao nome de usuário permissão para acessar recursos.
   - Para **Senha,** forneça a senha associada ao nome de usuário.
   - Você também pode ser solicitado a fornecer fatores adicionais se o seu admin configurou a autenticação dessa forma.

Após a assinatura, o Centro de Conexão deve exibir os recursos remotos.

Uma vez subscrito em um feed, o conteúdo do feed será atualizado automaticamente regularmente. Os recursos podem ser adicionados, alterados ou removidos com base nas alterações feitas pelo administrador.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar o cliente Android, confira [Comece com o cliente Android](/windows-server/remote/remote-desktop-services/clients/remote-desktop-android/).
