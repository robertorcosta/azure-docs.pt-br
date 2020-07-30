---
title: Conectar-se à área de trabalho virtual do Windows do Android – Azure
description: Como se conectar à área de trabalho virtual do Windows usando o cliente Android.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/25/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1d0aebffa5f3d27a77928c2a1c7461384bf99ce8
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387873"
---
# <a name="connect-to-windows-virtual-desktop-with-the-android-client"></a>Conectar-se à área de trabalho virtual do Windows com o cliente Android

> Aplica-se a: Android 4,1 e posterior, Chromebooks com ChromeOS 53 e posterior.

>[!IMPORTANT]
>Esse conteúdo se aplica à área de trabalho virtual do Windows com Azure Resource Manager objetos da área de trabalho virtual do Windows. Se você estiver usando a área de trabalho virtual do Windows (clássico) sem Azure Resource Manager objetos, consulte [Este artigo](./virtual-desktop-fall-2019/connect-android-2019.md).

Você pode acessar os recursos da área de trabalho virtual do Windows em seu dispositivo Android com nosso cliente baixável. Você também pode usar o cliente Android em dispositivos Chromebook que dão suporte ao Google Play Store. Este guia lhe dirá como configurar o cliente Android.

## <a name="install-the-android-client"></a>Instalar o cliente Android

Para começar, [Baixe](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) e instale o cliente em seu dispositivo Android.

## <a name="subscribe-to-a-feed"></a>Assinar um feed

Assine o feed fornecido pelo administrador para obter a lista de recursos gerenciados que você pode acessar em seu dispositivo Android.

Para assinar um feed:

1. Na Central de Conexão, toque em **+** e, em seguida, toque em **Feed de Recursos Remotos**.
2. Insira a URL do feed no campo **URL do feed** . A URL do feed pode ser uma URL ou um endereço de email.
   - Se você usar uma URL, use aquela que o administrador lhe forneceu, normalmente <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery> .
   - Para usar email, insira seu endereço de email. O cliente pesquisará uma URL associada ao seu endereço de email se o administrador configurou o servidor dessa maneira.
3. Toque em **AVANÇAR**.
4. Forneça suas credenciais quando solicitado.
   - Para **nome de usuário**, dê ao nome de usuário permissão para acessar recursos.
   - Para **senha**, forneça a senha associada ao nome de usuário.
   - Você também poderá ser solicitado a fornecer fatores adicionais se o administrador configurou a autenticação dessa maneira.

Após a assinatura, a central de conexões deve exibir os recursos remotos.

Após a assinatura de um feed, o conteúdo dele será atualizado automaticamente de maneira regular. Os recursos podem ser adicionados, alterados ou removidos com base nas alterações realizadas pelo seu administrador.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar o cliente Android, confira [introdução ao cliente Android](/windows-server/remote/remote-desktop-services/clients/remote-desktop-android/).
