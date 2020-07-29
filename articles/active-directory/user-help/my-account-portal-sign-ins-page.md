---
title: Exibição e pesquisa da atividade de entrada recente da página Minhas Entradas (versão prévia) – Azure Active Directory | Microsoft Docs
description: Detalhes sobre como exibir e pesquisar sua atividade de entrada recente na página Minhas Entradas do portal Minha Conta.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 10/28/2019
ms.author: curtand
ms.openlocfilehash: 0b3b0d686ae10f9b376c977bf165eccddda32239
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744523"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-preview-page"></a>Exibição e pesquisa da atividade de entrada recente da página Minhas entradas (versão prévia)

Você pode ver toda a atividade de entrada recente na sua conta corporativa ou de estudante, na página **Minhas Entradas** do portal **Minha Conta**. A análise do histórico de entradas ajuda a verificar atividades incomuns, já que ajuda a ver:

- Se alguém está tentando adivinhar sua senha.

- Se um invasor conseguiu entrar em sua conta e a partir de que localização.

- Quais aplicativos o invasor tentou acessar.

## <a name="view-your-recent-sign-in-activity"></a>Exibir sua atividade de entrada recente

1. Entre em sua conta corporativa ou de estudante e vá para a página https://myaccount.microsoft.com/.

2. Selecione **Minhas Entradas (versão prévia)** no painel de navegação à esquerda ou selecione o link **Analisar atividade recente** no bloco **Minhas entradas (versão prévia)** .

    ![Página Minha Conta, mostrando em destaque os links de Atividade recente](media/my-account-portal/my-account-portal-sign-ins.png)

3. Expanda e examine cada um dos itens de entrada, verificando que reconhece cada uma delas. Se encontrar um item de entrada que não parece familiar, é altamente recomendável que você altere sua senha para ajudar a proteger sua conta se ela tiver sido comprometida.

    ![Página de atividades recentes, com detalhes de entrada expandidos](media/my-account-portal/my-account-portal-sign-ins-page.png)

### <a name="if-you-see-a-successful-sign-in"></a>Se você vir uma Entrada bem-sucedida

Você deve reconhecer sua própria atividade como sendo normal. No entanto, se você notar uma Entrada bem-sucedida de uma localização, navegador ou sistema operacional estranhos, isso pode significar que um invasor conseguiu acessar a sua conta. Neste caso, recomendamos que você altere imediatamente sua senha e, em seguida, vá até a página [Informações de segurança](https://mysignins.microsoft.com/security-info) para atualizar suas configurações de segurança.

Antes de determinar que algo está errado, verifique se o que você está vendo não é um falso positivo (quando um item parece estranho, mas não há nada de errado). Por exemplo, determinamos a localização aproximada e o mapa com base no seu endereço IP. As redes móveis são especialmente difíceis de localizar com precisão, já que, às vezes, elas roteiam o tráfego através de localizações distantes. Portanto, se você tiver entrado usando seu dispositivo móvel no estado de Washington, a localização de entrada exibida poderá ser a Califórnia. Por isso, recomendamos fortemente que você verifique outros detalhes, além da localização. Verifique também se o sistema operacional, o navegador e o aplicativo fazem sentido para você.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>Se você vir uma Entrada recusada

Uma entrada recusada, sem atividade de sessão, significa que o método de verificação principal (nome de usuário/senha) falhou. Isso pode significar que você digitou errado seu nome de usuário ou senha, mas também pode significar que um invasor estava tentando adivinhar sua senha. Se você acha que foi um invasor tentando adivinhar sua senha sem sucesso, não é necessário alterá-la, mas recomendamos fortemente que você se registre na Autenticação Multifator do Azure (MFA). Com a MFA, mesmo que o hacker eventualmente adivinhe sua senha, isso não será o suficiente para acessar a sua conta.

Se você vir uma entrada recusada, com uma observação sob a Atividade de sessão que diz, **Falha na verificação adicional, código inválido**, isso significa que sua autenticação principal (nome de usuário/senha) foi bem-sucedida, mas a MFA falhou. Se esse era um invasor, ele adivinhou corretamente sua senha, mas não conseguiu passar o desafio de MFA. Neste caso, recomendamos que você altere sua senha mesmo assim, já que o invasor a descobriu e, em seguida, vá até a página [Informações de segurança](https://mysignins.microsoft.com/security-info) para atualizar suas configurações de segurança.

## <a name="search-for-specific-sign-in-activity"></a>Pesquisa de atividade de entrada específica

Você pode pesquisar sua atividade de entrada recente usando qualquer uma das informações disponíveis. Por exemplo, você pode pesquisar sua atividade de entrada recente por sistema operacional, localização, aplicativo, e assim por diante.

1. Na página **Analisar atividade recente**, digite as informações que quer pesquisar na barra **Pesquisa**. Por exemplo, digite `My Account` para pesquisar todas as atividades coletadas pelo aplicativo Minha Conta.

2. Selecione o botão **Pesquisar** para iniciar a pesquisa.

    ![Página Atividade Recente, mostrando a barra de pesquisa realçada, o botão de pesquisa e os resultados](media/my-account-portal/my-account-portal-sign-ins-page-search.png)

## <a name="next-steps"></a>Próximas etapas

Depois de ver sua atividade de entrada recente, você pode:

- Exibir ou gerenciar as [informações de segurança](user-help-security-info-overview.md).

- Exibir ou gerenciar os [dispositivos](my-account-portal-devices-page.md) conectados.

- Exibir ou gerenciar as [organizações](my-account-portal-organizations-page.md).

- Exibir como a organização [usa os dados relacionados à privacidade](my-account-portal-privacy-page.md).
