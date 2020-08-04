---
title: O que é Azure Active Directory Identity Protection?
description: Detectar, corrigir, investigar e analisar o risco com o Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 03/17/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80873b2e2655e7cedbafb526d0fe757eaa282312
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87019604"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>O que é Azure Active Directory Identity Protection?

Com a ferramenta Identity Protection, as organizações podem executar três tarefas importantes:

- Automatizar a detecção e a correção de riscos baseados em identidade.
- Investigar os riscos usando os dados no portal.
- Exportar os dados de detecção de riscos a utilitários de terceiros para análise adicional.

O Identity Protection usa os aprendizados que a Microsoft adquiriu com sua posição em organizações com o Azure AD, com o espaço do consumidor com as contas Microsoft e com os jogos do Xbox para proteger seus usuários. A Microsoft analisa 6,5 trilhões de sinais por dia para identificar e proteger os clientes contra ameaças.

Os sinais gerados pelo Identidade Protection e fornecidos a ele podem ser alimentados ainda mais em ferramentas como o Acesso Condicional para tomar decisões de acesso ou retroalimentar uma ferramenta SIEM (gerenciamento de evento e informações de segurança) para uma maior investigação com base nas políticas impostas por sua organização.

## <a name="why-is-automation-important"></a>Por que a automação é importante?

Em sua [postagem no blog em outubro de 2018](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Eight-essentials-for-hybrid-identity-3-Securing-your-identity/ba-p/275843), Alex Weinert, líder da equipe de Segurança e Proteção de Identidade da Microsoft, explica por que a automação é tão importante ao lidar com o volume de eventos:

> Todos os dias, nossos sistemas de aprendizado de máquina e heurística fornecem pontuações de risco para 18 bilhões de tentativas de logon para mais de 800 milhões de contas diferentes, das quais 300 milhões são perceptivelmente feitas por adversários (entidades como criminosos e hackers).
>
> No Ignite do ano passado, falei sobre os três principais ataques em nossos sistemas de identidade. Este é o volume recente desses ataques
>   
>   - **Repetição de violação**: 4,6 bilhões de ataques detectados em maio de 2018
>   - **Pulverização de senha**: 350 mil em abril de 2018
>   - **Phishing**: é difícil quantificar exatamente, mas vimos 23 milhões de eventos de risco em março de 2018, sendo que muitos deles estão relacionados a phishing

## <a name="risk-detection-and-remediation"></a>Detecção e correção de riscos

O Identity Protection identifica riscos nas seguintes classificações:

| Tipo de detecção de risco | Descrição |
| --- | --- |
| Viagem atípica | Entrada proveniente de uma localização atípica com base nas conexões recentes do usuário. |
| Endereço IP anônimo | Entrada de um endereço IP anônimo (por exemplo: navegador Tor, VPNs para anonimato). |
| Propriedades de entrada desconhecidas | Entrada com propriedades que não vimos recentemente para o usuário especificado. |
| Endereço IP vinculado a malware | Entrada de um endereço IP vinculado a malware |
| Credenciais vazadas | Essa detecção de risco indica que as credenciais válidas do usuário foram vazadas |
| Inteligência contra ameaças do Azure AD | As fontes internas e externas de inteligência contra ameaças da Microsoft identificaram um padrão de ataque conhecido |

Mais detalhes sobre esses riscos e como/quando são calculados podem ser encontrados no artigo, [O que é risco](concept-identity-protection-risks.md).

Os sinais de risco podem disparar esforços de remediação, como exigir que os usuários: executem a Autenticação Multifator do Azure, redefinam sua senha usando a redefinição de senha por autoatendimento ou bloqueiem até que um administrador executa uma ação.

## <a name="risk-investigation"></a>Investigação de riscos

Os administradores podem examinar as detecções e executar uma ação manual sobre elas, se necessário. Há três importantes relatórios que os administradores usam para investigações no Identity Protection:

- Usuários de risco
- Entradas de risco
- Detecções de risco

Mais informações podem ser encontradas no artigo, [Como investigar o risco](howto-identity-protection-investigate-risk.md).

## <a name="exporting-risk-data"></a>Exportar dados de risco

Os dados do Identity Protection podem ser exportados para outras ferramentas para serem arquivados, investigados ainda mais e correlacionados. As APIs baseadas no Microsoft Graph permitem que as organizações coletem esses dados para processamento adicional em uma ferramenta como o SIEM. Informações sobre como acessar a API do Identity Protection podem ser encontradas no artigo, [Introdução ao Azure Active Directory Identity Protection e ao Microsoft Graph](howto-identity-protection-graph-api.md)

Informações sobre integrar o Identity Protection ao Azure Sentinel podem ser encontradas no artigo, [Conectar dados do Azure AD Identity Protection](../../sentinel/connect-azure-ad-identity-protection.md).

## <a name="permissions"></a>Permissões

O Identity Protection requer que os usuários sejam um Leitor de Segurança, Operador de Segurança, Administrador da Segurança, Leitor Global ou Administrador Global para acessarem.

| Função | O que ele pode fazer | O que não pode fazer |
| --- | --- | --- |
| Administrador global | Acesso total à proteção de identidade |   |
| Administrador de segurança | Acesso total à proteção de identidade | Redefinir senha para um usuário |
| Operador de segurança | Exibir todos os relatórios da Proteção de Identidade e a folha de Visão Geral <br><br> Ignorar o risco do usuário, confirmar a entrada segura, confirmar o comprometimento | Configurar ou alterar políticas <br><br> Redefinir senha para um usuário <br><br> Configurar alertas |
| Leitor de segurança | Exibir todos os relatórios da Proteção de Identidade e a folha de Visão Geral | Configurar ou alterar políticas <br><br> Redefinir senha para um usuário <br><br> Configurar alertas <br><br> Fornecer comentários sobre as detecções |

Atualmente, a função de operador de segurança não pode acessar o relatório de entradas suspeitas.

Os administradores do Acesso Condicional também podem criar políticas que consideram o risco de entrada como uma condição; encontre mais informações no artigo [Acesso condicional: Condições](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk).

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

| Recurso | Detalhes | Azure AD Premium P2 | Azure AD Premium P1 | Azure AD Gratuito/Aplicativos do Office 365 |
| --- | --- | --- | --- | --- |
| Políticas de risco | Política de risco do usuário (por meio do Identity Protection) | Sim | Não | Não |
| Políticas de risco | Política de risco de entrada (por meio do Identity Protection ou do acesso condicional) | Sim | Não | Não |
| Relatórios de segurança | Visão geral | Sim | Não | Não |
| Relatórios de segurança | Usuários de risco | Acesso completo | Informações limitadas | Informações limitadas |
| Relatórios de segurança | Entradas de risco | Acesso completo | Informações limitadas | Informações limitadas |
| Relatórios de segurança | Detecções de risco | Acesso completo | Informações limitadas | Não |
| Notificações | Alertas de usuários em risco detectados | Sim | Não | Não |
| Notificações | Resumo semanal | Sim | Não | Não |
| | Política de registro de MFA | Sim | Não | Não |

## <a name="next-steps"></a>Próximas etapas

- [Visão geral de segurança](concept-identity-protection-security-overview.md)

- [O que é risco](concept-identity-protection-risks.md)

- [Políticas disponíveis para mitigar riscos](concept-identity-protection-policies.md)
