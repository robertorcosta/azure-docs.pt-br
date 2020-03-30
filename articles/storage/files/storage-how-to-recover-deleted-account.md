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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252627"
---
# <a name="how-to-recover-a-deleted-storage-account"></a>Como recuperar uma conta de armazenamento excluída

O Azure Storage fornece resiliência de dados através de réplicas automatizadas, mas não impede que usuários ou códigos de aplicativos corrompam dados, seja acidentalmente ou maliciosamente. Manter a fidelidade de dados durante as instâncias de erro do aplicativo ou do usuário requer técnicas mais avançadas, como copiar os dados para um local de armazenamento secundário com um registro de auditoria.

A tabela a seguir fornece uma visão geral do escopo da recuperação da conta de armazenamento, dependendo da estratégia de replicação.

| |LRS|ZRS|GRS|RA - GRS|
|---|---|---|---|---|
|Gerenciador de recursos do Azure conta de armazenamento|Sim|Sim|Sim|Sim|
|Clássico da conta de armazenamento|Sim|Sim|Sim|Sim|

Reúna as seguintes informações e faça uma solicitação de suporte com o Suporte do Microsoft:

* Nome da conta de armazenamento
* Data de exclusão
* Região da conta de armazenamento
* Como a conta de armazenamento foi excluída?
* Que método excluiu a conta de armazenamento? (Portal, PowerShell, etc.)

Pontos Importantes

* Geralmente, pode levar até 15 dias a partir do momento da exclusão para que o serviço de armazenamento realize a coleta de lixo, de modo que a recuperação das contas de armazenamento pode não ser recuperada com um SLA.
* O Suporte microsoft tentará recuperar o Contêiner/Conta com o melhor esforço e não pode garantir a recuperação.

> [!NOTE]
> A recuperação pode não ser bem sucedida se a conta tiver sido recriada. Se você já recriou a conta, você deve excluí-la primeiro antes que a recuperação possa ser tentada.
