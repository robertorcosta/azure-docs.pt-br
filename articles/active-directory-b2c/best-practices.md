---
title: Melhores práticas para Azure AD B2C
titleSuffix: Azure AD B2C
description: Recomendações e práticas recomendadas a serem consideradas ao trabalhar com o Azure Active Directory B2C (Azure AD B2C).
services: active-directory-b2c
author: vigunase
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: vigunase
ms.subservice: B2C
ms.openlocfilehash: a76852a6e3cc5ffcdfcac62ce29fe47c97af3df1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136152"
---
# <a name="recommendations-and-best-practices-for-azure-active-directory-b2c"></a>Recomendações e melhores práticas para o Azure Active Directory B2C

As seguintes práticas recomendadas e recomendações abrangem alguns dos principais aspectos da integração do Azure Active Directory (Azure AD) B2C em ambientes de aplicativos existentes ou novos.

## <a name="fundamentals"></a>Conceitos básicos

|  |  |
|--|--|
| Escolha fluxos de usuário para a maioria dos cenários | O Framework de Experiência de Identidade do Azure AD B2C é a força central do serviço. As políticas descrevem em detalhe experiências de identidade, tais como inscrição, entrada ou edição de perfil. Para ajudá-lo a configurar as tarefas de identidade mais comuns, o portal do Azure AD B2C inclui políticas predefinidas e configuráveis chamadas fluxos dos usuários. Com os fluxos do usuário, você pode criar ótimas experiências do usuário em minutos, com apenas alguns cliques. [Saiba quando usar fluxos de usuário versus políticas personalizadas](custom-policy-overview.md#comparing-user-flows-and-custom-policies).|
| Registros de aplicativo | Todas as aplicações (web, nativas) e API que estão sendo protegidas devem ser registradas no Azure AD B2C. Se um aplicativo tiver uma versão web e nativa do iOS e Android, você pode registrá-los como um aplicativo no Azure AD B2C com o mesmo ID do cliente. Saiba como [registrar aplicativos OIDC, SAML, web e nativos.](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications?tabs=applications) Saiba mais sobre [os tipos de aplicativos que podem ser usados no Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/application-types). |
| Passar para o faturamento mensal dos usuários ativos | O Azure AD B2C passou de autenticações ativas mensais para usuários ativos mensais (MAU). A maioria dos clientes achará esse modelo econômico. [Saiba mais sobre o faturamento mensal dos usuários ativos](https://azure.microsoft.com/updates/mau-billing/). |

## <a name="planning-and-design"></a>Planejamento e design

Defina sua arquitetura de aplicativos e serviços, estoque de sistemas atuais e planeje sua migração para o Azure AD B2C.

|  |  |
|--|--|
| Arquiteta uma solução completa | Inclua todas as dependências de seus aplicativos ao planejar uma integração Azure AD B2C. Considere todos os serviços e produtos que estão atualmente em seu ambiente ou que possam precisar ser adicionados à solução, por exemplo, funções do Azure, sistemas de gerenciamento de relacionamento com o cliente (CRM), gateway de gerenciamento de API azure e serviços de armazenamento. Leve em conta a segurança e a escalabilidade de todos os serviços. |
| Documente as experiências de seus usuários | Detalhe todas as jornadas de usuário que seus clientes podem experimentar em seu aplicativo. Inclua todas as telas e quaisquer fluxos de ramificação que possam encontrar ao interagir com os aspectos de identidade e perfil da sua aplicação. Inclua usabilidade, acessibilidade e localização em seu planejamento. |
| Escolha o protocolo de autenticação certo |  Para obter uma análise dos diferentes cenários de aplicativos e seus fluxos de autenticação recomendados, consulte [Cenários e fluxos de autenticação suportados](../active-directory/develop/authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows). |
| Pilote uma experiência de usuário de ponta a ponta (POC) | Comece com nossas [amostras de código microsoft](code-samples.md) e amostras da [comunidade](https://github.com/azure-ad-b2c/samples). |
| Crie um plano de migração |Planejar com antecedência pode fazer com que a migração vá mais tranquila. Saiba mais sobre [a migração de usuários](user-migration.md).|
| Usabilidade vs. segurança | Sua solução deve atingir o equilíbrio certo entre a usabilidade do aplicativo e o nível aceitável de risco da sua organização. |
| Mova as dependências do local para a nuvem | Para ajudar a garantir uma solução resiliente, considere mover as dependências de aplicativos existentes para a nuvem. |
| Migrar aplicativos existentes para b2clogin.com | A depreciação do login.microsoftonline.com entrou em vigor para todos os inquilinos Azure AD B2C em 04 de dezembro de 2020. [Saiba mais](b2clogin.md). |

## <a name="implementation"></a>Implementação

Durante a fase de implementação, considere as seguintes recomendações.

|  |  |
|--|--|
| Editar políticas personalizadas com a extensão Azure AD B2C para Visual Studio Code | Baixe Visual Studio Code e esta [extensão](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)construída pela comunidade no Visual Studio Code Marketplace . Embora não seja um produto oficial da Microsoft, a extensão Azure AD B2C para Visual Studio Code inclui vários recursos que ajudam a tornar mais fácil trabalhar com políticas personalizadas. |
| Saiba como solucionar problemas do Azure AD B2C | Aprenda a [solucionar políticas personalizadas](https://docs.microsoft.com/azure/active-directory-b2c/troubleshoot-custom-policies?tabs=applications) durante o desenvolvimento. Saiba como é um fluxo normal de autenticação e use ferramentas para descobrir anomalias e erros. Por exemplo, use [o Application Insights](troubleshoot-with-application-insights.md) para revisar os registros de saída das viagens do usuário. |
| Aproveite nossa biblioteca de padrões de políticas personalizados comprovados | Encontre [amostras](https://github.com/azure-ad-b2c/samples) para várias jornadas aprimoradas de identidade do cliente e gerenciamento de acesso (CIAM) do Azure AD B2C. |


## <a name="testing"></a>Testes

Teste e automatize sua implementação Azure AD B2C.

|  |  |
|--|--|
| Conta para o tráfego global | Use fontes de tráfego de diferentes endereços globais para testar os requisitos de desempenho e localização. Certifique-se de que todos os HTMLs, CSS e dependências possam atender às suas necessidades de desempenho. |
| Testes funcionais e de IA | Teste os fluxos de usuário de ponta a ponta. Adicione testes sintéticos a cada poucos minutos usando Selenium, VS Web Test, etc. |
| Teste de caneta | Antes de entrar ao vivo com sua solução, realize exercícios de teste de penetração para verificar se todos os componentes estão seguros, incluindo quaisquer dependências de terceiros. Verifique se você garantiu suas APIs com tokens de acesso e usou o protocolo de autenticação certo para o cenário do aplicativo. Saiba mais sobre [testes de penetração](https://docs.microsoft.com/azure/security/fundamentals/pen-testing) e as [regras de teste de penetração unificada da Microsoft Cloud.](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) |
| Teste A/B | Voe seus novos recursos com um pequeno conjunto aleatório de usuários antes de ser lançado para toda a sua população. Com o JavaScript ativado no Azure AD B2C, você pode se integrar com ferramentas de teste A/B, como Optimizely, Clarity e outras. |
| Teste de carga | O Azure AD B2C pode ser dimensionado, mas seu aplicativo só pode ser dimensionado se todas as suas dependências puderem ser dimensionadas. Teste de carga suas APIs e CDN. |
| Limitação |  O Azure AD B2C acelera o tráfego se muitas solicitações forem enviadas da mesma fonte em um curto período de tempo. Use várias fontes de tráfego durante `AADB2C90229` o teste de carga e manuseie o código de erro graciosamente em seus aplicativos. |
| Automação | Use pipelines de integração e entrega contínua (CI/CD) para automatizar testes e implantações, por exemplo, [Azure DevOps](deploy-custom-policies-devops.md). |

## <a name="operations"></a>Operações

Gerencie seu ambiente Azure AD B2C.

|  |  |
|--|--|
| Crie vários ambientes | Para facilitar a implementação de operações e implantação, crie ambientes separados para desenvolvimento, teste, pré-produção e produção. Crie inquilinos Azure AD B2C para cada um. |
| Use o controle de versão para suas políticas personalizadas | Considere usar o GitHub, a Azure Repos ou outro sistema de controle de versão baseado em nuvem para suas políticas personalizadas Azure AD B2C. |
| Use a API do Microsoft Graph para automatizar o gerenciamento de seus inquilinos B2C | APIs do gráfico da Microsoft:<br/>Gerenciar [o Identity Experience Framework](https://docs.microsoft.com/graph/api/resources/trustframeworkpolicy?view=graph-rest-beta) (políticas personalizadas)<br/>[Chaves](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset?view=graph-rest-beta)<br/>[Fluxos de Usuário](https://docs.microsoft.com/graph/api/resources/identityuserflow?view=graph-rest-beta) |
| Integre-se ao Azure DevOps | Um [pipeline CI/CD](deploy-custom-policies-devops.md) facilita a movimentação entre diferentes ambientes e garante a prontidão da produção o tempo todo.   |
| Integrar com o Azure Monitor | [Os eventos de registro de auditoria](view-audit-logs.md) são retidos apenas por sete dias. [Integre-se ao Azure Monitor](azure-monitor.md) para reter os logs para uso a longo prazo ou integrar-se com ferramentas de gerenciamento de eventos e informações de segurança de terceiros (SIEM) para obter insights sobre o ambiente. |
| Alerta e monitoramento ativos de configuração | [Rastreie o comportamento do usuário](active-directory-b2c-custom-guide-eventlogger-appins.md) no Azure AD B2C usando o Application Insights. |


## <a name="support-and-status-updates"></a>Atualizações de suporte e status

Mantenha-se atualizado com o estado do serviço e encontre opções de suporte.

|  |  |
|--|--|
| [Atualizações de serviço](https://azure.microsoft.com/updates/?product=active-directory-b2c) |  Mantenha-se atualizado com as atualizações e anúncios dos produtos Azure AD B2C. |
| [Suporte da Microsoft](support-options.md) | Arquive uma solicitação de suporte para problemas técnicos do Azure AD B2C. O suporte para gerenciamento de assinaturas e cobranças é fornecido sem custo adicional. |
| [Status do Azure](https://status.azure.com/status) | Veja o estado de saúde atual de todos os serviços do Azure. |