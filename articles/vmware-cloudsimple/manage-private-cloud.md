---
title: Gerenciar a nuvem privada do Azure VMware Solutions (AVS)
description: Descreve os recursos disponíveis para gerenciar seus recursos e atividades de nuvem privada da AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 47bf2251f71204b99245c1a9d55ef87157c41dd8
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014820"
---
# <a name="manage-avs-private-cloud-resources-and-activities"></a>Gerenciar recursos e atividades de nuvem privada da AVS

As nuvens privadas da AVS são gerenciadas no portal da AVS. Verifique o status, os recursos disponíveis, a atividade na nuvem privada da AVS e outras configurações no portal da AVS.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-avs-portal"></a>Acessar o portal da AVS

Acesse o [portal da AVS](access-cloudsimple-portal.md).

## <a name="view-the-list-of-avs-private-clouds"></a>Exibir a lista de nuvens privadas da AVS

A guia **nuvens privadas AVS** na página **recursos** lista todas as nuvens privadas da AVS em sua assinatura. As informações incluem o nome, o número de clusters vSphere, o local, o estado atual da nuvem privada da AVS e as informações sobre o recurso.

![Página de nuvem privada de AVS](media/manage-private-cloud.png)

Selecione uma nuvem privada de AVS para obter informações e ações adicionais.

## <a name="avs-private-cloud-summary"></a>Resumo de nuvem privada de AVS

Exiba um resumo abrangente da nuvem privada da AVS selecionada. A página Resumo inclui os servidores DNS implantados na nuvem privada da AVS. Você pode configurar o encaminhamento de DNS de servidores DNS locais para seus servidores DNS da nuvem privada de AVS. Para obter mais informações sobre o encaminhamento de DNS, consulte [Configurar o DNS para a resolução de nomes para o vCenter de nuvem privada da AVS local](https://docs.azure.cloudsimple.com/on-premises-dns-setup/).

![Resumo de nuvem privada de AVS](media/private-cloud-summary.png)

### <a name="available-actions"></a>Ações disponíveis

* [Inicie o cliente do vSphere](https://docs.azure.cloudsimple.com/vsphere-access/). Acesse o vCenter para esta nuvem privada de AVS.
* [Nós de compra](create-nodes.md). Adicione nós a esta nuvem privada de AVS.
* [Expanda](expand-private-cloud.md). Adicione nós a esta nuvem privada de AVS.
* **Atualizar**. Atualize as informações nesta página.
* **Excluir**. Você pode excluir a nuvem privada da AVS a qualquer momento. **Antes de excluir, certifique-se de ter feito backup de todos os sistemas e dados.** A exclusão de uma nuvem privada de AVS exclui todas as VMs, a configuração do vCenter e os dados. Clique em **excluir** na seção Resumo para a nuvem privada da AVS selecionada. Após a exclusão, todos os dados da nuvem privada da AVS são apagados em um processo de eliminação seguro e altamente compatível.
* [Altere os privilégios de vSphere](escalate-private-cloud-privileges.md). Escalonar seus privilégios nesta nuvem privada de AVS.

## <a name="avs-private-cloud-vlanssubnets"></a>VLANs/sub-redes de nuvem privada da AVS

Exiba a lista de VLANs/sub-redes definidas para a nuvem privada da AVS selecionada. A lista inclui as VLANs de gerenciamento/sub-redes criadas quando a nuvem privada da AVS foi criada.

![A nuvem privada da AVS-VLANs/sub-redes](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Ações disponíveis

* [Adicionar VLANs/sub-redes](https://docs.azure.cloudsimple.com/create-vlan-subnet/). Adicione uma VLAN/subconjunto a esta nuvem privada de AVS.

Selecione uma VLAN/sub-rede para as seguintes ações
* [Anexar tabela de firewall](https://docs.azure.cloudsimple.com/firewall/). Anexe uma tabela de firewall a esta nuvem privada de AVS.
* **Editar**
* **Excluir** (somente VLANs/sub-redes definidas pelo usuário)

## <a name="avs-private-cloud-activity"></a>Atividade de nuvem privada de AVS

Exiba as informações a seguir para a nuvem privada da AVS selecionada. As informações da atividade são uma lista filtrada de todas as atividades da nuvem privada da AVS selecionada. Esta página mostra até 25 atividades recentes.

* Alertas recentes
* Eventos recentes
* Tarefas recentes
* Auditoria recente

![Sincronização de nuvem privada AVS-atividade](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Racks de nuvem

Racks de nuvem são os blocos de construção da sua nuvem privada de AVS. Cada rack fornece uma unidade de capacidade. A AVS configura automaticamente os racks de nuvem com base em suas seleções ao criar ou expandir uma nuvem privada de AVS. Exiba a lista completa de racks de nuvem, incluindo a nuvem privada de AVS para a qual cada um está atribuído.

![Na nuvem privada de sincronização automática – racks de nuvem](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>Rede de gerenciamento de vSphere

Lista de recursos de gerenciamento e máquinas virtuais do VMware que estão configurados atualmente na nuvem privada da AVS. As informações incluem a versão do software, o FQDN (nome de domínio totalmente qualificado) e o endereço IP dos recursos.

![Rede de gerenciamento de vSphere de nuvem privada-sincronização automática](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Próximos passos

* [Consumir VMs VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre as [nuvens privadas da AVS](cloudsimple-private-cloud.md)