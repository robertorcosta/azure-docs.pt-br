---
title: Acesso Condicional - Bloquear acesso por localização - Diretório Ativo do Azure
description: Crie uma política de acesso condicional personalizado para bloquear o acesso aos recursos por localização IP
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34b29ceadaaf85e69d1214039fa1b563ed21a77d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295177"
---
# <a name="conditional-access-block-access-by-location"></a>Acesso Condicional: Bloquear o acesso por localização

Com a condição de localização no Acesso Condicional, você pode controlar o acesso aos seus aplicativos na nuvem com base na localização da rede de um usuário. A condição de localização é comumente usada para bloquear o acesso de países de onde sua organização sabe que o tráfego não deve vir.

## <a name="define-locations"></a>Definir locais

1. Faça login no **portal Azure** como administrador global, administrador de segurança ou administrador de Acesso Condicional.
1. Navegue até os**locais**nomeados para o **Azure Active Directory** > **Security** > **Conditional Access** > .
1. Escolha **novo local**.
1. Dê um nome à sua localização.
1. Escolha **faixas IP** se você conhecer as faixas de endereço Si4 acessíveis externamente específicas que compõem esse local ou **países/regiões**.
   1. Forneça as **faixas IP** ou selecione os **Países/Regiões** para o local especificado.
      * Se você escolher Países/Regiões, você pode optar opcionalmente por incluir áreas desconhecidas.
1. Escolha **Salvar**

Mais informações sobre a condição de localização no Acesso Condicional podem ser encontradas no artigo, [Qual é a condição de localização no Azure Active Directory Conditional Access](location-condition.md)

## <a name="create-a-conditional-access-policy"></a>Criar uma política de Acesso Condicional

1. Faça login no **portal Azure** como administrador global, administrador de segurança ou administrador de Acesso Condicional.
1. Navegue até o Acesso**Condicional de****Segurança** > do Diretório >  **Ativo do Azure**.
1. Selecione **Nova política**.
1. Dê um nome à sua apólice. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **Atribuições,** selecione **Usuários e grupos**
   1. Em **Incluir,** selecione **Todos os usuários**.
   1. Selecione **Feito**.
1. Em **Aplicativos ou ações** > na**Nuvem, selecione** **Todos os aplicativos em nuvem**e selecione **Done**.
1. Em **Condições** > **de Localização**.
   1. Definir **configurar** como **Sim**
   1. **Inclua** selecionar **locais selecionados**
   1. Selecione o local bloqueado que você criou para sua organização.
   1. Clique **em Selecionar** > **Feito** > **Feito**.
1. Em **Condições,** > **aplicativos do cliente (Visualização)**, **configure Configuração** para **Sim**e selecione **Feito**.
1. Em **Bloquear controles de** > acesso**e**selecionar **Selecionar**.
1. Confirme suas configurações e **configure Ativar a diretiva** **em**.
1. Selecione **Criar** para criar para ativar sua política.

## <a name="next-steps"></a>Próximas etapas

[Políticas comuns de Acesso Condicional](concept-conditional-access-policy-common.md)

[Determine o impacto usando o modo somente de relatório de acesso condicional](howto-conditional-access-report-only.md)

[Simule o comportamento do sinal usando a ferramenta Acesso Condicional E se](troubleshoot-conditional-access-what-if.md)
