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
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: b-juche
ms.openlocfilehash: b83f530549ffa43789963fd0c95b4982f5289356
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054457"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Delegar uma sub-rede ao Azure NetApp Files 

É necessário delegar uma sub-rede para Azure NetApp Files.   Quando você cria um volume, é necessário especificar a sub-rede delegada.

## <a name="considerations"></a>Considerações
* O assistente para criar uma nova sub-rede usa como padrão uma máscara de rede /24, que possibilita 251 endereços IP disponíveis. Usar uma máscara de rede /28, que possibilita 16 endereços IP utilizáveis, é suficiente para o serviço.
* Em cada VNet (Rede virtual do Azure), apenas uma sub-rede pode ser delegada para o Azure NetApp Files.   
   O Azure permite que você crie várias sub-redes delegadas em um VNet.  No entanto, qualquer tentativa de criar um novo volume falhará se você usar mais de uma sub-rede delegada.
* Não é possível designar um grupo de segurança de rede nem um ponto de extremidade de serviço na sub-rede delegada. Fazer isso causa a falha da delegação da sub-rede.
* O acesso a um volume de uma rede virtual com peerglobalmente não é suportado no momento.
* A criação [de rotas personalizadas definidas pelo usuário](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) em sub-redes VM com prefixo de endereço (destino) para uma sub-rede delegada aos Arquivos do Azure NetApp não é suportada. Isso afetará a conectividade VM.

## <a name="steps"></a>Etapas 
1.  Acesse a folha **Redes virtuais** no portal do Azure e selecione a rede virtual que você deseja usar para o Azure NetApp Files.    

1. Selecione **Sub-redes** na folha da Rede virtual e clique no botão **+ Sub-rede**. 

1. Crie uma nova sub-rede a ser usada para o Azure NetApp Files preenchendo os campos obrigatórios a seguir na página Adicionar sub-rede:
    * **Nome**: Especifique o nome da sub-rede.
    * **Intervalo de endereços**: Especifique o intervalo de endereços IP.
    * **Delegação de sub-rede**: Selecione **Microsoft.NetApp/volumes**. 

      ![Delegação de sub-rede](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
Também é possível criar e delegar uma sub-rede quando você [cria um volume do Azure NetApp Files](azure-netapp-files-create-volumes.md). 

## <a name="next-steps"></a>Próximas etapas  
* [Criar um volume para Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Conheça a integração de rede virtual para serviços do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


