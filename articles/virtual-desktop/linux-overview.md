---
title: Suporte para Linux de área de trabalho virtual do Windows – Azure
description: Uma breve visão geral do suporte ao Linux para área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3dc9d62141a63574a6796982542cb1491108eedc
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259860"
---
# <a name="linux-support"></a>Suporte para Linux

Os parceiros podem usar o SDK do Linux para área de trabalho virtual do Windows para criar um cliente de área de trabalho virtual do Windows autônomo. Você também pode usá-lo para habilitar o suporte à área de trabalho virtual do Windows em seu aplicativo cliente. Este guia rápido explicará o que é o SDK do Linux e como começar a usá-lo.

## <a name="connect-with-your-linux-device"></a>Conecte-se ao seu dispositivo Linux

Os parceiros a seguir aprovaram clientes de área de trabalho virtual do Windows para dispositivos Linux.

|Partner (parceiro)|Documentação do parceiro|Suporte a parceiros|
|:------|:--------------------|:--------------|
|![Logotipo do IGEL](./media/partners/igel.png)|[Documentação do cliente do IGEL](https://www.igel.com/igel-solution-family/windows-virtual-desktop/)|[Suporte do IGEL](https://www.igel.com/support/)|

## <a name="what-is-the-linux-sdk"></a>O que é o SDK do Linux?

Você pode usar as APIs do SDK para recuperar feeds de recursos, conectar-se a sessões de aplicativos remotos ou de área de trabalho e usar muitos dos redirecionamentos aos quais nossos clientes de primeira empresa dão suporte.

> [!NOTE]
> O SDK está atualmente em desenvolvimento. Atualizaremos este documento com instruções para acessar o SDK quando ele estiver disponível.

### <a name="supported-linux-distributions"></a>Distribuições Linux compatíveis

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

## <a name="next-steps"></a>Próximas etapas

Confira nossa documentação para os seguintes clientes:

- [Cliente de Área de Trabalho do Windows](connect-windows-7-10.md)
- [Cliente Web](connect-web.md)
- [Cliente para Android](connect-android.md)
- [Cliente para macOS](connect-macos.md)
- [Cliente iOS](connect-ios.md)
