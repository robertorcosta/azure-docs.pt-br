---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 3f71eb1045f08a2eb6121c8c1c2ce86685606acf
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94986874"
---
|Nome |Descrição |Políticas |Versão |
|---|---|---|---|
|[Auditar os computadores com configurações de segurança de senha não segura](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_WindowsPasswordSettingsAINE.json) |Essa iniciativa implanta os requisitos de política e audita computadores com configurações de segurança de senha não seguras. Para obter mais informações sobre as políticas de Configuração de Convidado, acesse [https://aka.ms/gcpol](https://aka.ms/gcpol) |9 |1.0.0 |
|[Implantar pré-requisitos para habilitar as políticas de Configuração de Convidado em máquinas virtuais](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json) |Essa iniciativa adiciona uma identidade gerenciada atribuída ao sistema e implanta a extensão de Configuração de Convidado apropriada para a plataforma em máquinas virtuais qualificadas para serem monitoradas pelas políticas de Configuração de Convidado. Este é um pré-requisito das políticas de Configuração de Convidado e precisa ser atribuído ao escopo da atribuição de política antes do uso de qualquer política de Configuração de Convidado. Para obter mais informações sobre a Configuração de Convidado, acesse [https://aka.ms/gcpol](https://aka.ms/gcpol). |4 |1.0.0 – versão prévia |
|[Os computadores Windows devem atender aos requisitos da linha de base de segurança do Azure](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_AzureBaseline.json) |Essa iniciativa audita os computadores Windows com configurações que não atendem à linha de base de segurança do Azure. Para obter detalhes, acesse [https://aka.ms/gcpol](https://aka.ms/gcpol) |29 |2.0.0-preview |
