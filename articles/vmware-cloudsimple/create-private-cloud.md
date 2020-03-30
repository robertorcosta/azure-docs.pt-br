---
title: Solução Azure VMware by CloudSimple - Crie cloudSimple Private Cloud
description: Descreve como criar uma Nuvem Privada CloudSimple para estender as cargas de trabalho do VMware para a nuvem com flexibilidade operacional e continuidade
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4f700ac34b6c6e2a651366bee7dd1785c608064f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024782"
---
# <a name="create-a-cloudsimple-private-cloud"></a>Crie uma nuvem privada cloudsimple

Um Private Cloud é uma pilha de VMware isolada que suporta hosts ESXi, vCenter, vSAN e NSX. As nuvens privadas são gerenciadas através do portal CloudSimple. Eles têm seu próprio servidor vCenter em seu próprio domínio de gerenciamento. A pilha é executada em nós dedicados e em cima de um hardware de metal nu isolado.

Criar uma Nuvem Privada ajuda você a atender a uma variedade de necessidades comuns para infra-estrutura de rede:

* **Crescimento**. Se você atingiu um ponto de atualização de hardware para sua infra-estrutura existente, uma Nuvem Privada permite que você se expanda sem nenhum novo investimento em hardware necessário.

* **Expansão rápida**. Se surgir alguma capacidade temporária ou não planejada, uma Nuvem Privada permite que você crie a capacidade adicional sem atrasos.

* **Maior proteção**. Com uma Nuvem Privada de três ou mais nós, você recebe redundância automática e proteção de alta disponibilidade.

* **Necessidades de infra-estrutura de longo prazo.** Se seus data centers estiverem em capacidade ou você quiser se reestruturar para reduzir seus custos, um Nuvem Privado permite que você aposente data centers e migre para uma solução baseada em nuvem, mantendo-se compatível com suas operações corporativas.

Quando você cria uma Nuvem Privada, você recebe um único cluster vSphere e todas as VMs de gerenciamento criadas nesse cluster.

## <a name="before-you-begin"></a>Antes de começar

Os nós devem ser provisionados antes que você possa criar sua Nuvem Privada. Para obter mais informações sobre nós de provisionamento, consulte [Os nós de provisão para a solução Azure VMware pelo CloudSimple](create-nodes.md).

Aloque um intervalo CIDR para sub-redes vSphere/vSAN para a Nuvem Privada. Um Nuvem Privado é criado como um ambiente isolado de pilha vMware (com hosts ESXi, vCenter, vSAN e NSX) gerenciado por um servidor vCenter. Os componentes de gerenciamento são implantados na rede selecionada para as sub-redes vSphere/vSAN CIDR. O intervalo CIDR da rede é dividido em diferentes sub-redes durante a implantação. O espaço de endereço da sub-rede vSphere/vSAN deve ser único. Ele não deve se sobrepor a nenhuma rede que se comunique com o ambiente CloudSimple. As redes que se comunicam com o CloudSimple incluem redes locais e redes virtuais Do Zure. Para obter mais informações sobre as sub-redes vSphere/vSAN, consulte VLANs e sub-redes.

* Prefixo mínimo da faixa de vSphere/vSAN CIDR: /24
* Prefixo máximo da faixa de vSphere/vSAN CIDR: /21


## <a name="access-the-cloudsimple-portal"></a>Acessar o portal da CloudSimple

Acesse o [portal CloudSimple](access-cloudsimple-portal.md).

## <a name="create-a-new-private-cloud"></a>Crie uma nova nuvem privada

1. Selecione **Todos os serviços**.
2. Procure por **Serviços CloudSimple**.
3. Selecione o serviço CloudSimple no qual deseja criar sua Nuvem Privada.
4. Em **Visão Geral,** clique **em Criar nuvem privada** para abrir uma nova guia do navegador para o portal CloudSimple. Se solicitado, faça login com suas credenciais do Azure.

    ![Criar nuvem privada do Azure](media/create-private-cloud-from-azure.png)

5. No portal CloudSimple, forneça um nome para sua Nuvem Privada.
6. Selecione **Local** para sua Nuvem Privada.
7. Selecione **tipo Deó,** consistente com o que você provisionou no Azure.
8. Especificar **contagem de nó**.  Pelo menos três nós são necessários para criar uma Nuvem Privada.

    ![Criar Nuvem Privada - Informações básicas](media/create-private-cloud-basic-info.png)

9. Clique **em Next: Opções avançadas**.
10. Digite a faixa CIDR para sub-redes vSphere/vSAN. Certifique-se de que o intervalo CIDR não se sobreponha a nenhuma de suas sub-redes no local ou outras sub-redes Azure (redes virtuais) ou com a sub-rede gateway.

    **Opções de faixa CIDR:** /24, /23, /22 ou /21. Uma faixa CIDR de 24 suporta até nove nódulos, uma faixa de /23 CIDR suporta até 41 nódulos e uma faixa de /22 e /21 CIDR suporta até 64 latinos (o número máximo de nódulos em uma Nuvem Privada).

    > [!IMPORTANT]
    > Os endereços IP na gama vSphere/vSAN CIDR são reservados para uso pela infra-estrutura Private Cloud.  Não use o endereço IP neste intervalo em nenhuma máquina virtual.

11. Clique **em Next: Revise e crie**.
12. Revise as configurações. Se você precisar alterar qualquer configuração, clique **em Anterior**.
13. Clique em **Criar**.

O processo de provisionamento privado em nuvem começa. Pode levar até duas horas para que a Nuvem Privada seja provisionada.

Para obter instruções sobre a expansão de uma nuvem privada existente, consulte [Expande uma nuvem privada](expand-private-cloud.md).
