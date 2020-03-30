---
title: Azure Monitor visualiza designer para resumo de conversão de livros de trabalho e acesso
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: fb53a966b0dc4959253ac0786ef09ef3c497e809
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658839"
---
# <a name="view-designer-to-workbooks-conversion-summary-and-access"></a>Exibir resumo de conversão e acesso de designer para livros de trabalho
[O View designer](view-designer.md) é um recurso do Azure Monitor que permite criar visualizações personalizadas para ajudá-lo a visualizar dados em seu espaço de trabalho do Log Analytics, com gráficos, listas e cronogramas. Eles estão sendo eliminados e substituídos por livros de trabalho, que fornecem funcionalidade adicional. Este artigo detalha como você pode criar um resumo de visão geral e permissões necessárias para acessar as abanuais.

## <a name="creating-your-workspace-summary-from-azure-dashboard"></a>Criando seu resumo do espaço de trabalho do painel do Azure
Os usuários do designer de visualização podem estar familiarizados em ter um azulejo de visão geral para representar um conjunto de visualizações. Para manter uma visão geral visual como o resumo do espaço de trabalho do designer de visualização, as apostilas oferecem etapas fixas, que podem ser fixadas no [painel do portal Azure](../../azure-portal/azure-portal-dashboards.md). Assim como as telhas de visão geral no resumo do Workspace, os itens da carteira de trabalho fixados se vincularão diretamente à exibição da carteira de trabalho.

Você pode aproveitar o alto nível de recursos de personalização fornecidos com painéis Azure, que permitem atualização automática, movimento, dimensionamento e filtragem adicional para seus itens e visualizações fixas. 

![Painel](media/view-designer-conversion-access/dashboard.png)

Crie um novo painel azure ou selecione um painel existente para começar a fixar itens de livros de trabalho.

Para fixar o item individual, você precisará ativar o ícone do pino para sua etapa específica. Para isso, selecione o botão **Editar** correspondente para o seu passo e, em seguida, selecione o ícone de engrenagem para abrir **Configurações Avançadas**. Verifique a opção de **sempre mostrar o ícone do pino nesta etapa**, e um ícone de pino aparecerá no canto superior direito do seu passo. Este pino permite que você fixar visualizações específicas no seu painel, como os ladrilhos de visão geral.

![Passo do pino](media/view-designer-conversion-access/pin-step.png)


Você também pode desejar fixar várias visualizações da Carteira de Trabalho ou de todo o conteúdo da Carteira de Trabalho em um painel de controle. Para fixar toda a agenda, selecione **Editar** na barra de ferramentas superior para alternar o **modo de edição**. Um ícone de pino aparecerá, permitindo que você fixe todo o item da Carteira de Trabalho ou todas as etapas e visualizações individuais na caderneta de trabalho.

![Pino tudo](media/view-designer-conversion-access/pin-all.png)



## <a name="sharing-and-viewing-permissions"></a>Compartilhamento e visualização de permissões 
As abanuais têm o benefício de serem um documento privado ou compartilhado. Por padrão, as carteiras de trabalho salvas serão salvas em **Meus Relatórios,** o que significa que apenas o criador pode visualizar esta carteira de trabalho.

Você pode compartilhar suas regras de trabalho selecionando o ícone **Compartilhar** na barra de ferramentas superior enquanto estiver no **modo de edição**. Você será solicitado a mover sua carteira de trabalho para **Relatórios Compartilhados,** o que gerará um link que fornece acesso direto à carteira de trabalho.

Para que um usuário visualize uma carteira de trabalho compartilhada, ele deve ter acesso tanto ao grupo de assinatura quanto aos recursos em que a carteira de trabalho é salva.

![Acesso por assinatura](media/view-designer-conversion-access/subscription-access.png)

## <a name="next-steps"></a>Próximas etapas

- [Tarefas comuns](view-designer-conversion-tasks.md)