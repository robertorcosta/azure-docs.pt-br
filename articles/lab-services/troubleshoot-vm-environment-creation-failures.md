---
title: Solução de problemas VM e falhas no ambiente Azure DevTest Labs
description: Aprenda a solucionar problemas de máquinavirtual (VM) e falhas de criação de ambientes no Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 155a359608cf6d846578306545f5ce0b4003949c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76166339"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>Solucionar problemas de máquina virtual (VM) e falhas de criação de ambientes no Azure DevTest Labs
O DevTest Labs lhe dá avisos se um nome de máquina é inválido ou se você está prestes a violar uma política de laboratório. Às vezes, `X` você vê vermelho ao lado de seu vm laboratório ou status de ambiente que informa que algo deu errado.  Este artigo fornece alguns truques que você pode usar para encontrar a questão subjacente e, espero, evitar o problema no futuro.

## <a name="portal-notifications"></a>Notificações do portal
Se você estiver usando o portal Azure, o primeiro lugar para olhar é o **painel de notificações**.  O painel de notificações, disponível na barra de comando principal clicando no ícone do **sino,** lhe dirá se a vm do laboratório ou a criação do ambiente foi bem sucedida ou não.  Se houve uma falha, você verá a mensagem de erro associada à falha de criação. Os detalhes geralmente dão mais informações para ajudá-lo a resolver o problema. No exemplo a seguir, a criação da máquina virtual falhou por causa da ficar sem núcleos. A mensagem detalhada informa como corrigir o problema e solicitar um aumento de cota principal.

![Notificação do portal Azure](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)

### <a name="vm-in-corruption-state"></a>VM em estado de corrupção
Se você ver o status da sua VM no laboratório como **Corrompido,** a VM subjacente pode ter sido excluída da página **da Máquina Virtual** para a qual o usuário pode navegar a partir da página **Máquinas Virtuais** (não da página DevTest Labs). Limpe seu laboratório nos Laboratórios DevTest excluindo o VM do laboratório. Então, recrie seu VM no laboratório. 

![VM em estado corrompido](./media/troubleshoot-vm-environment-creation-failures/vm-corrupted-state.png)



## <a name="activity-logs"></a>Logs de atividade
Veja os registros de atividades se você estiver investigando uma falha algum tempo depois de tentar a criação de sua VM ou ambiente. Esta seção mostra como encontrar registros de VMs e ambientes.

## <a name="activity-logs-for-virtual-machines"></a>Registros de atividades para máquinas virtuais

1. Na página inicial do seu laboratório, selecione a VM para iniciar a página **Máquina Virtual.**
2. Na página **Máquina Virtual,** na seção **MONITORING** do menu esquerdo, selecione **Registro de atividades** para ver todos os registros associados à VM.
3. Nos itens do registro de atividades, selecione a operação que falhou. Normalmente, a operação `Write Virtualmachines`fracassada é chamada .
4. No painel direito, mude para a guia JSON. Você vê os detalhes na exibição JSON do registro.

    ![Registro de atividades de uma VM](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. Olhe através do registro JSON `statusMessage` até encontrar a propriedade. Ele lhe dá a mensagem de erro principal e informações adicionais de detalhes, se aplicável. O JSON a seguir é um exemplo para o erro superior citado observado anteriormente neste artigo.

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at https://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>Registro de atividades para um ambiente

Para ver o registro de atividades para uma criação de ambiente, siga estas etapas:

1. Na página inicial do seu laboratório, **selecione Configuração e políticas** no menu esquerdo.
2. na página **Configuração e políticas,** selecione **Ativações de atividade** no menu.
3. Procure a falha na lista de atividades no registro e selecione-a.
4. No painel direito, mude para a guia JSON e procure a **mensagem de statusMessage**.

    ![Registro de atividades do ambiente](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Logs de implantação do modelo do Resource Manager
Se o seu ambiente ou máquina virtual foi criado através da automação, há um último lugar para procurar informações de erro. Esse é o registro de implantação do azure Resource Manager. Quando um recurso de laboratório é criado por meio da automação, muitas vezes é feito através de uma implantação de modelo do Azure Resource Manager. Consulte[https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) os modelos do Azure Resource Manager que criam recursos do DevTest Labs.

Para ver os registros de implantação do modelo de laboratório, siga estas etapas:

1. Inicie a página para o grupo de recursos em que o laboratório existe.
2. Selecione **Implantações** no menu esquerdo em **Configurações**.
3. Procure implantações com um status falho e selecione-o.
4. Na página **'Implantação',** selecione O link **Dedetalhes da Operação** para a operação que falhou.
5. Você vê detalhes sobre a operação que falhou na janela **de detalhes** da Operação.

## <a name="next-steps"></a>Próximas etapas
Ver [Falhas de artefatos de solução de problemas](devtest-lab-troubleshoot-artifact-failure.md)
