---
title: Como criar uma solicitação de suporte do Azure | Microsoft Docs
description: Os clientes que precisam de assistência podem usar o portal Azure para encontrar soluções de autoatendimento e criar e gerenciar solicitações de suporte.
services: Azure Supportability
author: ganganarayanan
manager: scotthit
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.service: azure-supportability
ms.topic: article
ms.date: 01/23/2020
ms.author: kfollis
ms.openlocfilehash: 28a29bbb94f63657a69e873bb8f969e96ee92c76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248483"
---
# <a name="how-to-create-an-azure-support-request"></a>Como criar uma solicitação de suporte do Azure

## <a name="overview"></a>Visão geral

O Azure permite que você crie e gerencie solicitações de suporte, também conhecidas como tickets de suporte. Você pode criar e gerenciar solicitações no [portal Azure,](https://portal.azure.com)que está coberto neste artigo. Você também pode criar e gerenciar solicitações de forma programática, usando o bilhete de suporte do [Azure REST API](/rest/api/support).

> [!NOTE]
> A URL do portal Azure é específica para a nuvem do Azure onde sua organização está implantada.
>
>* Portal Azure para uso comercial é:[https://portal.azure.com](https://portal.azure.com)
>* Portal azure para a Alemanha é:[https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* Portal azure para o governo dos Estados Unidos é:[https://portal.azure.us](https://portal.azure.us)
>
>

Com base no feedback do cliente, atualizamos a experiência de solicitação de suporte para focar em três objetivos principais:

* **Simplificado**: Facilite a solução de suporte e problemas e simplifique a forma como você envia uma solicitação de suporte.
* **Integrado**: Você pode facilmente abrir uma solicitação de suporte quando estiver solucionando um problema com um recurso do Azure, sem mudar o contexto.
* **Eficiente**: Reúna as principais informações que seu agente de suporte precisa para resolver seu problema de forma eficiente.

## <a name="getting-started"></a>Introdução

Você pode obter **ajuda + suporte** no portal Azure. Está disponível no menu do portal Azure, no cabeçalho global ou no menu de recursos de um serviço. Antes de apresentar uma solicitação de suporte, você deve ter permissões apropriadas.

### <a name="role-based-access-control"></a>Controle de acesso baseado em função

Para criar uma solicitação de suporte, você deve ser um admin ou ser designado para a função [contribuinte de solicitação de suporte](../../role-based-access-control/built-in-roles.md#support-request-contributor) no nível de assinatura.

### <a name="go-to-help--support-from-the-global-header"></a>Ir para Ajuda + suporte do cabeçalho global

Para iniciar uma solicitação de suporte de qualquer lugar do portal Azure:

1. Selecionar o **?** no cabeçalho global. Em seguida, **selecione Ajuda + suporte**.

   ![Ajuda e Suporte](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

2. Selecione **Nova solicitação de suporte**. Siga as instruções para nos fornecer informações sobre o seu problema. Sugeriremos algumas soluções possíveis, reuniremos detalhes sobre o problema e o ajudaremos a enviar e rastrear a solicitação de suporte.

   ![Nova solicitação de suporte](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>Ir para Ajuda + suporte a partir de um menu de recursos

Para iniciar uma solicitação de suporte no contexto do recurso, você está trabalhando com:

1. No menu de recursos, na seção **Suporte + Solução de problemas,** selecione Nova solicitação de **suporte**.

   ![No contexto](./media/how-to-create-azure-support-request/incontext2lower.png)

2. Siga as instruções para nos fornecer informações sobre o problema que você está tendo. Quando você inicia o processo de solicitação de suporte a partir do recurso, algumas opções são pré-selecionadas para você.

## <a name="create-a-support-request"></a>Criar uma solicitação de suporte

Vamos levá-lo através de alguns passos para coletar informações sobre o seu problema e ajudá-lo a resolvê-lo. Cada passo é descrito nas seções a seguir.

### <a name="basics"></a>Noções básicas

A primeira etapa do processo de solicitação de suporte coleta informações básicas sobre o problema e seu plano de suporte.

Na guia **Básico** da Nova solicitação de **suporte,** use os seletores para começar a nos contar sobre o problema. Primeiro, você identificará algumas categorias gerais para o tipo de emissão e escolherá a assinatura relacionada. Selecione o serviço, por exemplo, **Máquina Virtual executando o Windows**. Selecione o recurso, como o nome da sua máquina virtual. Descreva o problema em suas próprias palavras e **selecione o tipo de problema** para ser mais específico.

![Folha de Noções básicas](./media/how-to-create-azure-support-request/basics2lower.png)

> [!NOTE]
> O Azure oferece suporte ilimitado para gerenciamento de assinaturas, que inclui faturamento, ajustes de cotas e transferências de contas. Para obter suporte técnico, você precisa de um plano de suporte. [Saiba mais sobre planos de suporte](https://azure.microsoft.com/support/plans).
>
>

### <a name="solutions"></a>Soluções

Depois de coletar informações básicas, mostramos soluções para experimentar por conta própria. Em alguns casos, podemos até fazer um diagnóstico rápido. As soluções são escritas por engenheiros do Azure e resolverão os problemas mais comuns.

### <a name="details"></a>Detalhes

Em seguida, coletamos detalhes adicionais sobre o problema. Fornecer informações detalhadas e detalhadas nesta etapa nos ajuda a encaminhar sua solicitação de suporte para o agente certo.

Se possível, diga-nos quando o problema começou e quaisquer passos para reproduzi-lo. Você pode carregar um arquivo, como um arquivo de registro ou saída de diagnósticos.

Depois de termos todas as informações sobre o problema, escolha como obter suporte. Na seção **'Detalhes'** do método de **suporte,** selecione a gravidade do impacto. Forneça seu método de contato preferido, um bom momento para entrar em contato com você e seu idioma de suporte.

Em seguida, complete a seção **de informações** de contato para que saibamos como entrar em contato com você.

### <a name="review--create"></a>Examinar + criar

Complete todas as informações necessárias em cada guia e selecione **'Revisar + criar'.** Verifique os detalhes que você enviará para o Suporte. Volte para qualquer guia para fazer uma mudança, se necessário. Quando estiver satisfeito, a solicitação de suporte estiver concluída, selecione **Criar**.

Um agente de suporte entrará em contato com você usando o método indicado. Para obter informações sobre o tempo inicial de resposta, consulte [escopo de suporte e capacidade de resposta](https://azure.microsoft.com/support/plans/response/).

## <a name="all-support-requests"></a>Todas as solicitações de suporte

Você pode visualizar os detalhes e o status das solicitações de suporte indo para **Ajuda + suporte** >  **Todas as solicitações de suporte**.

![Todas as solicitações de suporte](./media/how-to-create-azure-support-request/allrequestslower.png)

Nesta página, você pode filtrar solicitações de suporte por **Assinatura**, Data **criada** (UTC) e **Status**. Além disso, você pode classificar e pesquisar para dar suporte a solicitações nesta página.

Selecione uma solicitação de suporte para visualizar detalhes, incluindo a gravidade e o tempo esperado para que um agente de suporte responda.

Se você quiser alterar a gravidade da solicitação, selecione **Impacto de negócios**. Escolha entre uma lista de gravidades a serem atribuídas.

> [!NOTE]
> O nível máximo de severidade depende do seu plano de suporte. [Saiba mais sobre planos de suporte](https://azure.microsoft.com/support/plans).
>
>
Para saber mais sobre opções de suporte a autoajuda no Azure, assista a este vídeo:

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

## <a name="next-steps"></a>Próximas etapas

* [Envie-nos seus comentários e sugestões](https://feedback.azure.com/forums/266794-support-feedback)
* Participe conosco no [Twitter](https://twitter.com/azuresupport)
* Obtenha ajuda de seus colegas nos fóruns do [MSDN](https://social.msdn.microsoft.com/Forums/azure)
* Saiba mais na [FAQ de suporte do Azure](https://azure.microsoft.com/support/faq)
