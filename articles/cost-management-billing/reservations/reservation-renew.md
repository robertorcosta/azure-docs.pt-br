---
title: Renovar automaticamente as reservas do Azure
description: Saiba como é possível renovar automaticamente as reservas do Azure para continuar recebendo descontos de reserva.
services: billing
author: bandersmsft
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: dfff02c554c94daf1dd7fd844cea461fd76589d8
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2020
ms.locfileid: "75995709"
---
# <a name="automatically-renew-reservations"></a>Renovar reservas automaticamente

Será possível renovar reservas para comprar automaticamente uma substituição quando uma reserva existente expirar. A renovação automática oferece uma maneira fácil de continuar recebendo descontos de reserva. Ela também poupa você de precisar monitorar de perto a expiração de uma reserva. Com a renovação automática, você impede a perda de benefícios de economia ao não precisar renovar manualmente. A configuração da renovação é desativada por padrão. Habilite o desabilite a configuração de renovação a qualquer momento, até a expiração da reserva existente.

A renovação de uma reserva cria uma quando a existente expira. Ela não prorroga o prazo da reserva existente.

Aceite renovar automaticamente a qualquer momento. O preço de renovação está disponível 30 dias antes do vencimento da reserva existente. Quando você habilita a renovação mais de 30 dias antes da expiração da reserva, você recebe um email que detalha os custos de renovação 30 dias antes da expiração. O preço da reserva pode ter mudado entre a hora em que você bloqueia o preço de renovação e a hora da renovação. Em caso afirmativo, seu custo de renovação é o menor dos dois custos. É possível fazer alterações na quantidade de reserva. Em caso afirmativo, a renovação é atualizada para usar o preço no mercado definido no momento da alteração da quantidade.

Não há obrigação de renovar e você pode recusar a renovação a qualquer momento antes do vencimento da reserva atual.

## <a name="set-up-renewal"></a>Configurar renovação

Acesse portal do Azure > **Reservas**.

1. Selecione a reserva.
2. Clique em **Renovação**.
3. Selecione **Comprar automaticamente uma nova reserva após o vencimento**.  
  ![Exemplo mostrando a renovação da reserva](./media/reservation-renew/reservation-renewal.png)

## <a name="if-you-dont-renew"></a>Se você não renovar

Seus serviços continuarão sendo executados normalmente. Serão cobradas taxas pagas conforme o uso após o vencimento da reserva.

## <a name="required-renewal-permissions"></a>Permissões de renovação necessárias

As seguintes condições são necessárias para renovar uma reserva:

- Você deverá ser proprietário da reserva existente.
- Você deverá ser proprietário da reserva se o escopo dela for uma assinatura única ou grupo de recursos.
- Você deverá ser proprietário da assinatura se ela tiver um escopo compartilhado.

## <a name="default-renewal-settings"></a>Configurações de renovação padrão

Por padrão, a renovação herda todas as propriedades da reserva que está expirando. Uma compra de renovação de reserva tem os mesmos SKU, região, escopo, assinatura para cobrança, prazo e quantidade.

No entanto, é possível atualizar a quantidade de compra de reserva de renovação para otimizar sua economia.

## <a name="when-the-new-reservation-is-purchased"></a>Quando a nova reserva é comprada

Uma nova reserva é comprada quando a existente expira. Tentamos impedir qualquer atraso entre as duas reservas. A continuidade garante que seus custos sejam previsíveis e que você continue tendo descontos.

## <a name="changing-parent-reservation-after-setting-renewal"></a>Alterar a reserva pai após a configuração da renovação

Se você fizer algumas das seguintes alterações no vencimento da reserva, a renovação dela será cancelada:

- Divisão
- Mesclar
- Transferir a reserva de uma conta para outra
- Transferir a reserva de uma assinatura WebDirect para uma assinatura EA (Contrato Enterprise) ou qualquer outro método de compra
- Renovar o registro

A nova reserva herda a configuração de flexibilidade de tamanho da instância e o escopo da reserva que está expirando durante a renovação.

## <a name="new-reservation-permissions"></a>Novas permissões de reserva

O Azure copia as permissões da reserva que está expirando para a nova reserva. Além disso, o administrador da conta de assinatura da compra de reserva tem acesso à nova reserva.

## <a name="potential-renewal-problems"></a>Potenciais problemas de renovação

O Azure poderá não processar a renovação se:

- O pagamento não puder ser cobrado
- Um erro do sistema ocorrer durante a renovação
- O SKU que está expirando não estiver ativo durante a renovação
- O EA for renovado para um EA diferente

Você receberá uma notificação por email se qualquer uma das condições anteriores ocorrerem e a renovação for desativada.

## <a name="renewal-notification"></a>Notificação de renovação

Os emails são enviados para diferentes pessoas dependendo do método de compra:

- Clientes EA – os emails são enviados para os contatos de notificação definidos no Portal do EA.
- Clientes de assinaturas individuais com taxas pagas conforme o uso – os emails são enviados aos usuários configurados como administradores da conta.
- Clientes do Provedor de Soluções na Nuvem – os emails são enviados para o contato de notificação parceiro.

## <a name="next-steps"></a>Próximas etapas
- Para obter mais informações sobre as Reservas do Azure, confira [O que são Reservas do Azure?](save-compute-costs-reservations.md)
