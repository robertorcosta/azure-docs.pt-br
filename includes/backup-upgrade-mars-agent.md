---
title: Atualizar o agente de backup do Azure
description: Essas informações explicam por que você deve atualizar o agente de backup do Azure e onde baixar a atualização.
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
ms.openlocfilehash: 227ec87081817b273b66684e31a0ebd0e11134a0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197106"
---
## <a name="upgrade-the-mars-agent"></a>Atualizar o agente MARS

As versões do agente de Serviços de Recuperação do Microsoft Azure (MARS) abaixo do 2.0.9083.0 têm uma dependência do serviço de controle de acesso do Azure. O agente MARS também é conhecido como o agente de backup do Azure.

No 2018, [a Microsoft preteriu o serviço de controle de acesso do Azure](../articles/active-directory/azuread-dev/active-directory-acs-migration.md). A partir de 19 de março de 2018, todas as versões do agente MARS abaixo de 2.0.9083.0 apresentarão falhas de backup. Para evitar ou resolver falhas de backup, [faça upgrade do seu Agente MARS para a última versão](https://go.microsoft.com/fwlink/?linkid=229525). Para identificar os servidores que exigem uma atualização de agente MARS, siga as etapas no [blog de backup para atualizar agentes Mars](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/).

O agente MARS é usado para fazer backup de arquivos e pastas e dados de estado do sistema no Azure. O System Center DPM e o Servidor de Backup do Azure usam o agente MARS para fazer backup de dados no Azure.
