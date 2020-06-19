---
title: Acesso Condicional – Bloquear acesso por local – Azure Active Directory
description: Criar uma política de Acesso Condicional personalizada para bloquear o acesso a recursos por localização de IP
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5350e728ad9c30a6e9258ce9c7615baf020473a8
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83995115"
---
# <a name="conditional-access-block-access-by-location"></a>Acesso Condicional: Bloquear o acesso por local

Com a condição de localização no Acesso Condicional, você pode controlar o acesso aos seus aplicativos na nuvem com base no local de rede de um usuário. A condição de localização é comumente usada para bloquear o acesso de países/regiões dos quais sua organização sabe que o tráfego não deveria vir.

## <a name="define-locations"></a>Definir locais

1. Entre no **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue até **Azure Active Directory** > **Segurança** > **Acesso Condicional** > **Locais nomeados**.
1. Escolha **Novo local**.
1. Dê um nome ao seu local.
1. Escolha **Intervalos de IP** se você souber os intervalos de endereços IPv4 acessíveis externamente específicos que constituem esse local ou **Países/Regiões**.
   1. Forneça os **Intervalos de IP** ou selecione os **Países/Regiões** para o local que você está especificando.
      * Se você escolher Países/Regiões, você poderá opcionalmente optar por incluir áreas desconhecidas.
1. Escolha **Salvar**

Mais informações sobre a condição de localização no Acesso Condicional podem ser encontradas no artigo [O que é a condição de localização no Acesso Condicional do Azure Active Directory](location-condition.md)

## <a name="create-a-conditional-access-policy"></a>Criar uma política de Acesso Condicional

1. Entre no **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Procure **Azure Active Directory** > **Segurança** > **Acesso Condicional**.
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **Atribuições**, selecione **Usuários e grupos**
   1. Em **Incluir**, selecione **Todos os usuários**.
   1. Selecione **Concluído**.
1. Em **Aplicativos ou ações de nuvem** > **Incluir**, selecione **Todos os aplicativos de nuvem** e selecione **Concluído**.
1. Em **Condições** > **Localização**.
   1. Defina **Configurar** como **Sim**
   1. **Incluir** selecione **Locais selecionados**
   1. Selecione o local bloqueado que você criou para sua organização.
   1. Clique em **Selecionar** > **Concluído** > **Concluído**.
1. Em **Condições** > **Aplicativos clientes (versão prévia)** , defina **Configurar** como **Sim** e selecione **Concluído**.
1. Em **Controles de acesso** > **Bloquear**, selecione **Selecionar**.
1. Confirme suas configurações e defina **Habilitar política** como **Ativado**.
1. Selecione **Criar** para criar e habilitar sua política.

## <a name="next-steps"></a>Próximas etapas

[Políticas comuns de Acesso Condicional](concept-conditional-access-policy-common.md)

[Determinar o impacto usando o modo somente relatório de Acesso Condicional](howto-conditional-access-report-only.md)

[Simular comportamento de entrada usando a ferramenta What If de Acesso Condicional](troubleshoot-conditional-access-what-if.md)
