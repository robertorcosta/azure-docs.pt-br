---
title: Suporte para Windows Virtual Desktop Linux - Azure
description: Uma breve visão geral do suporte ao Linux para o Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8c5de43ed29856451ad67e02a426b07cc34a0d54
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422423"
---
# <a name="linux-support"></a>Suporte para Linux

Os parceiros podem usar o Linux SDK para Windows Virtual Desktop para construir um cliente autônomo do Windows Virtual Desktop. Você também pode usá-lo para habilitar o suporte ao Windows Virtual Desktop no aplicativo do cliente. Este guia rápido explicará o que é o Linux SDK e como começar a usá-lo.

## <a name="connect-with-your-linux-device"></a>Conecte-se com seu dispositivo Linux

Os seguintes parceiros aprovaram clientes do Windows Virtual Desktop para dispositivos Linux.

|Partner (parceiro)|Documentação do parceiro|Suporte ao parceiro|
|:------|:--------------------|:--------------|
|![Logotipo IGEL](./media/partners/igel.png)|[Documentação do cliente IGEL](https://www.igel.com/igel-solution-family/windows-virtual-desktop/)|[Suporte iGEL](https://www.igel.com/support/)|

## <a name="what-is-the-linux-sdk"></a>O que é o Linux SDK?

Você pode usar as APIs sdk para recuperar feeds de recursos, conectar-se a sessões de desktop ou aplicativos remotos e usar muitos dos redirecionamentos que nossos clientes de primeira parte suportam.

> [!NOTE]
> O SDK está atualmente em desenvolvimento. Atualizaremos este documento com instruções para acessar o SDK quando ele estiver geralmente disponível.

### <a name="supported-linux-distributions"></a>Distribuições Linux suportadas

O SDK é compatível com a maioria dos sistemas operacionais baseados no Ubuntu 18.04 ou posterior. Se você tem uma distribuição Linux diferente, podemos trabalhar com você para descobrir como melhor apoiar suas necessidades.

### <a name="feature-support"></a>Suporte a recursos

O SDK suporta várias conexões para sessões de desktop e aplicativos remotos. Os seguintes redirecionamentos são suportados:

| Redirecionamento       | Com suporte |
| :---------------- | :-------: |
| Teclado          | &#10004;  |
| Mouse             | &#10004;  |
| Áudio em          | &#10004;  |
| Saída de áudio         | &#10004;  |
| Área de transferência (texto)  | &#10004;  |
| Área de transferência (imagem) | &#10004;  |
| Área de transferência (arquivo)  | &#10004;  |
| Cartão inteligente         | &#10004;  |
| Unidade/pasta      | &#10004;  |

O SDK também suporta várias configurações de exibição de monitor, desde que os monitores selecionados para sua sessão sejam contíguos.

Atualizaremos este documento à medida que adicionamos suporte para novos recursos e redirecionamentos. Se você quiser sugerir novos recursos e outras melhorias, visite nossa [página UserVoice](https://go.microsoft.com/fwlink/?linkid=2116523).

## <a name="get-started-with-the-linux-sdk"></a>Comece com o Linux SDK

Antes de desenvolver um cliente Linux para Windows Virtual Desktop, você precisa fazer as seguintes coisas:

1. Crie e implante um ambiente de desktop virtual do Windows para testes ou uso de produção.
2. Teste os clientes de primeira parte disponíveis para se familiarizar com a experiência do usuário do Windows Virtual Desktop.

## <a name="next-steps"></a>Próximas etapas

Confira nossa documentação para os seguintes clientes:

- [Cliente de Área de Trabalho do Windows](connect-windows-7-and-10.md)
- [Cliente web](connect-web.md)
- [Cliente para Android](connect-android.md)
- [Cliente para macOS](connect-macos.md)
- [Cliente para iOS](connect-ios.md)
