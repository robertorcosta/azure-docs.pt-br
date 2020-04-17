---
title: Configurar as configurações de cache Do Azure HPC
description: Explica como configurar configurações adicionais para o cache como MTU e squash sem raiz e como acessar os instantâneos expressos dos alvos de armazenamento do Azure Blob.
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: 137e41a3f7d346bb612605660d7798ac2fc297d1
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538811"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>Configure configurações adicionais de cache Azure HPC

A página **Configuração** no portal Azure tem opções para personalizar várias configurações. A maioria dos usuários não precisa alterá-los dos valores padrão.

Este artigo também descreve como usar o recurso de instantâneo para alvos de armazenamento Do Zure Blob. O recurso de instantâneo não tem configurações configuráveis.

Para ver as configurações, abra a página **configuração** do cache no portal Azure.

![captura de tela da página de configuração no portal Azure](media/configuration.png)

## <a name="adjust-mtu-value"></a>Ajustar o valor de MTU
<!-- linked from troubleshoot-nas article -->

Você pode selecionar o tamanho máximo da unidade de transmissão para o cache usando o menu suspenso rotulado tamanho **MTU**.

O valor padrão é de 1500 bytes, mas você pode alterá-lo para 1400.

> [!NOTE]
> Se você diminuir o tamanho do MTU do cache, certifique-se de que os clientes e sistemas de armazenamento que se comunicam com o cache tenham a mesma configuração de MTU ou um valor mais baixo.

Diminuir o valor do Cache MTU pode ajudá-lo a contornar as restrições de tamanho do pacote no resto da rede do cache. Por exemplo, algumas VPNs não podem transmitir pacotes de bytes de 1500 bytes em tamanho real com sucesso. Reduzir o tamanho dos pacotes enviados pela VPN pode eliminar esse problema. No entanto, observe que uma configuração de MTU de cache mais baixa significa que qualquer outro componente que se comunique com o cache - incluindo clientes e sistemas de armazenamento - também deve ter uma configuração mais baixa para evitar problemas de comunicação com o cache.

Se você não quiser alterar as configurações de MTU em outros componentes do sistema, não deverá baixar a configuração de MTU do cache. Existem outras soluções para contornar as restrições de tamanho de pacotes VPN. Leia [Ajustar as restrições de tamanho do pacote VPN](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) no artigo nas troublespara saber mais sobre o diagnóstico e o enfrentamento desse problema.

Saiba mais sobre as configurações do MTU nas redes virtuais do Azure lendo [a sintonia de desempenho TCP/IP para VMs Azure](../virtual-network/virtual-network-tcpip-performance-tuning.md).

## <a name="configure-root-squash"></a>Configurar abóbora raiz
<!-- linked from troubleshoot -->

A **configuração De squash raiz** controla como o Cache Azure HPC permite acesso raiz. O squash raiz ajuda a impedir o acesso ao nível raiz de clientes não autorizados.

Essa configuração permite que os usuários controlem o acesso ``no_root_squash`` raiz no nível de cache, o que pode ajudar a compensar a configuração necessária para sistemas NAS usados como alvos de armazenamento. (Leia mais sobre [os pré-requisitos de destino de armazenamento do NFS](hpc-cache-prereqs.md#nfs-storage-requirements).) Ele também pode melhorar a segurança quando usado com alvos de armazenamento Azure Blob.

A configuração padrão é **Sim**. (Os caches criados antes de abril de 2020 podem ter a configuração padrão **No**.) Quando ativado, esse recurso também impede o uso de bits de permissão set-UID nas solicitações do cliente para o cache.

## <a name="view-snapshots-for-blob-storage-targets"></a>Exibir instantâneos para alvos de armazenamento blob

O Cache Azure HPC salva automaticamente instantâneos de armazenamento para alvos de armazenamento Do Zure Blob. Os snapshots fornecem um ponto de referência rápido para o conteúdo do recipiente de armazenamento back-end. Os snapshots não são uma substituição para backups de dados e não incluem nenhuma informação sobre o estado dos dados armazenados em cache.

> [!NOTE]
> Esse recurso de instantâneo é diferente do recurso de instantâneo incluído no software de armazenamento NetApp, Isilon ou ZFS. Essas implementações de snapshot alteram o cache para o sistema de armazenamento back-end antes de tirar o instantâneo.
>
> Para eficiência, o snapshot do Cache Azure HPC não faz alterações primeiro, e apenas registra dados que foram gravados no contêiner Blob. Este instantâneo não representa o estado dos dados armazenados em cache, portanto, alterações recentes podem ser excluídas.

Este recurso está disponível apenas para alvos de armazenamento Do Zure Blob, e sua configuração não pode ser alterada.

Instantâneos são tirados a cada oito horas, em UTC 0:00, 08:00, e 16:00.

O Azure HPC Cache armazena instantâneos diários, semanais e mensais até que sejam substituídos por novos. Os limites são:

* até 20 instantâneos diários
* até 8 instantâneos semanais
* até 3 instantâneos mensais

Acesse os instantâneos `.snapshot` do diretório no espaço de nome do alvo de armazenamento blob.
