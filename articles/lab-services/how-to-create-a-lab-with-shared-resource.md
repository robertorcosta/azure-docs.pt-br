---
title: Como criar um laboratório com um recurso compartilhado | Azure Lab Services
description: Saiba como criar um laboratório que exige um recurso compartilhado entre os alunos.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 9cb5698f95aa220208fb02a35a52ff5363a173ac
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85443359"
---
# <a name="how-to-create-a-lab-with-a-shared-resource-in-azure-lab-services"></a>Como criar um laboratório com um recurso compartilhado no Azure Lab Services

Às vezes, ao criar um laboratório de sala de aula, pode haver alguns recursos que precisam ser compartilhados entre todos os alunos em um laboratório.  Por exemplo, você tem um servidor de licenciamento ou SQL Server para uma classe de banco de dados.  Este artigo abordará as etapas para habilitar o recurso compartilhado para um laboratório.  Também falaremos sobre como limitar o acesso a esse recurso compartilhado.

## <a name="architecture"></a>Arquitetura

Conforme mostrado no diagrama a seguir, teremos uma conta de laboratório com um laboratório.  A conta de laboratório terá as configurações de emparelhamento vnet para que a rede virtual do laboratório esteja conectada à rede do recurso compartilhado.  No diagrama abaixo, há duas redes virtuais com intervalos de IP não sobrepostos.  Esses intervalos de IP são apenas intervalos de exemplo.  Observe também que a rede virtual de recursos compartilhados está na mesma assinatura que a conta do laboratório.

![Serviços de laboratório com arquitetura de recursos compartilhados](./media/how-to-create-a-lab-with-shared-resource/shared-resource-architecture.png)

## <a name="setup-shared-resource"></a>Configurar recurso compartilhado

A rede virtual para o recurso compartilhado deve ser criada antes de o laboratório ser criado.  Para obter mais informações sobre como criar uma rede virtual, consulte [criar uma rede virtual](../virtual-network/quick-create-portal.md).  O planejamento de intervalos de rede virtual para que eles não se sobreponham ao endereço IP das máquinas de laboratório é importante.  Para obter mais informações sobre como planejar sua rede, consulte o artigo [planejar redes virtuais](../virtual-network/virtual-network-vnet-plan-design-arm.md) . Em nosso exemplo, o recurso compartilhado está em uma rede virtual com o intervalo de 10.2.0.0/16.  Se ainda não tiver feito isso, [crie uma sub-rede](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) para manter o recurso compartilhado.  No exemplo, usamos o intervalo 10.2.0.0/24, mas seu intervalo pode ser diferente dependendo das necessidades da sua rede.

O recurso compartilhado pode ser o software em execução em uma máquina virtual ou um serviço fornecido pelo Azure. O recurso compartilhado deve estar disponível por meio do endereço IP privado.  Ao disponibilizar o recurso compartilhado somente por meio de IP privado, você limita o acesso a esse recurso compartilhado.

O diagrama também mostra um NSG (grupo de segurança de rede) que pode ser usado para restringir o tráfego proveniente da VM do aluno.  Por exemplo, você pode escrever uma regra de segurança que declara que o tráfego dos endereços IP da VM do aluno só pode acessar um recurso compartilhado e nada mais.  Para obter mais informações sobre como definir regras de segurança, consulte [gerenciar grupo de segurança de rede](../virtual-network/manage-network-security-group.md#work-with-security-rules). Se você quiser restringir o acesso a um recurso compartilhado para um laboratório específico, obtenha o endereço IP do laboratório nas [configurações do laboratório da conta do laboratório](manage-labs.md#view-labs-in-a-lab-account) e defina uma regra de entrada para permitir o acesso somente desse endereço IP.  Não se esqueça de permitir as portas 49152 a 65535 para esse endereço IP.  Opcionalmente, você pode encontrar o endereço IP privado das VMs dos alunos usando a [página pool de máquinas virtuais](how-to-set-virtual-machine-passwords.md).

Se o recurso compartilhado for uma máquina virtual do Azure que executa o software necessário, talvez seja necessário modificar as regras de firewall padrão para a máquina virtual.

## <a name="lab-account"></a>Conta de laboratório

Para usar um recurso compartilhado, a conta de laboratório deve ser configurada para usar uma [rede virtual emparelhada](how-to-connect-peer-virtual-network.md).  Nesse caso, iremos emparelhar para a rede virtual que contém o recurso compartilhado.

>[!WARNING]
>O laboratório da sua classe deve ser criado **depois** que a conta de laboratório estiver emparelhada com a rede virtual de recursos compartilhados.  
Computador de modelo

Depois que sua conta de laboratório estiver emparelhada com a rede virtual, o computador de modelo agora deverá ter acesso ao recurso compartilhado.  Talvez seja necessário atualizar as regras de firewall, dependendo do recurso compartilhado que está sendo acessado.
