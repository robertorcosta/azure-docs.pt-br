---
title: Como recuperar uma conta de armazenamento excluída
description: Saiba como recuperar uma conta de armazenamento excluída
author: todmccoy
manager: dcscontentpm
ms.service: storage
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: rogarana
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: 05465d4a03335ac607ba8981116c66fd6dac9416
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252627"
---
# <a name="how-to-recover-a-deleted-storage-account"></a>Como recuperar uma conta de armazenamento excluída

O armazenamento do Azure fornece resiliência de dados por meio de réplicas automatizadas, mas não impede que os usuários ou o código do aplicativo corrompam dados, seja acidentalmente ou intencionalmente. Manter a fidelidade de dados durante instâncias de erro de aplicativo ou de usuário requer técnicas mais avançadas, como copiar os dados para um local de armazenamento secundário com um log de auditoria.

A tabela a seguir fornece uma visão geral do escopo da recuperação da conta de armazenamento, dependendo da estratégia de replicação.

| |LRS|ZRS|GRS|RA-GRS|
|---|---|---|---|---|
|Azure Resource Manager da conta de armazenamento|Sim|Sim|Sim|Sim|
|Conta de armazenamento clássica|Sim|Sim|Sim|Sim|

Reúna as informações a seguir e arquivo uma solicitação de suporte com Suporte da Microsoft:

* Nome da conta de armazenamento
* Data de exclusão
* Região da conta de armazenamento
* Como a conta de armazenamento foi excluída?
* Qual método excluiu a conta de armazenamento? (Portal, PowerShell, etc.)

Pontos importantes

* Em geral, pode levar até 15 dias a partir do momento da exclusão do serviço de armazenamento para executar a coleta de lixo, portanto, a recuperação das contas de armazenamento pode não ser recuperada com um SLA.
* Suporte da Microsoft tentará recuperar o contêiner/conta em uma base de melhor esforço e não poderá garantir a recuperação.

> [!NOTE]
> A recuperação poderá não ser bem-sucedida se a conta tiver sido recriada. Se você já criou a conta novamente, deverá excluí-la primeiro antes que a recuperação possa ser tentada.
