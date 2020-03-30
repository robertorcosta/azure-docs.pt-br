---
title: 'Azure AD Connect: msExchUserHoldPolicies e cloudMsExchUserHoldPolicies | Microsoft Docs'
description: Este tópico descreve o comportamento de atributos dos atributos msExchUserHoldPolicies e cloudMsExchUserHoldPolicies atributos
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/23/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4c637a01825616334cda8faa594efd08f29de8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74213088"
---
# <a name="azure-ad-connect---msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>Azure AD Connect - msExchUserHoldPolicies e cloudMsExchUserHoldPolicies
O documento de referência a seguir descreve esses atributos usados pelo Exchange e a maneira adequada de editar as regras de sincronização padrão.

## <a name="what-are-msexchuserholdpolicies-and-cloudmsexchuserholdpolicies"></a>O que são msExchUserHoldPolicies e cloudMsExchUserHoldPolicies?
Existem dois tipos de [sossemos](https://docs.microsoft.com/Exchange/policy-and-compliance/holds/holds?view=exchserver-2019) disponíveis para um Servidor de Intercâmbio: Contencioso Hold e In-Place Hold. Quando o Contencioso É habilitado, todos os itens da caixa de correio são colocados em espera.  Um Hold no lugar é usado para preservar apenas os itens que atendem aos critérios de uma consulta de pesquisa que você definiu usando a ferramenta In-Place eDiscovery.

Os atributos MsExchUserHoldPolcies e cloudMsExchUserHoldPolicies permitem que os AD e o Azure AD no local determinem quais usuários estão sob controle, dependendo se eles estão usando o Exchange ou o Exchange on-line no local.

## <a name="msexchuserholdpolicies-synchronization-flow"></a>msExchUserHoldPolicies fluxo de sincronização
Por padrão, o MsExchUserHoldPolcies é sincronizado pelo Azure AD Connect diretamente ao atributo msExchUserHoldPolicies no metaverso e, em seguida, ao atributo msExchUserHoldPolices no Azure AD

As tabelas a seguir descrevem o fluxo:

Entrada do Diretório Ativo no local:

|Atributo do Diretório Ativo|Nome do atributo|Tipo de fluxo|Atributo metaverso|Regra de sincronização|
|-----|-----|-----|-----|-----|
|Active Directory local|msExchUserHoldPolicies|Direto|mSExchUserHoldPolices|In from AD - User Exchange|

Saída para Azure AD:

|Atributo metaverso|Nome do atributo|Tipo de fluxo|Atributo do Azure AD|Regra de sincronização|
|-----|-----|-----|-----|-----|
|Azure Active Directory|msExchUserHoldPolicies|Direto|msExchUserHoldPolicies|Out to AAD – UserExchangeOnline|

## <a name="cloudmsexchuserholdpolicies-synchronization-flow"></a>cloudMsExchUserHoldPolicies fluxo de sincronização
Por nuvem padrão, ExchUserHoldPolicies é sincronizado pelo Azure AD Connect diretamente ao atributo cloudMsExchUserHoldPolicies no metaverso. Em seguida, se msExchUserHoldPolices não for nulo no metaverso, o atributo fluiu para o Active Directory.

As tabelas a seguir descrevem o fluxo:

Entrada do Azure AD:

|Atributo do Diretório Ativo|Nome do atributo|Tipo de fluxo|Atributo metaverso|Regra de sincronização|
|-----|-----|-----|-----|-----|
|Active Directory local|cloudMsExchUserHoldPolicies|Direto|cloudMsExchUserHoldPolicies|In from AAD - User Exchange|

Saída para o Diretório Ativo no local:

|Atributo metaverso|Nome do atributo|Tipo de fluxo|Atributo do Azure AD|Regra de sincronização|
|-----|-----|-----|-----|-----|
|Azure Active Directory|cloudMsExchUserHoldPolicies|IF (NÃO NULO)|msExchUserHoldPolicies|Out to AD – UserExchangeOnline|

## <a name="information-on-the-attribute-behavior"></a>Informações sobre o comportamento do atributo
O msExchangeUserHoldPolicies é um único atributo de autoridade.  Um único atributo de autoridade pode ser definido em um objeto (neste caso, objeto do usuário) no diretório local ou no diretório na nuvem.  As regras de Início de Autoridade ditam que, se o atributo estiver sincronizado a partir do local, o Azure AD não poderá atualizar esse atributo.

Para permitir que os usuários definam uma política de retida em um objeto de usuário na nuvem, o atributo cloudMSExchangeUserHoldPolicies é usado. Esse atributo é usado porque o Azure AD não pode definir msExchangeUserHoldPolicies diretamente com base nas regras explicadas acima.  Esse atributo sincronizará novamente para o diretório local se, o msExchangeUserHoldPolicies não for nulo e substituir o valor atual do msExchangeUserHoldPolicies.

Em certas circunstâncias, por exemplo, se ambos foram alterados no local e no Azure ao mesmo tempo, isso poderia causar alguns problemas.  

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [a integração de suas identidades no local com o Azure Active Directory](whatis-hybrid-identity.md).
