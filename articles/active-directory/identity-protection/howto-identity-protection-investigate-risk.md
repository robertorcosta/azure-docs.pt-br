---
title: Investigue o risco Azure Active Directory Identity Protection
description: Saiba como investigar usuários, detecções e logins arriscados no Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5191f05752c177aa29d121abe9d1aa29fde265a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253475"
---
# <a name="how-to-investigate-risk"></a>Como: Investigar risco

A Identity Protection fornece às organizações três relatórios que podem usar para investigar riscos de identidade em seu ambiente. Esses relatórios são os **usuários de risco, logins** **arriscados**e **detecções de risco.** A investigação de eventos é fundamental para entender melhor e identificar quaisquer pontos fracos em sua estratégia de segurança.

Todos os três relatórios permitem o download de eventos em . Formato CSV para análise suplementar fora do portal Azure. Os usuários arriscados e os relatórios de login arriscadopermitem baixar as 2500 entradas mais recentes, enquanto o relatório de detecção de risco permite baixar os 5000 registros mais recentes.

As organizações podem aproveitar as integrações da API do Microsoft Graph para agregar dados com outras fontes às as que podem ter acesso como organização.

Os três relatórios são encontrados no portal >  **Azure****Active Directory** > **Security**.

## <a name="navigating-the-reports"></a>Navegando pelos relatórios

Cada relatório é lançado com uma lista de todas as detecções para o período mostrado no topo do relatório. Cada relatório permite a adição ou remoção de colunas com base na preferência do administrador. Os administradores podem optar por baixar os dados em . CSV ou . Formato JSON. Os relatórios podem ser filtrados usando os filtros na parte superior do relatório.

A seleção de entradas individuais pode permitir entradas adicionais na parte superior do relatório, como a capacidade de confirmar um login como comprometido ou seguro, confirmar um usuário como comprometido ou descartar o risco do usuário.

Selecionar entradas individuais expande uma janela de detalhes abaixo das detecções. A visualização de detalhes permite que os administradores investiguem e executem ações em cada detecção. 

![Exemplo relatório de proteção de identidade mostrando logins e detalhes arriscados](./media/howto-identity-protection-investigate-risk/identity-protection-risky-sign-ins-report.png)

## <a name="risky-users"></a>Usuários de risco

Com as informações fornecidas pelo relatório de usuários de risco, os administradores podem encontrar:

- Quais usuários estão em risco, tiveram o risco remediado ou tiveram o risco descartado?
- Detalhes sobre detecções
- Histórico de todos os sign-ins arriscados
- Histórico de risco
 
Os administradores podem então optar por agir sobre esses eventos. Os administradores podem optar por:

- Redefinir a senha do usuário
- Confirme o comprometimento do usuário
- Ignorar o risco de usuário
- Bloqueie o usuário de fazer login
- Investigue mais usando o Azure ATP

## <a name="risky-sign-ins"></a>Entradas de risco

O relatório de logins de risco contém dados filtrados por até os últimos 30 dias (1 mês).

Com as informações fornecidas pelo relatório de logins de risco, os administradores podem encontrar:

- Quais signos são classificados como de risco, confirmados comprometidos, confirmados seguros, descartados ou remediados.
- Níveis de risco em tempo real e agregados associados a tentativas de login.
- Tipos de detecção acionados
- Políticas de Acesso Condicional aplicadas
- Detalhes do MFA
- Informações do dispositivo
- Informações do aplicativo
- Informações de localização

Os administradores podem então optar por agir sobre esses eventos. Os administradores podem optar por:

- Confirme o compromisso de login
- Confirme o cofre de login

## <a name="risk-detections"></a>Detecções de risco

O relatório de detecções de risco contém dados filtrados por até os últimos 90 dias (3 meses).

Com as informações fornecidas pelo relatório de detecção de risco, os administradores podem encontrar:

- Informações sobre cada detecção de risco, incluindo o tipo.
- Outros riscos desencadeados ao mesmo tempo
- Local de tentativa de login
- Link para mais detalhes do Microsoft Cloud App Security (MCAS).

Os administradores podem, então, optar por retornar ao relatório de risco ou logins do usuário para tomar ações com base nas informações coletadas.

## <a name="next-steps"></a>Próximas etapas

- [Políticas disponíveis para mitigar riscos](concept-identity-protection-policies.md)

- [Habilite políticas de login e risco de usuário](howto-identity-protection-configure-risk-policies.md)
