---
title: Requisitos e considerações para usar Azure NetApp Files replicação entre regiões do volume | Microsoft Docs
description: Descreve os requisitos e considerações para usar a funcionalidade de replicação entre regiões de volume do Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: f2a50872fdb71419a0c3f068712ec67523a098e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708517"
---
# <a name="requirements-and-considerations-for-using-cross-region-replication"></a>Requisitos e considerações para usar a replicação entre regiões 

Observe os seguintes requisitos e considerações sobre [o uso da funcionalidade de replicação entre regiões de volume](cross-region-replication-create-peering.md) do Azure NetApp Files:  

## <a name="requirements-and-considerations"></a>Requisitos e considerações 

* O recurso de replicação entre regiões está atualmente em visualização pública. Você precisa enviar uma solicitação Waitlist para acessar o recurso por meio da [página de envio de espera de replicação entre regiões Azure NetApp files](https://aka.ms/anfcrrpreviewsignup). Aguarde um email de confirmação oficial da equipe de Azure NetApp Files antes de usar o recurso de replicação entre regiões.
* Azure NetApp Files replicação só está disponível em determinados pares de regiões fixas. Consulte [pares de regiões com suporte](cross-region-replication-introduction.md#supported-region-pairs). 
* Os volumes SMB têm suporte junto com volumes NFS. A replicação de volumes SMB requer uma conexão Active Directory nas contas do NetApp de origem e de destino. A conexão do AD de destino deve ter acesso aos servidores DNS ou ao adicionar controladores de domínio que podem ser acessados da sub-rede delegada na região de destino. Para obter mais informações, consulte [Requirements for Active Directory Connections](azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections). 
* A conta de destino deve estar em uma região diferente da região do volume de origem. Você também pode selecionar uma conta existente do NetApp em uma região diferente.  
* Atualmente, a replicação do Azure NetApp Files não dá suporte a várias assinaturas; todas as replicações devem ser executadas em uma única assinatura.
* Você pode configurar um máximo de cinco volumes para replicação em uma única assinatura por região. Você pode abrir um tíquete de suporte para solicitar um aumento na cota padrão de cinco volumes de destino de replicação (por assinatura em uma região). 
* Pode haver um atraso de até cinco minutos para que a interface reflita um instantâneo recém-adicionado no volume de origem.  
* Não há suporte para topologias de entrada/saída em cascata e de ventilador.
* A configuração da replicação de volume para volumes de origem criados a partir do instantâneo não tem suporte no momento.
* Depois de configurar a replicação entre regiões, o processo de replicação cria *instantâneos SnapMirror* para fornecer referências entre o volume de origem e o volume de destino. Os instantâneos do SnapMirror são reciclados automaticamente quando um novo é criado para cada transferência incremental. Você não pode excluir instantâneos SnapMirror até que o relacionamento de replicação e o volume sejam excluídos. 
* Você pode excluir instantâneos manuais no volume de origem de um relacionamento de replicação quando a relação de replicação estiver ativa ou interrompida e também depois que a relação de replicação for excluída. Você não pode excluir instantâneos manuais para o volume de destino até que a relação de replicação seja interrompida.

## <a name="next-steps"></a>Próximas etapas
* [Criar um emparelhamento de replicação](cross-region-replication-create-peering.md)
* [Exibir o status de integridade da relação de replicação](cross-region-replication-display-health-status.md)
* [Gerenciar recuperação de desastre](cross-region-replication-manage-disaster-recovery.md)
* [Métricas de replicação de volume](azure-netapp-files-metrics.md#replication)
* [Solucionar problemas de replicação entre regiões](troubleshoot-cross-region-replication.md)


