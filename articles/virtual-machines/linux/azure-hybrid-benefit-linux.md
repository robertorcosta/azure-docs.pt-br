---
title: VMs Benefício Híbrido do Azure e Linux
description: O Benefício Híbrido do Azure permite que você economize dinheiro em suas máquinas virtuais Linux em execução no Azure.
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: westonh
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: alsin
ms.openlocfilehash: a9ea3b21908d1f461745ffe2689b36f8efcb6f89
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90994044"
---
# <a name="azure-hybrid-benefit--how-it-applies-for-linux-virtual-machines"></a>Benefício Híbrido do Azure – como ele se aplica para Máquinas Virtuais do Linux

## <a name="overview"></a>Visão geral

Benefício Híbrido do Azure permite que você migre mais facilmente seu servidor local Red Hat Enterprise Linux (RHEL) e SUSE Linux Enterprise (SLES) para o Azure usando sua própria assinatura de software Red Hat ou SUSE pré-existente. Com esse benefício, você paga apenas pelos custos de infraestrutura da sua VM, pois a taxa de software é coberta por sua assinatura RHEL ou SLES. O benefício é aplicável a todas as imagens de PAYG (pré-pago) do RHEL e SLES Marketplace.

## <a name="benefit-description"></a>Descrição do benefício

Por meio do Benefício Híbrido do Azure, você pode migrar mais facilmente seus servidores RHEL e SLES locais para o Azure convertendo VMs RHEL e SLES PAYG existentes no Azure para cobrança BYOS (traga sua própria assinatura). Normalmente, as VMs implantadas de imagens PAYG no Azure cobrarão uma taxa de infraestrutura, bem como uma taxa de software. Com o Benefício Híbrido do Azure, as VMs PAYG podem ser convertidas em um modelo de cobrança BYOS sem uma reimplantação, evitando qualquer risco de tempo de inatividade.

![Benefício Híbrido do Azure visualização de custo em VMs Linux](./media/ahb-linux/azure-hybrid-benefit-cost.png)

Após habilitar o benefício em uma VM RHEL ou SLES, você não será mais cobrado pela taxa de software adicional normalmente incorrida em uma VM PAYG. Em vez disso, sua VM começará a emitir um encargo de BYOS, que inclui apenas a taxa de hardware de computação e nenhuma taxa de software.

Se desejar, você também poderá converter uma VM que tenha o benefício habilitado nele de volta para um modelo de cobrança PAYG.

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Escopo da elegibilidade Benefício Híbrido do Azure para VMs Linux

Benefício Híbrido do Azure está disponível para todas as imagens de PAYG do RHEL e SLES Marketplace. O benefício ainda não está disponível para imagens RHEL ou SLES Marketplace BYOS ou imagens personalizadas.

As instâncias reservadas, os hosts dedicados e os benefícios híbridos do SQL não estarão qualificados para o Benefício Híbrido do Azure se você já estiver usando o benefício com VMs do Linux.

## <a name="how-to-get-started"></a>Como começar

O Benefício Híbrido do Azure está atualmente em uma fase de visualização para VMs do Linux. Depois de obter acesso à visualização, você poderá habilitar o benefício usando o portal do Azure ou o CLI do Azure.

### <a name="preview"></a>Versão Prévia

Nesta fase, você pode obter acesso ao benefício preenchendo o formulário [aqui](https://aka.ms/ahb-linux-form). Depois de preencher o formulário, suas assinaturas do Azure serão habilitadas para o benefício e você receberá uma confirmação da Microsoft dentro de três dias úteis.

### <a name="red-hat-customers"></a>Clientes do Red Hat

1.    Preencha o formulário de solicitação de visualização acima
1.    Registrar com o [programa de acesso à nuvem do Red Hat](https://aka.ms/rhel-cloud-access)
1.    Habilite suas assinaturas do Azure para acesso à nuvem e habilite as assinaturas que contêm as VMs com as quais você deseja usar o benefício
1.    Aplique o benefício às suas VMs existentes por meio do portal do Azure ou CLI do Azure
1.    Registre suas VMs recebendo o benefício com uma fonte separada de atualizações

### <a name="suse-customers"></a>Clientes do SUSE

1.    Preencha o formulário de solicitação de visualização acima
1.    Registre-se no programa de nuvem pública SUSE
1.    Aplique o benefício às suas VMs existentes por meio do portal do Azure ou CLI do Azure
1.    Registre suas VMs recebendo o benefício com uma fonte separada de atualizações

### <a name="enable-and-disable-the-benefit-in-the-azure-portal"></a>Habilitar e desabilitar o benefício no portal do Azure

Você pode habilitar o benefício em VMs existentes visitando a folha **configuração** e seguindo as etapas ali. Você pode habilitar o benefício em novas VMs durante a experiência de criação da VM.

### <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>Habilitar e desabilitar o benefício no CLI do Azure

Você pode usar o comando ' AZ VM Update ' para atualizar as VMs existentes. Para VMs RHEL, execute o comando com um parâmetro--License-Type de "RHEL_BYOS". Para VMs SLES, execute o comando com um parâmetro--License-Type de "SLES_BYOS".

#### <a name="cli-example-to-enable-the-benefit"></a>Exemplo de CLI para habilitar o benefício:
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
#### <a name="cli-example-to-disable-the-benefit"></a>Exemplo da CLI para desabilitar o benefício:
Para desabilitar o benefício, use um valor de tipo de licença de "nenhum"
```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

#### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>Exemplo de CLI para habilitar o benefício em um grande número de VMs
Para habilitar o benefício em um grande número de VMs, você pode usar o `--ids` parâmetro no CLI do Azure.

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file containing a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

Os exemplos a seguir mostram dois métodos para obter uma lista de IDs de recurso – um no nível de grupo de recursos, um no nível de assinatura.
```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="check-ahb-status-of-a-vm"></a>Verificar o status de AHB de uma VM
Você pode exibir o status AHB de uma VM de três maneiras: fazendo check-in no portal, usando CLI do Azure ou usando o serviço de metadados de instância do Azure (Azure IMDS).


### <a name="portal"></a>Portal

Exiba a folha configuração e verifique o status de licenciamento para ver se o AHB está habilitado para sua VM.

### <a name="azure-cli"></a>CLI do Azure

O `az vm get-instance-view` comando pode ser usado para essa finalidade. Procure um campo LicenseType na resposta. Se o campo LicenseType existir e o valor for ' RHEL_BYOS ' ou ' SLES_BYOS ', sua VM terá o benefício habilitado.

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Serviço de metadados de instância do Azure

De dentro da própria VM, você pode consultar os metadados atestados do IMDS para determinar o LicenseType da VM. Um valor de LicenseType de ' RHEL_BYOS ' ou ' SLES_BYOS ' indicará que sua VM tem o benefício habilitado. Saiba mais sobre os metadados atestados [aqui](https://docs.microsoft.com/azure/virtual-machines/linux/instance-metadata-service#attested-data)

## <a name="compliance"></a>Conformidade

### <a name="red-hat"></a>Red Hat

Para usar Benefício Híbrido do Azure para suas VMs RHEL, você deve primeiro ser registrado com o programa Red Hat Cloud Access. Você pode fazer isso por meio do site do Red Hat Cloud Access aqui. Depois de habilitar o benefício em sua VM, você deve registrar a VM com sua própria fonte de atualizações com o Gerenciador de assinaturas do Red Hat ou o satélite do Red Hat. O registro para atualizações garantirá que você permaneça em um estado com suporte.

### <a name="suse"></a>SUSE

Para usar Benefício Híbrido do Azure para suas VMs SLES, você deve primeiro ser registrado com o programa de nuvem pública SUSE. Saiba mais sobre o programa aqui. Depois de adquirir as assinaturas do SUSE, você deve registrar suas VMs usando essas assinaturas em sua própria fonte de atualizações usando o centro do cliente do SUSE, o servidor da ferramenta de gerenciamento de assinatura ou o Gerenciador do SUSE.

## <a name="frequently-asked-questions"></a>Perguntas frequentes
*P: posso usar um tipo de licença "RHEL_BYOS" com uma imagem SLES ou vice-versa?*

R: não é possível. A tentativa de inserir um tipo de licença que corresponde incorretamente ao distribuição em execução em sua VM não atualizará nenhum metadado de cobrança. No entanto, se você inserir acidentalmente o tipo de licença errado, atualizar sua VM novamente para o tipo de licença correto ainda habilitará o benefício.

*P: Eu me registrei com o Red Hat Cloud Access, mas ainda não consigo habilitar o benefício em minhas VMs RHEL. O que devo fazer?*

R: pode levar algum tempo para que o registro da assinatura do Red Hat Cloud Access se propague do Red Hat para o Azure. Se você ainda estiver vendo o erro após um dia útil, entre em contato com o suporte da Microsoft.

## <a name="common-errors"></a>Erros comuns
Esta seção contém uma lista de erros comuns e etapas para mitigação.

| Erro | Atenuação |
| ----- | ---------- |
| "A assinatura não está registrada para a versão prévia do Linux do Benefício Híbrido do Azure. Para obter instruções passo a passo, consulte https://aka.ms/ahb-linux " | Preencha o formulário em https://aka.ms/ahb-linux-form para registrar-se para a versão prévia do Linux do benefício híbrido do Azure.
| "A ação não pôde ser concluída porque nossos registros mostram que você não habilitou com êxito o acesso à nuvem Red Hat em sua assinatura do Azure...." | Para usar o benefício com VMs RHEL, você deve primeiro registrar suas assinaturas do Azure com acesso à nuvem Red Hat. Visite este link para saber mais sobre como registrar suas assinaturas do Azure para acesso à nuvem Red Hat

## <a name="next-steps"></a>Próximas etapas
* Comece com a versão prévia preenchendo o formulário [aqui](https://aka.ms/ahb-linux-form).
