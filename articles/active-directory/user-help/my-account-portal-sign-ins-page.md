---
title: Exibir e pesquisar sua atividade de login recente na página Meu login (visualização) - Diretório Ativo do Azure | Microsoft Docs
description: Detalhes sobre como visualizar e pesquisar sua atividade de login recente na página Meus Logins do portal Minha Conta.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: curtand
ms.openlocfilehash: b68e7b517ddaa9b2aaef00cf87d5b6e63871654b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064012"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-preview-page"></a>Exibir e pesquisar sua atividade de login recente na página Meus logins (visualização)

Você pode visualizar toda a sua atividade de login de trabalho ou de conta escolar, a partir da página **Meus Logins** do portal **Minha Conta.** Revisar seu histórico de login ajuda você a verificar se há atividades incomuns, ajudando você a ver:

- Se alguém está tentando adivinhar sua senha.

- Se um invasor fez um sucesso em sua conta, e em que local.

- Quais aplicativos o atacante tentou acessar.

## <a name="view-your-recent-sign-in-activity"></a>Exibir sua atividade de entrada recente

1. Entre em sua conta corporativa ou de estudante e vá para a página https://myprofile.microsoft.com/.

2. Selecione **Minhas entradas (visualização)** no painel de navegação à esquerda ou selecione o link **De atividade recente** de Revisão no bloco **Minhas entradas (visualização).**

    ![Minha página da conta, mostrando links de atividades recentes](media/my-account-portal/my-account-portal-sign-ins.png)

3. Expanda e examine cada um dos itens de entrada, verificando que reconhece cada uma delas. Se você encontrar um item de login que não pareça familiar, recomendamos que você altere sua senha para ajudar a proteger sua conta se ela estiver comprometida.

    ![Página de atividades recentes, com detalhes de entrada expandidos](media/my-account-portal/my-account-portal-sign-ins-page.png)

### <a name="if-you-see-a-successful-sign-in"></a>Se você vir um login bem sucedido

Você deve reconhecer sua própria atividade como sendo normal. No entanto, se você notar um login bem-sucedido de localização, navegador ou sistema operacional estranho, isso pode significar que um invasor teve acesso à sua conta. Nesta situação, recomendamos que você altere imediatamente sua senha e, em seguida, vá para a página [de informações de segurança](https://mysignins.microsoft.com/security-info) para atualizar suas configurações de segurança.

Antes de determinar se algo está incorreto, certifique-se de que não está vendo um falso positivo (onde o item parece questionável, mas está bem). Por exemplo, determinamos sua localização aproximada e mapa com base no seu endereço IP. As redes móveis são especialmente difíceis de identificar, uma vez que às vezes direcionam o tráfego através de locais distantes. Então, se você se inscreveu usando seu dispositivo móvel no estado de Washington, o local pode mostrar o login vindo da Califórnia. Por causa disso, sugerimos fortemente que você verifique mais detalhes, além apenas da localização. Você também deve ter certeza de que o sistema operacional, o navegador e o aplicativo também fazem sentido.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>Se você vir um login mal sucedido

Um login mal sucedido, sem atividade de sessão, significa que seu método de verificação principal (nome de usuário/senha) falhou. Isso pode significar que você digitou mal seu nome de usuário ou senha, mas também pode significar que um invasor estava tentando adivinhar sua senha. Se você acha que foi o invasor tentando sem sucesso adivinhar sua senha, você não precisa alterar sua senha, mas sugerimos fortemente que você se registre no Azure Multi-Factor Authentication (MFA). Com o MFA, mesmo que o hacker eventualmente adivinhe sua senha, não será suficiente para acessar sua conta.

Se você vir um login mal sucedido, com uma nota na atividade da Sessão que diz: **Falha de verificação adicional, código inválido,** significa que sua autenticação primária (nome de usuário/senha) foi bem sucedida, mas o MFA falhou. Se este era um invasor, eles adivinharam corretamente sua senha, mas ainda não foram capazes de passar no desafio MFA. Neste caso, recomendamos que você ainda altere sua senha, já que o invasor acertou essa parte e, em seguida, vá para a página [de informações de segurança](https://mysignins.microsoft.com/security-info) para atualizar suas configurações de segurança.

## <a name="search-for-specific-sign-in-activity"></a>Procure por atividades específicas de login

Você pode pesquisar sua atividade de login recente por qualquer uma das informações disponíveis. Por exemplo, você pode pesquisar por sua atividade de login recente por sistema operacional, localização, aplicativo e assim por diante.

1. Na página **de atividades recentes do Review,** digite as informações que deseja procurar na barra **de pesquisa.** Por exemplo, `My Account` digite para pesquisar todas as atividades coletadas pelo aplicativo Minha Conta.

2. Selecione o botão **Pesquisar** para começar a procurar.

    ![Página atividade recente, mostrando barra de pesquisa destacada, botão de pesquisa e resultados](media/my-account-portal/my-account-portal-sign-ins-page-search.png)

## <a name="next-steps"></a>Próximas etapas

Depois de visualizar sua atividade de login recente, você pode:

- Visualize ou gerencie suas [informações de segurança](user-help-security-info-overview.md).

- Exibir ou gerenciar seus [dispositivos](my-account-portal-devices-page.md)conectados .

- Visualize ou gerencie suas [organizações.](my-account-portal-organizations-page.md)

- Veja como sua organização [usa seus dados relacionados à privacidade](my-account-portal-privacy-page.md).
