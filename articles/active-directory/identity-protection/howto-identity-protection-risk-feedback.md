---
title: Forneça feedback de risco no Azure Active Directory Identity Protection
description: Como e por que você deve fornecer feedback sobre detecções de risco de proteção de identidade.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4be8290f12d64f0c556100c63ec159bd414c6fcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382086"
---
# <a name="how-to-give-risk-feedback-in-azure-ad-identity-protection"></a>Como: Dar feedback de risco no Azure AD Identity Protection

O Azure AD Identity Protection permite que você dê feedback sobre sua avaliação de risco. O documento a seguir lista os cenários em que você gostaria de dar feedback sobre a avaliação de risco do Azure AD Identity Protection e como a incorporamos.

## <a name="what-is-a-detection"></a>O que é uma detecção?

Uma detecção de Proteção de Identidade é um indicador de atividade suspeita de uma perspectiva de risco de identidade. Essas atividades suspeitas são chamadas de detecções de risco. Essas detecções baseadas em identidade podem ser baseadas em heurística, aprendizado de máquina ou podem vir de produtos parceiros. Essas detecções são usadas para determinar o risco de login e o risco do usuário,

* O risco do usuário representa a probabilidade de uma identidade ser comprometida.
* O risco de login representa a probabilidade de um login ser comprometido (por exemplo, o login não é autorizado pelo proprietário da identidade).

## <a name="why-should-i-give-risk-feedback-to-azure-ads-risk-assessments"></a>Por que devo dar feedback de risco às avaliações de risco do Azure AD? 

Existem várias razões pelas quais você deve dar ao Azure um feedback de risco:

- **Você achou incorreta a avaliação de risco do usuário ou login do Azure AD**. Por exemplo, um login mostrado no relatório 'Risky sign-ins' foi benigno e todas as detecções naquele login foram falsos positivos.
- **Você validou que a avaliação de risco do usuário ou login do Azure estava correta.** Por exemplo, um login mostrado no relatório 'Risky sign-ins' foi realmente malicioso e você quer que o Azure AD saiba que todas as detecções naquele login foram verdadeiros positivos.
- **Você corrigiu o risco naquele usuário fora do Azure AD Identity Protection** e deseja que o nível de risco do usuário seja atualizado.

## <a name="how-does-azure-ad-use-my-risk-feedback"></a>Como o Azure AD usa meu feedback de risco?

O Azure AD usa seu feedback para atualizar o risco do usuário subjacente e/ou login e a precisão desses eventos. Esse feedback ajuda a proteger o usuário final. Por exemplo, uma vez que você confirma que um login está comprometido, o Azure AD aumenta imediatamente o risco agregado do usuário e o risco agregado de login (não risco em tempo real) para Alto. Se esse usuário estiver incluído em sua política de risco de usuário para forçar usuários de alto risco a redefinir suas senhas com segurança, o usuário se remediará automaticamente na próxima vez que fizer login.

## <a name="how-should-i-give-risk-feedback-and-what-happens-under-the-hood"></a>Como devo dar feedback de risco e o que acontece o capô?

Aqui estão os cenários e mecanismos para dar feedback de risco ao Azure AD.

| Cenário | Como dar feedback? | O que acontece debaixo do capô? | Observações |
| --- | --- | --- | --- |
| **Login não comprometido (Falso positivo)** <br> O relatório 'Sign-ins de risco' mostra um login em risco [Estado de risco = Em risco] mas que o login não foi comprometido. | Selecione o login e clique em 'Confirmar o cofre de login'. | O Azure AD mudará o risco agregado do login para nenhum [Estado de risco = seguro confirmado; Nível de risco (Agregado) = -] e reverterá seu impacto sobre o risco do usuário. | Atualmente, a opção 'Confirmar seguro de login' só está disponível no relatório 'Entradas arriscadas'. |
| **Login comprometido (True positivo)** <br> O relatório 'Risky Sign-ins' mostra um login em risco [Estado de risco = Risco] com baixo risco [Nível de risco (Agregado) = Baixo] e que o login foi de fato comprometido. | Selecione o login e clique em 'Confirmar login comprometido'. | O Azure AD moverá o risco agregado do login e o risco do usuário para Alto [Estado de risco = Confirmado comprometido; Nível de risco = Alto]. | Atualmente, a opção 'Confirmar login comprometido' só está disponível no relatório 'Logins arriscados'. |
| **Usuário comprometido (True positivo)** <br> O relatório 'Usuários de risco' mostra um usuário em risco [Estado de risco = Risco] com baixo risco [Nível de risco = Baixo] e que o usuário foi de fato comprometido. | Selecione o usuário e clique em 'Confirmar o usuário comprometido'. | O Azure AD mudará o risco do usuário para alto [Estado de risco = Confirmado comprometido; Nível de risco = Alto] e adicionará uma nova detecção 'Usuário confirmado admin comprometido'. | Atualmente, a opção 'Confirmar usuário comprometido' só está disponível no relatório 'Usuários arriscados'. <br> A detecção 'Admin confirmou comprometimento do usuário' é mostrada na guia 'Detecções de risco não vinculadas a um login' no relatório 'Usuários arriscados'. |
| **Usuário remediado fora do Azure AD Identity Protection (True positive + Remediado)** <br> O relatório 'Usuários arriscados' mostra um usuário em risco e, posteriormente, remediarei o usuário fora do Azure AD Identity Protection. | 1. Selecione o usuário e clique em 'Confirmar o usuário comprometido'. (Este processo confirma ao Azure AD que o usuário foi de fato comprometido.) <br> 2. Aguarde que o 'nível de risco' do usuário vá para o Alto. (Desta vez, dá ao Azure AD o tempo necessário para levar o feedback acima para o motor de risco.) <br> 3. Selecione o usuário e clique em 'Descartar o risco do usuário'. (Este processo confirma ao Azure AD que o usuário não está mais comprometido.) |  O Azure AD transfere o risco do usuário para nenhum [Estado de risco = Demitido; Nível de risco = -] e fecha o risco em todos os sign-ins existentes com risco ativo. | Clicar em 'Descartar risco de usuário' encerrará todos os riscos no usuário e nos logins passados. Esta ação não pode ser desfeita. |
| **Usuário não comprometido (Falso positivo)** <br> O relatório 'Usuários arriscados' mostra-se no usuário em risco, mas o usuário não está comprometido. | Selecione o usuário e clique em 'Descartar risco de usuário'. (Este processo confirma ao Azure AD que o usuário não está comprometido.) | O Azure AD transfere o risco do usuário para nenhum [Estado de risco = Demitido; Nível de risco = -]. | Clicar em 'Descartar risco de usuário' encerrará todos os riscos no usuário e nos logins passados. Esta ação não pode ser desfeita. |
| Quero encerrar o risco do usuário, mas não tenho certeza se o usuário está comprometido/seguro. | Selecione o usuário e clique em 'Descartar risco de usuário'. (Este processo confirma ao Azure AD que o usuário não está mais comprometido.) | O Azure AD transfere o risco do usuário para nenhum [Estado de risco = Demitido; Nível de risco = -]. | Clicar em 'Descartar risco de usuário' encerrará todos os riscos no usuário e nos logins passados. Esta ação não pode ser desfeita. Recomendamos que você remediao o usuário clicando em 'Redefinir senha' ou solicitar ao usuário para redefinir/alterar suas credenciais com segurança. |

O feedback sobre as detecções de risco do usuário na Identity Protection é processado offline e pode levar algum tempo para ser atualizado. A coluna de estado de processamento de risco fornecerá o estado atual de processamento de feedback.

![Estado de processamento de risco para relatório de usuário de risco](./media/howto-identity-protection-risk-feedback/risky-users-provide-feedback.png)

## <a name="next-steps"></a>Próximas etapas

- [Referência em detecções de risco de proteção de identidade do Azure Active Directory](risk-events-reference.md)
