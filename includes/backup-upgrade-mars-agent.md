---
title: Upgrade do agente de Backup do Azure
description: Essas informações explicam por que você deve fazer upgrade do agente de Backup do Azure e onde baixar o upgrade.
services: backup
cloud: ''
suite: ''
author: dcurwin
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 8/29/2018
ms.author: dacurwin
ms.openlocfilehash: c22fbcd07286ddffedd8fc2fdc12017b9338d7f7
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161813"
---
## <a name="upgrade-the-mars-agent"></a>Fazer upgrade do agente MARS

As versões do agente de MARS (Serviço de Recuperação do Microsoft Azure) abaixo de 2.0.9083.0 dependem do ACS (Serviço de Controle de Acesso do Microsoft Azure). O agente de MARS também é mencionado como agente de Backup do Azure. Em 2018, o Azure [preteriu o serviço de Controle de Acesso do Azure (ACS)](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). A partir de 19 de março de 2018, todas as versões do agente MARS abaixo de 2.0.9083.0 experimentarão falhas de backup. Para evitar ou resolver falhas de backup, [faça upgrade do seu agente de MARS para a versão mais recente](https://go.microsoft.com/fwlink/?linkid=229525). Para identificar os servidores que exigem uma atualização do agente de MARS, siga as etapas no [Blog de backup para fazer upgrade dos agentes de MARS](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/). O agente de MARS é usado para fazer backup de arquivos e pastas e dados de estado do sistema no Azure. O System Center DPM e o Servidor de Backup do Azure usam o agente de MARS para fazer backup de dados no Azure.
