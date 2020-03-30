---
title: Suporte para usar a recuperação do site do Azure com backup do Azure
description: Fornece uma visão geral de como o Azure Site Recovery e o Azure Backup podem ser usados juntos.
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: sideeksh
ms.openlocfilehash: d8c896bc655339fbd7d51f738fb629450e8c12ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72376209"
---
# <a name="support-for-using-site-recovery-with-azure-backup"></a>Suporte para usar a recuperação do site com backup do Azure

Este artigo resume o suporte ao uso do [serviço de recuperação de sites](site-recovery-overview.md) em conjunto com o serviço de backup do [Azure](https://docs.microsoft.com/azure/backup/backup-overview).

**Ação** | **Suporte do Site Recovery** | **Detalhes**
--- | --- | ---
**Implantar serviços em conjunto** | Com suporte | Os serviços são interoperáveis e podem ser configurados juntos.
**Backup/restauração de arquivos** | Com suporte | Quando o backup e a replicação são ativados para uma VM e os backups são tomados, não há problema em restaurar arquivos nas VMs do lado de origem ou no grupo de VMs. A replicação continua como de costume, sem mudanças na saúde da replicação.
**Restauração de disco** | Sem suporte atual | Se você restaurar um disco de backup, você precisa desativar e reativar a replicação para a VM novamente.
**Restauração da VM** | Sem suporte atual | Se você restaurar uma VM ou um grupo de VMs, você precisará desativar e reativar a replicação para a VM.  


