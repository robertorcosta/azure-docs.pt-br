---
title: Investigação de riscos no Azure Active Directory Identity Protection
description: Saiba como investigar usuários, detecções e entradas suspeitos no Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3610fa5ea442e5ccb15255d8a1ddcee211871aeb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84463936"
---
# <a name="how-to-investigate-risk"></a>Como: Investigar risco

O Identity Protection fornece às organizações três relatórios que elas podem usar para investigar os riscos de identidade nos ambientes. Esses relatórios são os **usuários suspeitos**, as **entradas suspeitas** e as **detecções de risco**. A investigação de eventos é a chave para uma melhor compreensão e identificação dos pontos fracos em sua estratégia de segurança.

Todos os três relatórios permitem o download de eventos no formato CSV para análise adicional fora do portal do Azure. Os relatórios de usuários suspeitos e de entradas suspeitas permitem baixar as 2500 entradas mais recentes, e o relatório de detecções de risco permite baixar os 5000 registros mais recentes.

As organizações podem aproveitar as integrações da API do Microsoft Graph para agregar dados com outras fontes às quais podem ter acesso como uma organização.

Os três relatórios são encontrados no **portal do Azure** > **Azure Active Directory** > **Segurança**.

## <a name="navigating-the-reports"></a>Navegando nos relatórios

Cada relatório é iniciado com uma lista de todas as detecções do período que aparece na parte superior do relatório. Cada relatório permite a adição ou a remoção de colunas com base na preferência do administrador. Os administradores podem optar por baixar os dados no formato .CSV ou .JSON. Os relatórios podem ser filtrados usando-se os filtros na parte superior do relatório.

A seleção de entradas individuais pode habilitar entradas adicionais na parte superior do relatório, como a capacidade de confirmar que uma entrada é comprometida ou segura, confirmar que um usuário é comprometido ou ignorar o risco do usuário.

A seleção de entradas individuais expande uma janela de detalhes abaixo das detecções. A exibição de detalhes permite que os administradores investiguem e executem ações em cada detecção. 

![Exemplo de relatório do Identity Protection mostrando detalhes e entradas suspeitas](./media/howto-identity-protection-investigate-risk/identity-protection-risky-sign-ins-report.png)

## <a name="risky-users"></a>Usuários de risco

Com as informações fornecidas pelo relatório de usuários suspeitos, os administradores podem descobrir:

- Quais usuários estão em risco, tiveram o risco corrigido ou tiveram o risco ignorado?
- Detalhes sobre as detecções
- Histórico de todas as entradas suspeitas
- Histórico de risco
 
Depois, os administradores poderão optar por tomar medidas sobre esses eventos. Os administradores podem escolher:

- Redefinir a senha do usuário
- Confirmar usuário comprometido
- Ignorar o risco de usuário
- Bloquear a entrada de um usuário
- Investigar mais usando o ATP do Azure

## <a name="risky-sign-ins"></a>Entradas de risco

O relatório de entradas suspeitas contém dados filtráveis para até os últimos 30 dias (1 mês).

Com as informações fornecidas pelo relatório de entradas suspeitas, os administradores podem descobrir:

- Quais entradas são classificadas como em risco, comprometimento confirmado, segurança confirmada, ignoradas ou corrigidas.
- Níveis de risco agregados e em tempo real associados a tentativas de entrada.
- Tipos de detecção disparados
- Políticas de acesso condicional aplicadas
- Detalhes da Autenticação Multifator
- Informações do dispositivo
- Informações do aplicativo
- Informações de localização

Depois, os administradores poderão optar por tomar medidas sobre esses eventos. Os administradores podem escolher:

- Confirmar entrada comprometida
- Confirmar entrada segura

> [!NOTE] 
> O Identity Protection avalia o risco de todos os fluxos de autenticação, sejam eles interativos ou não. No entanto, o relatório de entradas mostra apenas as entradas interativas. Você poderá ver entradas suspeitas que ocorreram em entradas não interativas, mas a entrada não será mostrada no relatório de entradas do Azure Active Directory.

## <a name="risk-detections"></a>Detecções de risco

O relatório de detecções de risco contém dados filtráveis para até os últimos 90 dias (3 meses).

Com as informações fornecidas pelo relatório de detecções de risco, os administradores podem descobrir:

- Informações sobre cada detecção de risco, incluindo o tipo.
- Outros riscos disparados ao mesmo tempo
- Local da tentativa de entrada
- Link para mais detalhes da Microsoft Cloud App Security (MCAS).

Depois, os administradores poderão optar por retornar ao relatório de risco ou de entradas do usuário para executar ações com base nas informações coletadas.

## <a name="next-steps"></a>Próximas etapas

- [Políticas disponíveis para mitigar riscos](concept-identity-protection-policies.md)

- [Habilitar as políticas de risco com usuários e entradas](howto-identity-protection-configure-risk-policies.md)
