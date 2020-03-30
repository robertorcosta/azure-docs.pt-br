---
title: Perguntas frequentes sobre proteção de identidade no Diretório Ativo do Azure
description: Perguntas frequentes Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 140ad45d9c4f6b6f49a4ea4aefb9298e58a2cf10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443565"
---
# <a name="frequently-asked-questions-identity-protection-in-azure-active-directory"></a>Perguntas frequentes de Proteção de Identidade no Diretório Ativo do Azure

## <a name="dismiss-user-risk-known-issues"></a>Descartar problemas conhecidos de risco do usuário

**Descartar o risco do usuário** no clássico Identity Protection define o ator no histórico de risco do usuário em Proteção de Identidade para **Azure AD**.

**Descartar o risco do usuário** na Proteção de Identidade define o ator no histórico de risco do usuário em Proteção de Identidade para o ** \<nome do Admin com um hiperlink apontando para a lâmina\>do usuário**.

Há um problema conhecido atual que causa latência no fluxo de demissão de risco do usuário. Se você tiver uma "política de risco do usuário", ela deixará de ser aplicada aos usuários descartados alguns minutos após clicar em "Ignorar o risco de usuário". No entanto, há atrasos conhecidos na experiência do usuário que atualizam o "Estado de risco" de usuários descartados. Como alternativa, atualize a página no nível do navegador para ver o "Estado de risco" mais recente do usuário.

## <a name="risky-users-report-known-issues"></a>Usuários de risco relatam problemas conhecidos

As consultas no campo **nome de usuário** diferenciam maiúsculas de minúsculas, enquanto consultas no campo **nome** não diferenciam.

Ativar/desativar **Mostrar datas como** oculta a coluna **ÚLTIMA ATUALIZAÇÃO DO RISCO**. Para adicionar novamente o clique de coluna **Colunas** na parte superior da folha Usuários Arriscados.

**Descartar todos os eventos** na Proteção de Identidade clássica define o status das detecções de risco para **Fechado (resolvido)**.

## <a name="risky-sign-ins-report-known-issues"></a>Relatório de logins arriscados questões conhecidas

**A resolução** em uma detecção de risco define o status para **Usuários aprovados por MFA orientado por política baseada em riscos**.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="why-is-a-user-is-at-risk"></a>Por que um usuário está em risco?

Se você é um cliente de Proteção de Identidade AD do Azure, vá até a visualização [de usuários arriscados](howto-identity-protection-investigate-risk.md#risky-users) e clique em um usuário em risco. Na gaveta na parte inferior, a guia 'Histórico de risco' mostrará todos os eventos que levaram a uma mudança de risco do usuário. Para ver todos os logins arriscados para o usuário, clique em 'Logins arriscados do usuário'. Para ver todas as detecções de risco para este usuário, clique em 'Detecções de risco do usuário'.

### <a name="how-can-i-get-a-report-of-detections-of-a-specific-type"></a>Como posso obter um relatório de detecções de um tipo específico?

Vá para a visualização e filtro de detecções de risco por 'Tipo de detecção'. Em seguida, você pode baixar este relatório em . CSV ou . Formato JSON usando o botão **Download** na parte superior. Para obter mais informações, consulte o artigo [Como: Investigar o risco](howto-identity-protection-investigate-risk.md#risk-detections).

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-detection"></a>Por que não posso definir meus próprios níveis de risco para cada detecção de risco?

Os níveis de risco no Identity Protection baseiam-se na precisão da detecção e são alimentados por nosso aprendizado de máquina supervisionado. Para personalizar a quais experiências os usuários são apresentados, o administrador pode incluir/excluir determinados usuários/grupos das políticas de Risco de Usuário e Risco de Entrada.

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>Por que o local de uma entrada não corresponde àquele de onde o usuário realmente entrou?

O mapeamento de localização geográfica do IP é um desafio de toda a indústria. Se você sentir que o local listado no relatório de login não corresponde à localização real, entre em contato com o suporte da Microsoft. 

### <a name="how-can-i-close-specific-risk-detections-like-i-did-in-the-old-ui"></a>Como posso fechar detecções de risco específicas como fiz na antiga ui?

Você pode dar feedback sobre detecções de risco confirmando o login vinculado como comprometido ou seguro. O feedback dado no login escorre até todas as detecções feitas nesse login. Se você quiser fechar detecções que não estão vinculadas a um login, você pode fornecer esse feedback no nível do usuário. Para obter mais informações, consulte o artigo [Como: Dar feedback de risco no Azure AD Identity Protection](howto-identity-protection-risk-feedback.md).

### <a name="how-far-can-i-go-back-in-time-to-understand-whats-going-on-with-my-user"></a>Até onde posso voltar no tempo para entender o que está acontecendo com meu usuário?

- A [visualização de usuários arriscados](howto-identity-protection-investigate-risk.md#risky-users) mostra o risco de um usuário de pé com base em todos os logins passados. 
- A [visão de sign-ins de risco](howto-identity-protection-investigate-risk.md#risky-sign-ins) mostra sinais de risco nos últimos 30 dias. 
- A visão [de detecções de risco](howto-identity-protection-investigate-risk.md#risk-detections) mostra detecções de risco feitas nos últimos 90 dias.

### <a name="how-can-i-learn-more-about-a-specific-detection"></a>Como posso aprender mais sobre uma detecção específica?

Todas as detecções de risco estão documentadas no artigo [O que é risco](concept-identity-protection-risks.md#risk-types-and-detection). Você pode passar o tempo sobre o símbolo (i) ao lado da detecção no portal Azure para saber mais sobre uma detecção.

### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Como funcionam os mecanismos de comentários no Identity Protection?

**Confirmar como comprometido** (em uma entrada) – informa o Azure AD Identity Protection que a entrada não foi executada pelo proprietário da identidade e indica um comprometimento.

- Depois de receber seus comentários, movemos o estado de risco de entrada e de usuário para **Confirmado como comprometido** e o nível de risco para **Alto**.

- Além disso, fornecemos as informações para nossos sistemas de aprendizado de máquina para futuras melhorias na avaliação de risco.

    > [!NOTE]
    > Se o usuário já foi corrigido, não clique em **Confirmar como comprometido** porque ele muda o estado de risco de entrada e de usuário para **Confirmado como comprometido** e o nível de risco para **Alto**.

**Confirmar como seguro** (em uma entrada) – informa o Azure AD Identity Protection que a entrada foi executada pelo proprietário da identidade e não indica um comprometimento.

- Depois de receber esses comentários, movemos o estado de risco de entrada (não o de usuário) para **Confirmado como seguro** e o nível de risco para **-**.

- Além disso, fornecemos as informações para nossos sistemas de aprendizado de máquina para futuras melhorias na avaliação de risco.

    > [!NOTE]
    > Se você achar que o usuário não foi comprometido, use **Ignorar o risco de usuário** no nível do usuário em vez de usar **Confirmado como seguro** no nível de entrada. Um **risco de descarte** do usuário no nível do usuário fecha o risco do usuário e todos os logins e detecções de risco passados.

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-detections-are-shown-in-identity-protection"></a>Por que estou vendo um usuário com uma pontuação de risco baixa (ou acima), mesmo que não sejam mostrados logins de risco ou detecções de risco de risco?

Dado que o risco do usuário é cumulativo e não expira, um usuário pode ter um risco de usuário de baixo ou superior, mesmo se não houver logins de risco ou detecção de risco recentes mostrados na Identity Protection. Essa situação poderia acontecer se a única atividade maliciosa em um usuário ocorresse além do prazo para o qual armazenamos os detalhes de logins arriscados e detecções de risco. Nós não expiramos o risco do usuário porque os atores ruins comprovadamente permanecem no ambiente do cliente por mais de 140 dias por trás de uma identidade comprometida antes aumentar seu ataque. Os clientes podem examinar a linha do tempo de risco do usuário para entender por que um usuário está em risco ao acessar: `Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>Por que uma entrada tem uma pontuação de "risco de entrada (agregação)" alta quando as detecções associadas a ela são de risco baixo ou médio?

A pontuação de risco de agregação alta pode ser baseada em outros recursos de entrada ou o fato de que mais de uma detecção foi acionada para essa entrada. E, por outro lado, uma entrada pode ter um risco de entrada (agregação) médio, mesmo se as detecções associadas com a entrada são de alto risco. 
