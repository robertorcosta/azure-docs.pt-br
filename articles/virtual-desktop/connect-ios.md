---
title: Conectar-se à área de trabalho virtual do Windows do iOS-Azure
description: Como se conectar à área de trabalho virtual do Windows usando o cliente iOS.
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4239f5271e4df282cf667b4bf1eff5f55f6f6323
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611697"
---
# <a name="connect-with-the-ios-client"></a>Conectar-se ao cliente iOS

> Aplica-se a: iOS 13,0 ou posterior. Compatível com iPhone, iPad e iPod Touch.

>[!IMPORTANT]
>Este conteúdo se aplica à atualização do Spring 2020 com Azure Resource Manager objetos da área de trabalho virtual do Windows. Se você estiver usando a área de trabalho virtual do Windows, a versão 2019 sem Azure Resource Manager objetos, consulte [Este artigo](./virtual-desktop-fall-2019/connect-ios-2019.md).
>
> A atualização 2020 de área de trabalho virtual do Windows está em visualização pública no momento. Esta versão de visualização é fornecida sem um contrato de nível de serviço e não é recomendável usá-la para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. 
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Você pode acessar os recursos da área de trabalho virtual do Windows em seu dispositivo iOS com nosso cliente baixável. Este guia lhe dirá como configurar o cliente iOS.

## <a name="install-the-ios-client"></a>Instalar o cliente iOS

Para começar, [Baixe](https://aka.ms/rdios) e instale o cliente em seu dispositivo IOS.

## <a name="subscribe-to-a-feed"></a>Assinar um feed

Assine o feed fornecido pelo administrador para obter a lista de recursos gerenciados que você pode acessar em seu dispositivo iOS.

Para assinar um feed:

1. No centro de conexões, toque **+** em e em **adicionar espaço de trabalho**.
2. Insira a URL do feed no campo **URL do feed** . A URL do feed pode ser uma URL ou um endereço de email.
   - Se você usar uma URL, use aquela que o administrador lhe forneceu. Normalmente, a URL é <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery>.
   - Para usar o email, insira seu endereço de email. Isso instrui o cliente a procurar uma URL associada ao seu endereço de email se o administrador configurou o servidor dessa maneira.
3. Toque em **Avançar**.
4. Forneça suas credenciais quando solicitado.
   - Para **nome de usuário**, dê ao nome de usuário permissão para acessar recursos.
   - Para **senha**, forneça a senha associada ao nome de usuário.
   - Você também poderá ser solicitado a fornecer fatores adicionais se o administrador configurou a autenticação dessa maneira.
5. Toque em **Salvar**.

Depois disso, a central de conexões deve exibir os recursos remotos.

Após a assinatura de um feed, o conteúdo do feed será atualizado automaticamente regularmente. Os recursos podem ser adicionados, alterados ou removidos com base nas alterações feitas pelo administrador.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar o cliente iOS, confira a documentação [introdução ao cliente Ios](/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios/) .
