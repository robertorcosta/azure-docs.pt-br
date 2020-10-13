---
title: Início Rápido – Obter um número de telefone dos Serviços de Comunicação do Azure
description: Saiba como comprar um número de telefone dos Serviços de Comunicação usando o portal do Azure.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 10/05/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
ms.openlocfilehash: e06c3720e180c1dc4fa2f227fd86d15cbbb0ff33
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91756836"
---
# <a name="quickstart-get-a-phone-number-using-the-azure-portal"></a>Início Rápido: Obter um número de telefone usando o portal do Azure

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Comece a usar os Serviços de Comunicação do Azure usando o portal do Azure para comprar um número de telefone.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Um recurso ativo dos Serviços de Comunicação.](../create-communication-resource.md)

## <a name="get-a-phone-number"></a>Obter um número de telefone

Para começar a provisionar números, vá até seu recurso dos Serviços de Comunicação no [portal do Azure](https://portal.azure.com).

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="Captura de tela mostrando a página principal de um recurso dos Serviços de Comunicação.":::

### <a name="getting-new-phone-numbers"></a>Obtendo novos números de telefone

Navegue até a folha Números de Telefone no menu de recursos.

:::image type="content" source="../media/manage-phone-azure-portal-phone-page.png" alt-text="Captura de tela mostrando a página principal de um recurso dos Serviços de Comunicação.":::

Pressione o botão `Get` para iniciar o assistente. O assistente na folha `Phone numbers` orientará você ao longo de uma série de perguntas que ajudarão a escolher o número de telefone mais adequado para seu cenário. 

Primeiro, você precisará escolher o `Country/region` em que deseja provisionar o número de telefone. Após selecionar o país/região, você precisará selecionar o `use case` mais adequado às suas necessidades. 

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers.png" alt-text="Captura de tela mostrando a página principal de um recurso dos Serviços de Comunicação.":::

### <a name="select-a-phone-plan"></a>Selecionar um plano de telefonia

A seleção de um plano de telefonia é dividida em duas etapas: 

1. A seleção do [Tipo de número](../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)
2. A seleção do [Plano](../../concepts/telephony-sms/plan-solution.md#phone-number-plans-in-azure-communication-services)

Atualmente, oferecemos dois tipos de número: `Geographic` e `Toll-free`. Após ter selecionado um Tipo de número, você verá vários Planos entre os quais poderá escolher.

Em nosso exemplo, escolhemos um Tipo de número `Toll-free` com os Planos `Outbound calling` e `Inbound and Outbound SMS`.

:::image type="content" source="../media/manage-phone-azure-portal-select-plans.png" alt-text="Captura de tela mostrando a página principal de um recurso dos Serviços de Comunicação.":::

Aqui, clique no botão `Next: Numbers` na parte inferior da página para personalizar os números de telefone que gostaria de provisionar.

### <a name="customizing-phone-numbers"></a>Personalizando números de telefone

Na página `Numbers`, você personalizará os números de telefone que deseja provisionar.

:::image type="content" source="../media/manage-phone-azure-portal-select-numbers-start.png" alt-text="Captura de tela mostrando a página principal de um recurso dos Serviços de Comunicação.":::

> [!NOTE]
> Este início rápido está mostrando o fluxo de personalização do Tipo de número `Toll-free`. A experiência poderá ser ligeiramente diferente se você tiver escolhido o Tipo de número `Geographic`, mas o resultado final será o mesmo.

Escolha o `Area code` na lista de Códigos de área disponíveis, insira a quantidade que deseja provisionar e clique em `Search` para encontrar números que atendam aos requisitos selecionados. Os números de telefone que atenderem às suas necessidades serão mostrados, bem como seu custo mensal.

:::image type="content" source="../media/manage-phone-azure-portal-found-numbers.png" alt-text="Captura de tela mostrando a página principal de um recurso dos Serviços de Comunicação.":::

> [!NOTE]
> A disponibilidade depende do Tipo de número, do local e do plano que você selecionou.
> Os números ficam reservados por um período curto antes que a transação expire. Se a transação expirar, você precisará selecionar os números novamente.

Para exibir o resumo da compra e fazer seu pedido, clique no botão `Next: Summary` na parte inferior da página.

### <a name="place-order"></a>Fazer pedido

A página de resumo trará uma revisão com o Tipo de número, os Recursos, os Números de telefone e o Custo mensal total para provisionar os números de telefone.

> [!NOTE]
> Os preços mostrados são os **encargos mensais recorrentes**, que cobrem o custo de cessão do número de telefone selecionado a você. Nessa exibição, não estão incluídos os **Custos pagos conforme o uso**, que são cobrados quando você faz ou recebe chamadas. As listas de preços estão [disponíveis aqui](../../concepts/pricing.md). Esses custos dependem do tipo de número e dos destinos das chamadas. Por exemplo, o preço por minuto de uma chamada de um número regional de Seattle para um número regional de Nova York e de uma chamada do mesmo número para um número de celular do Reino Unido poderão ser diferentes.

Por fim, clique em `Place order` na parte inferior da página para confirmar.

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers-summary.png" alt-text="Captura de tela mostrando a página principal de um recurso dos Serviços de Comunicação.":::

## <a name="find-your-phone-numbers-on-the-azure-portal"></a>Localizar seus números de telefone no portal do Azure

Navegue até seu Recurso de Comunicação do Azure no [portal do Azure](https://portal.azure.com):

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="Captura de tela mostrando a página principal de um recurso dos Serviços de Comunicação.":::

Selecione a folha Números de Telefone no menu para gerenciar seus números de telefone.

:::image type="content" source="../media/manage-phone-azure-portal-phones.png" alt-text="Captura de tela mostrando a página principal de um recurso dos Serviços de Comunicação.":::

> [!NOTE]
> Poderão ser necessários alguns minutos para que os números provisionados sejam mostrados nesta página.

### <a name="customizing-phone-number-plans"></a>Como personalizar planos de números de telefone
Na página `Numbers`, você pode selecionar o número de telefone clicando no número do qual deseja personalizar o plano.

:::image type="content" source="../media/manage-phone-azure-portal-capability-update.png" alt-text="Captura de tela mostrando a página principal de um recurso dos Serviços de Comunicação.":::

Selecione as funcionalidades na lista de funcionalidades de chamada e SMS disponíveis e clique em `Confirm` para aplicar a seleção.

## <a name="troubleshooting"></a>Solução de problemas

Perguntas e problemas comuns:

- Atualmente, há suporte para a compra de números de telefone somente nos EUA. Isso se baseia no endereço para cobrança da assinatura à qual o recurso está associado. No momento, não é possível mover um recurso para outra assinatura.

- Quando um número de telefone é liberado, ele não é liberado nem pode ser readquirido até o final do período de cobrança.

- Quando um recurso dos Serviços de Comunicação é excluído, os números de telefone associados a ele são liberados automaticamente ao mesmo tempo.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a:

> [!div class="checklist"]
> * Comprar um número de telefone
> * Gerenciar seu número de telefone
> * Liberar um número de telefone

> [!div class="nextstepaction"]
> [Enviar SMS](../telephony-sms/send.md)
> [Introdução às chamadas](../voice-video-calling/getting-started-with-calling.md)
