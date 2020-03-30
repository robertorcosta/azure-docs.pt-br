---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 12/21/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 3f9b86dd3716a25ab95265cf46b616144f57163b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "71168650"
---
## <a name="overview-of-ssh-and-keys"></a>Visão geral do SSH e das chaves

[SSH](https://www.ssh.com/ssh/) é um protocolo de conexão criptografado que permite entradas seguras sobre conexões não seguras. SSH é o protocolo de conexão padrão para as VMs Linux hospedadas no Azure. Embora o SSH em si forneça uma conexão criptografada, usar senhas com conexões SSH ainda deixa a VM vulnerável a ataques de força bruta ou à adivinhação de senhas. Um método mais seguro e preferido de se conectar a uma VM usando SSH é usando um par de chaves público-privado, também conhecido como *chaves SSH*. 

* A *chave pública* é colocada na VM Linux ou em qualquer outro serviço que você queira usar com criptografia de chave pública.

* A *chave privada* permanece em seu sistema local. Proteja essa chave privada. Não a compartilhe.

Quando você usa um cliente SSH para se conectar à sua VM do Linux (que tem a chave pública), a VM remota testa o cliente para garantir que ele tenha a chave privada. Se o cliente tiver a chave privada, será concedido o acesso à VM. 

Dependendo das políticas de segurança da sua organização, você pode reutilizar um único par de chaves públicas e privadas para acessar vários serviços e VMs do Azure. Não é preciso um par de chaves separado para cada VM ou serviço que você deseja acessar. 

Sua chave pública pode ser compartilhada com qualquer pessoa, mas apenas você (ou sua infraestrutura de segurança local) possui sua chave privada.