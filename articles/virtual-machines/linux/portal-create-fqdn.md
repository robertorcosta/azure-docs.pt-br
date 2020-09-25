---
title: Criar FQDN para uma VM no portal do Azure
description: Saiba como criar um nome de domínio totalmente qualificado, ou FQDN, para uma máquina virtual Linux no portal do Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: deae22ad0c763e48df053d19beefba6054ed2767
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91331464"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Criar um nome de domínio totalmente qualificado no Portal do Azure para uma VM Linux

Quando você cria uma máquina virtual (VM) no [portal do Azure](https://portal.azure.com), um recurso de IP público para a máquina virtual é criado automaticamente. Use esse endereço IP para acessar a VM remotamente. Embora o portal não crie um [nome de domínio totalmente qualificado](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) ou FQDN, você poderá adicionar um depois que a VM for criada. Este artigo apresenta as etapas para criar um nome DNS ou FQDN.

## <a name="create-a-fqdn"></a>Criar um FQDN
Este artigo pressupõe que você já tenha criado uma VM. Se necessário, [crie uma VM no portal](quick-create-portal.md) ou [com a CLI do Azure](quick-create-cli.md). Siga estas etapas depois que a VM estiver em execução:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Agora você pode se conectar à VM remotamente usando esse nome DNS, assim como com `ssh azureuser@mydns.westus.cloudapp.azure.com`.

## <a name="next-steps"></a>Próximas etapas
Agora que sua VM tem um IP público e um nome DNS, é possível implantar estruturas comuns do aplicativo ou serviços, como o nginx, MongoDB, Docker, etc.

Leia mais sobre como [usar o Resource Manager](../../azure-resource-manager/management/overview.md) para obter dicas sobre a criação de implantações do Azure.

