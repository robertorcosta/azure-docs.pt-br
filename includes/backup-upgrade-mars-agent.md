---
title: Atualize o agente de backup do Azure
description: Essas informações explicam por que você deve atualizar o Azure Backup Agent e onde baixar a atualização.
services: backup
cloud: ''
suite: ''
author: dcurwin
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 03/03/2020
ms.author: dacurwin
ms.openlocfilehash: bd298f758d6109b908db01dd2ae3b97e5e2f714a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673211"
---
## <a name="upgrade-the-mars-agent"></a>Atualize o Agente MARS

As versões do Agente microsoft azure recovery services (MARS) abaixo de 2.0.9083.0 têm uma dependência do serviço de controle de acesso do Azure. O Agente MARS também é referido como o Agente de Backup do Azure.

Em 2018, a Microsoft [depreciou o serviço de controle de acesso do Azure.](../articles/active-directory/azuread-dev/active-directory-acs-migration.md) A partir de 19 de março de 2018, todas as versões do AGENTE MARS abaixo de 2.0.9083.0 sofrerão falhas de backup. Para evitar ou resolver falhas de backup, [atualize seu Agente MARS para a versão mais recente](https://support.microsoft.com/help/4538314/update-for-azure-backup-for-microsoft-azure-recovery-services-agent). Para identificar servidores que requerem uma atualização do MARS Agent, siga as etapas do [agente Upgrade the Microsoft Azure Recovery Services (MARS).](../articles/backup/upgrade-mars-agent.md)

O Agente MARS é usado para fazer backup de arquivos e pastas e dados do estado do sistema para o Azure. O DPM do Centro do Sistema e o Azure Backup Server usam o Agente MARS para fazer backup dos dados no Azure.
