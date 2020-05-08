---
title: Concluir uma recuperação de desastre de máquinas virtuais
description: Este artigo mostra como concluir uma recuperação de desastre de máquinas virtuais usando a AVS
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 86f823444d4fff3edf8651f4d949c71d2c981ec7
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740545"
---
# <a name="complete-a-disaster-recovery-of-virtual-machines-using-azure-vmware-solution"></a>Concluir uma recuperação de desastre de máquinas virtuais usando a solução VMWare do Azure

Este artigo contém o processo para concluir uma recuperação de desastre de suas máquinas virtuais com a solução de HCX (extensão de nuvem híbrida) do VMWare e usando uma nuvem privada da solução Azure VMWare como o site de recuperação ou destino.

O VMWare HCX fornece várias operações que fornecem uma granularidade e um controle fino nas políticas de replicação. As operações disponíveis incluem:

- Inverter – após a ocorrência de um desastre. Reverse ajuda a tornar o site B o site de origem e o site a onde a VM protegida agora reside.

- Pausar – pausar a política de replicação atual associada à máquina virtual selecionada.

- Retomar-pausa a política de replicação atual associada à máquina virtual selecionada.

- Remover-Remove a política de replicação atual associada à máquina virtual selecionada.

- Sincronizar agora – fora da máquina virtual de origem de sincronização vinculada à VM protegida.

Neste guia, os seguintes cenários de replicação são abordados:

- Proteger uma VM ou um grupo de VMs.

- Conclua uma recuperação de teste de uma VM ou de um grupo de VMs.

- Recuperar uma VM ou um grupo de VMs.

- Proteção reversa de uma VM ou de um grupo de VMs.

## <a name="protect-virtual-machines"></a>Proteger máquinas virtuais

Faça logon no **vSphere Client** no site de origem e acesse o **plug-in HCX**.

:::image type="content" source="./media/disaster-recovery/hcx-vsphere.png" alt-text="Opção HCX em vSphere" border="true":::

Insira a área de **recuperação de desastre** e clique em **proteger VMs**.

:::image type="content" source="./media/disaster-recovery/protect-virtual-machine.png" alt-text="Selecione proteger VMs" border="true":::

Na janela que é aberta, selecione a origem e os sites remotos, o site remoto, nesse caso, deve ser a nuvem privada da AVS.

:::image type="content" source="./media/disaster-recovery/protect-virtual-machines.png" alt-text="janela proteger VMs" border="true":::

Se necessário, selecione as opções de replicação padrão:

- **Habilitar compactação:** Recomendado para cenários de baixa taxa de transferência.

- **Habilitar quiescence:** Pausa a VM para garantir que uma cópia consistente seja sincronizada com o site remoto.

- **Armazenamento de destino:** Selecione o repositório de armazenamento remoto para as VMs protegidas. Em uma nuvem privada de AVS, essa seleção deve ser o armazenamento de datavsan.

- **Contêiner de computação:** O cluster vSphere remoto ou o pool de recursos.

- **Pasta de destino:** A pasta de destino remoto, essa configuração é opcional e, se nenhuma pasta for selecionada, as VMs serão modeladas diretamente no cluster selecionado.

- **RPO:** Esse valor é o intervalo de sincronização entre a máquina virtual de origem e a máquina virtual protegida e pode ser de 5 minutos a 24 horas.

- **Intervalo de instantâneos:** Intervalo entre instantâneos.

- **Número de instantâneos:** Número total de instantâneos dentro do intervalo de instantâneos configurado.

:::image type="content" source="./media/disaster-recovery/protect-virtual-machine-options.png" alt-text="opções de proteção de máquinas virtuais" border="true":::

Selecione uma ou mais máquinas virtuais na lista e configure as opções de replicação da máquina virtual conforme necessário.

Por padrão, as máquinas virtuais herdarão a política de configurações globais configurada nas opções de replicação padrão. Para cada interface de rede na VM selecionada, configure o **grupo de portas de rede** remota e selecione **concluir** para iniciar o processo de proteção.

:::image type="content" source="./media/disaster-recovery/network-interface-options.png" alt-text="opções de interface de rede" border="true":::

Como visto na imagem a seguir, você pode monitorar o processo para cada uma das máquinas virtuais selecionadas na mesma área de recuperação de desastre.

:::image type="content" source="./media/disaster-recovery/protect-monitor-progress.png" alt-text="monitorar o progresso da proteção" border="true":::

Depois que a VM tiver sido protegida, os instantâneos diferentes poderão ser vistos na guia **instantâneos** .

:::image type="content" source="./media/disaster-recovery/list-of-snapshots.png" alt-text="lista de instantâneos" border="true":::

O triângulo amarelo significa que os instantâneos e o virtual não foram testados em uma operação de recuperação de teste.

Há diferenças importantes entre uma VM desligada e outra ligada.
A captura de tela anterior mostra o processo de sincronização para uma máquina virtual ligada. Ele iniciou o processo de sincronização até que ele termine o primeiro instantâneo, que é uma cópia completa da VM e, em seguida, conclua as próximas no intervalo configurado.

Para uma VM desligada, ela sincronizará uma cópia e, em seguida, a VM aparecerá como inativa e a operação de proteção será mostrada como concluída.

Quando a máquina virtual estiver ligada, ela iniciará o processo de sincronização para o site remoto.

## <a name="complete-a-test-recover-of-virtual-machines"></a>Concluir uma recuperação de teste de máquinas virtuais

Faça logon no **vSphere Client** no site remoto, que é a nuvem privada da AVS. No **plug-in HCX**, na área recuperação de desastres, selecione as reticências verticais em qualquer VM para exibir o menu operações. Selecione **testar recuperar VM**.

:::image type="content" source="./media/disaster-recovery/test-recover-virtual-machine.png" alt-text="Selecione testar recuperação de VM" border="true":::

Na nova janela, selecione as opções para o teste. Selecione o instantâneo que você deseja usar para testar Estados diferentes da máquina virtual.

:::image type="content" source="./media/disaster-recovery/choose-snapshot.png" alt-text="Escolha um instantâneo e clique em testar" border="true":::

Depois de clicar no **teste**, a operação de recuperação será iniciada.

Quando a operação de recuperação de teste for concluída, a nova VM poderá ser verificada no vCenter de nuvem privada da AVS.

:::image type="content" source="./media/disaster-recovery/verify-test-recovery.png" alt-text="verificar a operação de recuperação" border="true":::

Finalmente, após o teste ter sido feito na VM ou em qualquer aplicativo em execução, faça uma limpeza para excluir a instância de teste.

:::image type="content" source="./media/disaster-recovery/cleanup-test-instance.png" alt-text="instância de teste de limpeza" border="true":::

## <a name="recover-virtual-machines"></a>Recuperar as máquinas virtuais

Faça logon no **vSphere Client** no site remoto, que é a nuvem privada da AVS e acesse o **plug-in do HCX**.

Para o cenário de recuperação, um grupo de máquinas virtuais usado para este exemplo.

Selecione a máquina virtual a ser recuperada na lista, abra o menu **ações** e selecione **recuperar VMs**.

:::image type="content" source="./media/disaster-recovery/recover-virtual-machines.png" alt-text="recuperar máquinas virtuais" border="true":::

Configure as opções de recuperação para cada instância e clique em **recuperar** para iniciar a operação de recuperação.

:::image type="content" source="./media/disaster-recovery/recover-virtual-machines-confirm.png" alt-text="confirmação de recuperação de máquinas virtuais" border="true":::

Depois que a operação de recuperação for concluída, as novas VMs serão exibidas no inventário de vCenter Server remoto.

## <a name="complete-a-reverse-replication-on-virtual-machines"></a>Concluir uma replicação inversa em máquinas virtuais

Faça logon no **cliente vSphere** na sua nuvem privada da AVS e acesse o **plug-in HCX**.
É necessário que as máquinas virtuais originais no site de origem sejam desligadas antes de você iniciar a replicação inversa. A operação falhará se as máquinas virtuais não estiverem desligadas.

Selecione as máquinas virtuais a serem replicadas de volta para o site de origem na lista, abra o menu **ações** e selecione **reverter**. Na janela pop-up, clique em **reverter** para iniciar a replicação.

:::image type="content" source="./media/disaster-recovery/reverse-operation-virtual-machines.png" alt-text="Selecionar ação inversa em proteger operações" border="true":::

A replicação pode ser monitorada na seção de detalhes de cada máquina virtual.

:::image type="content" source="./media/disaster-recovery/review-reverse-operation.png" alt-text="examinar os resultados da ação inversa" border="true":::

## <a name="disaster-recovery-plan-automation"></a>Automação do plano de recuperação de desastre

Atualmente, o VMWare HCX não tem um mecanismo interno para criar e automatizar um plano de recuperação de desastres. Esse recurso não existe no HCX. No entanto, ele fornece um conjunto de APIs REST, incluindo APIs para a operação de recuperação de desastre.

A especificação de API pode ser acessada no HCX Manager na URL.

As operações a seguir na recuperação de desastres são cobertas por essas APIs.

- Proteger

- Recuperar

- Recuperação de teste

- Recuperação planejada

- Reverse

- Consulta

- Limpeza de teste

- Pausar

- Retomar

- Remover Proteção

- Reconfigurar

Um exemplo de uma carga de operação de recuperação em JSON é mostrado abaixo.

```json
[

    {

        "replicationId": "string",

        "needPowerOn": true,

        "instanceId": "string",

        "source": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "destination": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "placement": [

            {

                "containerType": "string",

                "containerId": "string"

            }

        ],

        "resourceId": "string",

        "forcePowerOff": true,

        "isTest": true,

        "forcePowerOffAfterTimeout": true,

        "isPlanned": true

    }

]
```

Usando essas APIs, um cliente pode criar um mecanismo personalizado para automatizar a criação e a execução de um plano de recuperação de desastres.
