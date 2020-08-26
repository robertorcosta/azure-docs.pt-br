---
title: Exibir reservas para recursos do Azure | Microsoft Docs
description: Saiba como exibir as Reservas do Azure no portal do Azure. Confira as reservas e a utilização por meio das APIs, do PowerShell, da CLI e do Power BI.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: ec91d0997717d536c3b47ce7b276f75b21d1baa1
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88681644"
---
# <a name="view-azure-reservations"></a>Exibir reservas do Azure

Você pode ver e gerenciar a reserva comprada no portal do Azure.

## <a name="permissions-to-view-a-reservation"></a>Permissões para exibir uma reserva

Para exibir ou gerenciar uma reserva, você precisa ter uma permissão de leitor ou proprietário nela. Por padrão, quando você compra uma reserva, você e o administrador da conta obtêm automaticamente a função Proprietário no pedido de reserva e na reserva. Para permitir que outras pessoas exibam a reserva, você precisa adicioná-las como **Proprietário** ou **Leitor** no pedido de reserva ou na reserva. Adicionar alguém à assinatura fornecida para cobrar a reserva não adiciona essa pessoa à reserva automaticamente. 

Para obter mais informações, consulte [Adicionar ou alterar os usuários que podem gerenciar uma reserva](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

## <a name="view-reservation-and-utilization-in-the-azure-portal"></a>Ver a reserva e a utilização no portal do Azure

Para ver uma reserva como Proprietário ou Leitor

1. Entre no [portal do Azure](https://portal.azure.com).
2. Acesse [Reservas](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
3. A lista mostrará todas as reservas nas quais você tiver a função Proprietário ou Leitor. Cada reserva mostra o último percentual de utilização conhecido.
4. Clique no percentual de utilização para ver o histórico e os detalhes de utilização. Veja os detalhes no vídeo abaixo.
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="get-reservations-and-utilization-using-apis-powershell-cli"></a>Obtenha reservas e a utilização usando APIs, o PowerShell e a CLI

Obter uma lista de todas as reservas usando os recursos a seguir
- [API: Pedido de Reserva – Lista](/rest/api/reserved-vm-instances/reservationorder/list)
- [PowerShell: Pedido de Reserva – Lista](/powershell/module/azurerm.reservations/get-azurermreservationorder)
- [CLI: Pedido de Reserva – Lista](/cli/azure/reservations/reservation-order#az-reservations-reservation-order-list)

Você também pode obter a [utilização de reserva](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) usando a API de uso da Instância Reservada. 

## <a name="see-reservations-and-utilization-in-power-bi"></a>Ver reservas e a utilização no Power BI

Há duas opções para os usuários do Power BI
- Pacote de Conteúdo: os dados de compras e utilização de reservas estão disponíveis no [pacote de conteúdo do Consumption Insights para Power BI](/power-bi/desktop-connect-azure-cost-management). Crie os relatórios que você deseja usando esse pacote de conteúdo. 
- Aplicativo de gerenciamento de custos: Use o [Aplicativo de Gerenciamento de Custos](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp) para os relatórios pré-criados que você pode personalizar ainda mais.

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar Reservas do Azure](manage-reserved-vm-instance.md).
- [Entender o uso de reserva para a sua assinatura paga conforme o uso](understand-reserved-instance-usage.md)
- [Entender o uso de reserva para seu registro de empresa](understand-reserved-instance-usage-ea.md)
- [Entender o uso de reserva para assinaturas de CSP](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Precisa de ajuda? Fale conosco

Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
