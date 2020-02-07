---
title: Suporte para Linux de área de trabalho virtual do Windows – Azure
description: Uma breve visão geral do suporte ao Linux para área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
ms.openlocfilehash: 47e38d79e8aa4656b8164c94b4ef439bf431e01d
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049662"
---
# <a name="linux-support"></a>Suporte para Linux

Você pode usar o SDK do Linux para área de trabalho virtual do Windows para criar um cliente de área de trabalho virtual do Windows autônomo. Você também pode usá-lo para habilitar o suporte à área de trabalho virtual do Windows em seu aplicativo cliente. Este guia rápido explicará o que é o SDK do Linux e como começar a usá-lo.

## <a name="what-is-the-linux-sdk"></a>O que é o SDK do Linux?

Você pode usar as APIs do SDK para recuperar feeds de recursos, conectar-se a sessões de aplicativos remotos ou de área de trabalho e usar muitos dos redirecionamentos aos quais nossos clientes de primeira empresa dão suporte.

### <a name="supported-linux-distributions"></a>Distribuições do Linux com suporte

O SDK é compatível com a maioria dos sistemas operacionais baseados no Ubuntu 18, 4 ou posterior. Se você tiver uma distribuição do Linux diferente, poderemos trabalhar com você para descobrir como oferecer o melhor suporte às suas necessidades.

### <a name="feature-support"></a>Suporte a recursos

O SDK dá suporte a várias conexões para desktop e sessões de aplicativos remotos. Há suporte para os seguintes redirecionamentos:

| Redirecionamento       | Com suporte |
| :---------------- | :-------: |
| Teclado          | &#10004;  |
| Mouse             | &#10004;  |
| Entrada de áudio          | &#10004;  |
| Saída de áudio         | &#10004;  |
| Área de transferência (texto)  | &#10004;  |
| Área de transferência (imagem) | &#10004;  |
| Área de transferência (arquivo)  | &#10004;  |
| Cartão inteligente         | &#10004;  |
| Unidade/pasta      | &#10004;  |

O SDK também dá suporte a várias configurações de exibição de monitor, contanto que os monitores selecionados para a sessão sejam contíguos.

Atualizaremos este documento à medida que adicionamos suporte para novos recursos e redirecionamentos. Se você quiser sugerir novos recursos e outras melhorias, visite nossa [página uservoice](https://go.microsoft.com/fwlink/?linkid=2116523).

## <a name="get-started-with-the-linux-sdk"></a>Introdução ao SDK do Linux

Para poder desenvolver um cliente Linux para área de trabalho virtual do Windows, você precisa fazer o seguinte:

1. Crie e implante um ambiente de área de trabalho virtual do Windows para teste ou uso em produção.
2. Teste os clientes primários disponíveis para se familiarizar com a experiência do usuário da área de trabalho virtual do Windows.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

O SDK está atualmente em desenvolvimento. Atualizaremos este documento com instruções para acessar o SDK quando ele estiver disponível.

Confira nossa documentação para os seguintes clientes:

- [Cliente de área de trabalho do Windows](connect-windows-7-and-10.md)
- [Cliente Web](connect-web.md)
- [Cliente Android](connect-android.md)
- [cliente macOS](connect-macos.md)
- [cliente iOS](connect-ios.md)
