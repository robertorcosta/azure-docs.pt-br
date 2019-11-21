---
title: Glossário do Azure Active Directory Identity Protection
description: Glossário do Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a3e2df956aaa4f9fd0af83dd2a18e04d731c714
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232354"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Glossário do Azure Active Directory Identity Protection

### <a name="at-risk-user"></a>Em risco (Usuário)
A user with one or more active risk detections. 

### <a name="atypical-sign-in-location"></a>Local de entrada atípico
Uma entrada de um local geográfico incomum para o usuário específico, usuários semelhantes ou o locatário.

### <a name="azure-ad-identity-protection"></a>Azure AD Identity Protection
A security module of Azure Active Directory that provides a consolidated view into risk detections and potential vulnerabilities affecting an organization’s identities.

### <a name="conditional-access"></a>Acesso condicional
Uma política para proteger o acesso aos recursos. Conditional Access rules are stored in the Azure Active Directory and are evaluated by Azure AD before granting access to the resource.  As regras de exemplo incluem restrição baseada na localização do usuário, integridade do dispositivo ou método de autenticação do usuário.

### <a name="credentials"></a>Credenciais
Informações que incluem a identificação e prova de identificação usadas para obter acesso ao local e aos recursos da rede. Exemplos de credenciais são nomes de usuário e senhas, cartões inteligentes e certificados.

### <a name="event"></a>Evento
Um registro de uma atividade no Azure Active Directory.

### <a name="false-positive-risk-detection"></a>False-positive (risk detection)
A risk detection status set manually by an Identity Protection user, indicating that the risk detection was investigated and was incorrectly flagged as a risk detection.

### <a name="identity"></a>Identidade
Uma pessoa ou entidade que deve ser verificada por meio de autenticação com base em critérios como senha ou certificado.

### <a name="identity-risk-detection"></a>Identity risk detection
Azure AD event that was flagged as anomalous by Identity Protection, and may indicate that an identity has been compromised.

### <a name="ignored-risk-detection"></a>Ignored (risk detection)
A risk detection status set manually by an Identity Protection user, indicating that the risk detection is closed without taking a remediation action.

### <a name="impossible-travel-from-atypical-locations"></a>Viagem impossível de locais atípicos
A risk detection triggered when two sign-ins for the same user are detected, where at least one of them is from an atypical sign-in location, and where the time between the sign-ins is shorter than the minimum time it would take to physically travel between these locations.  

### <a name="investigation"></a>Investigação
The process of reviewing the activities, logs, and other relevant information related to a risk detection to decide whether remediation or mitigation steps are necessary, understand if and how the identity was compromised, and understand how the compromised identity was used.

### <a name="leaked-credentials"></a>Credenciais vazadas
A risk detection triggered when current user credentials (user name and password) are found posted publicly in the Dark   web by our researchers.

### <a name="mitigation"></a>Redução
Uma ação que visa limitar ou eliminar a capacidade de um invasor explorar uma identidade ou um dispositivo comprometidos sem restaurá-los para um estado seguro. A mitigation does not resolve previous risk detections associated with the identity or device.

### <a name="multi-factor-authentication"></a>Autenticação multifator
Um método de autenticação que exige dois ou mais métodos de autenticação, que podem incluir algo que o usuário tem, como um certificado; algo que o usuário conhece, como nomes de usuário, senhas ou frases secretas; atributos físicos, como uma impressão digital; e atributos pessoais, como uma assinatura pessoal.

### <a name="offline-detection"></a>Detecção offline
A detecção de anomalias e a avaliação do risco de um evento, tal como uma tentativa de entrada após o ocorrido, para um evento que já aconteceu.

### <a name="policy-condition"></a>Condição da política
Uma parte de uma política de segurança que define as entidades (grupos, usuários, aplicativos, plataformas de dispositivos, Estados de dispositivo, intervalos de IP e tipos de cliente) incluídas na política ou excluídas dela.

### <a name="policy-rule"></a>Regra de política
The part of a security policy that describes the circumstances that would trigger the policy, and the actions taken when the policy is triggered.

### <a name="prevention"></a>Prevenção
Uma ação para evitar danos à organização por abuso de uma identidade ou dispositivo que sofreu comprometimento conhecido ou suspeito. A prevention action does not secure the device or identity, and does not resolve previous risk detections.

### <a name="privileged-user"></a>Privilegiado (usuário)
A user that at the time of a risk detection, had permanent or temporary admin permissions to one or more resources in Azure Active Directory, such as a Global Administrator, Billing Administrator, Service Administrator, User administrator, and Password Administrator. 

### <a name="real-time"></a>Tempo real
Consulte Detecção em tempo real.

### <a name="real-time-detection"></a>Detecção em tempo real.
A detecção de anomalias e a avaliação do risco de um evento, tal como uma tentativa de entrada antes de permitir que o evento prossiga.

### <a name="remediated-risk-detection"></a>Remediated (risk detection)
A risk detection status set automatically by Identity Protection, indicating that the risk detection was remediated using the standard remediation action for this type of risk detection. For example, when the user password is reset, many risk detections that indicate that the previous password was compromised are automatically remediated.

### <a name="remediation"></a>Correção
Uma ação que visa proteger uma identidade ou um dispositivo que sofreu comprometimento conhecido ou suspeito anteriormente. A remediation action restores the identity or device to a safe state, and resolves previous risk detections associated with the identity or device.

### <a name="resolved-risk-detection"></a>Resolved (risk detection)
A risk detection status set manually by an Identity Protection user, indicating that the user took an appropriate remediation action outside Identity Protection, and that the risk detection should be considered closed.

### <a name="risk-detection-status"></a>Risk detection status
A property of a risk detection, indicating whether the event is active, and if closed, the reason for closing it.

### <a name="risk-detection-type"></a>Tipo de detecção de risco
A category for the risk detection, indicating the type of anomaly that caused the event to be considered risky.

### <a name="risk-level-risk-detection"></a>Risk level (risk detection)
An indication (High, Medium, or Low) of the severity of the risk detection to help Identity Protection users prioritize the actions they take to reduce the risk to their organization. 

### <a name="risk-level-sign-in"></a>Nível de risco (entrada)
Uma indicação (Alta, Média ou Baixa) da probabilidade de que outra pessoa esteja tentando usar a identidade do usuário para uma entrada específica.

### <a name="risk-level-user-compromise"></a>Nível de risco (comprometimento do usuário)
Uma indicação (Alta, Média ou Baixa) da probabilidade de uma identidade ter sido comprometida.

### <a name="risk-level-vulnerability"></a>Nível de risco (vulnerabilidade)
Uma indicação (Alta, Média ou Baixa) da severidade da vulnerabilidade para ajudar os usuários do Identity Protection a priorizar as ações tomadas para reduzir o risco para a organização.

### <a name="secure-identity"></a>Proteger (identidade)
Realizar uma ação de correção como uma alteração de senha ou reimplantação da imagem no computador para restaurar uma identidade potencialmente comprometida para um estado não comprometido.

### <a name="security-policy"></a>Política de segurança
Uma coleção de regras e condição da política. Uma política pode ser aplicada a entidades como usuários, grupos, aplicativos, dispositivos, plataformas de dispositivos, estados de dispositivo, intervalos de IP e tipos de cliente do Auth2.0. Quando uma política está habilitada, ela é avaliada sempre que um token para um recurso é emitido para uma entidade incluída na política.

### <a name="sign-in-v"></a>Entrar (v)
Autenticar-se em uma identidade no Azure Active Directory.

### <a name="sign-in-n"></a>Entrada (n)
O processo ou a ação de autenticar uma identidade no Azure Active Directory e o evento que captura essa operação.

### <a name="sign-in-from-anonymous-ip-address"></a>Entrada de um endereço IP anônimo
A risk detection triggered after a successful sign-in from IP address that has been identified as an anonymous proxy IP address.

### <a name="sign-in-from-infected-device"></a>Entrada de um dispositivo infectado
A risk detection triggered when a sign-in originates from an IP address, which is known to be used by one or more compromised devices, which are actively attempting to communicate with a bot server.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>Entrada de um endereço IP com atividade suspeita
A risk detection triggered after a successful sign-in from an IP address with a high number of failed login attempts across multiple user accounts over a short period of time.

### <a name="sign-in-from-unfamiliar-location"></a>Entrada de um local desconhecido
A risk detection triggered when a user successfully signs in from a new location (IP, Latitude/Longitude, and ASN).

### <a name="sign-in-risk"></a>Risco de entrada
Consulte Nível de risco (entrada)

### <a name="sign-in-risk-policy"></a>Política de risco de entrada
A Conditional Access policy that evaluates the risk to a specific sign-in and applies mitigations based on predefined conditions and rules.

### <a name="user-compromise-risk"></a>Risco de comprometimento do usuário
Consulte Nível de risco (comprometimento do usuário)

### <a name="user-risk"></a>Risco do usuário
Consulte Nível de risco (comprometimento do usuário).

### <a name="user-risk-policy"></a>Política de risco do usuário
A Conditional Access policy that considers the sign-in and applies mitigations based on predefined conditions and rules.

### <a name="users-flagged-for-risk"></a>Usuários sinalizados por risco
Users that have risk detections, which are either active or remediated

### <a name="vulnerability"></a>Vulnerabilidade
Uma configuração ou condição no Azure Active Directory que torna o diretório suscetível a vulnerabilidades ou ameaças.

## <a name="see-also"></a>Consulte

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
