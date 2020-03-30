---
title: Faturamento medido usando o serviço de medição de marketplace | Mercado Azure
description: Esta documentação é um guia para isvs que publicam ofertas SaaS com modelos flexíveis de faturamento.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 8e5a4813301cbab16d1cffabaaa60688f6e826ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281316"
---
# <a name="metered-billing-using-the-marketplace-metering-service"></a>Faturamento medido usando o serviço de medição de marketplace

Com o serviço de medição marketplace, você pode criar ofertas de software como serviço (SaaS) no programa de marketplace comercial que são cobradas de acordo com unidades não padronizadas.  Antes de publicar esta oferta, você define as dimensões de faturamento, como largura de banda, tickets ou e-mails processados.  Os clientes então pagam de acordo com o consumo dessas dimensões, com seu sistema informando a Microsoft através da API de serviço de medição marketplace de eventos faturados à medida que ocorrem.  

## <a name="prerequisites-for-metered-billing"></a>Pré-requisitos para faturamento medido

Para que uma oferta SaaS use faturamento medido, ele deve:

* Atenda a todos os requisitos de oferta para uma [venda através da oferta da Microsoft](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#sell-through-microsoft) conforme descrito em Criar uma oferta [SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer).
* Integre-se às [APIs de Atendimento saas](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) para que os clientes ofereçam e conectem-se à sua oferta.  
* Configure-se para o modelo de preços **de taxa fixa** para cobrar dos clientes pelo seu serviço.  As dimensões são uma extensão opcional para o modelo de preços de taxa fixa. 
* Integre-se com as [APIs](./marketplace-metering-service-apis.md) do serviço de medição marketplace para informar a Microsoft sobre eventos faturados.

>[!Note]
>O serviço de medição de marketplace está disponível apenas para o modelo de faturamento de tarifa fixa, e não se aplica ao modelo de faturamento por usuário.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Como o faturamento medido se encaixa com o preço

Quando se trata de definir a oferta junto com seus modelos de preços, é importante entender a hierarquia da oferta.

* Cada oferta SaaS é configurada para vender através da Microsoft ou não.  Esta configuração não pode ser alterada após a publicação de uma oferta.
* Cada oferta SaaS, configurada para vender através da Microsoft, pode ter um ou mais planos. Um usuário assina a oferta SaaS, mas ela é comprada através da Microsoft dentro do contexto de um plano.
* Cada plano tem um modelo de preços associado a ele: **taxa fixa** ou **por usuário.** Todos os planos em uma oferta devem estar associados ao mesmo modelo de preços. Por exemplo, não pode haver uma oferta onde um de seus planos é o modelo de preços de taxa fixa, e outro é por modelo de preços do usuário.
* Dentro de cada plano configurado para um modelo de faturamento de taxa fixa, pelo menos uma taxa recorrente (que pode ser de $0) está incluída:
    * Mensalidade **monthly** recorrente: mensalidade fixa que é pré-paga em uma recorrência mensal quando o usuário compra o plano.
    * Taxa **anual** recorrente: taxa anual fixa que é pré-paga em uma recorrência anual quando o usuário compra o plano.
* Além das taxas recorrentes, o plano também pode incluir dimensões opcionais usadas para cobrar dos clientes pelo uso não incluído na tarifa fixa.   Cada dimensão representa uma unidade de faturamento que seu serviço comunicará à Microsoft usando a [API](./marketplace-metering-service-apis.md)do serviço de medição Marketplace .

## <a name="sample-offer"></a>Oferta de amostra

Como exemplo, Contoso é um editor com um serviço SaaS chamado Contoso Notification Services (CNS). O CNS permite que os clientes enviem notificações por e-mail ou texto. Contoso está registrado como editor no Partner Center para o programa de marketplace comercial para publicar ofertas aos clientes do Azure.  Existem dois planos associados ao CNS, descritos abaixo:

* Plano base
    * Envie 10000 e-mails e 1000 textos por $0/mês
    * Além dos e-mails de 10000, pague $1 para cada 100 e-mails
    * Além dos 1000 textos, pague $0,02 por cada texto
* Plano Premium
    * Envie 50000 e-mails e 10000 textos por $350/mês
    * Além dos e-mails de 50000, pague $0,5 para cada 100 e-mails
    * Além dos textos de 10000, pague $0,01 por cada texto

Um cliente do Azure que assina o serviço CNS poderá enviar a quantidade incluída de texto e e-mails por mês com base no plano selecionado.  Contoso mede o uso até a quantidade incluída sem enviar nenhum evento de uso para a Microsoft.  Quando os clientes consomem mais do que a quantidade incluída, eles não têm que mudar de planos ou fazer nada diferente.  Contoso irá medir o excesso além da quantidade incluída e começar a emitir eventos de uso para a Microsoft para uso adicional usando a [API do serviço de medição Marketplace](./marketplace-metering-service-apis.md).  A Microsoft, por sua vez, cobrará do cliente pelo uso adicional, conforme especificado pelo editor.

## <a name="billing-dimensions"></a>Dimensões de faturamento

As dimensões de faturamento são usadas para comunicar ao cliente sobre como eles serão cobrados pelo uso do software, e também para comunicar eventos de uso à Microsoft. Eles são definidos da seguinte forma:

* **Identificador de dimensão:** o identificador imutável referenciado ao emitir eventos de uso.
* **Nome da dimensão**: o nome de exibição associado à dimensão, por exemplo, "mensagens de texto enviadas".
* **Unidade de medida**: a descrição da unidade de faturamento, por exemplo, "por mensagem de texto" ou "por 100 e-mails".
* **Preço por unidade**: o preço de uma unidade da dimensão.  
* **Quantidade incluída para o prazo mensal**: quantidade de dimensão incluída por mês para os clientes que pagam a mensalidade recorrente, deve ser um inteiro.
* **Quantidade incluída para o prazo anual**: quantidade de dimensão incluída por mês para os clientes que pagam a taxa anual recorrente, deve ser um inteiro.

As dimensões de faturamento são compartilhadas em todos os planos para uma oferta.  Alguns atributos se aplicam à dimensão em todos os planos, e outros atributos são específicos do plano.

Os atributos que definem a dimensão em si são compartilhados em todos os planos para uma oferta.  Antes de publicar a oferta, uma alteração feita a esses atributos a partir do contexto de qualquer plano afetará a definição de dimensão em todos os planos.  Uma vez que você publicar a oferta, esses atributos não serão mais editáveis.  Esses atributos são:

* Identificador
* Nome
* Unidade de medida

Os outros atributos de uma dimensão são específicos para cada plano e podem ter valores diferentes de plano para plano.  Antes de publicar o plano você pode editar esses valores e somente este plano será afetado.  Uma vez que você publicar o plano, esses atributos não serão mais editáveis.  Esses atributos são:

* Preço por unidade
* Quantidade incluída para clientes mensais 
* Quantidade incluída para clientes anuais 

As dimensões também têm dois conceitos especiais, "habilitados" e "infinitos":

* **Habilitado** indica que este plano participa dessa dimensão.  Você pode querer deixar isso desverificado se você está criando um novo plano que não envie eventos de uso com base nesta dimensão.  Além disso, quaisquer novas dimensões adicionadas após a primeira publicação de um plano aparecerão como "não habilitadas" no plano já publicado.  Uma dimensão desabilitada não aparecerá em nenhuma lista de dimensões para um plano visto pelos clientes.
* **Infinito**, representado pelo símbolo infinito "⁄", indica que este plano participa desta dimensão, mas não medidorde uso contra esta dimensão.  Se você quiser indicar aos seus clientes que a funcionalidade representada por essa dimensão está incluída no plano, mas sem limite de uso.  Uma dimensão com uso infinito aparecerá em listas de dimensões para um plano visto pelos clientes, com uma indicação de que nunca incorrerá em uma cobrança para este plano.

>[!Note] 
>Os seguintes cenários são explicitamente suportados: <br> - Você pode adicionar uma nova dimensão a um novo plano.  A nova dimensão não será habilitada para nenhum plano já publicado. <br> - Você pode publicar um plano **de taxa fixa** sem quaisquer dimensões, em seguida, adicionar um novo plano e configurar uma nova dimensão para esse plano. A nova dimensão não será habilitada para planos já publicados.

## <a name="constraints"></a>Restrições

### <a name="trial-behavior"></a>Comportamento experimental

O faturamento medido usando o serviço de medição de marketplace não é compatível com a oferta de uma avaliação gratuita.  Não é possível configurar um plano para usar tanto o faturamento medido quanto uma avaliação gratuita.

### <a name="locking-behavior"></a>Comportamento de bloqueio

Como uma dimensão usada com o serviço de medição marketplace representa um entendimento de como um cliente pagará pelo serviço, todos os detalhes para uma dimensão não são mais editáveis uma vez que você publicá-lo.  É importante que você tenha suas dimensões totalmente definidas para um plano antes de publicar.
  
Uma vez que uma oferta é publicada com uma dimensão, os detalhes de nível de oferta para essa dimensão não podem mais ser alterados:

* Identificador
* Nome
* Unidade de medida

Uma vez que um plano é publicado, os detalhes do nível do plano não podem mais ser alterados:

* Preço por unidade
* Quantidade incluída para o prazo mensal
* Quantidade incluída para o período anual
* Se a dimensão está habilitada para o plano

### <a name="upper-limits"></a>Limites superiores

O número máximo de dimensões que podem ser configuradas para uma única oferta é de 18 dimensões únicas.

## <a name="get-support"></a>Obter suporte

Se você tiver um dos seguintes, você pode abrir um bilhete de apoio.

* Problemas técnicos com a API de serviço de medição de marketplace.
* Um problema que precisa ser amenado por causa de um erro ou bug do seu lado (ex. evento de uso errado).
* Quaisquer outras questões relacionadas ao faturamento medido. 

Siga as etapas abaixo para enviar seu bilhete de suporte:

1. Vá para a [página de suporte](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff). Os primeiros menus suspensos são preenchidos automaticamente para você. Para suporte ao Marketplace, identifique a família de produtos como **Cloud e Online Services**, o produto como Marketplace **Publisher**.  Não altere as seleções de menu suspenso pré-preenchidos.
2. Em "Select the product version", selecione **'Gerenciamento de ofertas ao vivo'.**
3. Em "Selecione uma categoria que melhor descreva o problema", escolha **aplicativos SaaS**.
4. Em "Selecione um problema que melhor descreva o problema", **selecione o faturamento medido**.
5. Ao selecionar o botão **Seguinte,** você será direcionado para a página **Desembecões,** onde poderá inserir mais detalhes sobre o seu problema.

Consulte [suporte para o programa de mercado comercial no Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support) para obter mais opções de suporte a editores.

## <a name="next-steps"></a>Próximas etapas

- Consulte [APIs de serviço de medição do Marketplace](./marketplace-metering-service-apis.md) para obter mais informações.
