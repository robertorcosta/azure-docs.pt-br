---
title: O que é o modo somente de relatório de acesso condicional? - Diretório Ativo do Azure
description: Como o modo somente relatório pode ajudar com a implantação da política de acesso condicional
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd41e79a1e08c57e806f6ada32faccfa5fdf5792
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295293"
---
# <a name="what-is-conditional-access-report-only-mode"></a>O que é o modo somente de relatório de acesso condicional?

O Acesso Condicional é amplamente utilizado por nossos clientes para se manter seguro aplicando os controles de acesso certos nas circunstâncias certas. No entanto, um dos desafios com a implantação de uma política de Acesso Condicional em sua organização é determinar o impacto para os usuários finais. Pode ser difícil prever o número e os nomes dos usuários impactados por iniciativas comuns de implantação, como bloquear a autenticação do legado, exigir autenticação multifatorial para uma população de usuários ou implementar políticas de risco de login. 

O modo somente relatório é um novo estado de política de acesso condicional que permite que os administradores avaliem o impacto das políticas de acesso condicional antes de habilitá-las em seu ambiente.  Com a liberação do modo somente relatório:

- As políticas de acesso condicional podem ser habilitadas no modo somente relatório.
- Durante o login, as políticas no modo somente relatório são avaliadas, mas não aplicadas.
- Os resultados estão registrados nas guias **Conditional Access** and **Report-only (Preview)** dos detalhes do registro de login.
- Os clientes com uma assinatura do Azure Monitor podem monitorar o impacto de suas políticas de acesso condicional usando a pasta de trabalho do Conditional Access.

> [!WARNING]
> Políticas no modo somente relatório que requerem dispositivos compatíveis podem solicitar aos usuários no Mac, iOS e Android que selecionem um certificado de dispositivo durante a avaliação da diretiva, mesmo que a conformidade com o dispositivo não seja aplicada. Essas solicitações podem se repetir até que o dispositivo esteja em conformidade. Para evitar que os usuários finais recebam solicitações durante o login, exclua as plataformas de dispositivos Mac, iOS e Android de políticas somente de relatórios que executam verificações de conformidade de dispositivos.

![Guia somente de relatórios no log de login do Azure AD](./media/concept-conditional-access-report-only/report-only-detail-in-sign-in-log.png)

## <a name="policy-results"></a>Resultados da política

Quando uma política no modo somente relatório é avaliada para um dado login, existem quatro novos valores de resultado possíveis:

| Result | Descrição |
| --- | --- |
| Somente relatório: Sucesso | Todas as condições de diretiva configuradas, controles de subvenção não interativos necessários e controles de sessão foram satisfeitos. Por exemplo, um requisito de autenticação de vários fatores é satisfeito por uma reclamação MFA já presente no token, ou uma política de dispositivo compatível é satisfeita ao realizar uma verificação de dispositivo em um dispositivo compatível. |
| Somente relatório: Falha | Todas as condições de política configuradas estavam satisfeitas, mas nem todos os controles de subvenção não interativos necessários ou controles de sessão estavam satisfeitos. Por exemplo, uma diretiva se aplica a um usuário onde um controle de bloco é configurado ou um dispositivo falha em uma política de dispositivo compatível. |
| Somente relatório: Ação do usuário necessária | Todas as condições de diretiva configuradas foram satisfeitas, mas a ação do usuário seria necessária para satisfazer os controles de concessão ou controles de sessão necessários. Com o modo somente relatório, o usuário não é solicitado a satisfazer os controles necessários. Por exemplo, os usuários não são solicitados para desafios de autenticação de vários fatores ou termos de uso.   |
| Somente relatório: Não aplicado | Nem todas as condições políticas configuradas foram satisfeitas. Por exemplo, o usuário é excluído da diretiva ou a política só se aplica a determinados locais nomeados confiáveis. |

## <a name="conditional-access-insights-workbook"></a>A caderneta de visão do Conditional Access Insights

Os administradores têm a capacidade de criar várias políticas no modo somente relatório, por isso é necessário entender tanto o impacto individual de cada política quanto o impacto combinado de múltiplas políticas avaliadas em conjunto. A nova carteira de trabalho Conditional Access Insights permite que os administradores visualizem consultas de acesso condicional e monitorem o impacto de uma diretiva para um determinado intervalo de tempo, conjunto de aplicativos e usuários. 
 
## <a name="next-steps"></a>Próximas etapas

[Configure o modo somente de relatório em uma diretiva de acesso condicional](howto-conditional-access-report-only.md)
