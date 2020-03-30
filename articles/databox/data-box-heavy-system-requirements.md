---
title: Microsoft Azure Data Box Requisitos pesados do sistema| Microsoft Docs
description: Conheça os requisitos de software e rede para o Seu Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 6097dba9193fce4c8158137b488080107881994a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260066"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Azure Data Box Requisitos pesados do sistema

Este artigo descreve os requisitos importantes do sistema para o seu dispositivo Azure Data Box Heavy e para os clientes que se conectam ao dispositivo. Recomendamos que você revise as informações cuidadosamente antes de implantar sua Caixa de Dados Pesada e, em seguida, consulte-as conforme necessário durante a implantação e operação subsequente.

Os requisitos do sistema incluem:

* **Os requisitos de software para hosts que se conectam à Data Box Heavy** - descreve as plataformas suportadas, navegadores para a interface do usuário local, clientes SMB e quaisquer requisitos adicionais para hosts que possam se conectar à Data Box.
* **Os requisitos de rede para o Data Box Heavy** - fornece informações sobre os requisitos de rede para o funcionamento ideal do dispositivo Data Box Heavy.

## <a name="software-requirements"></a>Requisitos de software

Os requisitos de software incluem as informações sobre os sistemas operacionais com suporte, os navegadores com suporte para a interface do usuário Web local e os clientes SMB.

### <a name="supported-operating-systems-for-clients"></a>Sistemas operacionais compatíveis para clientes

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Sistemas de arquivos com suporte para clientes Linux

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>Contas de armazenamento com suporte

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Tipos de armazenamento com suporte

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Navegadores da Web com suporte

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Requisitos de rede

Seu datacenter deve ter uma rede de alta velocidade. Para velocidades de cópia mais rápidas, duas conexões de 40 GbE podem ser utilizadas em paralelo (uma por nó). Se você não tiver 40 GbE disponíveis, recomendamos que você tenha pelo menos duas conexões de 10 GbE (uma por nó).

### <a name="port-requirements"></a>Requisitos de porta

A tabela a seguir lista as portas que precisam ser abertas em seu firewall para permitir o tráfego de SMB ou NFS. Nesta tabela, *entrada* ou *de entrada* refere-se à direção da qual as solicitações do cliente acessam o dispositivo. *Saída* ou *saída* refere-se à direção em que seu dispositivo Data Box Heavy envia dados externamente, além da implantação: por exemplo, saída para a Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]

## <a name="next-steps"></a>Próximas etapas

* [Implante seu Azure Data Box](data-box-deploy-ordered.md)
