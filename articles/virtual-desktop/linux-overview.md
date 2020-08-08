---
title: Suporte para Linux de área de trabalho virtual do Windows – Azure
description: Uma breve visão geral do suporte ao Linux para área de trabalho virtual do Windows.
author: Heidilohr
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: bd3cc6c5220e2e84cbbd30b29b8034f53c813f1e
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008688"
---
# <a name="linux-support"></a>Suporte para Linux

Você pode acessar os recursos da área de trabalho virtual do Windows de seus dispositivos Linux com os seguintes clientes com suporte, fornecidos por nossos parceiros de cliente fino do Linux. Estamos trabalhando com vários parceiros para habilitar clientes de área de trabalho virtual do Windows com suporte em mais sistemas operacionais e dispositivos baseados em Linux. Se você quiser o suporte à área de trabalho virtual do Windows em uma plataforma Linux que não está listada aqui, informe-nos em nossa [página uservoice](https://remotedesktop.uservoice.com/forums/923035-remote-desktop-support-on-linux).

## <a name="connect-with-your-linux-device"></a>Conecte-se ao seu dispositivo Linux

Os parceiros a seguir aprovaram clientes de área de trabalho virtual do Windows para dispositivos Linux.

|Partner (parceiro)|Documentação do parceiro|Suporte a parceiros|
|:------|:--------------------|:--------------|
|![Logotipo do IGEL](./media/partners/igel.png)|[Documentação do cliente do IGEL](https://www.igel.com/igel-solution-family/windows-virtual-desktop/)|[Suporte do IGEL](https://www.igel.com/support/)|

## <a name="what-is-the-linux-sdk"></a>O que é o SDK do Linux?

Os parceiros de cliente fino do Linux podem usar as APIs do SDK do Linux para área de trabalho virtual do Windows para recuperar feeds de recursos, conectar-se a desktops ou sessões de aplicativos remotos e usar muitos dos redirecionamentos que nossos clientes de primeira empresa dão suporte. O SDK é compatível com a maioria dos sistemas operacionais baseados no Ubuntu 18, 4 ou posterior.

### <a name="feature-support"></a>Suporte a recursos

O SDK dá suporte a várias conexões para desktop e sessões de aplicativos remotos. Há suporte para os seguintes redirecionamentos:

| Redirecionamento       | Com suporte |
| :---------------- | :-------: |
| Keyboard          | &#10004;  |
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

## <a name="next-steps"></a>Próximas etapas

Confira nossa documentação para os seguintes clientes:

- [Cliente de Área de Trabalho do Windows](connect-windows-7-10.md)
- [Cliente Web](connect-web.md)
- [Cliente Android](connect-android.md)
- [Cliente macOS](connect-macos.md)
- [Cliente iOS](connect-ios.md)
