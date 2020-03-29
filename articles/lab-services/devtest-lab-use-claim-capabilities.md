---
title: Use recursos de reclamação no Azure DevTest Labs | Microsoft Docs
description: Conheça diferentes cenários para usar recursos de reclamação/não-ressonância do Azure DevTest Labs
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: spelluru
ms.openlocfilehash: a15148260bccadc59966c86031100f0e0332b0f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67861424"
---
# <a name="use-claim-capabilities-in-azure-devtest-labs"></a>Use recursos de reclamação no Azure DevTest Labs
O serviço Azure DevTest Labs melhora a eficácia e a eficiência dos desenvolvedores e testadores. Este artigo se concentra na capacidade de reivindicar ou não as máquinas virtuais no Azure DevTest Labs. Ele também lista várias maneiras que esse recurso melhora a experiência do usuário. Antes de olhar para diferentes cenários onde esse recurso pode ser usado, vamos ver o que é **alegação** e como ele funciona.

## <a name="claimable-machines"></a>Máquinas areclamadas
Uma máquina reivindicável é uma máquina virtual (VM) que é criada em um laboratório sem um proprietário. Uma vez que a máquina é reivindicada, o usuário tem uma gama completa de opções para essa VM. Quando um usuário reivindica uma máquina, algumas alterações são feitas. A VM é movida da lista **de máquinas virtuais reivindicadas** para a lista **Minhas máquinas virtuais** no portal Azure. 

O usuário pode se conectar à VM, personalizar artefatos, reiniciar, parar ou não reclamar da máquina. Existem algumas maneiras de tornar um VM reivindicável:

- Crie uma máquina e imclaim-a para que ela se mova para o pool reivindicado. 
- Crie uma VM e coloque no pool compartilhado usando [configurações avançadas](https://azure.microsoft.com/updates/azure-devtest-labs-claim-lab-vms-from-a-shared-pool/).

Existem dois casos em que os recursos de reivindicação/não-reivindicação podem ser usados de forma eficaz. O primeiro caso requer mais premeditação e planejamento, para ser projetado e executado corretamente. E o segundo é mais situacional. A seguir, alguns exemplos dos diferentes casos.

## <a name="designed-use-of-claimable-machines"></a>Uso projetado de máquinas reivindicadas

- **Desenvolvimento /teste de software:** Permita que desenvolvedores ou testadores sejam mais produtivos por terem máquinas configuradas prontas e em um estado não reclamado. Ter um conjunto de VMs com diferentes configurações, ferramentas necessárias e com o código mais recente permite que os usuários reivindiquem uma VM e comecem a trabalhar sem ter que gastar o tempo para configurar uma máquina. Antes que as VMs sejam reivindicadas, as máquinas são provisionadas, mas estão desligadas minimizando o custo de ter máquinas que são usadas com menos frequência. Quando as VMs são necessárias, um usuário simplesmente reivindica a VM, que inicia a máquina. A opção de não reclamar não é tão útil neste caso, já que a criação de uma nova VM é muitas vezes mais fácil e mais barata.
- **Sala de aula/laboratórios:** Tenha VMs pré-configurados para uma aula ou um laboratório para que os alunos possam se conectar imediatamente a uma máquina usando o portal Azure.  Uma vez que um aluno reivindica uma VM, o laboratório garante que ninguém pode reivindicar a mesma máquina. A automatização desse processo garante que o número necessário de máquinas com o ambiente especificado esteja disponível. Se os alunos não aparecerem ou estiverem atrasados, as máquinas não reclamadas podem ser mantidas disponíveis até que a sessão acabe com o custo mínimo. A opção de não reclamar não é tão eficaz neste cenário, uma vez que a VM está em um estado desconhecido quando o usuário anterior é feito.
- **Demonstrações:** Use máquinas para demonstrações, onde as máquinas do laboratório são configuradas com ambientes específicos. Esse recurso é útil onde várias pessoas podem estar dando uma demonstração ao mesmo tempo ou em momentos aleatórios, como em uma conferência. A opção de isenção pode ser útil nesta situação, pois a demonstração não deve alterar o estado da máquina, permitindo que os usuários devolvam uma VM de volta ao pool reivindicado para a próxima demonstração. Com a máquina não reclamada sendo desprovisionada e incorrendo em custo mínimo, as VMs podem ser deixadas em laboratório por períodos mais longos.
- **Trabalhadores temporários/contratados:** Permitir que os usuários usem uma máquina. Quando eles saem, eles retornam a VM para o pool reivindicavel sem perda de dados. Com a VM não reclamada, outro usuário pode reivindicar a VM e continuar ou revisar a máquina para obter informações adicionais.
- **Em geral:** A capacidade de ter uma única fonte automaticamente configurar e implantar VMs, em uma cadência específica, é útil em muitas situações diferentes. Existem várias situações diferentes em que o recurso de reclamação/não-relegação ajuda os usuários a serem mais eficientes, tendo um processo automatizado para construir as VMs em um estado não reclamado com uma configuração definida. As configurações podem incluir diferentes sistemas operacionais, idiomas, discos ou [outros softwares (artefatos)](devtest-lab-artifact-author.md) dependendo de suas necessidades. A capacidade de reivindicar um VM do laboratório permite que o usuário do laboratório obtenha um sistema configurado corretamente sem gastar o tempo ou esforço na configuração da máquina. O gerente do laboratório poderia usar o estado reivindicado das VMs para melhorar o número de máquinas geradas, limpar máquinas e determinar a prioridade das configurações. A [fábrica image](image-factory-create.md) é um bom exemplo de um processo automatizado para construir VMs e imagens para vários laboratórios. Os scripts podem ser modificados para executar qualquer uma das seguintes situações com as alterações apropriadas ou ser usados como referência para a criação de um sistema personalizado.

## <a name="situational-use-of-claimable-machines"></a>Uso situacional de máquinas reclamadas

- Use o recurso de reivindicação/não-reivindicação que permite que os usuários passem o controle de máquinas de uma para outra e não tenham que saber explicitamente quem pegará a máquina em seguida.
- Desenvolvimento, teste e depuração de um cenário onde uma configuração específica da máquina pode reproduzir um bug, então a máquina pode ser não reclamada permitindo que outro desenvolvedor possa reivindicar a máquina e continuar o trabalho. Esse recurso é especialmente útil, pois mais pessoas estão trabalhando remotamente em diferentes áreas do mundo. 
- Os membros da equipe podem trabalhar com um único ambiente. Por exemplo, você pode configurar manualmente um ambiente complexo que não pode ser automatizado ou criar recursos que só podem lidar com modificações para uma única entrada como imagens. No passado, esse problema era resolvido por ter uma máquina dedicada funcionando. O recurso reivindicado é uma melhoria em relação ao processo manual, tendo controle de acesso incorporado ao usuário e identificação visual quando disponível. Quando não reclamado, o VM é desprovisionado para reduzir custos.
- Tenha um disco de dados que esteja conectado a uma VM. Cada disco de até ~ 1 TB de dados permite que um grande volume de dados seja passado sem ter que copiar ou duplicar os dados. A VM seria inicialmente criada com um disco conectado que tinha o grande volume de dados.  Qualquer usuário poderia então reivindicar a máquina e acessar os dados. Quando feito, não reclame a VM para permitir que outros usuários para a máquina.

Existem algumas ressalvas ao uso de máquinas reivindicadas, mais comumente em torno de obter acesso à máquina. Se a máquina é domínio unido, então o usuário que alega que a máquina precisará já ter sido concedido acesso, geralmente é feito concedendo acesso a um grupo que abrange todos os usuários no laboratório quando a VM é criada. Se a máquina não for acompanhada de domínio, o artefato **Reset VM Password** no repositório público precisará ser executado para adicionar o usuário como administrador.  Os artefatos podem ser aplicados mesmo após o início ou a apelação da máquina.

## <a name="next-steps"></a>Próximas etapas
Veja o artigo a seguir: [Crie e gerencie VMs reivindicados no Azure DevTest Labs](devtest-lab-add-claimable-vm.md)
