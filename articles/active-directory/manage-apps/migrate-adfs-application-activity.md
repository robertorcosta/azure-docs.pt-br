---
title: Use o relatório de atividades para mover aplicativos AD FS para o Azure Active Directory | Microsoft Docs
description: O relatório de atividades de aplicativos do Active Directory Federation Services (AD FS) permite migrar rapidamente os aplicativos do AD FS para o Azure Active Directory (Azure AD). Esta ferramenta de migração para AD FS identifica compatibilidade com o Azure AD e fornece orientação de migração.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/14/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 333e440fdd5f5062dda45fb12a83543c63e66c04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978028"
---
# <a name="use-the-ad-fs-application-activity-report-preview-to-migrate-applications-to-azure-ad"></a>Use o relatório de atividade de aplicativos Do AD FS (preview) para migrar aplicativos para o Azure AD

Muitas organizações usam os Serviços de Federação de Diretórios Ativos (AD FS) para fornecer o login único em aplicativos em nuvem. Existem benefícios significativos para mover seus aplicativos AD FS para o Azure AD para autenticação, especialmente em termos de gerenciamento de custos, gerenciamento de riscos, produtividade, conformidade e governança. Mas entender quais aplicativos são compatíveis com o Azure AD e identificar etapas específicas de migração pode ser demorado.

O relatório de atividade de aplicativos Do AD FS (preview) no portal Azure permite identificar rapidamente quais de seus aplicativos são capazes de serem migrados para o Azure AD. Ele avalia todos os aplicativos AD FS para compatibilidade com o Azure AD, verifica quaisquer problemas e dá orientações sobre a preparação de aplicativos individuais para migração. Com o relatório de atividade de aplicativo AD FS, você pode:

* **Descubra aplicativos AD FS e escopo sua migração.** O relatório de atividade de aplicativos do AD FS lista todos os aplicativos AD FS em sua organização e indica sua prontidão para migração para o Azure AD.
* **Priorizar aplicações para migração.** Obtenha o número de usuários únicos que fizeram o acesso ao aplicativo nos últimos 1, 7 ou 30 dias para ajudar a determinar a criticidade ou o risco de migrar o aplicativo.
* **Execute testes de migração e corrija problemas.** O serviço de emissão de relatórios executa automaticamente testes para determinar se um aplicativo está pronto para migrar. Os resultados são exibidos no relatório de atividade do aplicativo AD FS como um status de migração. Se possíveis problemas de migração forem identificados, você receberá orientações específicas sobre como lidar com os problemas.

Os dados de atividade do aplicativo AD FS estão disponíveis para usuários que são atribuídos a qualquer uma dessas funções de administração: administrador global, leitor de relatórios, leitor de segurança, administrador de aplicativos ou administrador de aplicativos na nuvem.

## <a name="prerequisites"></a>Pré-requisitos

* Sua organização deve estar usando AD FS para acessar aplicativos.
* O Azure AD Connect Health deve ser habilitado no seu inquilino Azure AD.
   * [Saiba mais sobre o Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)
   * [Comece a configurar o Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>Descubra aplicativos AD FS que podem ser migrados 

O relatório de atividades do aplicativo AD FS está disponível no portal Azure **o azure** AD Usage & relatórios de insights. O relatório de atividade do aplicativo AD FS analisa cada aplicativo AD FS para determinar se ele pode ser migrado como está ou se é necessária uma revisão adicional. 

1. Faça login no [portal Dozure](https://portal.azure.com) com uma função de administrador que tenha acesso aos dados de atividade do aplicativo AD FS (administrador global, leitor de relatórios, leitor de segurança, administrador de aplicativos ou administrador de aplicativos na nuvem).

2. Selecione **o Azure Active Directory**e selecione **aplicativos Corporativos**.

3. Em **Atividade,** **selecione Use & Insights (Preview)** e selecione **a atividade de aplicativos AD FS** para abrir uma lista de todos os aplicativos AD FS em sua organização.

   ![Atividade de aplicativo AD FS](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. Para cada aplicativo na lista de atividades do aplicativo AD FS, exibir o **status Migração**:

   * **Pronto para migrar** significa que a configuração do aplicativo AD FS é totalmente suportada no Azure AD e pode ser migrada como está.

   * **A revisão** das necessidades significa que algumas das configurações do aplicativo podem ser migradas para o Azure AD, mas você precisará rever as configurações que não podem ser migradas como está.

   * **Etapas adicionais necessárias** significam que o Azure AD não suporta algumas das configurações do aplicativo, de modo que o aplicativo não pode ser migrado em seu estado atual.

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>Avalie a prontidão de um aplicativo para migração 

1. Na lista de atividades do aplicativo AD FS, clique no status na coluna **de status Migração** para abrir detalhes da migração. Você verá um resumo dos testes de configuração que passaram, juntamente com quaisquer possíveis problemas de migração.

   ![Detalhes da migração](media/migrate-adfs-application-activity/migration-details.png)

2. Clique em uma mensagem para abrir detalhes adicionais da regra de migração. Para obter uma lista completa das propriedades testadas, consulte a tabela [de testes de configuração do aplicativo AD FS,](#ad-fs-application-configuration-tests) abaixo.

   ![Detalhes da regra de migração](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>Testes de configuração de aplicativos AD FS

A tabela a seguir lista todos os testes de configuração que são realizados em aplicativos AD FS.

|Result  |Passe/Aviso/Falha  |Descrição  |
|---------|---------|---------|
|Test-ADFSRPRegras de autenticação adicional <br> Pelo menos uma regra não migratória foi detectada para autenticação adicional.       | Passe/Aviso          | A parte que confia tem regras para solicitar a autenticação multifatorial (MFA). Para mudar para o Azure AD, traduza essas regras em políticas de acesso condicional. Se você estiver usando um MFA no local, recomendamos que você se mude para o Azure MFA. [Saiba mais sobre o Acesso Condicional](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).        |
|Teste-ADFSRPAdditionalWSFedEndpoint <br> O partido de confiar tem O Ponto AdicionalWSFedEndpoint definido como verdadeiro.       | Aprovado/Reprovado          | O partido que confia no AD FS permite vários pontos finais de afirmação ws-fed.Atualmente, o Azure AD suporta apenas um.Se você tem um cenário onde este resultado está bloqueando a migração, [avise-nos](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints).     |
|Teste-ADFSRPAllowedAuthenticationClassReferences <br> O Grupo de Confiam definiu AllowedAuthenticationClassReferences.       | Aprovado/Reprovado          | Esta configuração no AD FS permite especificar se o aplicativo está configurado para permitir apenas certos tipos de autenticação. Recomendamos o uso do Acesso Condicional para alcançar esse recurso. Se você tem um cenário onde este resultado está bloqueando a migração, [avise-nos](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication).  [Saiba mais sobre o Acesso Condicional](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).          |
|Teste-ADFSRPSempre requerem autenticação <br> AlwaysRequireAuthenticationCheckResult      | Aprovado/Reprovado          | Esta configuração no AD FS permite que você especifique se o aplicativo está configurado para ignorar cookies SSO e **Sempre Solicitar para Autenticação**. No Azure AD, você pode gerenciar a sessão de autenticação usando políticas de Acesso Condicional para alcançar um comportamento semelhante. [Saiba mais sobre a configuração do gerenciamento da sessão de autenticação com o Conditional Access](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).          |
|Atualização automática do Teste-ADFSRPHabilitado <br> Relying Party tem autoupdateEnabled set to true       | Passe/Aviso          | Essa configuração no AD FS permite especificar se o AD FS está configurado para atualizar automaticamente o aplicativo com base em alterações nos metadados da federação. O Azure AD não suporta isso hoje, mas não deve bloquear a migração do aplicativo para o Azure AD.           |
|Test-ADFSRPClaimsProviderName <br> O Grupo de Confiança tem vários provedores de sinistros ativados       | Aprovado/Reprovado          | Esta configuração no AD FS chama os provedores de identidade dos quais a parte que confia está aceitando reivindicações. No Azure AD, você pode habilitar a colaboração externa usando o Azure AD B2B. [Saiba mais sobre o Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b).          |
|Regras de autorização de delegação do Teste-ADFSRP      | Aprovado/Reprovado          | O aplicativo tem regras de autorização de delegação personalizadas definidas. Este é um conceito WS-Trust que o Azure AD suporta usando protocolos de autenticação modernos, como OpenID Connect e OAuth 2.0. [Saiba mais sobre a Plataforma de Identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc).          |
|Regras de autorização de imitação do Teste-ADFSRP       | Passe/Aviso          | O aplicativo tem regras de autorização de imitação personalizadas definidas.Este é um conceito WS-Trust que o Azure AD suporta usando protocolos de autenticação modernos, como OpenID Connect e OAuth 2.0. [Saiba mais sobre a Plataforma de Identidade da Microsoft](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc).          |
|Regras de autorização de emissão de Teste-ADFSRP <br> Pelo menos uma regra não migratória foi detectada para autorização de emissão.       | Passe/Aviso          | O aplicativo tem regras de autorização de emissão personalizadas definidas no AD FS.O Azure AD suporta essa funcionalidade com o Azure AD Conditional Access. [Saiba mais sobre o Acesso Condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/overview). <br> Você também pode restringir o acesso a um aplicativo por usuário ou grupos atribuídos ao aplicativo. [Saiba mais sobre como atribuir usuários e grupos para acessar aplicativos](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups).            |
|Test-ADFSRPEeçõesdetransformaregras <br> Pelo menos uma regra não migratória foi detectada para emissãoTransform.       | Passe/Aviso          | O aplicativo tem regras de transformação de emissão personalizadas definidas no AD FS. O Azure AD suporta personalizar as reivindicações emitidas no token. Para saber mais, consulte [Personalizar reivindicações emitidas no token SAML para aplicativos corporativos](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).           |
|Test-ADFSRPMonitoringEnabled <br> O Partido De Relying tem monitoramentoativado definido como verdadeiro.       | Passe/Aviso          | Essa configuração no AD FS permite especificar se o AD FS está configurado para atualizar automaticamente o aplicativo com base em alterações nos metadados da federação. O Azure AD não suporta isso hoje, mas não deve bloquear a migração do aplicativo para o Azure AD.           |
|Teste-ADFSRPNotBeforeSkew <br> NotBeforeSkewCheckResult      | Passe/Aviso          | O AD FS permite uma distorção de tempo com base nos tempos NotBefore e NotOnOrAfter no token SAML. O Azure AD lida com isso automaticamente por padrão.          |
|Test-ADFSRPRequestMFAFromClaimsProviders <br> O Partido De Suma, solicita que o RequestMFAFromClaimsProviders seja definido como verdadeiro.       | Passe/Aviso          | Essa configuração no AD FS determina o comportamento do MFA quando o usuário vem de um provedor de sinistros diferente. No Azure AD, você pode habilitar a colaboração externa usando o Azure AD B2B. Em seguida, você pode aplicar políticas de acesso condicional para proteger o acesso dos hóspedes. Saiba mais sobre [o Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) e [o Conditional Access](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).          |
|Teste-ADFSRPSignedSamlRequestsRequired <br> A Parte Que Confia tem pedidos assinadosSamlRequired set to true       | Aprovado/Reprovado          | O aplicativo é configurado no AD FS para verificar a assinatura na solicitação SAML. A Azure AD aceita uma solicitação saml assinada; no entanto, não verificará a assinatura. O Azure AD tem diferentes métodos para proteger contra chamadas maliciosas. Por exemplo, o Azure AD usa as URLs de resposta configuradas no aplicativo para validar a solicitação SAML. O Azure AD só enviará um token para responder URLs configurados para o aplicativo. Se você tem um cenário onde este resultado está bloqueando a migração, [avise-nos](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature).          |
|Test-ADFSRPTokenLifetime <br> TokenLifetimeCheckResult        | Passe/Aviso         | O aplicativo é configurado para uma vida útil de token personalizada. O padrão do AD FS é de uma hora.O Azure AD suporta essa funcionalidade usando o Conditional Access. Para saber mais, consulte [Configurar o gerenciamento da sessão de autenticação com o Conditional Access](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).          |
|O Partido De Suma, está definido para criptografar reivindicações. Isso é suportado pelo Azure AD       | Aprovado          | Com o Azure AD, você pode criptografar o token enviado para o aplicativo. Para saber mais, consulte [Configure Azure AD SAML token encryption](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).          |
|Nome criptografadoIdRequiredCheckResult      | Aprovado/Reprovado          | O aplicativo é configurado para criptografar a reivindicação de nameID no token SAML.Com o Azure AD, você pode criptografar todo o token enviado para o aplicativo.A criptografia de reivindicações específicas ainda não é suportada. Para saber mais, consulte [Configure Azure AD SAML token encryption](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).         |

## <a name="check-the-results-of-claim-rule-tests"></a>Confira os resultados dos testes de regra de sinistro

Se você configurou uma regra de reclamação para o aplicativo no AD FS, a experiência fornecerá uma análise granular para todas as regras de sinistro. Você verá quais regras de reivindicação podem ser movidas para o Azure AD e quais precisam de mais revisão.

1. Na lista de atividades do aplicativo AD FS, clique no status na coluna **de status Migração** para abrir detalhes da migração. Você verá um resumo dos testes de configuração que passaram, juntamente com quaisquer possíveis problemas de migração.

2. Na página detalhes da **regra Migração,** expanda os resultados para exibir detalhes sobre possíveis problemas de migração e para obter orientação adicional. Para obter uma lista detalhada de todas as regras de sinistro testadas, consulte a tabela [De verificar os resultados da tabela de testes de regra de sinistro,](#check-the-results-of-claim-rule-tests) abaixo.

   O exemplo abaixo mostra os detalhes da regra de migração para a regra EmissãoTransform. Ele lista as partes específicas da reivindicação que precisam ser revisadas e endereçadas antes que você possa migrar o aplicativo para o Azure AD.

   ![Regra de migração detalha orientação adicional](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>Testes de regra de reivindicação

A tabela a seguir lista todos os testes de regra de reivindicação que são realizados em aplicativos AD FS.

|Propriedade  |Descrição  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | A declaração de condição usa Expressões Regulares para avaliar se a reivindicação corresponde a um determinado padrão.Para obter uma funcionalidade semelhante no Azure AD, você pode usar transformações pré-definidas como IfEmpty(), StartWith(), Contains(), entre outros. Para obter mais informações, consulte [Personalizar reclamações emitidas no token SAML para aplicativos corporativos](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|UNSUPPORTED_CONDITION_CLASS      | A declaração de condição tem várias condições que precisam ser avaliadas antes de executar a declaração de emissão.O Azure AD pode suportar essa funcionalidade com as funções de transformação da reivindicação, onde você pode avaliar vários valores de reivindicação.Para obter mais informações, consulte [Personalizar reclamações emitidas no token SAML para aplicativos corporativos](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|UNSUPPORTED_RULE_TYPE      | A regra da reivindicação não pôde ser reconhecida. Para obter mais informações sobre como configurar reclamações no Azure AD, consulte [Personalizar reivindicações emitidas no token SAML para aplicativos corporativos](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | A declaração de condição usa um Emissor que não é suportado no Azure AD.Atualmente, o Azure AD não fornece reivindicações de lojas diferentes que o Active Directory ou o Azure AD. Se isso está bloqueando você de migrar aplicativos para o Azure AD, [avise-nos](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).         |
|UNSUPPORTED_CONDITION_FUNCTION      | A declaração de condição usa uma função agregada para emitir ou adicionar uma única reclamação, independentemente do número de correspondências.No Azure AD, você pode avaliar o atributo de um usuário para decidir qual valor usar para a reivindicação com funções como IfEmpty(), StartWith(), Contains(), entre outras.Para obter mais informações, consulte [Personalizar reclamações emitidas no token SAML para aplicativos corporativos](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|RESTRICTED_CLAIM_ISSUED      | A declaração de condição usa uma reivindicação que é restrita no Azure AD. Você pode ser capaz de emitir uma reclamação restrita, mas não pode modificar sua fonte ou aplicar qualquer transformação. Para obter mais informações, consulte [Personalizar reivindicações emitidas em tokens para um aplicativo específico no Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).          |
|EXTERNAL_ATTRIBUTE_STORE      | A declaração de emissão usa um armazenamento de atributos diferente do Active Directory. Atualmente, o Azure AD não fornece reivindicações de lojas diferentes que o Active Directory ou o Azure AD. Se este resultado estiver bloqueando você de migrar aplicativos para o Azure AD, [avise-nos](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).          |
|UNSUPPORTED_ISSUANCE_CLASS      | A declaração de emissão usa ADD para adicionar reivindicações ao conjunto de reclamações recebidas. No Azure AD, isso pode ser configurado como várias transformações de sinistros.Para obter mais informações, consulte [Personalizar reclamações emitidas no token SAML para aplicativos corporativos](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | A declaração de emissão usa Expressões Regulares para transformar o valor da reivindicação a ser emitida.Para obter funcionalidadesemelhante no Azure AD, você pode usar transformações pré-definidas como Extract(), Trim(), ToLower, entre outros. Para obter mais informações, consulte [Personalizar reclamações emitidas no token SAML para aplicativos corporativos](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |


## <a name="next-steps"></a>Próximas etapas

- [Vídeo: Como usar o relatório de atividades do AD FS para migrar um aplicativo](https://www.youtube.com/watch?v=OThlTA239lU)
- [Gerenciamento de aplicativos com o Azure Active Directory](what-is-application-management.md)
- [Gerenciar o acesso aos aplicativos](what-is-access-management.md)
- [Federação Azure AD Connect](../hybrid/how-to-connect-fed-whatis.md)
