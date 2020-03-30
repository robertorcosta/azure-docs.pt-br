---
title: Solucionar problemas de políticas personalizadas com insights de aplicativos
titleSuffix: Azure AD B2C
description: Como configurar o Application Insights para rastrear a execução de suas políticas personalizadas.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 403dbe6106cb7a1d277ba672112d2bc45dbc2987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186260"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>Colete registros B2C do Active Directory do Azure com insights de aplicativos

Este artigo fornece etapas para coletar registros do Active Directory B2C (Azure AD B2C) para que você possa diagnosticar problemas com suas políticas personalizadas. O Application Insights fornece uma maneira de diagnosticar exceções e visualizar os problemas de desempenho do aplicativo. O Azure AD B2C inclui um recurso para o envio de dados para o Application Insights.

Os registros de atividade detalhados descritos aqui devem ser habilitados **somente** durante o desenvolvimento de suas políticas personalizadas.

> [!WARNING]
> Não habilite o modo de desenvolvimento na produção. Os logs coletam todas as reclamações enviadas de e para provedores de identidade. Você, como desenvolvedor, assume a responsabilidade por quaisquer dados pessoais coletados em seus registros do Application Insights. Esses registros detalhados são coletados somente quando a diretiva é colocada no **MODO DESENVOLVEDOR**.

## <a name="set-up-application-insights"></a>Configurar insights de aplicativos

Se você ainda não tiver um, crie uma instância de Insights de Aplicativo em sua assinatura.

1. Faça login no [portal Azure](https://portal.azure.com).
1. Selecione o filtro **de assinatura Diretório +** no menu superior e selecione o diretório que contém sua assinatura Azure (não o diretório Azure AD B2C).
1. Selecione **Criar um recurso** no menu de navegação à esquerda.
1. Pesquise e selecione **Insights de aplicativos**e selecione **Criar**.
1. Preencha o formulário, selecione **'Revisar + criar'** e, em seguida, selecione **Criar**.
1. Uma vez concluída a implantação, **selecione Ir para recurso**.
1. Em **Configure** no menu '''Crições de aplicativos', selecione **Propriedades**.
1. Registre a **CHAVE DE INSTRUMENTAÇÃO** para uso posteriormente.

## <a name="configure-the-custom-policy"></a>Configure a política personalizada

1. Abra o arquivo de parte de fundo (RP), por *exemplo, SignUpOrSignin.xml*.
1. Adicione os atributos a seguir ao elemento `<TrustFrameworkPolicy>`:

   ```XML
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. Se ele ainda não existir, `<UserJourneyBehaviors>` adicione um `<RelyingParty>` nó infantil ao nó. Deve ser localizado imediatamente `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />`após .
1. Adicione o seguinte nó como um filho do elemento `<UserJourneyBehaviors>`. Certifique-se `{Your Application Insights Key}` de substituir pela chave de **instrumentação** de insights de aplicativo que você gravou anteriormente.

    ```XML
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"`diz ao ApplicationInsights para agilizar a telemetria através do pipeline de processamento. Bom para o desenvolvimento, mas constrangido em grandes volumes.
    * `ClientEnabled="true"`envia o script do lado do cliente do ApplicationInsights para rastrear a visualização da página e erros do lado do cliente. Você pode visualizá-los na tabela **browserTimings** no portal Application Insights. Ao `ClientEnabled= "true"`definir, você adiciona Insights de aplicativo ao seu script de página e você recebe tempos de cargas de página e chamadas AJAX, contagens, detalhes de exceções do navegador e falhas do AJAX e contagem de usuários e sessões. Este campo é **opcional**e `false` é definido como padrão.
    * `ServerEnabled="true"` envia o JSON UserJourneyRecorder existente como um evento personalizado para o Application Insights.

    Por exemplo: 

    ```XML
    <TrustFrameworkPolicy
      ...
      TenantId="fabrikamb2c.onmicrosoft.com"
      PolicyId="SignUpOrSignInWithAAD"
      DeploymentMode="Development"
      UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
    >
    ...
    <RelyingParty>
      <DefaultUserJourney ReferenceId="UserJourney ID from your extensions policy, or equivalent (for example: SignUpOrSigninWithAzureAD)" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
    </TrustFrameworkPolicy>
    ```

1. Carregue a política.

## <a name="see-the-logs-in-application-insights"></a>Veja os logs de no Application Insights

Há um pequeno atraso, normalmente menos de cinco minutos, antes de você poder ver novos logs no Application Insights.

1. Abra o recurso do Application Insights que você criou no [Portal do Azure](https://portal.azure.com).
1. No **menu Visão geral,** selecione **'Análise ''Análise'.**
1. Abra uma nova guia no Application Insights.

Aqui está uma lista de consultas que você pode usar para ver os logs:

| Consulta | Descrição |
|---------------------|--------------------|
`traces` | Veja todos os logs gerados pelo Azure AD B2C |
`traces | where timestamp > ago(1d)` | Veja todos os logs gerados pelo Azure AD B2C para o último dia

As entradas podem ser longas. Exporte para CSV para uma análise mais detalhada.

Para obter mais informações sobre consulta, consulte [Visão geral das consultas de log no Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>Próximas etapas

A comunidade desenvolveu um visualizador userjourney para ajudar os desenvolvedores de identidade. Ele faz leitura da instância do Application Insights e fornece uma visão bem estruturada dos eventos do percurso do usuário. Obtenha o código-fonte e o implante em sua própria solução.

O player de jornada do usuário não é suportado pela Microsoft, e é disponibilizado estritamente como está.

Você pode encontrar a versão do visualizador que lê eventos do Application Insights no GitHub, aqui:

[Azure-Samples/active-directory-b2c-advanced-policies](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)
