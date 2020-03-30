---
title: Configurações de oferta de aplicativos do Azure | Mercado Azure
description: Defina as configurações de oferta para uma oferta de aplicativo do Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: dsindona
ms.openlocfilehash: d111471e909a091d8c887f13ad89d1da50cf72b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280109"
---
# <a name="azure-application-offer-settings-tab"></a>Guia de Configurações de Oferta de aplicativo do Azure

Este artigo descreve como definir as configurações de oferta para um aplicativo do Azure.

A página **Azure Applications > New Offer** abre com o foco na guia **Configurações de oferta.** Um asterisco (*) anexado ao nome do campo indica que é necessário.

![Formulário de Identidade de Oferta](./media/azureapp-offer-settings-tab.png)

## <a name="offer-identity-settings"></a>Configurações de Identidade de Oferta

Em **Oferecer Identidade**, forneça as informações para os campos descritos na tabela a seguir.  

|    Campo         |       Descrição                                                            |
|  ---------       |     ---------------                                                          |
| **ID da oferta\***       | Um identificador exclusivo (dentro de um perfil de editor) para a oferta. Este identificador estará visível em URLs de produto e relatórios de insight. Tem um comprimento máximo de 50 caracteres e pode usar caracteres alfanuméricos minúsculos e traços (-). (O identificador não pode terminar com um traço.) **Nota:** Este campo não pode ser mudado depois que uma oferta for ao ar. <br> Por exemplo, se a Contoso publica uma oferta com o ID de oferta **sample-container**, ela é atribuída ao URL do Azure Marketplace `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-container?tab=Overview`. |
| **ID do editor\***     | Identificador exclusivo da sua organização no Azure Marketplace. Todas as suas ofertas devem ser associadas à sua ID do publicador. Esse valor não pode ser alterado depois que a oferta é salva. |
| **Nome\***          | O nome de exibição da oferta. Esse nome é exibido no Azure Marketplace e no Portal do Cloud Partner. Ele pode ter um máximo de 50 caracteres. É recomendável usar um nome de marca reconhecível para o seu produto. Não inclua o nome da sua organização a menos que seja assim que seu produto é comercializado. Se você estiver promovendo essa oferta em outros sites e publicações, verifique se o nome é exatamente o mesmo em todas as publicações. |
|  |  |

Selecione **Salvar** para salvar suas configurações de oferta.

## <a name="next-steps"></a>Próximas etapas

Use a guia [SKUs](./cpp-skus-tab.md) para configurar as SKUs para sua oferta.
