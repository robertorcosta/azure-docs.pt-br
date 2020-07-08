---
title: Conectar-se ao cliente Web da Área de Trabalho Virtual do Windows – Azure
description: Como se conectar à Área de Trabalho Virtual do Windows usando o cliente Web.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: bb3a73566cd2477e493ca8621336fb3a51f3b3b8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85212455"
---
# <a name="connect-with-the-web-client"></a>Conectar-se ao cliente Web

>[!IMPORTANT]
>Este conteúdo se aplica à atualização da Spring 2020 com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver usando a Área de Trabalho Virtual do Windows na versão 2019, sem objetos do Azure Resource Manager, confira [este artigo](./virtual-desktop-fall-2019/connect-web-2019.md).
>
> A atualização 2020 da Área de Trabalho Virtual do Windows está em versão prévia pública no momento. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendamos usá-la para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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

Em um navegador, vá até a versão integrada do Azure Resource Manager do cliente Web da Área de Trabalho Virtual do Windows em <https://rdweb.wvd.microsoft.com/arm/webclient> e entre com sua conta de usuário.

>[!NOTE]
>Se você estiver usando a versão Outono de 2019 da Área de Trabalho Virtual do Windows sem a integração do Azure Resource Manager, conecte-se aos seus recursos em <https://rdweb.wvd.microsoft.com/webclient>.

>[!NOTE]
>Se já tiver entrado com uma conta do Azure Active Directory diferente da que deseja usar para a Área de Trabalho Virtual do Windows, você deverá sair ou usar uma janela de navegador privada.

Depois de entrar, você verá uma lista de recursos. Você pode iniciar os recursos selecionando-os da mesma maneira que faria com um aplicativo normal na guia **Todos os recursos**.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como usar o cliente Web em [Introdução ao cliente Web](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
