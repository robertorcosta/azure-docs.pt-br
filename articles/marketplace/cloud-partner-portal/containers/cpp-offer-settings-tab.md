---
title: Configurações da oferta para uma imagem de contêineres do Azure | Azure Marketplace
description: Definir configurações de oferta para um contêiner do Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 8742f4eb6ce8f3e1b7c4b0449c2f19d8a254e5dc
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823176"
---
# <a name="container-offer-settings-tab"></a>Guia de configuração da oferta do contêiner

A página **Containers> New Offer** abre com o foco na guia **Offer Settings**. 

![Identidade da Oferta](./media/containers-offer-settings.png)

## <a name="offer-identity-settings"></a>Configurações de Identidade de Oferta

Em **Oferecer Identidade**, forneça as informações para os campos descritos na tabela a seguir. Um asterisco (*) anexado ao nome do campo indica que é obrigatório. 

|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
| **ID da oferta\***       | Um identificador exclusivo (dentro de um perfil de editor) para a oferta. Este identificador estará visível em URLs de produto e relatórios de insight. Tem um comprimento máximo de 50 caracteres e pode usar caracteres alfanuméricos minúsculos e traços (-). (O identificador não pode terminar com um traço.) **Observação:** Este campo não pode ser alterado depois que uma oferta é ativada. <br> Por exemplo, se a Contoso publica uma oferta com o ID de oferta **sample-container**, ela é atribuída ao URL do Azure Marketplace `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-container?tab=Overview`. |
| **ID do Publicador\***     | Identificador exclusivo da sua organização no Azure Marketplace. Todas as suas ofertas devem ser associadas à sua ID do publicador. Esse valor não pode ser alterado depois que a oferta é salva. |
| **Nome\***          | O nome de exibição da oferta. Esse nome é exibido no Azure Marketplace e no Portal do Cloud Partner. Ele pode ter um máximo de 50 caracteres. É recomendável usar um nome de marca reconhecível para o seu produto. Não inclua o nome da sua organização, a menos que seja como o produto é comercializado. Se você estiver promovendo essa oferta em outros sites e publicações, verifique se o nome é exatamente o mesmo em todas as publicações. |
|  |  |

Selecione **Salvar** para salvar Configurações de Oferta.


## <a name="next-steps"></a>Próximas etapas

Use a guia [SKUs](./cpp-skus-tab.md) para configurar as SKUs para sua oferta.
