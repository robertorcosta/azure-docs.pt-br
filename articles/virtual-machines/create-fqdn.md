---
title: Criar FQDN para uma VM no portal do Azure
description: Saiba como criar um FQDN (nome de domínio totalmente qualificado) para uma máquina virtual no portal do Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/03/2020
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 08d5e20bf0755a71e70a0e446cf96d33bb42ad59
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93351861"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Criar um nome de domínio totalmente qualificado no Portal do Azure para uma VM Linux

Quando você cria uma máquina virtual (VM) no [portal do Azure](https://portal.azure.com), um recurso de IP público para a máquina virtual é criado automaticamente. Use esse endereço IP para acessar a VM remotamente. Embora o portal não crie um [nome de domínio totalmente qualificado](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) ou FQDN, você poderá adicionar um depois que a VM for criada. Este artigo apresenta as etapas para criar um nome DNS ou FQDN. 

## <a name="create-a-fqdn"></a>Criar um FQDN
Este artigo pressupõe que você já tenha criado uma VM. Se necessário, você pode criar uma VM do [Linux](./linux/quick-create-portal.md) ou do [Windows](./windows/quick-create-portal.md) no Portal. Siga estas etapas depois que a VM estiver em execução:


1. Selecione sua VM no portal. Em **nome DNS** , selecione **Configurar**.
2. Insira o nome DNS e, em seguida, selecione **salvar** na parte superior da página.
3. Para retornar à folha de visão geral da VM, feche a folha **configuração** selecionando o **X** no canto superior direito. 
4. Verifique se o *nome DNS* agora é mostrado corretamente.
   



## <a name="next-steps"></a>Próximas etapas
Agora que sua VM tem um IP público e um nome DNS, você pode implantar estruturas de aplicativo ou serviços comuns, como Nginx, MongoDB e Docker.

Leia mais sobre como [usar o Resource Manager](../azure-resource-manager/management/overview.md) para obter dicas sobre a criação de implantações do Azure.

