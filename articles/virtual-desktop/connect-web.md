---
title: Conectar o cliente Web da área de trabalho virtual do Windows-Azure
description: Como se conectar à área de trabalho virtual do Windows usando o cliente Web.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 70fd29944d31682de5a6a34f76fdf27e3ff7d3ff
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82607836"
---
# <a name="connect-with-the-web-client"></a>Conectar-se ao cliente Web

>[!IMPORTANT]
>Este conteúdo se aplica à atualização do Spring 2020 com Azure Resource Manager objetos da área de trabalho virtual do Windows. Se você estiver usando a área de trabalho virtual do Windows, a versão 2019 sem Azure Resource Manager objetos, consulte [Este artigo](./virtual-desktop-fall-2019/connect-web-2019.md).
>
> A atualização 2020 de área de trabalho virtual do Windows está em visualização pública no momento. Esta versão de visualização é fornecida sem um contrato de nível de serviço e não é recomendável usá-la para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. 
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O cliente Web permite que você acesse seus recursos de área de trabalho virtual do Windows em um navegador da Web sem o processo de instalação demorado.

>[!NOTE]
>No momento, o cliente Web não tem suporte para so móvel.

## <a name="supported-operating-systems-and-browsers"></a>Navegadores e sistemas operacionais compatíveis

Embora qualquer navegador compatível com HTML5 funcione, oficialmente damos suporte aos seguintes sistemas operacionais e navegadores.

| Navegador           | SO com suporte                     | Anotações               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Versão 55 ou posterior |
| Google Chrome     | Windows, macOS, Linux, sistema operacional Chrome |                     |

## <a name="access-remote-resources-feed"></a>Acessar feed de recursos remotos

Em um navegador, navegue até o [cliente Web da área de trabalho virtual do Windows](https://rdweb.wvd.microsoft.com/arm/webclient) e entre com sua conta de usuário.

>[!NOTE]
>Se você já tiver entrado com uma conta de Azure Active Directory diferente daquela que deseja usar para a área de trabalho virtual do Windows, você deverá sair ou usar uma janela de navegador privada.

Depois de entrar, você verá agora uma lista de recursos. Você pode iniciar recursos selecionando-os como faria com um aplicativo normal na guia **todos os recursos** .

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar o cliente Web, confira introdução [ao cliente Web](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).
