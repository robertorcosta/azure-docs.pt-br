---
title: Reescreva os cabeçalhos de solicitação e resposta HTTP no portal - Gateway do aplicativo Azure
description: Saiba como usar o portal Azure para configurar um Gateway de aplicativo Azure para reescrever os cabeçalhos HTTP nas solicitações e respostas que passam pelo gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: b90736b3ed1c1f69488fde4a386cf215d751c362
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74012850"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>Reescreva os cabeçalhos de solicitação e resposta HTTP com o Gateway de aplicativos Do Azure - portal Azure

Este artigo descreve como usar o portal Azure para configurar uma instância [SKU do Application Gateway v2](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) para reescrever os cabeçalhos HTTP em solicitações e respostas.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Você precisa ter uma instância De Gateway de aplicativo v2 SKU para concluir as etapas deste artigo. A reescrita de cabeçalhos não é suportada no V1 SKU. Se você não tiver o V2 SKU, crie uma instância [SKU do Application Gateway v2](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) antes de começar.

## <a name="create-required-objects"></a>Criar objetos necessários

Para configurar a reescrita do cabeçalho HTTP, você precisa concluir essas etapas.

1. Crie os objetos necessários para reescrever o cabeçalho HTTP:

   - **Reescrever ação**: Usado para especificar os campos de solicitação e solicitação de cabeçalho que você pretende reescrever e o novo valor para os cabeçalhos. Você pode associar uma ou mais condições de reescrita com uma ação de reescrita.

   - **Condição de reescrita:** Uma configuração opcional. As condições de regravação avaliam o conteúdo das solicitações e respostas HTTP(S). A ação de reescrita ocorrerá se a solicitação ou resposta HTTP(S) corresponder à condição de reescrita.

     Se você associar mais de uma condição a uma ação, a ação só ocorre quando todas as condições são atendidas. Em outras palavras, a operação é uma operação lógica e.

   - **Regra de reescrita**: Contém combinações de várias combinações de ação de reescrita/ reescrita.

   - **Seqüência de**regras : Ajuda a determinar a ordem em que as regras de reescrita são executadas. Essa configuração é útil quando você tem várias regras de reescrita em um conjunto de reescrita. Uma regra de reescrita que tem um valor de seqüência de regras mais baixo é executada primeiro. Se você atribuir o mesmo valor de seqüência de regras a duas regras de reescrita, a ordem de execução não será determinista.

   - **Conjunto de regravação**: Contém várias regras de reescrita que serão associadas a uma regra de roteamento de solicitação.

2. Anexar o conjunto de reescrita a uma regra de roteamento. A configuração de reescrita é anexada ao ouvinte de origem através da regra de roteamento. Quando você usa uma regra básica de roteamento, a configuração de regravação de cabeçalho está associada a um ouvinte de origem e é uma reescrita global de cabeçalho. Quando você usa uma regra de roteamento baseada em caminho, a configuração de regravação de cabeçalho é definida no mapa de caminho da URL. Nesse caso, aplica-se apenas à área de caminho específica de um site.

Você pode criar vários conjuntos de reescrita de cabeçalho HTTP e aplicar cada conjunto de regravação a vários ouvintes. Mas você pode aplicar apenas uma reescrita definida a um ouvinte específico.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com/) com sua conta do Azure.

## <a name="configure-header-rewrite"></a>Configurar regravação do cabeçalho

Neste exemplo, modificaremos uma URL de redirecionamento reescrevendo o cabeçalho de localização na resposta HTTP enviada por um aplicativo back-end.

1. Selecione **Todos os recursos**e selecione o gateway de aplicativo.

2. Selecione **Regravações** no painel esquerdo.

3. Selecionar **regravar conjunto**:

   ![Adicionar conjunto de regravação](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Forneça um nome para o conjunto de reescrita e associe-o a uma regra de roteamento:

   - Digite o nome para a reescrita definida na caixa **Nome.**
   - Selecione uma ou mais regras listadas na lista **de regras de roteamento associadas.** Você pode selecionar apenas regras que não foram associadas a outros conjuntos de reescrita. As regras que já foram associadas a outros conjuntos de reescrita são escurecidas.
   - Selecione **Avançar**.
   
     ![Adicionar nome e associação](media/rewrite-http-headers-portal/name-and-association.png)

5. Criar uma regra de reescrita:

   - Selecione **Adicionar regra de reescrita**.

     ![Adicionar regra de reescrita](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - Digite um nome para a regra de reescrita na caixa **De nome de regra Reescrever.** Digite um número na caixa **de seqüência de regras.**

     ![Adicionar nome de regra de reescrita](media/rewrite-http-headers-portal/rule-name.png)

6. Neste exemplo, reescreveremos o cabeçalho de localização somente quando ele contiver uma referência a azurewebsites.net. Para isso, adicione uma condição para avaliar se o cabeçalho de localização na resposta contém azurewebsites.net:

   - Selecione **Adicionar condição** e, em seguida, selecione a caixa que contém as instruções **If** para expandi-la.

     ![Adicione uma condição](media/rewrite-http-headers-portal/add-condition.png)

   - No **Tipo de variável para verificar a** lista, selecione **cabeçalho HTTP**.

   - Na lista **de tipos de cabeçalho,** selecione **'Resposta '''**

   - Porque neste exemplo estamos avaliando o cabeçalho de localização, que é um cabeçalho comum, selecione **Cabeçalho comum** em **nome de Cabeçalho**.

   - Na lista **de cabeçalhos comum,** selecione **Local**.

   - Em **caso sensível a maiúsculas**e minúsculas, **selecione Não**.

   - Na lista **Operador,** selecione **igual (=)**.

   - Digite um padrão de expressão regular. Neste exemplo, usaremos o `(https?):\/\/.*azurewebsites\.net(.*)$`padrão .

   - Selecione **OK**.

     ![Configure uma condição Se](media/rewrite-http-headers-portal/condition.png)

7. Adicionar uma ação para reescrever o cabeçalho de localização:

   - Na lista **de tipos de ação,** selecione **Definir**.

   - Na lista **de tipos de cabeçalho,** selecione **'Resposta '''**

   - Em **nome de cabeçalho,** selecione **Cabeçalho comum**.

   - Na lista **de cabeçalhos comum,** selecione **Local**.

   - Digite o valor do cabeçalho. Neste exemplo, usaremos `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` como valor de cabeçalho. Este valor substituirá *azurewebsites.net* por *contoso.com* no cabeçalho de localização.

   - Selecione **OK**.

     ![Adicionar uma ação](media/rewrite-http-headers-portal/action.png)

8. Selecione **Criar** para criar o conjunto de regravação:

   ![Selecione Criar](media/rewrite-http-headers-portal/create.png)

9. A exibição do conjunto Reescrever será aberta. Verifique se o conjunto de regravação criado está na lista de conjuntos de regravação:

   ![Regravar a exibição do conjunto](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como configurar alguns casos de uso comum, consulte [cenários comuns de reescrita de cabeçalho](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).