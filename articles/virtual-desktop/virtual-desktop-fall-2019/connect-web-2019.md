---
title: Conectar-se ao cliente Web da Área de Trabalho Virtual do Windows – Azure
description: Como se conectar à Área de Trabalho Virtual do Windows usando o cliente Web.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8b73a6bf2c8b853d9e3e939b6c8ff6fc05ac7021
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83657264"
---
# <a name="connect-with-the-web-client"></a>Conectar-se ao cliente Web

>[!IMPORTANT]
>Esse conteúdo se aplica à versão Outono 2019 que não é compatível com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver tentando gerenciar objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager introduzidos na atualização Spring 2020, confira [este artigo](../connect-web.md).

O cliente Web permite acessar os recursos da Área de Trabalho Virtual do Windows em um navegador da Web sem o lento processo de instalação.

>[!NOTE]
>No momento, o cliente Web não dá suporte a sistemas operacionais de dispositivos móveis.

## <a name="supported-operating-systems-and-browsers"></a>Navegadores e sistemas operacionais compatíveis

Por mais que qualquer navegador compatível com HTML5 funcione, oficialmente damos suporte aos seguintes navegadores e sistemas operacionais.

| Navegador           | SO com suporte                     | Observações               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Versão 55 ou posterior |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>Acessar feed de recursos remotos

Em um navegador, vá até o cliente Web da Área de Trabalho Virtual do Windows em <https://rdweb.wvd.microsoft.com/webclient> e entre com sua conta de usuário.

>[!NOTE]
>Já se estiver usando a versão Primavera de 2020 da Área de Trabalho Virtual do Windows com a integração Azure Resource Manager, conecte-se a seus recursos em <https://rdweb.wvd.microsoft.com/arm/webclient>.

>[!NOTE]
>Se já tiver entrado com uma conta do Azure Active Directory diferente da que deseja usar para a Área de Trabalho Virtual do Windows, você deverá sair ou usar uma janela de navegador privada.

Depois de entrar, você verá uma lista de recursos. Você pode iniciar os recursos selecionando-os da mesma maneira que faria com um aplicativo normal na guia **Todos os Recursos**.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como usar o cliente Web em [Introdução ao cliente Web](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
