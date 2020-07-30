---
title: Delegar uma sub-rede para Azure NetApp Files | Microsoft Docs
description: Descreve como delegar uma sub-rede para o Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/28/2020
ms.author: b-juche
ms.openlocfilehash: 14c97fdea57fa50faf8b73275ec406ea36fbf552
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420389"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Delegar uma sub-rede ao Azure NetApp Files 

É necessário delegar uma sub-rede para Azure NetApp Files.   Quando você cria um volume, é necessário especificar a sub-rede delegada.

## <a name="considerations"></a>Considerações
* O assistente para criar uma nova sub-rede usa como padrão uma máscara de rede /24, que possibilita 251 endereços IP disponíveis. Usar uma máscara de rede/28, que fornece por 11 endereços IP utilizáveis, é suficiente para o serviço.
* Em cada VNet (Rede virtual do Azure), apenas uma sub-rede pode ser delegada para o Azure NetApp Files.   
   O Azure permite que você crie várias sub-redes delegadas em uma VNet.  No entanto, qualquer tentativa de criar um novo volume falhará se você usar mais de uma sub-rede delegada.  
   Você pode ter apenas uma única sub-rede delegada em uma VNet. Uma conta do NetApp pode implantar volumes em vários VNets, cada um com sua própria sub-rede delegada.  
* Não é possível designar um grupo de segurança de rede nem um ponto de extremidade de serviço na sub-rede delegada. Fazer isso causa a falha da delegação da sub-rede.
* Atualmente, não há suporte para o acesso a um volume de uma rede virtual emparelhada globalmente.
* Não há suporte para a criação de [rotas personalizadas definidas pelo usuário](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) em sub-redes VM com prefixo de endereço (destino) para uma sub-rede delegada a Azure NetApp files. Isso afetará a conectividade da VM.

## <a name="steps"></a>Etapas 
1.  Acesse a folha **Redes virtuais** no portal do Azure e selecione a rede virtual que você deseja usar para o Azure NetApp Files.    

1. Selecione **Sub-redes** na folha da Rede virtual e clique no botão **+ Sub-rede**. 

1. Crie uma nova sub-rede a ser usada para o Azure NetApp Files preenchendo os campos obrigatórios a seguir na página Adicionar sub-rede:
    * **Nome**: especifique o nome da sub-rede.
    * **Intervalo de endereços**: especifique o intervalo de endereços IP.
    * **Delegação de sub-rede**: selecione **Microsoft. NetApp/volumes**. 

      ![Delegação de sub-rede](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
Também é possível criar e delegar uma sub-rede quando você [cria um volume do Azure NetApp Files](azure-netapp-files-create-volumes.md). 

## <a name="next-steps"></a>Próximas etapas  
* [Criar um volume para Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Saiba mais sobre a integração de rede virtual para os serviços do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


