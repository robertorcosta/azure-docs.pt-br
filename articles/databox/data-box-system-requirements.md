---
title: Requisitos de sistema do Microsoft Azure Data Box | Microsoft Docs
description: Saiba mais sobre os requisitos de sistema importantes para seu Azure Data Box e para os clientes que se conectam ao Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 10/02/2020
ms.author: alkohli
ms.openlocfilehash: 5f1623ef4dde59e816e3afe5a5f5894c49469580
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91767875"
---
# <a name="azure-data-box-system-requirements"></a>Requisitos de sistema do Azure Data Box

Este artigo descreve os requisitos de sistema importantes para seu Microsoft Azure Data Box e para clientes que se conectam ao Data Box. Recomendamos que você examine as informações cuidadosamente antes de implantar seu Data Box e, em seguida, consulte-os conforme necessário durante a implantação e a operação.

Os requisitos do sistema incluem:

* **Requisitos de software:** Para hosts que se conectam ao Data Box, descreve sistemas operacionais com suporte, protocolos de transferência de arquivo, contas de armazenamento, tipos de armazenamento e navegadores para a interface do usuário da Web local.
* **Requisitos de rede:** Para o Data Box, descreve os requisitos de conexão de rede e de porta para a operação ideal do Data Box.


## <a name="software-requirements"></a>Requisitos de software

Os requisitos de software incluem sistemas operacionais com suporte, protocolos de transferência de arquivo, contas de armazenamento, tipos de armazenamento e navegadores para a interface do usuário da Web local.

### <a name="supported-operating-systems-for-clients"></a>Sistemas operacionais compatíveis para clientes

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]


### <a name="supported-file-transfer-protocols-for-clients"></a>Protocolos de transferência de arquivo com suporte para clientes

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

> [!IMPORTANT] 
> Não há suporte para a conexão com Data Box compartilhamentos via REST para ordens de exportação. 

### <a name="supported-storage-accounts"></a>Contas de armazenamento com suporte

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Tipos de armazenamento com suporte

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Navegadores da Web com suporte

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Requisitos de rede

Seu datacenter deve ter uma rede de alta velocidade. É altamente recomendável que você tenha pelo menos uma conexão de 10 GbE. Se uma conexão de 10 GbE não estiver disponível, um link de dados de 1 GbE poderá ser usado para copiar dados, mas as velocidades de cópia serão afetadas.

### <a name="port-requirements"></a>Requisitos de porta

A tabela a seguir lista as portas que precisam ser abertas no firewall para permitir o tráfego SMB ou NFS. Nesta tabela, *in* (*entrada*) refere-se à direção da qual o cliente de entrada solicita acesso ao seu dispositivo. *Out* (ou *saída*) refere-se à direção na qual o dispositivo de data Box envia dados externamente, além da implantação: por exemplo, de saída para a Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Próximas etapas

* [Implante seu Azure Data Box](data-box-deploy-ordered.md)
