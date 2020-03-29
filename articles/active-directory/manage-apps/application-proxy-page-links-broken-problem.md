---
title: Os links na página não funcionam para um aplicativo de Proxy de Aplicativo
description: Como solucionar problemas com links desfeitos em aplicativos de Proxy de Aplicativo integrados com o Azure AD
services: active-directory
author: msmimart
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca782b5a190704911472d70c414d12afa7e558f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430238"
---
# <a name="links-on-the-page-dont-work-for-an-application-proxy-application"></a>Os links na página não funcionam para um aplicativo de Proxy de Aplicativo

Este artigo ajuda-o a solucionar problemas porque links no seu aplicativo de Proxy de Aplicativo do Azure Active Directory não funcionam corretamente.

## <a name="overview"></a>Visão geral 
Após publicar um aplicativo de Proxy de Aplicativo, os únicos links que funcionam por padrão no aplicativo são links para destinos contidos na URL raiz publicada. Os links nos aplicativos não estão funcionando, a URL interna para o aplicativo provavelmente não inclui todos os destinos de links dentro do aplicativo.

**Por que isso acontece?** Ao clicar em um link em um aplicativo, o Proxy de aplicativo tenta resolver a URL como uma URL interna dentro do mesmo aplicativo ou como uma URL disponível externamente. Se o link aponta para uma URL interna que não está dentro do mesmo aplicativo, ele não pertence a nenhum um desses buckets e resulta em um erro não encontrado.

## <a name="ways-you-can-resolve-broken-links"></a>Maneiras de resolver links desfeitos

Há três maneiras de resolver esse problema. As escolhas abaixo estão listadas em crescente complexidade.

1.  Certifique-se de se que a URL interna é uma raiz que contém todos os links relevantes para o aplicativo. Isso permite que todos os links sejam resolvidos como conteúdo publicado no mesmo aplicativo.

    Se você alterar a URL interna, mas não quiser alterar a página de aterrissagem para usuários, altere a URL da home page para a URL interna publicada anteriormente. Isso pode ser feito indo para "Azure Active Directory" -&gt; Registros de aplicativos -&gt; selecione o aplicativo -&gt; Branding. Na seção de marca, você vê o campo "URL da página inicial", que você pode ajustar para ser a página de pouso desejada. Se você ainda estiver usando a experiência de registros do aplicativo legado, a guia propriedades mostraria os detalhes da "URL da página inicial". 
    
    > [!IMPORTANT]
    > Para fazer as alterações acima, você exige direitos para modificar objetos de aplicativo no Azure AD. O usuário precisa ser designado função [administrador de aplicativos](../users-groups-roles/roles-delegate-app-roles.md#assign-built-in-application-admin-roles) que concede direitos de modificação do aplicativo no Azure AD ao usuário.
    >

2.  Se seus aplicativos usam nomes de domínio totalmente qualificados (FQDNs), use [domínios personalizados](application-proxy-configure-custom-domain.md) para publicar seus aplicativos. Esse recurso permite que a mesma URL seja usada internamente e externamente.

    Essa opção garante que os links em seu aplicativo sejam acessíveis externamente através do Proxy de Aplicativo, pois os links dentro do aplicativo para URLs internas também são reconhecidos externamente. Todos os links ainda precisam pertencer a um aplicativo publicado. No entanto, com essa opção os links não precisam pertencer ao mesmo aplicativo e podem pertencer a vários aplicativos.

3.  Se nenhuma dessas opções for viável, há várias opções para habilitar a conversão de link embutido. Essas opções incluem o uso do Intune Managed Browser, da extensão My Apps ou o uso da configuração de conversão de link em seu aplicativo. Para saber mais sobre cada uma dessas opções e como habilitá-las, confira [Redirecionar links codificados para aplicativos publicados com o Proxy de Aplicativo do Azure AD](application-proxy-configure-hard-coded-link-translation.md).

## <a name="next-steps"></a>Próximas etapas
[Trabalhar com servidores proxy locais existentes](application-proxy-configure-connectors-with-proxy-servers.md)

