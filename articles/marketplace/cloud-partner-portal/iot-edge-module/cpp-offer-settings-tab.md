---
title: Configurações de oferta para um módulo Azure IoT Edge | Azure Marketplace
description: Defina as configurações de oferta para um módulo Azure IoT Edge.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d6d2306dfd44bad6e1f903c3cef3eeb4993f4cd9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82148031"
---
# <a name="iot-edge-module-offer-settings-tab"></a>Guia de Configurações da Oferta do módulo Azure IoT Edge

>[!Important]
>A partir de 13 de abril de 2020, começaremos a mover o gerenciamento de suas ofertas de módulo IoT Edge para o Partner Center. Após a migração, você criará e gerenciará suas ofertas no Partner Center. Siga as instruções em [criar um módulo de IOT Edge oferta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation) para gerenciar suas ofertas migradas.

A página **Módulos do Azure IoT Edge > Nova oferta** é aberta com o foco na guia **Configurações de Oferta**.

![Nova página de oferta para módulos do Azure IoT Edge](./media/iot-edge-module-offer-settings-tab.png)


## <a name="offer-identity-settings"></a>Configurações de Identidade de Oferta

Em **Oferecer Identidade**, forneça as informações para os campos descritos na tabela a seguir. Um asterisco (*) anexado ao nome do campo indica que é obrigatório. 

|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
| **ID da oferta\***       | Um identificador exclusivo (dentro de um perfil de editor) para a oferta. Este identificador estará visível em URLs de produto e relatórios de insight. Tem um comprimento máximo de 50 caracteres e pode usar caracteres alfanuméricos minúsculos e traços (-). (O identificador não pode terminar com um traço.) **Observação:** Este campo não pode ser alterado depois que uma oferta é ativada. <br> Por exemplo, se a Contoso publica uma oferta com a ID de oferta **sample-iot-edge-module**, ela é atribuída à URL do Azure Marketplace`https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-iot-edge-module?tab=Overview`. |
| **Editor\***     | Identificador exclusivo da sua organização no Azure Marketplace. Todas as suas ofertas devem ser associadas à sua ID do publicador. Esse valor não pode ser alterado depois que a oferta é salva. |
| **Name\***          | O nome de exibição da oferta. Esse nome é exibido no Azure Marketplace e no Portal do Cloud Partner. Ele pode ter um máximo de 50 caracteres. É recomendável usar um nome de marca reconhecível para o seu produto. Não inclua o nome da sua organização, a menos que seja como o produto é comercializado. Se você estiver promovendo essa oferta em outros sites e publicações, verifique se o nome é exatamente o mesmo em todas as publicações. |
|  |  |


Selecione **salvar** para salvar suas configurações de oferta.


## <a name="next-steps"></a>Próximas etapas

Use a guia [SKUs](./cpp-skus-tab.md) para configurar as SKUs para sua oferta.
