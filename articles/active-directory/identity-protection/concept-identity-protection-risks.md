---
title: O que é risco? Azure AD Identity Protection
description: Explicando o risco na Proteção de Identidade Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 775ff6b3ba003bed22ccd5a42cb4da005c4dbb69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253683"
---
# <a name="what-is-risk"></a>O que é risco?

As detecções de risco no Azure AD Identity Protection incluem quaisquer ações suspeitas identificadas relacionadas a contas de usuário no diretório.

A Identity Protection fornece às organizações acesso a recursos poderosos para ver e responder rapidamente a essas ações suspeitas. 

![Visão geral de segurança mostrando usuários arriscados e logins](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

## <a name="risk-types-and-detection"></a>Tipos de risco e detecção

Existem dois tipos de risco **Usuário** e **Login** e dois tipos de detecção ou cálculo **em tempo real** e **offline**.

### <a name="user-risk"></a>Risco do usuário

Um risco de usuário representa a probabilidade de que uma determinada identidade ou conta seja comprometida. 

Esses riscos são calculados offline usando as fontes de inteligência de ameaças internas e externas da Microsoft, incluindo pesquisadores de segurança, profissionais de aplicação da lei, equipes de segurança da Microsoft e outras fontes confiáveis.

| Detecção de risco | Descrição |
| --- | --- |
| Credenciais vazadas | Este tipo de detecção de risco indica que as credenciais válidas do usuário foram vazadas. Quando criminosos cibernéticos comprometem senhas válidas de usuários legítimos, geralmente compartilham essas credenciais. Esse compartilhamento é normalmente feito postando publicamente na dark web, colar sites ou negociando e vendendo as credenciais no mercado negro. Quando o serviço de credenciais vazadas da Microsoft adquire credenciais de usuário da dark web, sites de colar ou outras fontes, eles são verificados com as credenciais válidas atuais dos usuários do Azure AD para encontrar correspondências válidas. |
| Inteligência contra ameaças do Azure AD | Esse tipo de detecção de risco indica a atividade do usuário incomum para o determinado usuário ou é consistente com padrões de ataque conhecidos com base nas fontes de inteligência de ameaças internas e externas da Microsoft. |

### <a name="sign-in-risk"></a>Risco de entrada

Um risco de login representa a probabilidade de que uma determinada solicitação de autenticação não seja autorizada pelo proprietário da identidade. 

Esses riscos podem ser calculados em tempo real ou calculados offline usando as fontes de inteligência de ameaças internas e externas da Microsoft, incluindo pesquisadores de segurança, profissionais de aplicação da lei, equipes de segurança da Microsoft e outras fontes confiáveis.

| Detecção de risco | Tipo de detecção | Descrição |
| --- | --- | --- |
| Endereço IP anônimo | Tempo real | Esse tipo de detecção de risco indica logins de um endereço IP anônimo (por exemplo, navegador Tor ou VPN anônima). Normalmente, esses endereços IP são usados por atores que desejam ocultar sua telemetria de logon (endereço IP, localização, dispositivo etc.) para, potencialmente, más intenções. |
| Viagem atípica | Offline | Esse tipo de detecção de risco identifica dois logins originários de locais geograficamente distantes, onde pelo menos um dos locais também pode ser atípico para o usuário, dado o comportamento passado. Entre muitos outros fatores, esse algoritmo de aprendizado de máquina leva em consideração o tempo entre as duas entradas e o tempo que seria necessário para o usuário ir do primeiro até o segundo local, indicando que um usuário diferente está usando as mesmas credenciais. <br><br> Esse algoritmo ignora “falsos positivos” óbvios que contribuem para condições impossíveis de viagem, como VPNs e locais regularmente usados por outros usuários na organização. O sistema tem um período inicial de aprendizado dos primeiros 14 dias ou 10 logins, durante o qual aprende o comportamento de login de um novo usuário. |
| Endereço IP vinculado a malware | Offline | Esse tipo de detecção de risco indica logins de endereços IP infectados com malware que é conhecido por se comunicar ativamente com um servidor de bot. Essa detecção é determinada correlacionando endereços IP do dispositivo do usuário contra endereços IP que estavam em contato com um servidor bot enquanto o servidor bot estava ativo. |
| Propriedades de entrada desconhecidas | Tempo real | Este tipo de detecção de risco considera o histórico de login passado (IP, Latitude / Longitude e ASN) para procurar por logins anômalos. O sistema armazena informações sobre locais anteriores usados por um usuário e considera esses locais "familiares". A detecção de risco é desencadeada quando o login ocorre a partir de um local que ainda não está na lista de locais familiares. Os usuários recém-criados estarão em "modo de aprendizagem" por um período de tempo em que detecções de risco de entrada desconhecidas serão desligadas enquanto nossos algoritmos aprendem o comportamento do usuário. A duração do modo de aprendizagem é dinâmica e depende de quanto tempo o algoritmo leva para coletar informações suficientes sobre os padrões de login do usuário. A duração mínima é de cinco dias. Um usuário pode voltar ao modo de aprendizagem após um longo período de inatividade. O sistema também ignora entradas de dispositivos conhecidos e locais que são geograficamente próximos de uma localização familiar. <br><br> Também podemos executar essa detecção para a autenticação Básica (ou protocolos herdados). Como esses protocolos não têm propriedades modernas como a ID do cliente, há uma telemetria limitada para reduzir os falsos positivos. Recomendamos que nossos clientes mudem para a autenticação moderna. |
| O usuário confirmado pelo usuário confirmado | Offline | Essa detecção indica que um admin selecionou 'Confirmar usuário comprometido' na IA de usuários arriscados ou usando API de usuários arriscados. Para ver qual admin confirmou que esse usuário foi comprometido, verifique o histórico de risco do usuário (via UI ou API). |
| Endereço IP malicioso | Offline | Esta detecção indica o login de um endereço IP malicioso. Um endereço IP é considerado malicioso com base em altas taxas de falha devido às credenciais inválidas recebidas do endereço IP ou de outras fontes de reputação IP. |
| Regras de manipulação de caixa de entrada suspeita | Offline | Essa detecção é descoberta pelo [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules). Essa detecção analisa o ambiente e dispara alertas quando regras suspeitas que excluem ou movem mensagens ou pastas são definidas na caixa de entrada de um usuário. Isso pode indicar que a conta do usuário está comprometida, que as mensagens estão sendo intencionalmente ocultas e que a caixa de correio está sendo usada para distribuir spam ou malware em sua organização. |
| Viagem impossível | Offline | Essa detecção é descoberta pelo [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#impossible-travel). Essa detecção identifica duas atividades do usuário (é uma única ou várias sessões) provenientes de locais geograficamente distantes em um período de tempo menor que o tempo que teria levado para o usuário viajar do primeiro local para o segundo, indicando que um usuário diferente está usando as mesmas credenciais. |

### <a name="other-risk-detections"></a>Outras detecções de risco

| Detecção de risco | Tipo de detecção | Descrição |
| --- | --- | --- |
| Risco adicional detectado | Tempo real ou offline | Esta detecção indica que uma das detecções premium acima foi detectada. Uma vez que as detecções premium são visíveis apenas para os clientes Azure AD Premium P2, eles são intitulados "risco adicional detectado" para clientes sem licenças Azure AD Premium P2. |

## <a name="next-steps"></a>Próximas etapas

- [Políticas disponíveis para mitigar riscos](concept-identity-protection-policies.md)

- [Visão geral da segurança](concept-identity-protection-security-overview.md)
