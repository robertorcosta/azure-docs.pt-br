---
title: O que é o diagnóstico de entrada do Azure AD? | Microsoft Docs
description: Fornece uma visão geral do diagnóstico de entrada do Azure AD.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 12/15/2020
ms.author: markvi
ms.reviewer: tspring
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6aedf41fbf1ed0d70467a2efe97431fdecaa4fa
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585898"
---
# <a name="what-is-sign-in-diagnostic-in-azure-ad"></a>O que é o diagnóstico de entrada do Azure AD?

O Azure AD fornece um modelo de segurança flexível para controlar o que os usuários podem fazer com os recursos gerenciados. O acesso a esses recursos é controlado por **quem** você é e por **como** você os acessa. Normalmente, a flexibilidade vem junto com um certo grau de complexidade devido ao número de opções de configuração disponíveis. A complexidade tem o potencial de aumentar o risco de erros.

Como administrador de TI, você precisa encontrar uma solução que forneça o nível certo de insights sobre as atividades do sistema, de modo que possa diagnosticar e resolver problemas com facilidade quando eles ocorrerem. O diagnóstico de entrada do Azure AD é um exemplo dessa solução. Use o diagnóstico para analisar o que aconteceu durante uma entrada e quais ações você pode executar para resolver problemas sem precisar envolver o Suporte da Microsoft.

Este artigo fornece uma visão geral do que a solução faz e como você pode usá-la.


## <a name="requirements"></a>Requisitos

O diagnóstico de entrada está disponível em todas as edições do Azure AD.<br> Você precisa ser um Administrador global no Azure AD para usá-lo.

## <a name="how-it-works"></a>Como ele funciona

No Azure AD, a resposta a uma tentativa de entrada está vinculada a **quem** você é e **como** você acessa seu locatário. Por exemplo, como administrador, normalmente, você pode configurar todos os aspectos do seu locatário quando entra na rede corporativa. No entanto, você pode ser, até mesmo, bloqueado quando se conecta com a mesma conta em uma rede não confiável.
 
Devido à maior flexibilidade do sistema para responder a uma tentativa de entrada, você poderá terminar em cenários em que precise solucionar problemas de entradas. O diagnóstico de entrada é um recurso que:

- Analisa dados de entradas. 

- Exibe o que aconteceu e as recomendações sobre como resolver problemas. 

O diagnóstico de entrada para o Azure AD foi projetado para habilitar o autodiagnóstico de erros de entrada. Para concluir o processo de diagnóstico, você precisará:

![Processo de diagnóstico de entrada](./media/overview-sign-in-diagnostics/process.png)
 
1. **Definir** o escopo dos eventos de entrada de interesse

2. **Selecionar** a entrada que deseja examinar

3. **Examinar** o resultado do diagnóstico

4. **Executar** ações

 
### <a name="define-scope"></a>Definir o escopo

A meta desta etapa é definir o escopo para as entradas que deseja investigar. Seu escopo é baseado em um usuário ou um identificador (correlationId, requestId) e um intervalo de tempo. Para restringir ainda mais o escopo, você também pode especificar um nome de aplicativo. O Azure AD usa as informações de escopo para localizar os eventos certos para você.  

### <a name="select-sign-in"></a>Selecionar a entrada  

Com base nos critérios de pesquisa, o Azure AD recupera todas as entradas correspondentes e as apresenta em uma exibição de lista de resumo de autenticação. 

![Resumo de autenticação](./media/overview-sign-in-diagnostics/authentication-summary.png)
 
Você pode personalizar as colunas mostradas nessa exibição.

### <a name="review-diagnostic"></a>Examinar o diagnóstico 

Para o evento de entrada selecionado, o Azure AD fornece um resultado de diagnóstico. 

![Resultados de diagnóstico](./media/overview-sign-in-diagnostics/diagnostics-results.png)

 
O resultado começa com uma avaliação. A avaliação explica em algumas frases o que aconteceu. A explicação ajuda você a entender o comportamento do sistema. 

Como uma próxima etapa, você obtém um resumo das políticas de acesso condicional relacionadas que foram aplicadas à entrada selecionada. Essa parte é concluída por etapas de correção recomendadas para resolver o problema. Como nem sempre é possível resolver problemas sem ajuda adicional, uma etapa recomendada pode ser a abertura de um tíquete de suporte. 

### <a name="take-action"></a>Executar ação 
Neste ponto, você deverá ter as informações necessárias para corrigir o problema.


## <a name="scenarios"></a>Cenários

Esta seção fornece uma visão geral dos cenários de diagnóstico abordados. Os seguintes cenários são implementados: 
 
- Bloqueado pelo acesso condicional

- Acesso condicional com falha

- MFA por meio do acesso condicional

- MFA por meio de outros requisitos

- Verificação da MFA necessária

- A Verificação da MFA é necessária, mas a tentativa de entrada do usuário não é proveniente de uma localização segura

- Entrada bem-sucedida


### <a name="blocked-by-conditional-access"></a>Bloqueado pelo acesso condicional

Esse cenário se baseia em uma entrada que foi bloqueada por uma política de acesso condicional.

![Acesso bloqueado](./media/overview-sign-in-diagnostics/block-access.png)

A seção de diagnóstico deste cenário mostra os detalhes sobre a entrada do usuário e as políticas aplicadas.


### <a name="failed-conditional-access"></a>Acesso condicional com falha

Esse cenário normalmente é um resultado de uma entrada com falha devido aos requisitos de uma política de acesso condicional não serem atendidos. Alguns exemplos comuns são:

![Exigir controles](./media/overview-sign-in-diagnostics/require-controls.png)

- Exigir um dispositivo ingressado no Azure AD híbrido

- Exigir um aplicativo cliente aprovado

- Requer política de proteção do aplicativo   


A seção de diagnóstico deste cenário mostra os detalhes sobre a entrada do usuário e as políticas aplicadas.


### <a name="mfa-from-conditional-access"></a>MFA por meio do acesso condicional

Esse cenário se baseia em uma política de acesso condicional que tem o requisito de entrada usando a autenticação multifator definida.

![Exigir autenticação multifator](./media/overview-sign-in-diagnostics/require-mfa.png)

A seção de diagnóstico deste cenário mostra os detalhes sobre a entrada do usuário e as políticas aplicadas.



### <a name="mfa-from-other-requirements"></a>MFA por meio de outros requisitos

Esse cenário se baseia em um requisito de autenticação multifator que não foi imposto por uma política de acesso condicional. Por exemplo, a autenticação multifator por usuário.


![Exigir a autenticação multifator por usuário](./media/overview-sign-in-diagnostics/mfa-per-user.png)


A intenção deste cenário de diagnóstico é fornecer mais detalhes sobre:

- A origem da interrupção da autenticação multifator. 
- O resultado da interação do cliente.

Além disso, esta seção fornece todos os detalhes sobre a tentativa de entrada do usuário. 


### <a name="mfa-proof-up-required"></a>Verificação da MFA necessária

Esse cenário se baseia em entradas que foram interrompidas por solicitações para configurar a autenticação multifator. Essa configuração também é conhecida como “verificação”.

A verificação da autenticação multifator ocorre quando um usuário precisa usar a autenticação multifator, mas ainda não a configurou, ou um administrador configurou o usuário para configurá-la.

A intenção desse cenário de diagnóstico é fornecer o insight de que a interrupção da autenticação multifator se destinava a configurá-la e fornecer a recomendação para que o usuário conclua a verificação.

### <a name="mfa-proof-up-required-from-a-risky-sign-in"></a>Verificação da MFA necessária em uma entrada suspeita

Esse cenário é o resultado de entradas que foram interrompidas por uma solicitação para configurar a autenticação multifator em um logon suspeito. 

A intenção desse cenário de diagnóstico é fornecer o insight de que a interrupção da autenticação multifator se destinava a configurá-la e fornecer a recomendação para que o usuário conclua a verificação, mas faça isso em uma localização de rede que não pareça suspeita. Por exemplo, se uma rede corporativa for definida como uma tentativa de localização nomeada para fazer a verificação na rede corporativa.


### <a name="successful-sign-in"></a>Entrada bem-sucedida

Esse cenário se baseia em entradas que não foram interrompidas pelo acesso condicional nem pela autenticação multifator.

A intenção desse cenário de diagnóstico é fornecer o insight sobre o que o usuário forneceu durante a entrada, caso haja uma política de acesso condicional ou políticas que precisem ser aplicadas ou uma autenticação multifator configurada que precise interromper a entrada do usuário.



## <a name="next-steps"></a>Próximas etapas

* [O que são os relatórios do Azure Active Directory?](overview-reports.md)
* [O que é o monitoramento do Azure Active Directory?](overview-monitoring.md)
