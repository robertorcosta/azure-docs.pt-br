---
title: incluir arquivo
description: incluir arquivo
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 9333bb36971fc28a23a443e50d191abeef05b758
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208198"
---
Para visualizar e copiar as chaves de acesso da conta de armazenamento ou a seqüência de conexões do portal Azure:

1. Navegue até o [Portal do Azure](https://portal.azure.com).
2. Localize sua cadeia de conexão.
3. Em **Configurações,** selecione **Teclas de acesso**. Suas chaves de acesso da conta são exibidas, bem como a cadeia de conexão completa para cada chave.
4. Encontre o valor da **Chave** em **key1** e clique no botão **Copiar** para copiar a chave de conta.
5. Como alternativa, você pode copiar a cadeia de conexão inteira. Encontre o valor da **Cadeia de conexão** em **key1** e clique no botão **Copiar** para copiar a cadeia de conexão.

    ![Captura de tela mostrando como visualizar chaves de acesso no portal Azure](media/storage-view-keys-include/portal-connection-string.png)

Você pode usar qualquer chave para acessar o Azure Storage, mas em geral é uma boa prática usar a primeira chave e reservar o uso da segunda chave para quando você estiver girando as chaves.
