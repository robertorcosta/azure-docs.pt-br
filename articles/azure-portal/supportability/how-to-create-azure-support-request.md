---
title: Como criar uma solicitação de suporte do Azure | Microsoft Docs
description: Os clientes que precisam de assistência podem usar o portal do Azure para encontrar soluções de autoatendimento e para criar e gerenciar solicitações de suporte.
services: Azure Supportability
author: ganganarayanan
manager: scotthit
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.service: azure-supportability
ms.topic: article
ms.date: 03/31/2020
ms.author: kfollis
ms.openlocfilehash: 0bd1191c0b92203b100b1713971119ec828352ea
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835539"
---
# <a name="how-to-create-an-azure-support-request"></a>Como criar uma solicitação de suporte do Azure

## <a name="overview"></a>Visão geral

O Azure permite que você crie e gerencie solicitações de suporte, também conhecidas como tíquetes de suporte. Você pode criar e gerenciar solicitações no [portal do Azure](https://portal.azure.com), que é abordado neste artigo. Você também pode criar e gerenciar solicitações programaticamente, usando a [API REST do tíquete de suporte do Azure](/rest/api/support).

> [!NOTE]
> A URL do portal do Azure é específica para a nuvem do Azure onde sua organização está implantada.
>
>* O portal do Azure para uso comercial é: [https://portal.azure.com](https://portal.azure.com)
>* O portal do Azure para Alemanha é: [https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* O portal do Azure para o governo dos Estados Unidos é: [https://portal.azure.us](https://portal.azure.us)
>
>

Com base nos comentários dos clientes, atualizamos a experiência de solicitação de suporte com foco em três metas principais:

* **Simplificado**: torne o suporte e a solução de problemas fáceis de localizar e simplifique a forma como você envia uma solicitação de suporte.
* **Integrado**: você pode abrir uma solicitação de suporte quando estiver solucionando um problema com um recurso do Azure sem alternância de contexto.
* **Eficiente**: reúna as principais informações que o agente de suporte precisa para resolver eficazmente o problema.

## <a name="getting-started"></a>Introdução

Você pode obter **Ajuda + suporte** no portal do Azure. Disponível no menu do portal do Azure, no cabeçalho global ou no menu de recursos de um serviço. Antes de poder arquivar uma solicitação de suporte, você deverá ter as permissões apropriadas.

### <a name="role-based-access-control"></a>Controle de acesso baseado em função

Para criar uma solicitação de suporte, você deverá ser um [Proprietário](../../role-based-access-control/built-in-roles.md#owner), [Colaborador](../../role-based-access-control/built-in-roles.md#contributor) ou ser atribuído à função [Colaborador de solicitação de suporte](../../role-based-access-control/built-in-roles.md#support-request-contributor) no nível da assinatura. Para criar uma solicitação de suporte sem uma assinatura, por exemplo o cenário do AAD (Azure Active Directory), você deve ser um [administrador](../../active-directory/users-groups-roles/directory-assign-admin-roles.md).

### <a name="go-to-help--support-from-the-global-header"></a>Vá para Ajuda + suporte no cabeçalho global

Para iniciar uma solicitação de suporte de qualquer lugar no portal do Azure:

1. Selecione o **?** no cabeçalho global. Em seguida, selecione **Ajuda + suporte**.

   ![Ajuda e Suporte](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

2. Selecione **Nova solicitação de suporte**. Siga os prompts para nos fornecer informações sobre o problema. Vamos sugerir algumas soluções possíveis, coletar detalhes sobre o problema e ajudá-lo a enviar e acompanhar a solicitação de suporte.

   ![Nova solicitação de suporte](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>Ir para Ajuda + suporte em um menu de recursos

Para iniciar uma solicitação de suporte no contexto do recurso no qual você está trabalhando atualmente:

1. No menu de recursos, na seção **Suporte + Solução de problemas** e selecione **Nova solicitação de suporte**.

   ![No contexto](./media/how-to-create-azure-support-request/incontext2lower.png)

2. Siga os prompts para nos fornecer informações sobre o problema. Quando você inicia o processo de solicitação de suporte do recurso, algumas opções são previamente selecionadas para você.

## <a name="create-a-support-request"></a>Criar uma solicitação de suporte

Vamos orientá-lo em algumas etapas para coletar informações para solucionar o problema. Cada etapa é descrita nas seções a seguir.

### <a name="basics"></a>Noções básicas

A primeira etapa do processo de solicitação de suporte coleta informações básicas sobre o problema e seu plano de suporte.

Na guia **Básico** da **Nova solicitação de suporte**, use os seletores para começar a nos contar sobre o problema. Primeiro, você identificará algumas categorias gerais para o tipo de problema e escolherá a assinatura relacionada. Selecione o serviço, por exemplo, **Máquina virtual executando o Windows**. Selecione o recurso, como o nome da sua máquina virtual. Descreva o problema em suas próprias palavras e, em seguida, **Selecionar um tipo de problema** para obter mais informações específicas.

![Folha de Noções básicas](./media/how-to-create-azure-support-request/basics2lower.png)

> [!NOTE]
> O Azure fornece suporte ilimitado para o gerenciamento das assinaturas, que inclui cobrança, ajustes de cota e transferências de conta. Para obter suporte técnico, você precisa de um plano de suporte. [Saiba mais sobre planos de suporte](https://azure.microsoft.com/support/plans).
>
>

### <a name="solutions"></a>Soluções

Depois de coletar informações básicas, mostramos a você as soluções que você pode experimentar por conta própria. Em alguns casos, poderemos até mesmo executar um diagnóstico rápido. As soluções foram escritas por engenheiros do Azure e resolverão os problemas mais comuns.

### <a name="details"></a>Detalhes

Em seguida, coletamos detalhes adicionais sobre o problema. Fornecer informações completas e detalhadas nesta etapa nos ajuda a rotear sua solicitação de suporte para o agente correto.

Se possível, diga-nos quando o problema foi iniciado e as etapas para reproduzi-lo. Você pode carregar um arquivo, como um arquivo de log ou saída do diagnóstico.

Depois de ter todas as informações sobre o problema, escolha como obter suporte. Na seção **Método de suporte** de **Detalhes**, selecione a gravidade do impacto. Forneça seu método de contato preferencial, um bom momento para contatar você e o idioma de suporte.

Em seguida, complete a seção **Informações de contato** para saber como contatá-lo.

### <a name="review--create"></a>Examinar + criar

Conclua todas as informações necessárias em cada guia e, em seguida, selecione **Examinar + criar**. Verifique os detalhes que você enviará para o Suporte. Volte para qualquer guia para fazer uma alteração, se for necessário. Quando estiver satisfeito, a solicitação de suporte será concluída, selecione **Criar**.

Um agente de suporte o contatará usando o método que você indicou. Para obter informações sobre o tempo de resposta inicial, confira [Escopo de suporte e capacidade de resposta](https://azure.microsoft.com/support/plans/response/).

## <a name="all-support-requests"></a>Todas as solicitações de suporte

Você pode exibir os detalhes e o status das solicitações de suporte acessando **Ajuda + suporte** >  **Todas as solicitações de suporte**.

![Todas as solicitações de suporte](./media/how-to-create-azure-support-request/allrequestslower.png)

Nessa página, você pode filtrar as solicitações de suporte por **Assinatura**, **Data de criação** (UTC) e **Status**. Além disso, você pode classificar e pesquisar para dar suporte a solicitações nesta página.

Escolha uma solicitação de suporte para exibir os detalhes, incluindo a severidade e o tempo esperado que será necessário para um agente de suporte responder.

Se quiser alterar a severidade da solicitação, selecione **Impacto nos negócios**. Escolha em uma lista de severidades a atribuir.

> [!NOTE]
> O nível máximo de severidade depende do seu plano de suporte. [Saiba mais sobre planos de suporte](https://azure.microsoft.com/support/plans).
>
>
Para saber mais sobre as opções de suporte de autoajuda no Azure, assista a este vídeo:

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

## <a name="next-steps"></a>Próximas etapas

* [Envie-nos seus comentários e sugestões](https://feedback.azure.com/forums/266794-support-feedback)
* Entre em contato conosco no [Twitter](https://twitter.com/azuresupport)
* Obter ajuda de seus colegas na [página de perguntas de P e R da Microsoft](https://docs.microsoft.com/answers/products/azure)
* Saiba mais em [Perguntas frequentes sobre o suporte do Azure](https://azure.microsoft.com/support/faq)
