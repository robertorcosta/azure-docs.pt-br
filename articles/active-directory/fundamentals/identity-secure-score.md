---
title: O que é pontuação segura de identidade? - Diretório Ativo do Azure
description: Como você pode usar a pontuação de segurança de identidade para melhorar a postura de segurança do seu diretório
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tilarso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f75dea2cffbe710bf2778ceab5eacc91ffcca9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523094"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>O que é a classificação de segurança de identidade no Azure Active Directory?

Quão seguro é seu locatário do Azure AD? Se você não sabe como responder a essa pergunta, este artigo explica como a pontuação de segurança de identidade ajuda você a monitorar e melhorar sua postura de segurança de identidade.

## <a name="what-is-an-identity-secure-score"></a>O que é uma classificação de segurança de identidade?

A pontuação de segurança de identidade é o número entre 1 e 223 que funciona como um indicador de quão alinhado você está com as recomendações de melhores práticas da Microsoft para segurança. Cada ação de melhoria na pontuação de segurança de identidade é adaptada à sua configuração específica.  

![Classificação de segurança](./media/identity-secure-score/identity-secure-score-overview.png)

A classificação ajuda você a:

- Medir objetivamente a sua postura de segurança de identidade
- Planejar aprimoramentos de segurança de identidade
- Examinar o sucesso das suas melhorias

Você pode acessar a classificação e informações relacionadas a no painel de classificação de segurança de identidade. Neste painel, você encontrará:

- Sua pontuação segura de identidade
- Um gráfico de comparação mostrando como sua pontuação segura de identidade se compara a outros inquilinos da mesma indústria e tamanho semelhante
- Um gráfico de tendências mostrando como sua pontuação segura de identidade mudou ao longo do tempo
- Uma lista de possíveis melhorias

Ao seguir as ações de melhoria, você pode:

- Melhore sua postura de segurança e sua pontuação
- Aproveite os recursos disponíveis para sua organização como parte de seus investimentos em identidade

## <a name="how-do-i-get-my-secure-score"></a>Como faço para obter minha classificação de segurança?

A pontuação de segurança de identidade está disponível em todas as edições do Azure AD. As organizações podem acessar sua pontuação de segurança de identidade a partir do portal > **Security** >  >  **Azure****Active Directory**Security**Secure Score**.

## <a name="how-does-it-work"></a>Como ele funciona?

A cada 48 horas, o Azure analisa sua configuração de segurança e compara suas configurações com as práticas recomendadas. Com base no resultado desta avaliação, uma nova pontuação é calculada para o seu diretório. É possível que sua configuração de segurança não esteja totalmente alinhada com a orientação de boas práticas e as ações de melhoria sejam apenas parcialmente atendidas. Nestes cenários, você só receberá uma parte da pontuação máxima disponível para o controle.

Cada recomendação é medida com base na sua configuração do Azure AD. Se você estiver usando produtos de terceiros para habilitar uma recomendação de práticas recomendadas, você pode indicar essa configuração nas configurações de uma ação de melhoria. Você também tem a opção de definir recomendações a serem ignoradas se elas não se aplicarem ao seu ambiente. Uma recomendação ignorada não contribui para o cálculo de sua classificação.

![Ignorar ou marcar ação como coberto por terceiros](./media/identity-secure-score/identity-secure-score-ignore-or-third-party-reccomendations.png)

## <a name="how-does-it-help-me"></a>Como isso me ajuda?

A classificação de segurança ajuda você a:

- Medir objetivamente a sua postura de segurança de identidade
- Planejar aprimoramentos de segurança de identidade
- Examinar o sucesso das suas melhorias

## <a name="what-you-should-know"></a>O que você deve saber

### <a name="who-can-use-the-identity-secure-score"></a>Quem pode usar a classificação de segurança de identidade?

A classificação de segurança de identidade pode ser usada pelas funções a seguir:

- Administrador global
- Administrador de segurança
- Leitores de segurança

### <a name="how-are-controls-scored"></a>Como os controles são pontuados?

Os controles podem ser marcados de duas maneiras. Alguns são pontuados de forma binária - você recebe 100% da pontuação se tiver o recurso ou configuração configurado com base em nossa recomendação. Outras pontuações são calculadas como uma porcentagem da configuração total. Por exemplo, se a recomendação de melhoria afirma que você obterá 30 pontos se você proteger todos os seus usuários com MFA e você tiver apenas 5 de 100 usuários totais protegidos, você receberá uma pontuação parcial em torno de 2 pontos (5 protegidos / 100 total * 30 pts max = 2 pts pontuação parcial).

### <a name="what-does-not-scored-mean"></a>O que [Não Pontuado] significa?

Ações rotuladas como [Não Pontuada] são aquelas você pode executar em sua organização, mas não serão pontuadas porque não estão conectadas à ferramenta (ainda!). Portanto, você ainda pode melhorar sua segurança, mas não receberá crédito por essas ações no momento.

### <a name="how-often-is-my-score-updated"></a>Com que frequência minha classificação é atualizada?

A classificação é calculada uma vez por dia (aproximadamente à 1h PST). Se você fizer uma alteração a uma ação de medida, a classificação será atualizada automaticamente no dia seguinte. Podem ser necessárias até 48 horas para uma alteração ser refletida na sua classificação.

### <a name="my-score-changed-how-do-i-figure-out-why"></a>Minha classificação mudou. Como faço para descobrir por quê?

Vá até o [centro de segurança do Microsoft 365,](https://security.microsoft.com/)onde você encontrará sua pontuação completa da Microsoft segura. Você pode ver facilmente todas as alterações em sua pontuação segura, revendo as alterações detalhadas na guia histórico.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>A pontuação segura mede o risco de ser violada?

Em breve, não. A pontuação segura não expressa uma medida absoluta de quão provável você é de ser violado. Expressa a extensão em que você adotou recursos que podem compensar o risco de sofrer uma violação. Nenhum serviço pode garantir que você não será violado, e a pontuação segura não deve ser interpretada como uma garantia de forma alguma.

### <a name="how-should-i-interpret-my-score"></a>Como devo interpretar a minha classificação?

Você recebe pontos para configuração recursos de segurança recomendados ou realizar tarefas relacionadas a segurança (como a leitura de relatórios). Algumas ações são pontuadas para conclusão parcial, como habilitar MFA (autenticação multifator) para seus usuários. Sua pontuação segura é diretamente representativa dos serviços de segurança da Microsoft que você usa. Lembre-se que a segurança deve ser equilibrada com usabilidade. Todos os controles de segurança têm um componente de impacto ao usuário. Controles com baixo impacto ao usuário devem ter pouco ou nenhum efeito sobre as operações diárias dos usuários.

Para ver seu histórico de pontuação, vá até o [centro de segurança do Microsoft 365](https://security.microsoft.com/) e revise sua pontuação geral de segurança da Microsoft. Você pode rever alterações na sua pontuação geral segura clicando em Histórico de exibição. Escolha uma data específica para ver quais controles foram habilitados para aquele dia e que pontos você ganhou para cada um.

### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>Como a classificação de segurança de identidade se relaciona à classificação de segurança do Office 365?

A [pontuação segura da Microsoft](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score) contém cinco categorias distintas de controle e pontuação:

- Identidade
- Dados
- Dispositivos
- Infraestrutura
- Aplicativos

A pontuação de segurança de identidade representa a parte de identidade da pontuação segura da Microsoft. Essa sobreposição significa que suas recomendações para a pontuação segura de identidade e a pontuação de identidade na Microsoft são as mesmas.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre a pontuação segura da Microsoft](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score)
