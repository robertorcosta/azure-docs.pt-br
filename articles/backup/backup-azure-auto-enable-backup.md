---
title: Habilitar automaticamente o backup na criação da VM usando o Azure Policy
description: Um artigo descrevendo como usar a Política do Azure para habilitar automaticamente o backup para todas as VMs criadas em um determinado escopo
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 1d423371788f87155328e8f5c9334e47349a68e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77584261"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Habilitar automaticamente o backup na criação da VM usando o Azure Policy

Uma das principais responsabilidades de um administrador de backup ou conformidade em uma organização é garantir que todas as máquinas essenciais para os negócios sejam apoiadas com a retenção apropriada.

Hoje, o Azure Backup fornece uma diretiva incorporada (usando a diretiva do Azure) que pode ser atribuída a **todas as VMs do Azure em um local especificado dentro de um grupo de assinatura ou de recursos**. Quando essa diretiva é atribuída a um determinado escopo, todas as novas VMs criadas nesse escopo são automaticamente configuradas para backup em um **cofre existente no mesmo local e assinatura**. O usuário pode especificar o cofre e a política de retenção à qual as VMs de backup devem ser associadas.

## <a name="supported-scenarios"></a>Cenários com suporte

* A tualmente, a política incorporada é suportada apenas para VMs azure. Os usuários devem tomar cuidado para garantir que a política de retenção especificada durante a atribuição seja uma política de retenção de VM. Consulte [este](https://docs.microsoft.com/azure/backup/backup-azure-policy-supported-skus) documento para ver todas as SKUs VM suportadas por esta política.

* A apólice pode ser atribuída a um único local e assinatura por vez. Para habilitar o backup de VMs em locais e assinaturas, várias instâncias da atribuição de políticas precisam ser criadas, uma para cada combinação de localização e assinatura.

* O cofre especificado e as VMs configuradas para backup podem estar em diferentes grupos de recursos.

* O escopo do Management Group não tem suporte.

* A política incorporada não está disponível atualmente nas nuvens nacionais.

## <a name="using-the-built-in-policy"></a>Usando a política incorporada

Para atribuir a diretiva ao escopo necessário, siga as etapas abaixo:

1. Faça login no Portal Azure e navegue até o **Painel de Políticas.**
2. Selecione **Definições** no menu à esquerda para obter uma lista de todas as políticas incorporadas em Recursos do Azure.
3. Filtrar a lista para **Categoria=Backup**. Você verá a lista filtrada para uma única diretiva chamada 'Configurar backup em VMs de um local para um Cofre central existente no mesmo local'.
![Painel de políticas](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. Clique no nome da apólice. Você será redirecionado para a definição detalhada desta política.
![Lâmina de definição de política](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. Clique no botão **Atribuir** na parte superior da lâmina. Isso o redireciona para a lâmina **Deatribuição de** política.
6. Em **Basics,** clique nos três apontados ao lado do campo **Escopo.** Isso abre uma lâmina de contexto correta onde você pode selecionar a assinatura para a política a ser aplicada. Você também pode selecionar opcionalmente um grupo de recursos, de modo que a diretiva seja aplicada apenas para VMs em um determinado grupo de recursos.
![Noções básicas de atribuição de políticas](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. Na guia **Parâmetros,** escolha um local a partir da queda e selecione a diretiva de cofre e backup à qual as VMs no escopo devem ser associadas.
![Parâmetros de atribuição de políticas](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. Certifique-se de que **o Efeito** está definido para implantarIfNotExist.
9. Navegue para **Revisar+criar** e clicar **em Criar**.

> [!NOTE]
>
> A Política Azure também pode ser usada em VMs existentes, usando [remediação](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources).

> [!NOTE]
>
> Recomenda-se que esta política não seja atribuída a mais de 200 VMs por vez. Se a diretiva for atribuída a mais de 200 VMs, poderá fazer com que o backup seja acionado algumas horas depois do especificado pelo cronograma.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre a Política do Azure](https://docs.microsoft.com/azure/governance/policy/overview)
