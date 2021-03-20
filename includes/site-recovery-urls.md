---
title: incluir arquivo
description: incluir arquivo
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 09/12/2018
ms.author: raynew
ms.openlocfilehash: f4cd9cad3813378fcdc3f06e8ab1c28eced4f93c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95999171"
---
Nome | URL comercial | URL governamental | Descrição
--- | --- | --- | ---
Azure Active Directory | ``login.microsoftonline.com`` | ``login.microsoftonline.us`` | Usadas pelo Azure Active Directory para o gerenciamento de identidade e controle de acesso. 
Backup | ``*.backup.windowsazure.com`` | ``*.backup.windowsazure.us`` | Usado para transferência de dados de replicação e coordenação.
Replicação | ``*.hypervrecoverymanager.windowsazure.com`` | ``*.hypervrecoverymanager.windowsazure.us``  | Usado para operações de gerenciamento de replicação e coordenação.
Armazenamento | ``*.blob.core.windows.net`` | ``*.blob.core.usgovcloudapi.net``  | Usado para acessar a conta de armazenamento que armazena os dados replicados.
Telemetria (opcional) | ``dc.services.visualstudio.com`` | ``dc.services.visualstudio.com`` | Usado para telemetria.
Sincronização da hora | ``time.windows.com`` | ``time.nist.gov`` | Usado para verificar a sincronização de data/hora entre o tempo do sistema e o tempo global, em todas as implantações.


