---
title: Guia configurações de oferta de máquina virtual no portal de parceiros em nuvem para o Azure Marketplace
description: Descreve a guia Configurações da oferta usada na criação de uma oferta de VM do Marketplace do Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: e5353797aaa7d40e1b6a95685b79cb3fe99f5ecd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277643"
---
# <a name="virtual-machine-offer-settings-tab"></a>Guia de configuração da oferta de máquina virtual

A página **Nova oferta** para máquinas virtuais é aberta na primeira guia chamada **Offer Settings**.  

![Nova página de oferta para máquinas virtuais](./media/publishvm_004.png)


## <a name="offer-settings-fields"></a>Campos de Configurações da Oferta

Na guia **Configurações de oferta,** você deve fornecer os seguintes campos.  Um asterisco anexado (*) no nome do campo indica que isso é necessário. 

|  **Campo**       |     **Descrição**                                                          |
|  ---------       |     ---------------                                                          |
| **ID da oferta\***   | Um identificador exclusivo (dentro de um perfil de editor) para a oferta. Esse identificador ficará visível em URLs de produtos, modelos do Azure Resource Manager e relatórios de faturamento. Ele tem um comprimento máximo de 50 caracteres, só pode ser composto de caracteres alfanuméricos minúsculos e traços (-), mas não pode terminar em um traço. Este campo não pode ser alterado depois que uma oferta entra no ar. <br> Por exemplo, se a Contoso publica uma oferta com o ID de oferta **sample-vm**, ela é atribuída ao URL do Azure Marketplace`https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview`. |
| **Editor\***  | Identificador exclusivo da sua organização no Azure Marketplace. Todas as suas ofertas devem ser associadas à sua ID do publicador. Este valor não pode ser modificado quando a oferta é salva. |
| **Nome\***       | Nome de exibição da sua oferta. Este nome será exibido no Azure Marketplace e no Portal do Cloud Partner. Ele pode ter um máximo de 50 caracteres. A orientação aqui é incluir um nome de marca reconhecível para o seu produto. Não inclua o nome da sua organização aqui, a menos que seja assim que é comercializado. Se você estiver promovendo essa oferta em outros sites e publicações, verifique se o nome é exatamente o mesmo em todas as publicações. |
|   |   |
 
Clique **em Salvar** depois de fornecer todos os campos. 


## <a name="next-steps"></a>Próximas etapas

A próxima guia, você adicionará [SKUs](./cpp-skus-tab.md) para sua oferta.
