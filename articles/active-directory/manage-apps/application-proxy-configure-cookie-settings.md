---
title: Configurações de cookie do Proxy de Aplicativo – Azure Active Directory | Microsoft Docs
description: O Azure AD (Azure Active Directory) tem cookies de sessão e de acesso para acessar aplicativos locais por meio do Proxy de Aplicativo. Neste artigo, você descobrirá como usar e definir as configurações de cookie.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcedb24a0efdbabaaef150fc3d5aff07d210ce23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481357"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Configurações de cookie para acessar aplicativos locais no Azure Active Directory

O Azure AD (Azure Active Directory) tem cookies de sessão e de acesso para acessar aplicativos locais por meio do Proxy de Aplicativo. Saiba como usar as configurações de cookie do Proxy de Aplicativo. 

## <a name="what-are-the-cookie-settings"></a>O que são as configurações de cookie?

O [Proxy de Aplicativo](application-proxy.md) utiliza as configurações de cookie de sessão e de acesso a seguir.

| Configuração de cookies | Padrão | Descrição | Recomendações |
| -------------- | ------- | ----------- | --------------- |
| Usar Cookie Somente HTTP | **Não** | **Sim** permite que o Proxy de Aplicativo inclua o sinalizador HTTPOnly nos cabeçalhos de resposta HTTP. Esse sinalizador oferece benefícios de segurança adicionais, por exemplo, impede que o script CSS (do lado do cliente) copie ou modifique os cookies.<br></br><br></br>Antes de suportarmos a configuração HTTP-Only, o Proxy de aplicativo criptografou e transmitiu cookies através de um canal TLS protegido para proteger contra modificações. | Use **Sim** para ter os benefícios de segurança adicionais.<br></br><br></br>Use **Não** para clientes ou agentes de usuário que exijam acesso ao cookie de sessão. Por exemplo, use **Não** para um cliente RDP ou MTSC que se conecta a um servidor de Gateway de Área de Trabalho Remota por meio do Proxy de Aplicativo.|
| Usar um Cookie Seguro | **Não** | **Sim** permite que o Proxy de Aplicativo inclua o sinalizador Secure nos cabeçalhos de resposta HTTP. Os cookies seguros melhoram a segurança ao transmitir cookies em um canal TLS seguro, como HTTPS. Isso impede que os cookies sejam observados por partes não autorizadas devido à transmissão do cookie em texto não criptografado. | Use **Sim** para ter os benefícios de segurança adicionais.|
| Usar cookie persistente | **Não** | **Sim** permite que o Proxy de Aplicativo defina seus cookies de acesso para não expirarem quando o navegador da Web for fechado. A persistência dura até que o token de acesso expire ou até que o usuário exclua manualmente os cookies persistentes. | Use **Não** por causa do risco de segurança associado à autenticação contínua dos usuários.<br></br><br></br>Sugerimos usar **Sim** apenas para aplicativos mais antigos que não podem compartilhar cookies entre processos. É melhor atualizar seu aplicativo para lidar com o compartilhamento de cookies entre processos em vez de usar cookies persistentes. Por exemplo, você pode precisar de cookies persistentes para permitir que um usuário abra documentos do Office no modo de exibição de gerenciador em um site do SharePoint. Sem cookies persistentes, essa operação poderá falhar se os cookies de acesso não forem compartilhados entre o navegador, o processo do gerenciador e o processo do Office. |

## <a name="samesite-cookies"></a>Cookies do mesmo site
Começando na versão Chrome 80 e eventualmente em navegadores aproveitando o Chromium, os cookies que não especificam o atributo [SameSite](https://web.dev/samesite-cookies-explained) serão tratados como se estivessem definidos como **SameSite=Lax**. O atributo SameSite declara como os cookies devem ser restritos a um contexto do mesmo site. Quando definido como Lax, o cookie é enviado apenas para solicitações do mesmo site ou navegação de nível superior. No entanto, o Proxy de aplicativo exige que esses cookies sejam preservados no contexto de terceiros, a fim de manter os usuários devidamente conectados durante a sessão. Devido a isso, estamos fazendo atualizações para o acesso ao Proxy de aplicativo e cookies de sessão para evitar impacto adverso dessa alteração. As atualizações incluem:

* Definindo o atributo **SameSite** em **Nenhum**. Isso permite que os cookies de acesso ao Proxy de aplicativos sejam enviados corretamente no contexto de terceiros.
* Definir a **configuração Usar cookies seguro** para usar **Sim** como padrão. O Chrome também exige que os cookies especifiquem o sinalizador Secure ou ele será rejeitado. Essa alteração se aplicará a todos os aplicativos existentes publicados através do Proxy do Aplicativo. Observe que os cookies de acesso ao Proxy do aplicativo sempre foram definidos como Secure e transmitidos apenas através de HTTPS. Esta alteração só se aplicará aos cookies da sessão.

Essas alterações nos cookies do Proxy de aplicativos serão lançadas ao longo das próximas semanas antes da data de lançamento do Chrome 80.

Além disso, se o seu aplicativo back-end tiver cookies que precisam estar disponíveis em um contexto de terceiros, você deve optar explicitamente alterando seu aplicativo para usar sameSite=None para esses cookies. O Proxy do aplicativo traduz o cabeçalho Set-Cookie para suas URLS e respeitará as configurações desses cookies definidas pelo aplicativo back-end.



## <a name="set-the-cookie-settings---azure-portal"></a>Definir as configurações de cookie – portal do Azure
Para definir as configurações de cookie usando o portal do Azure:

1. Faça login no [portal Azure](https://portal.azure.com). 
2. Navegue até os **aplicativos** > **do Azure Active Directory** >Enterprise Todos os **aplicativos**.
3. Selecione o aplicativo para o qual você deseja habilitar a configuração de um cookie.
4. Clique **em Proxy de aplicativo**.
5. Em **Configurações Adicionais**, defina a configuração do cookie como **Sim** ou **Não**.
6. Clique em **Salvar** para aplicar suas alterações. 

## <a name="view-current-cookie-settings---powershell"></a>Exibir as configurações atuais de cookies - PowerShell

Para ver as configurações atuais de cookies para o aplicativo, use este comando PowerShell:  

```powershell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## <a name="set-cookie-settings---powershell"></a>Definir configurações de cookies - PowerShell

Nos seguintes comandos ```<ObjectId>``` PowerShell, está o ObjectId do aplicativo. 

**Biscoito somente http** 

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**Biscoito seguro**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**Cookies persistentes**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```
