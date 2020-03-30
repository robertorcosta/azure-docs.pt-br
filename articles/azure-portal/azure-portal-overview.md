---
title: Visão geral do Portal do Azure | Microsoft Docs
description: O portal Azure é uma interface gráfica de usuário que você pode usar para gerenciar seus serviços do Azure. Saiba como navegar e encontrar recursos no portal Azure.
services: azure-portal
keywords: portal
author: mgblythe
ms.author: mblythe
ms.date: 12/20/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: c5e02e791185db3713c67b8ff97b8f7eebe9da77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310723"
---
# <a name="azure-portal-overview"></a>Visão geral do portal do Azure

Este artigo introduz o portal Azure, identifica elementos de página do portal e ajuda você a se familiarizar com a experiência de gerenciamento do portal Azure.

## <a name="what-is-the-azure-portal"></a>O que é o portal do Azure?

O portal do Azure é um console unificado baseado na Web que fornece uma alternativa para as ferramentas de linha de comando. Com o portal Azure, você pode gerenciar sua assinatura do Azure usando uma interface gráfica de usuário. Você pode construir, gerenciar e monitorar tudo, desde aplicativos web simples até implantações complexas na nuvem. Crie painéis personalizados para uma visão organizada dos recursos. Configure opções de acessibilidade para uma experiência ideal.

O portal Azure foi projetado para resiliência e disponibilidade contínua. Tem presença em todos os data centers do Azure. Essa configuração torna o portal Azure resiliente a falhas individuais do data center e evita a lentidão da rede por estar perto dos usuários. O portal Azure é atualizado continuamente e não requer tempo de inatividade para atividades de manutenção.

## <a name="azure-portal-menu"></a>Menu do portal Azure

Você pode escolher o modo padrão para o menu portal. Pode ser ancorado ou pode atuar como um painel de flyout.

Quando o menu portal está no modo flyout, ele está escondido até que você precise dele. Selecione o ícone do menu para abrir ou fechar o menu.

![Menu portal azure no modo flyout](./media/azure-portal-overview/azure-portal-overview-portal-menu-flyout.png)

Se você escolher o modo docked para o menu portal, ele sempre estará visível. Você pode desabar o menu para fornecer mais espaço de trabalho.

![Menu portal azure no modo ancorado](./media/azure-portal-overview/azure-portal-overview-portal-menu-expandcollapse.png)

## <a name="azure-home"></a>Casa Azure

Como novo assinante dos serviços do Azure, a primeira coisa que você vê depois [de entrar no portal](https://portal.azure.com) é o **Azure Home**. Esta página compila recursos que ajudam você a obter o máximo da sua assinatura do Azure. Incluímos links para cursos online gratuitos, documentação, serviços essenciais e sites úteis para se manter atualizado e gerenciar mudanças para sua organização. Para acesso rápido e fácil ao trabalho em andamento, também mostramos uma lista de seus recursos mais recentemente visitados. Você não pode personalizar esta página, mas você pode escolher se quer ver **o Azure Home** ou **o Azure Dashboard** como sua exibição padrão. A primeira vez que você faz login, há um prompt no topo da página onde você pode salvar sua preferência.

![Captura de tela mostrando o seletor de exibição padrão](./media/azure-portal-overview/azure-portal-default-view.png)

Tanto o menu do portal Azure quanto a exibição padrão do Azure podem ser alterados nas **configurações do Portal**. Se você alterar sua seleção, a alteração será imediatamente aplicada.

![Captura de tela mostrando o seletor de exibição padrão](./media/azure-portal-overview/azure-portal-overview-portal-settings-menu-home.png)

## <a name="azure-dashboard"></a>Painel do Azure

Os dashboards fornecem uma visão focada dos recursos em sua assinatura que mais importam para você. Nós lhe demos um painel padrão para começar. Você pode personalizar este painel para trazer os recursos que você usa com freqüência em uma única exibição. Quaisquer alterações que você fizer na exibição padrão afetam apenas sua experiência. No entanto, você pode criar painéis adicionais para seu próprio uso ou publicar seus dashboards personalizados e compartilhá-los com outros usuários em sua organização. Para obter mais informações, consulte [Criar e compartilhar dashboards no portal Azure](../azure-portal/azure-portal-dashboards.md).

## <a name="getting-around-the-portal"></a>Andando pelo portal

É útil entender o layout básico do portal e como interagir com ele. Aqui, vamos introduzir os componentes da interface do usuário e algumas das terminologias que usamos para dar instruções. Para um tour mais detalhado do portal, consulte a aula de curso [Navegue pelo portal.](https://docs.microsoft.com/learn/modules/tour-azure-portal/3-navigate-the-portal)

O menu do portal Azure e o cabeçalho da página são elementos globais que estão sempre presentes. Esses recursos persistentes são o "shell" para a interface do usuário associada a cada serviço ou recurso individual e o cabeçalho fornece acesso a controles globais. A página de configuração (às vezes chamada de "lâmina") para um recurso também pode ter um menu de recursos para ajudá-lo a se mover entre os recursos.

A figura abaixo rotula os elementos básicos do portal Azure, cada um dos quais são descritos na tabela a seguir.

![Captura de tela mostrando a visualização do portal em tela cheia e a chave para os elementos da UI](./media/azure-portal-overview/azure-portal-overview-portal-callouts.png)

![Captura de tela mostrando menu de portal expandido](./media/azure-portal-overview/azure-portal-overview-portal-menu-callouts.png)

|Chave|Descrição
|:---:|---|
|1|Cabeçalho de página. Aparece no topo de cada página do portal e contém elementos globais.|
|2| Busca global. Use a barra de pesquisa para encontrar rapidamente um recurso específico, um serviço ou documentação.|
|3|Controles globais. Como todos os elementos globais, esses recursos persistem em todo o portal e incluem: Cloud Shell, filtro de assinatura, notificações, configurações do portal, ajuda e suporte, e nos envie feedback.|
|4|Sua conta. Veja informações sobre sua conta, troque diretórios, faça login ou faça login com uma conta diferente.|
|5|Menu portal. O menu portal é um elemento global que ajuda você a navegar entre os serviços. Às vezes referido como barra lateral, o modo menu portal pode ser alterado nas **configurações do Portal**.|
|6|Menu de recursos. Muitos serviços incluem um menu de recursos para ajudá-lo a gerenciar o serviço. Você pode ver este elemento referido como o painel esquerdo.|
|7|Barra de comando. Os controles na barra de comando são contextuais para o seu foco atual.|
|8|Painel de trabalho.  Exibe os detalhes sobre o recurso que está atualmente em foco.|
|9|Breadcrumb. Você pode usar os links de migalhas de pão para mover um nível no seu fluxo de trabalho.|
|10|Controle mestre para criar um novo recurso na assinatura atual. Expanda ou abra o menu do portal para encontrar **+ Criar um recurso**. Pesquise ou navegue no Azure Marketplace para saber o tipo de recurso que deseja criar.|
|11|Sua lista de favoritos. Consulte [Adicionar, remover e classificar favoritos](../azure-portal/azure-portal-add-remove-sort-favorites.md) para aprender a personalizar a lista.|

## <a name="get-started-with-services"></a>Comece com os serviços

Se você é um novo assinante, você terá que criar um recurso antes que haja algo para gerenciar. Selecione **+ Crie um recurso** para visualizar os serviços disponíveis no Azure Marketplace. Você encontrará aplicativos e serviços de centenas de provedores aqui, todos certificados para serem executados no Azure.

Nós pré-povoamos seus Favoritos na barra lateral com links para serviços comumente usados.  Para visualizar todos os serviços disponíveis, selecione **Todos os serviços** na barra lateral.

> [!TIP]
> A maneira mais rápida de encontrar um recurso, serviço ou documentação é usar o *Search* no cabeçalho global. Use os links de migalhas de pão para voltar às páginas anteriores.
>
Assista a este vídeo para uma demonstração sobre como usar a pesquisa global no portal Azure.


> [!VIDEO https://www.youtube.com/embed/nZ7WwTZcQbo]

[Como usar a pesquisa global no portal Azure](https://www.youtube.com/watch?v=nZ7WwTZcQbo)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre onde executar o portal Azure [em navegadores e dispositivos suportados](../azure-portal/azure-portal-supported-browsers-devices.md)
* Fique ligado em qualquer movimento com o [aplicativo móvel Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* A bordo e configure seu ambiente de nuvem com o [Azure Quickstart Center](../azure-portal/azure-portal-quickstart-center.md)
