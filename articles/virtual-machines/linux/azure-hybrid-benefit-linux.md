---
title: VMs Benefício Híbrido do Azure e Linux
description: O Benefício Híbrido do Azure permite que você economize dinheiro em suas máquinas virtuais Linux em execução no Azure.
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: westonh
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: mathapli
ms.openlocfilehash: 35bed58f95deebb78d8e787c8bc3f522ce7004df
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326962"
---
# <a name="azure-hybrid-benefit--how-it-applies-for-linux-virtual-machines"></a>Benefício Híbrido do Azure – como ele se aplica para Máquinas Virtuais do Linux

## <a name="overview"></a>Visão geral

Benefício Híbrido do Azure permite que você migre com mais facilidade suas VMs (máquinas virtuais) Red Hat Enterprise Linux RHEL (local) e SUSE Linux Enterprise Server (SLES) para o Azure usando sua própria assinatura preexistente do software Red Hat ou SUSE. Com esse benefício, você paga apenas pelos custos de infraestrutura da sua VM, pois a taxa de software é coberta por sua assinatura RHEL ou SLES. O benefício é aplicável a todas as imagens de PAYG (pré-pago) do RHEL e SLES Marketplace.

> [!IMPORTANT]
> Benefício Híbrido do Azure para VMs do Linux está disponível para o público agora


## <a name="benefit-description"></a>Descrição do benefício

Por meio do Benefício Híbrido do Azure, você pode migrar mais facilmente seus servidores RHEL e SLES locais para o Azure convertendo VMs RHEL e SLES PAYG existentes no Azure para cobrança BYOS (traga sua própria assinatura). Normalmente, as VMs implantadas de imagens PAYG no Azure cobrarão uma taxa de infraestrutura, bem como uma taxa de software. Com o Benefício Híbrido do Azure, as VMs PAYG podem ser convertidas em um modelo de cobrança BYOS sem uma reimplantação, evitando qualquer risco de tempo de inatividade.

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Benefício Híbrido do Azure visualização de custos em VMs do Linux.":::

Após habilitar o benefício em uma VM RHEL ou SLES, você não será mais cobrado pela taxa de software adicional normalmente incorrida em uma VM PAYG. Em vez disso, sua VM começará a emitir um encargo de BYOS, que inclui apenas a taxa de hardware de computação e nenhuma taxa de software.

Se desejar, você também poderá converter uma VM que tenha o benefício habilitado nele de volta para um modelo de cobrança PAYG.

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Escopo da elegibilidade Benefício Híbrido do Azure para VMs Linux

Benefício Híbrido do Azure está disponível para todas as imagens de PAYG do RHEL e SLES Marketplace. O benefício ainda não está disponível para imagens RHEL ou SLES Marketplace BYOS ou imagens personalizadas.

As instâncias reservadas, os hosts dedicados e os benefícios híbridos do SQL não estarão qualificados para o Benefício Híbrido do Azure se você já estiver usando o benefício com VMs do Linux.

## <a name="how-to-get-started"></a>Como começar

### <a name="red-hat-customers"></a>Clientes do Red Hat

Benefício Híbrido do Azure para RHEL está disponível para clientes que têm assinaturas RHEL ativas/não usadas qualificadas para uso no Azure e que habilitaram uma ou mais dessas assinaturas para uso no Azure com o programa [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) . 

1.  Habilite uma ou mais de suas assinaturas RHEL qualificadas para uso no Azure usando a [interface do cliente do Red Hat Cloud Access](https://access.redhat.com/management/cloud).
1.  As assinaturas do Azure fornecidas durante o processo de habilitação do acesso à nuvem Red Hat terão permissão para usar o recurso Benefício Híbrido do Azure.
1.  Aplique o Benefício Híbrido do Azure a qualquer uma das suas VMs PAYG do RHEL existentes, bem como a novas VMs RHEL que você implantar de imagens PAYG do Azure Marketplace.
1.  Siga [as próximas etapas](https://access.redhat.com/articles/5419341) recomendadas para configurar fontes de atualização para suas VMs RHEL e para obter diretrizes de conformidade de assinatura do RHEL.


### <a name="suse-customers"></a>Clientes do SUSE

1.    Registre-se no programa de nuvem pública SUSE
1.    Aplique o benefício às VMs existentes por meio de CLI do Azure
1.    Registre suas VMs recebendo o benefício com uma fonte separada de atualizações


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
Você pode exibir o status de AHB de uma VM de duas maneiras: usando CLI do Azure ou usando o serviço de metadados de instância do Azure (Azure IMDS).


### <a name="azure-cli"></a>CLI do Azure

O `az vm get-instance-view` comando pode ser usado para essa finalidade. Procure um campo LicenseType na resposta. Se o campo LicenseType existir e o valor for ' RHEL_BYOS ' ou ' SLES_BYOS ', sua VM terá o benefício habilitado.

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Serviço de metadados de instância do Azure

De dentro da própria VM, você pode consultar os metadados atestados do IMDS para determinar o LicenseType da VM. Um valor de LicenseType de ' RHEL_BYOS ' ou ' SLES_BYOS ' indicará que sua VM tem o benefício habilitado. Saiba mais sobre os metadados atestados [aqui](./instance-metadata-service.md#attested-data)

## <a name="compliance"></a>Conformidade

### <a name="red-hat"></a>Red Hat

Os clientes que usam o Benefício Híbrido do Azure para RHEL concordam com os [termos legais](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Cloud_Software_Subscription_Agreement_for_Microsoft_Azure.pdf) padrão e a [política de privacidade](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Privacy_Statement_for_Microsoft_Azure.pdf) associada às ofertas do Azure Marketplace RHEL.

Os clientes que usam o Benefício Híbrido do Azure para RHEL têm três opções para fornecer atualizações de software e patches para essas VMs:

1.  [RHUI (Red Hat Update Infrastructure)](../workloads/redhat/redhat-rhui.md) (opção padrão)
1.  Servidor de satélite Red Hat
1.  Gerenciador de assinaturas do Red Hat

Os clientes que escolhem a opção RHUI podem continuar a usar o RHUI como a principal origem de atualização para suas VMs AHB RHEL sem anexar assinaturas de RHEL a essas VMs.  Os clientes que escolhem a opção RHUI são responsáveis por garantir a conformidade da assinatura do RHEL.

Os clientes que escolherem o servidor satélite Red Hat ou o Gerenciador de assinaturas do Red Hat devem remover a configuração do RHUI e anexar uma assinatura RHEL habilitada para acesso à nuvem às VMs do AHB RHEL.  

Mais informações sobre a conformidade da assinatura do Red Hat, atualizações de software e fontes para VMs AHB RHEL podem ser encontradas [aqui](https://access.redhat.com/articles/5419341).

### <a name="suse"></a>SUSE

Para usar Benefício Híbrido do Azure para suas VMs SLES, você deve primeiro ser registrado com o programa de nuvem pública SUSE. Saiba mais sobre o programa aqui. Depois de adquirir as assinaturas do SUSE, você deve registrar suas VMs usando essas assinaturas em sua própria fonte de atualizações usando o centro do cliente do SUSE, o servidor da ferramenta de gerenciamento de assinatura ou o Gerenciador do SUSE.

## <a name="frequently-asked-questions"></a>Perguntas frequentes
*P: posso usar um tipo de licença "RHEL_BYOS" com uma imagem SLES ou vice-versa?*

R: não é possível. A tentativa de inserir um tipo de licença que corresponde incorretamente ao distribuição em execução em sua VM não atualizará nenhum metadado de cobrança. No entanto, se você inserir acidentalmente o tipo de licença errado, atualizar sua VM novamente para o tipo de licença correto ainda habilitará o benefício.

*P: Eu me registrei com o Red Hat Cloud Access, mas ainda não consigo habilitar o benefício em minhas VMs RHEL. O que devo fazer?*

R: pode levar algum tempo para que o registro da assinatura do Red Hat Cloud Access se propague do Red Hat para o Azure. Se você ainda estiver vendo o erro após um dia útil, entre em contato com o suporte da Microsoft.

*P: Eu implantei uma VM usando um RHEL BYOS "imagem dourada". Posso converter a cobrança nessas imagens de BYOS para PAYG?*

R: não é possível. Benefício Híbrido do Azure dá suporte à conversão somente em imagens pagas conforme o uso.

*P: Eu implantei uma VM usando um RHEL BYOS "imagem dourada". Posso converter a cobrança nessas imagens de BYOS para PAYG?*

R: não é possível. Benefício Híbrido do Azure dá suporte à conversão somente em imagens pagas conforme o uso.

*P: Eu carreguei minha própria imagem RHEL do local (por meio de migrações para Azure, ASR ou de outra forma) para o Azure. Posso converter a cobrança nessas imagens de BYOS para PAYG?*

R: não é possível. A funcionalidade Benefício Híbrido do Azure está disponível apenas para imagens RHEL e SLES Marketplace hoje. 

*P: Eu carreguei minha própria imagem RHEL do local (por meio de migrações para Azure, ASR ou de outra forma) para o Azure. Preciso fazer algo para se beneficiar do Benefício Híbrido do Azure?*

R: não você não tem. As imagens RHEL que você carrega já são consideradas BYOS, e você é cobrado apenas pelos custos de infraestrutura do Azure. Você é responsável por custos de assinaturas do RHEL, assim como faz para seus ambientes locais. 

*P: posso usar Benefício Híbrido do Azure em VMs implantadas de imagens do Marketplace RHEL e do SLES SAP?*

R: Sim, você pode. Você pode usar o tipo de licença ' RHEL_BYOS ' para VMs RHEL e ' SLES_BYOS ' para conversões de VMs implantadas a partir de imagens do Marketplace RHEL e SLES SAP.

*P: posso usar Benefício Híbrido do Azure no conjunto de dimensionamento de máquinas virtuais (VMSS) para RHEL e SLES?*

R: não, você não pode. Os VMSS atualmente não estão no escopo de Benefício Híbrido do Azure para RHEL e SLES no momento.

*P: posso usar Benefício Híbrido do Azure em instâncias reservadas (RIs) para RHEL e SLES?*

R: não, você não pode. O RIs não está no escopo de Benefício Híbrido do Azure hoje para RHEL e SLES atualmente.

*P: posso usar Benefício Híbrido do Azure em uma máquina virtual implantada para SQL Server em imagens RHEL?*

R: não, você não pode. Não há nenhum plano para dar suporte a esses.
 

## <a name="common-issues"></a>Problemas comuns
Esta seção contém uma lista de problemas comuns que podem ser encontrados e as etapas de mitigação.

| Erro | Atenuação |
| ----- | ---------- |
| "A ação não pôde ser concluída porque nossos registros mostram que você não habilitou com êxito o acesso à nuvem Red Hat em sua assinatura do Azure...." | Para usar o benefício com VMs RHEL, você deve primeiro registrar suas assinaturas do Azure com acesso à nuvem Red Hat. Visite este link para saber mais sobre como registrar suas assinaturas do Azure para acesso à nuvem Red Hat

## <a name="next-steps"></a>Próximas etapas
* Saiba como criar e atualizar VMs e adicionar tipos de licença (RHEL_BYOS, SLES_BYOS) para Benefício Híbrido do Azure usando [CLI do Azure aqui.](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest&preserve-view=true)
