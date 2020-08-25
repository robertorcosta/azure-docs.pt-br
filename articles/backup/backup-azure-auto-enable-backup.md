---
title: Habilitar automaticamente o backup na criação da VM usando o Azure Policy
description: Um artigo que descreve como usar Azure Policy para habilitar o backup automaticamente para todas as VMs criadas em um determinado escopo
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: de4923000bc842203535e03727fd532c67a8f517
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88826065"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Habilitar automaticamente o backup na criação da VM usando o Azure Policy

Uma das principais responsabilidades de um administrador de backup ou conformidade em uma organização é garantir que todos os computadores críticos para os negócios sejam submetidos a backup com a retenção apropriada.

Hoje, o backup do Azure fornece uma política interna (usando Azure Policy) que pode ser atribuída a **todas as VMs do Azure em um local especificado dentro de uma assinatura ou grupo de recursos**. Quando essa política é atribuída a um determinado escopo, todas as novas VMs criadas nesse escopo são automaticamente configuradas para backup em um **cofre existente no mesmo local e assinatura**. O usuário pode especificar o cofre e a política de retenção à qual as VMs de backup devem ser associadas.

## <a name="supported-scenarios"></a>Cenários com suporte

* Atualmente, a política interna tem suporte apenas para VMs do Azure. Os usuários devem tomar cuidado para garantir que a política de retenção especificada durante a atribuição seja uma política de retenção de VM. Consulte [este](./backup-azure-policy-supported-skus.md) documento para ver todos os SKUs de VM com suporte nesta política.

* A política pode ser atribuída a um único local e assinatura de cada vez. Para habilitar o backup para VMs em locais e assinaturas, várias instâncias da atribuição de política precisam ser criadas, uma para cada combinação de localização e assinatura.

* O cofre especificado e as VMs configuradas para backup podem estar em grupos de recursos diferentes.

* O escopo do grupo de gerenciamento não tem suporte no momento.

* Atualmente, a política interna não está disponível em nuvens nacionais.

## <a name="using-the-built-in-policy"></a>Usando a política interna

Para atribuir a política ao escopo necessário, siga as etapas abaixo:

1. Entre no portal do Azure e navegue até o painel de **política** .
1. Selecione **definições** no menu à esquerda para obter uma lista de todas as políticas internas nos recursos do Azure.
1. Filtre a lista para **Category = backup**. Você verá a lista filtrada para uma única política denominada ' Configurar backup em VMs de um local para um cofre central existente no mesmo local '.
![Painel de política](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
1. Selecione o nome da política. Você será redirecionado para a definição detalhada dessa política.
![Painel de definição de política](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
1. Selecione o botão **atribuir** na parte superior do painel. Isso redireciona você para o painel **atribuir política** .
1. Em **noções básicas**, selecione os três pontos ao lado do campo **escopo** . Isso abre um painel de contexto à direita no qual você pode selecionar a assinatura na qual a política será aplicada. Você também pode selecionar um grupo de recursos, para que a política seja aplicada somente a VMs em um determinado grupo de recursos.
![Noções básicas de atribuição de política](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
1. Na guia **parâmetros** , escolha um local na lista suspensa e selecione o cofre e a política de backup aos quais as VMs no escopo devem ser associadas.
![Parâmetros de atribuição de política](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
1. Verifique se o **efeito** está definido como deployIfNotExists.
1. Navegue até **revisar + criar** e selecione **criar**.

> [!NOTE]
>
> Azure Policy também pode ser usado em VMs existentes, usando a [correção](../governance/policy/how-to/remediate-resources.md).

> [!NOTE]
>
> É recomendável que essa política não seja atribuída a mais de 200 VMs por vez. Se a política for atribuída a mais de 200 VMs, isso poderá fazer com que o backup seja disparado algumas horas depois daquela especificada pela agenda.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre o Azure Policy](../governance/policy/overview.md)
