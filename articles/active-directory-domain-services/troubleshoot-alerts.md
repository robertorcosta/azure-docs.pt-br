---
title: Alertas e resoluções comuns nos Serviços de Domínio Ad do Azure | Microsoft Docs
description: Saiba como resolver alertas comuns gerados como parte do status de saúde dos Serviços de Domínio do Diretório Ativo do Azure
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: ed791ea10c072308c16baac9fa469a46b19ec648
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654493"
---
# <a name="known-issues-common-alerts-and-resolutions-in-azure-active-directory-domain-services"></a>Problemas conhecidos: Alertas e resoluções comuns nos Serviços de Domínio do Diretório Ativo do Azure

Como parte central da identidade e autenticação para aplicativos, o Azure Active Directory Domain Services (Azure AD DS) às vezes tem problemas. Se você encontrar problemas, existem alguns alertas comuns e etapas associadas de solução de problemas para ajudá-lo a fazer as coisas funcionarem novamente. A qualquer momento, você também pode [abrir uma solicitação de suporte do Azure][azure-support] para assistência adicional de solução de problemas.

Este artigo fornece informações de solução de problemas para alertas comuns no Azure AD DS.

## <a name="aadds100-missing-directory"></a>AADDS100: Diretório ausente

### <a name="alert-message"></a>Mensagem de alerta

*O diretório Azure AD associado ao seu domínio gerenciado pode ter sido excluído. O domínio gerenciado não está mais em uma configuração suportada. A Microsoft não pode monitorar, gerenciar, corrigir e sincronizar seu domínio gerenciado.*

### <a name="resolution"></a>Resolução

Esse erro geralmente é causado quando uma assinatura do Azure é movida para um novo diretório Azure AD e o antigo diretório Azure AD associado ao Azure AD DS é excluído.

Esse erro é irrecuperável. Para resolver o alerta, [exclua o domínio gerenciado pelo Azure AD DS existente](delete-aadds.md) e recrie-o em seu novo diretório. Se você tiver problemas para excluir o domínio gerenciado pelo Azure AD DS, [abra uma solicitação de suporte do Azure][azure-support] para assistência adicional de solução de problemas.

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: O Azure AD B2C está em execução neste diretório

### <a name="alert-message"></a>Mensagem de alerta

*Serviços de domínio do Azure AD não podem ser habilitados em um diretório do Azure AD B2C.*

### <a name="resolution"></a>Resolução

O Azure AD DS sincroniza automaticamente com um diretório Azure AD. Se o diretório Azure AD estiver configurado para B2C, o Azure AD DS não poderá ser implantado e sincronizado.

Para usar o Azure AD DS, você deve recriar seu domínio gerenciado em um diretório AD B2C não-Azure usando as seguintes etapas:

1. [Exclua o domínio gerenciado do Azure AD DS](delete-aadds.md) do diretório Azure AD existente.
1. Crie um novo diretório Azure AD que não seja um diretório Azure AD B2C.
1. [Crie um domínio gerenciado pelo Azure AD DS](tutorial-create-instance.md)de substituição .

O Azure AD DS gerenciado a saúde do domínio atualiza-se automaticamente dentro de duas horas e remove o alerta.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>ADDS103: Endereço está em um intervalo de IP público

### <a name="alert-message"></a>Mensagem de alerta

*O intervalo de endereços IP para a rede virtual na qual você habilitou o Azure AD Domain Services está em uma faixa de IP pública. Os Serviços de Domínio Ad do Azure devem ser ativados em uma rede virtual com uma gama de endereços IP privada. Essa configuração afeta a capacidade da Microsoft de monitorar, gerenciar, corrigir e sincronizar seu domínio gerenciado.*

### <a name="resolution"></a>Resolução

Antes de começar, certifique-se de entender [os espaços de endereços IP v4 privados](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces).

Dentro de uma rede virtual, as VMs podem fazer solicitações aos recursos do Azure na mesma faixa de endereço IP configurada para a sub-rede. Se você configurar um intervalo de endereço IP público para uma sub-rede, as solicitações roteadas dentro de uma rede virtual podem não alcançar os recursos da Web pretendidos. Essa configuração pode levar a erros imprevisíveis com o Azure AD DS.

> [!NOTE]
> Se você possui o intervalo de endereços IP na internet que está configurada em sua rede virtual, esse alerta pode ser ignorado. No entanto, os Serviços de Domínio Ad do Azure Não podem se comprometer com o [SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)] com essa configuração, pois pode levar a erros imprevisíveis.

Para resolver esse alerta, exclua o domínio gerenciado pelo Azure AD DS existente e recrie-o em uma rede virtual com um intervalo de endereçoIP privado. Esse processo é disruptivo, pois o domínio gerenciado pelo Azure AD DS não está disponível e todos os recursos personalizados que você criou como UOs ou contas de serviço são perdidos.

1. [Exclua o domínio gerenciado do Azure AD DS](delete-aadds.md) do seu diretório.
1. Para atualizar a gama de endereços IP da rede virtual, pesquise e selecione *a rede Virtual* no portal Azure. Selecione a rede virtual para Azure AD DS que tem incorretamente um conjunto de intervalos de endereçoIP público.
1. Em **Configurações,** *selecione Espaço de endereço*.
1. Atualize a gama de endereços escolhendo o intervalo de endereços existente e editando-o ou adicionando um intervalo de endereços adicional. Certifique-se de que o novo intervalo de endereçoip está em um intervalo IP privado. Quando estiver pronto, **salve** as alterações.
1. Selecione **Sub-redes** na navegação à esquerda.
1. Escolha a sub-rede que deseja editar ou crie uma sub-rede adicional.
1. Atualize ou especifique um intervalo de endereçoIP privado e **salve** suas alterações.
1. [Crie um domínio gerenciado pelo Azure AD DS](tutorial-create-instance.md)de substituição . Certifique-se de escolher a sub-rede virtual atualizada com uma faixa de endereço IP privada.

O Azure AD DS gerenciado a saúde do domínio atualiza-se automaticamente dentro de duas horas e remove o alerta.

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: Assinatura do Azure não encontrada

### <a name="alert-message"></a>Mensagem de alerta

*Sua assinatura do Azure associada ao seu domínio gerenciado foi excluída.  O Azure AD Domain Services requer uma assinatura ativa para continuar funcionando corretamente.*

### <a name="resolution"></a>Resolução

O Azure AD DS requer uma assinatura ativa e não pode ser movido para uma assinatura diferente. Se a assinatura do Azure com a que o domínio gerenciado pelo Azure AD DS foi associada for excluída, você deverá recriar uma assinatura do Azure e o domínio gerenciado do Azure AD DS.

1. [Crie uma assinatura do Azure](../cost-management-billing/manage/create-subscription.md).
1. [Exclua o domínio gerenciado do Azure AD DS](delete-aadds.md) do diretório Azure AD existente.
1. [Crie um domínio gerenciado pelo Azure AD DS](tutorial-create-instance.md)de substituição .

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: Sua Assinatura do Azure está desabilitada

### <a name="alert-message"></a>Mensagem de alerta

*Sua assinatura do Azure associada ao seu domínio gerenciado não está ativa.  O Azure AD Domain Services requer uma assinatura ativa para continuar funcionando corretamente.*

### <a name="resolution"></a>Resolução

O Azure AD DS requer uma assinatura ativa. Se a assinatura do Azure com a que o domínio gerenciado pelo Azure AD DS estava associada não estiver ativa, você deve renová-la para reativar a assinatura.

1. [Renovar sua assinatura do Azure](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable).
2. Uma vez que a assinatura é renovada, uma notificação Azure AD DS permite que você reative o domínio gerenciado.

Quando o domínio gerenciado é ativado novamente, o Azure AD DS gerenciado a saúde do domínio atualiza-se automaticamente dentro de duas horas e remove o alerta.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: Diretórios de assinatura movida

### <a name="alert-message"></a>Mensagem de alerta

*A assinatura usada pelo Azure AD Domain Services foi transferida para outro diretório. O Azure AD Domain Services precisa ter uma assinatura ativa no mesmo diretório para funcionar corretamente.*

### <a name="resolution"></a>Resolução

O Azure AD DS requer uma assinatura ativa e não pode ser movido para uma assinatura diferente. Se a assinatura do Azure com a que o domínio gerenciado pelo Azure AD DS foi associada for movida, mova a assinatura de volta para o diretório anterior ou [exclua seu domínio gerenciado](delete-aadds.md) do diretório existente e [crie um domínio gerenciado pelo Azure AD DS na assinatura escolhida.](tutorial-create-instance.md)

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: Recursos para seu domínio gerenciado que não pode ser encontrado

### <a name="alert-message"></a>Mensagem de alerta

*Um recurso usado para o domínio gerenciado foi excluído. Esse recurso é necessário para que os Serviços de Domínio AD do Azure funcionem corretamente.*

### <a name="resolution"></a>Resolução

O Azure AD DS cria recursos adicionais para funcionar corretamente, como endereços IP públicos, interfaces de rede virtuais e um balanceador de carga. Se algum desses recursos for excluído, o domínio gerenciado está em um estado sem suporte e impede que o domínio seja gerenciado. Para obter mais informações sobre esses recursos, consulte [os recursos de rede usados pelo Azure AD DS](network-considerations.md#network-resources-used-by-azure-ad-ds).

Esse alerta é gerado quando um desses recursos necessários é excluído. Se o recurso foi excluído há menos de 4 horas, há uma chance de que a plataforma Azure possa recriar automaticamente o recurso excluído. As etapas a seguir descrevem como verificar o estado de saúde e o carimbo de tempo para exclusão de recursos:

1. No portal Azure, procure e selecione **Serviços de Domínio**. Escolha o domínio gerenciado pelo Azure AD DS, como *aaddscontoso.com*.
1. Na navegação à esquerda, selecione **Saúde**.
1. Na página de saúde, selecione o alerta com o ID *AADDS109*.
1. O alerta tem um carimbo de data e hora para quando foi encontrado pela primeira vez. Se esse carimbo de tempo for menor que 4 horas atrás, a plataforma Azure poderá recriar automaticamente o recurso e resolver o alerta sozinho.

    Se o alerta tiver mais de 4 horas, o domínio gerenciado pelo Azure AD DS está em um estado irrecuperável. [Exclua o domínio gerenciado do Azure AD DS](delete-aadds.md) e, em seguida, [crie um domínio gerenciado de substituição](tutorial-create-instance.md).

## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: A sub-rede associada ao seu domínio gerenciado está cheia

### <a name="alert-message"></a>Mensagem de alerta

*A sub-rede selecionada para a implantação do Azure Active Directory Domain Services está cheio e não tem espaço para o controlador de domínio adicional precisa ser criado.*

### <a name="resolution"></a>Resolução

A sub-rede virtual do Azure AD DS precisa de endereços IP suficientes para os recursos criados automaticamente. Este espaço de endereço IP inclui a necessidade de criar recursos de substituição se houver um evento de manutenção. Para minimizar o risco de ficar sem endereços IP disponíveis, não implante recursos adicionais, como suas próprias VMs, na mesma sub-rede virtual do Azure AD DS.

Esse erro é irrecuperável. Para resolver o alerta, [exclua o domínio gerenciado pelo Azure AD DS existente](delete-aadds.md) e recrie-o. Se você tiver problemas para excluir o domínio gerenciado pelo Azure AD DS, [abra uma solicitação de suporte do Azure][azure-support] para assistência adicional de solução de problemas.

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: Principal serviço não autorizado

### <a name="alert-message"></a>Mensagem de alerta

*Um princípio de serviço que o Azure AD Domain Services usa para atender seu domínio não está autorizado a gerenciar recursos na assinatura do Azure. O principal de serviço precisa obter permissões para atender seu domínio gerenciado.*

### <a name="resolution"></a>Resolução

Alguns princípios de serviço gerados automaticamente são usados para gerenciar e criar recursos para um domínio gerenciado pelo Azure AD DS. Se as permissões de acesso a um desses principais de serviço forem alteradas, o domínio não poderá gerenciar corretamente os recursos. As etapas a seguir mostram como entender e, em seguida, conceder permissões de acesso a um diretor de serviço:

1. Leia sobre [o controle de acesso baseado em papéis e como conceder acesso a aplicativos no portal Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).
2. Reveja o acesso que o diretor do serviço com o ID *abba844e-bc0e-44b0-947a-dc74e5d09022* tem e conceda o acesso que foi negado em uma data anterior.

## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: Não há endereços IP suficientes disponíveis no domínio gerenciado

### <a name="alert-message"></a>Mensagem de alerta

*Identificamos que a sub-rede da rede virtual neste domínio pode não ter endereços IP suficientes. O Azure AD Domain Services precisa de pelo menos dois endereços IP disponíveis na sub-rede em que está habilitado. Recomendamos ter pelo menos 3-5 endereços IP sobressalentes dentro da sub-rede. Isso pode ter ocorrido se outras máquinas virtuais forem implantadas dentro da sub-rede, esgotando assim o número de endereços IP disponíveis ou se houver uma restrição no número de endereços IP disponíveis na sub-rede.*

### <a name="resolution"></a>Resolução

A sub-rede virtual do Azure AD DS precisa de endereços IP suficientes para os recursos criados automaticamente. Este espaço de endereço IP inclui a necessidade de criar recursos de substituição se houver um evento de manutenção. Para minimizar o risco de ficar sem endereços IP disponíveis, não implante recursos adicionais, como suas próprias VMs, na mesma sub-rede virtual do Azure AD DS.

Para resolver esse alerta, exclua o domínio gerenciado pelo Azure AD DS existente e recrie-o em uma rede virtual com um intervalo de endereçoIP grande o suficiente. Esse processo é disruptivo, pois o domínio gerenciado pelo Azure AD DS não está disponível e todos os recursos personalizados que você criou como UOs ou contas de serviço são perdidos.

1. [Exclua o domínio gerenciado do Azure AD DS](delete-aadds.md) do seu diretório.
1. Para atualizar a gama de endereços IP da rede virtual, pesquise e selecione *a rede Virtual* no portal Azure. Selecione a rede virtual para Azure AD DS que tenha a pequena gama de endereços IP.
1. Em **Configurações,** *selecione Espaço de endereço*.
1. Atualize a gama de endereços escolhendo o intervalo de endereços existente e editando-o ou adicionando um intervalo de endereços adicional. Certifique-se de que o novo intervalo de endereços IP é grande o suficiente para a gama de sub-redes Azure AD DS. Quando estiver pronto, **salve** as alterações.
1. Selecione **Sub-redes** na navegação à esquerda.
1. Escolha a sub-rede que deseja editar ou crie uma sub-rede adicional.
1. Atualize ou especifique um intervalo de endereço IP grande o suficiente e **salve** suas alterações.
1. [Crie um domínio gerenciado pelo Azure AD DS](tutorial-create-instance.md)de substituição . Certifique-se de escolher a sub-rede virtual atualizada com um intervalo de endereçoIP grande o suficiente.

O Azure AD DS gerenciado a saúde do domínio atualiza-se automaticamente dentro de duas horas e remove o alerta.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: Recursos são irrecuperáveis

### <a name="alert-message"></a>Mensagem de alerta

*Os recursos usados pelo Azure Active Directory Domain Services foram detectados em um estado inesperado e não podem ser recuperados.*

### <a name="resolution"></a>Resolução

Esse erro é irrecuperável. Para resolver o alerta, [exclua o domínio gerenciado pelo Azure AD DS existente](delete-aadds.md) e recrie-o. Se você tiver problemas para excluir o domínio gerenciado pelo Azure AD DS, [abra uma solicitação de suporte do Azure][azure-support] para assistência adicional de solução de problemas.

## <a name="aadds114-subnet-invalid"></a>AADDS114: Sub-rede inválida

### <a name="alert-message"></a>Mensagem de alerta

*A sub-rede selecionada para implantação dos Serviços de Domínio Ad do Azure é inválida e não pode ser usada.*

### <a name="resolution"></a>Resolução

Esse erro é irrecuperável. Para resolver o alerta, [exclua o domínio gerenciado pelo Azure AD DS existente](delete-aadds.md) e recrie-o. Se você tiver problemas para excluir o domínio gerenciado pelo Azure AD DS, [abra uma solicitação de suporte do Azure][azure-support] para assistência adicional de solução de problemas.

## <a name="aadds115-resources-are-locked"></a>AADDS115: Os recursos estão bloqueados

### <a name="alert-message"></a>Mensagem de alerta

*Um ou mais dos recursos de rede usados pelo domínio gerenciado não podem ser operados uma vez que o escopo de destino foi bloqueado.*

### <a name="resolution"></a>Resolução

Os bloqueios de recursos podem ser aplicados aos recursos do Azure para evitar alterações ou exclusões. Como o Azure AD DS é um serviço gerenciado, a plataforma Azure precisa da capacidade de fazer alterações de configuração. Se um bloqueio de recursos for aplicado em alguns dos componentes do Azure AD DS, a plataforma Azure não poderá executar suas tarefas de gerenciamento.

Para verificar se há bloqueios de recursos nos componentes Do Azure AD DS e removê-los, complete as seguintes etapas:

1. Para cada um dos componentes da rede Azure AD DS em seu grupo de recursos, como rede virtual, interface de rede ou endereço IP público, verifique os registros de operação no portal Azure. Esses registros de operação devem indicar por que uma operação está falhando e onde um bloqueio de recursos é aplicado.
1. Selecione o recurso onde um bloqueio é aplicado, em seguida, em **Bloqueios,** selecione e remova o bloqueio(s).

## <a name="aadds116-resources-are-unusable"></a>AADDS116: Os recursos são inutilizáveis

### <a name="alert-message"></a>Mensagem de alerta

*Um ou mais dos recursos de rede usados pelo domínio gerenciado não podem ser operados devido às restrições de política.*

### <a name="resolution"></a>Resolução

As políticas são aplicadas aos recursos e grupos de recursos do Azure que controlam quais ações de configuração são permitidas. Como o Azure AD DS é um serviço gerenciado, a plataforma Azure precisa da capacidade de fazer alterações de configuração. Se uma política for aplicada em alguns dos componentes do Azure AD DS, a plataforma Azure poderá não ser capaz de executar suas tarefas de gerenciamento.

Para verificar as políticas aplicadas nos componentes Do Ad DS do Azure e atualizá-los, complete as seguintes etapas:

1. Para cada um dos componentes da rede Azure AD DS em seu grupo de recursos, como rede virtual, NIC ou endereço IP público, verifique os registros de operação no portal Azure. Esses registros de operação devem indicar por que uma operação está falhando e onde uma política restritiva é aplicada.
1. Selecione o recurso onde uma diretiva é aplicada, em seguida, em **Políticas,** selecione e edite a diretiva para que ela seja menos restritiva.

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: A sincronização não é realizada há um tempo

### <a name="alert-message"></a>Mensagem de alerta

*O domínio gerenciado foi sincronizado pela última vez com o Azure AD em [data]. Os usuários podem não poder fazer login no domínio gerenciado ou as associações de grupo podem não estar em sincronia com o Azure AD.*

### <a name="resolution"></a>Resolução

[Verifique a saúde do Azure AD DS](check-health.md) para obter quaisquer alertas que indiquem problemas na configuração do domínio gerenciado. Problemas com a configuração da rede podem bloquear a sincronização do Azure AD. Se você for capaz de resolver alertas que indicam um problema de configuração, espere duas horas e verifique se a sincronização foi concluída com sucesso.

As seguintes razões comuns fazem com que a sincronização pare em domínios gerenciados pelo Azure AD DS:

* A conectividade de rede necessária está bloqueada. Para saber mais sobre como verificar a rede virtual do Azure em busca de problemas e o que é necessário, consulte grupos de segurança de [rede](alert-nsg.md) e os requisitos de [rede para os Serviços de Domínio AD do Azure.](network-considerations.md)
*  A sincronização de senha não foi configurada ou concluída com sucesso quando o domínio gerenciado pelo Azure AD DS foi implantado. Você pode configurar a sincronização de senha para [usuários somente](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) na nuvem ou usuários [híbridos a partir do prem](tutorial-configure-password-hash-sync.md).

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: O backup não é realizado há algum tempo

### <a name="alert-message"></a>Mensagem de alerta

*O backup do domínio gerenciado foi feito pela última vez em [data].*

### <a name="resolution"></a>Resolução

[Verifique a saúde do Azure AD DS](check-health.md) para obter alertas que indiquem problemas na configuração do domínio gerenciado. Problemas com a configuração da rede podem impedir que a plataforma Azure faça backups com sucesso. Se você for capaz de resolver alertas que indicam um problema de configuração, espere duas horas e verifique se a sincronização foi concluída com sucesso.

## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: Suspensão devido a uma assinatura desativada

### <a name="alert-message"></a>Mensagem de alerta

*O domínio gerenciado está suspenso porque a assinatura do Azure associada ao domínio não está ativa.*

### <a name="resolution"></a>Resolução

> [!WARNING]
> Se um domínio gerenciado pelo Azure AD DS for suspenso por um longo período de tempo, há o perigo de ele ser excluído. Resolva o motivo da suspensão o mais rápido possível. Para obter mais informações, consulte [Entenda os estados suspensos para o Azure AD DS](suspension.md).

O Azure AD DS requer uma assinatura ativa. Se a assinatura do Azure com a que o domínio gerenciado pelo Azure AD DS estava associada não estiver ativa, você deve renová-la para reativar a assinatura.

1. [Renovar sua assinatura do Azure](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable).
2. Uma vez que a assinatura é renovada, uma notificação Azure AD DS permite que você reative o domínio gerenciado.

Quando o domínio gerenciado é ativado novamente, o Azure AD DS gerenciado a saúde do domínio atualiza-se automaticamente dentro de duas horas e remove o alerta.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: Suspensão devido a uma configuração inválida

### <a name="alert-message"></a>Mensagem de alerta

*O domínio gerenciado está suspenso devido a uma configuração inválida. O serviço não foi capaz de gerenciar, corrigir ou atualizar os controladores de domínio para o seu domínio gerenciado por um longo tempo.*

### <a name="resolution"></a>Resolução

> [!WARNING]
> Se um domínio gerenciado pelo Azure AD DS for suspenso por um longo período de tempo, há o perigo de ele ser excluído. Resolva o motivo da suspensão o mais rápido possível. Para obter mais informações, consulte [Entenda os estados suspensos para o Azure AD DS](suspension.md).

[Verifique a saúde do Azure AD DS](check-health.md) para obter alertas que indiquem problemas na configuração do domínio gerenciado. Se você for capaz de resolver alertas que indicam um problema de configuração, espere duas horas e verifique se a sincronização foi concluída. Quando estiver pronto, [abra uma solicitação de suporte do Azure][azure-support] para reativar o domínio gerenciado pelo Azure AD DS.

## <a name="next-steps"></a>Próximas etapas

Se você ainda tiver problemas, [abra uma solicitação de suporte do Azure][azure-support] para assistência adicional de solução de problemas.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
