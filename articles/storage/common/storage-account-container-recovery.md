---
title: Recuperação do contêiner da conta de armazenamento
description: Recuperação do contêiner da conta de armazenamento
services: storage
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
ms.date: 10/30/2019
ms.service: storage
manager: dcscontentpm
ms.openlocfilehash: 8b9b69fb32edab54fb2df558ea292df264e00cbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562268"
---
# <a name="storage-account-container-recovery"></a>Recuperação de contêiner de Conta de Armazenamento

O Armazenamento do Azure fornece resiliência de dados por meio de réplicas automatizadas. No entanto, isso não impede que o código do aplicativo ou os usuários corrompam dados, seja acidental ou maliciosamente. Manter a fidelidade dos dados no caso de erro do usuário ou aplicativo requer técnicas mais avançadas, como copiar os dados para um local de armazenamento secundário com um log de auditoria.

## <a name="checking-azure-storage-account-type"></a>Verificando o tipo de conta do Azure Storage

1. Navegue até o [Portal do Azure](https://portal.azure.com/).

2. Localize sua cadeia de conexão.

3. Na seção **Visão geral,** verifique se há **replicação**.

   ![Imagem](media/storage-account-container-recovery/1.png)

4. Se o tipo de replicação for **GRS/RA-GRS,** a recuperação do contêiner da conta é possível sem garantia. Para todos os outros tipos de replicação, não é possível.

5. Reúna as seguintes informações e faça uma solicitação de suporte com o Microsoft Support.

   * Nome da conta de armazenamento:
   * Nome do Contêiner:
   * Tempo de exclusão:

   A tabela a seguir fornece uma visão geral do escopo da recuperação de contêineres da conta de armazenamento, dependendo da estratégia de replicação.

   |Tipo de Conteúdo|LRS|ZRS|GRS|RA - GRS| 
   |---|---|---|---|---|
   |Contêiner de armazenamento|Não|Não|Sim|Sim| 

   * Podemos tentar restaurar o contêiner da conta de armazenamento, mas sem qualquer garantia. 

## <a name="things-not-to-do-for-recover-to-be-successful"></a>Coisas que não fazer para recuperar para ser bem sucedido

* Por favor, não recrie o recipiente (com o mesmo nome).  
* Se você já recriou o contêiner, você precisa excluir o contêiner antes de apresentar uma solicitação de suporte para recuperação.

## <a name="steps-to-prevent-this-in-the-future"></a>Passos para evitar isso no futuro

1. Para evitar isso no futuro, o recurso mais recomendado para usar é [o Soft Delete](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete).

2. Também recomendamos o recurso [Snapshot.](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)
 
## <a name="next-steps"></a>Próximas etapas

Aqui estão dois códigos de amostra no recurso:

  * [Criar e gerenciar um instantâneo blob no .NET](https://docs.microsoft.com/azure/storage/storage-blob-snapshots)
  * [Usando instantâneos blob com PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)
  

