---
title: Solucionar problemas de acesso condicional usando a ferramenta E se - Azure Active Directory
description: Onde encontrar quais políticas de Acesso Condicional foram aplicadas e por que
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 07/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 661afc08c76c6cde61b02a29a55b4a8bec932e21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73175821"
---
# <a name="troubleshooting-conditional-access-using-the-what-if-tool"></a>Solução de problemas Acesso Condicional usando a ferramenta E se

A [ferramenta E se](what-if-tool.md) no Acesso Condicional é poderosa ao tentar entender por que uma política foi ou não aplicada a um usuário em uma circunstância específica ou se uma política seria aplicada em um estado conhecido.

A ferramenta E se está localizada no portal >  **Azure****Azure Active Directory** > **Acesso** > condicional**E Se**.

![Acesso Condicional E se ferramenta em estado padrão](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-tool.png)

> [!NOTE]
> A ferramenta E se atualmente não avalia políticas no modo somente relatório.

## <a name="gathering-information"></a>Coleta de informações

A ferramenta E se requer apenas um **Usuário** para começar. 

As seguintes informações adicionais são opcionais, mas ajudarão a reduzir o escopo para casos específicos.

* Aplicativos na nuvem ou ações
* Endereço IP 
* País/Região
* Plataforma de dispositivos
* Aplicativos clientes (visualização)
* Estado do dispositivo (visualização) 
* Risco de entrada

Essas informações podem ser coletadas do usuário, seu dispositivo ou do registro de logins do Azure AD.

## <a name="generating-results"></a>Gerando resultados

Insira os critérios coletados na seção anterior e selecione **E Se** para gerar uma lista de resultados. 

A qualquer momento, você pode selecionar **Redefinir** para limpar qualquer entrada de critérios e retornar ao estado padrão.

## <a name="evaluating-results"></a>Avaliação de resultados

### <a name="policies-that-will-apply"></a>Políticas que serão aplicadas

Esta lista mostrará quais políticas de Acesso Condicional se aplicariam dadas as condições. A lista incluirá os controles de concessão e de sessão que se aplicam. Exemplos incluem exigir autenticação multifatorial para acessar um aplicativo específico.

### <a name="policies-that-will-not-apply"></a>Políticas que não se aplicam

Esta lista mostrará políticas de acesso condicional que não se aplicarão se as condições se aplicassem. A lista incluirá quaisquer políticas e a razão pela qual elas não se aplicam. Exemplos incluem usuários e grupos que podem ser excluídos de uma política.

## <a name="use-case"></a>Caso de uso

Muitas organizações criam políticas baseadas em locais de rede, permitindo locais confiáveis e bloqueando locais onde o acesso não deve ocorrer.

Para validar que uma configuração foi feita adequadamente, um administrador poderia usar a ferramenta Ese para imitar o acesso, a partir de um local que deve ser permitido e de um local que deve ser negado.

![E se ferramenta mostrando resultados com acesso ao bloco](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png)

Neste caso, o usuário seria impedido de acessar qualquer aplicativo em nuvem em sua viagem à Coreia do Norte, já que Contoso bloqueou o acesso a partir desse local.

Este teste poderia ser expandido para incorporar outros pontos de dados para estreitar o escopo.

## <a name="next-steps"></a>Próximas etapas

* [O que é acesso condicional?](overview.md)
* [O que é Azure Active Directory Identity Protection?](../identity-protection/overview-v2.md)
* [O que é uma identidade do dispositivo?](../devices/overview.md)
* [Como funciona: autenticação multifator do Azure](../authentication/concept-mfa-howitworks.md)
