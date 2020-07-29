---
title: Conectar-se à área de trabalho virtual do Windows com o cliente Web-Azure
description: Como se conectar à Área de Trabalho Virtual do Windows usando o cliente Web.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3fdbd0ddb33785655cada2600acdecdced4aeb2f
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87283428"
---
# <a name="connect-to-windows-virtual-desktop-with-the-web-client"></a>Conectar-se à área de trabalho virtual do Windows com o cliente Web

>[!IMPORTANT]
>Esse conteúdo se aplica à área de trabalho virtual do Windows com Azure Resource Manager objetos da área de trabalho virtual do Windows. Se você estiver usando a área de trabalho virtual do Windows (clássico) sem Azure Resource Manager objetos, consulte [Este artigo](./virtual-desktop-fall-2019/connect-web-2019.md).

O cliente Web permite acessar os recursos da Área de Trabalho Virtual do Windows em um navegador da Web sem o lento processo de instalação.

>[!NOTE]
>No momento, o cliente Web não dá suporte a sistemas operacionais de dispositivos móveis.

## <a name="supported-operating-systems-and-browsers"></a>Navegadores e sistemas operacionais compatíveis

Por mais que qualquer navegador compatível com HTML5 funcione, oficialmente damos suporte aos seguintes navegadores e sistemas operacionais.

| Navegador           | SO com suporte                     | Observações               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          | Versão 11 ou posterior |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Versão 55 ou posterior |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>Acessar feed de recursos remotos

Em um navegador, vá até a versão integrada do Azure Resource Manager do cliente Web da Área de Trabalho Virtual do Windows em <https://rdweb.wvd.microsoft.com/arm/webclient> e entre com sua conta de usuário.

>[!NOTE]
>Se você estiver usando a área de trabalho virtual do Windows (clássica) sem integração Azure Resource Manager, conecte-se aos seus recursos em <https://rdweb.wvd.microsoft.com/webclient> vez disso.

>[!NOTE]
>Se já tiver entrado com uma conta do Azure Active Directory diferente da que deseja usar para a Área de Trabalho Virtual do Windows, você deverá sair ou usar uma janela de navegador privada.

Depois de entrar, você verá uma lista de recursos. Você pode iniciar os recursos selecionando-os da mesma maneira que faria com um aplicativo normal na guia **Todos os recursos**.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como usar o cliente Web em [Introdução ao cliente Web](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
