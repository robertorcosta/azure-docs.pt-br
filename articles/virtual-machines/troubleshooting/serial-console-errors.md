---
title: Erros do Console Serial Azure | Microsoft Docs
description: Erros comuns dentro do Console Serial Do Azure
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: 61ae0ef92fe522a2a038a6076a5e0c0a10ee47b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060702"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Erros comuns dentro do Console Serial Do Azure
Há um conjunto de erros conhecidos dentro do Console Serial Azure. Esta é uma lista desses erros e etapas de mitigação para eles.

## <a name="common-errors"></a>Erros comuns

Erro                             |   Atenuação
:---------------------------------|:--------------------------------------------|
"O Azure Serial Console requer que os diagnósticos de inicialização sejam ativados. Clique aqui para configurar diagnósticos de inicialização para sua máquina virtual." | Certifique-se de que o conjunto de escala de VM ou máquina virtual tenha [diagnósticos de inicialização ativados.](boot-diagnostics.md) Se você estiver usando o console serial em uma instância de conjunto de escala de máquina virtual, certifique-se de que sua instância tenha o modelo mais recente.
"O Azure Serial Console requer uma máquina virtual para ser executado. Use o botão Iniciar acima para iniciar sua máquina virtual."  | A instância de conjunto de escala de VM ou máquina virtual deve estar em um estado inicial para acessar o console serial (sua VM não deve ser parada ou desalocada). Certifique-se de que a instância de conjunto de escala da vm ou da máquina virtual esteja sendo executado e tente novamente.
"O Azure Serial Console não está habilitado para esta assinatura, entre em contato com o administrador de assinatura para habilitar." | O Azure Serial Console pode ser desativado em um nível de assinatura. Se você é um administrador de assinatura, você pode [ativar e desativar o Azure Serial Console](./serial-console-enable-disable.md). Se você não é um administrador de assinatura, você deve entrar em contato com o administrador de assinatura para os próximos passos.
Uma resposta "Proibido" foi encontrada ao acessar a conta de armazenamento do diagnóstico de inicialização desta VM. | Certifique-se de que o diagnóstico de inicialização não tem um firewall de conta. Uma conta de armazenamento do diagnóstico de inicialização acessível é necessária para o console serial funcione. Por design, o console serial não pode funcionar com firewalls de conta de armazenamento habilitados na conta de armazenamento do diagnóstico de inicialização.
Você não tem as permissões necessárias para usar esta VM com o console serial. Certifique-se de ter pelo menos permissões de função de Colaborador da Máquina Virtual.| O acesso ao console serial exige que você tenha acesso ao nível do contribuinte ou acima em seu conjunto de escala de VM ou máquina virtual. Para obter mais informações, consulte a [página visão geral](serial-console-overview.md).
A conta de armazenamento '' usada para diagnósticos de inicialização nesta VM não pôde ser encontrada. Verifique se o diagnóstico de inicialização está ativado para esta VM, esta conta de armazenamento não foi excluída e você tem acesso a esta conta de armazenamento. | Verifique duas vezes se você não excluiu a conta de armazenamento de diagnóstico de inicialização para o seu conjunto de escala de VM ou máquina virtual
A conexão do console serial com o VM encontrou um erro: 'Solicitação ruim' (400) | Isso pode acontecer se o uri de diagnóstico de inicialização estiver incorreto. Por exemplo, "http://" foi usado em vez de "https://". O URI de diagnóstico de inicialização pode ser corrigido com este comando:`az vm boot-diagnostics enable --name vmName --resource-group rgName --storage https://<storageAccountUri>.blob.core.windows.net/`
Você não tem as permissões necessárias para escrever na conta de armazenamento de diagnóstico de inicialização desta VM. Por favor, certifique-se de ter pelo menos permissões de contribuinte vm | O acesso ao console serial requer acesso ao nível do contribuinte na conta de armazenamento de diagnóstico de inicialização. Para obter mais informações, consulte a [página visão geral](serial-console-overview.md).
Não é possível determinar o grupo de * &lt;&gt;* recursos para a conta de armazenamento de diagnóstico de inicialização STORAGEACCOUNTNAME . Verifique se o diagnóstico de inicialização está habilitado para essa VM e se você tem acesso a essa conta de armazenamento. | O acesso ao console serial requer acesso ao nível do contribuinte na conta de armazenamento de diagnóstico de inicialização. Para obter mais informações, consulte a [página visão geral](serial-console-overview.md).
O provisionamento desta VM ainda não foi bem sucedido. Certifique-se de que a VM está totalmente implantada e tente novamente a conexão do console serial. | Seu conjunto de escala de VM ou máquina virtual ainda pode estar provisionando. Espere algum tempo e tente de novo.
O soquete da Web está fechado ou não pôde ser aberto. | Você pode precisar adicionar `*.console.azure.com`acesso a firewall a . Uma abordagem mais detalhada, mas mais longa, é permitir o acesso ao firewall às faixas IP do [Microsoft Azure Datacenter,](https://www.microsoft.com/download/details.aspx?id=41653)que mudam regularmente.
O console serial não funciona com uma conta de armazenamento usando o Azure Data Lake Storage Gen2 com namespaces hierárquicos. | Este é um problema conhecido com namespaces hierárquicos. Para mitigar, certifique-se de que a conta de armazenamento de diagnóstico de inicialização da VM não seja criada usando o Azure Data Lake Storage Gen2. Essa opção só pode ser definida após a criação da conta de armazenamento. Você pode ter que criar uma conta de armazenamento de diagnóstico de inicialização separada sem que o Azure Data Lake Storage Gen2 seja habilitado para mitigar esse problema.
A conexão serial do console com a VM encontrou um erro: 'Proibido'(SubscriptionNotEnabled) - Nome da assinatura indefinido, id \<de assinatura> está em estado não habilitado indefinido | Esse problema pode ocorrer se a assinatura em que um usuário criou sua conta de armazenamento Cloud Shell foi desativada. Para mitigar, inicie o Cloud Shell e [execute as etapas necessárias](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage#unmount-clouddrive-1) para reprovisionar uma conta de armazenamento de backup para o Cloud Shell na assinatura atual.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre o [Console Serial Azure para VMs Linux](./serial-console-linux.md)
* Saiba mais sobre o [Console Serial Azure para VMs Windows](./serial-console-windows.md)