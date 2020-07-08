---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 11/05/2019
ms.author: alkohli
ms.openlocfilehash: 16647b6a13e64073ab570d36a8a380d0e36bd855
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "73799613"
---
- Não copie arquivos diretamente para nenhum dos compartilhamentos precriados. Você precisa criar uma pasta sob o compartilhamento e, em seguida, copiar os arquivos nessa pasta.
- Uma pasta no *StorageAccount_BlockBlob* e no *StorageAccount_PageBlob* é um contêiner. Por exemplo, os contêineres são criados como *StorageAccount_BlockBlob/container* e *StorageAccount_PageBlob/container*.
- Cada pasta criada diretamente em *StorageAccount_AzureFiles* é convertida em um compartilhamento de arquivos do Azure.
- Se você tiver um objeto existente do Azure (como um blob ou arquivo) na nuvem com o mesmo nome que o objeto que está sendo copiado, o Data Box substituirá o arquivo na nuvem.
- Todos os arquivos gravados nos compartilhamentos *StorageAccount_BlockBlob* e *StorageAccount_PageBlob* são carregados como um blob de blocos e um blob de páginas, respectivamente.
- O armazenamento de BLOBs do Azure não dá suporte a diretórios. Se você criar uma pasta na pasta *StorageAccount_BlockBlob*, as pastas virtuais serão criadas no nome do blob. Para os Arquivos do Azure, a estrutura de diretório real é mantida.
- Qualquer hierarquia de diretório vazia (sem nenhum arquivo) criada em pastas *StorageAccount_BlockBlob* e *StorageAccount_PageBlob* não é carregada.
- Se há erros ao carregar dados no Azure, um log de erros é criado na conta de armazenamento de destino. O caminho para esse log de erros estará disponível quando o upload for concluído e você poderá examinar o log para tomar uma ação corretiva. Não exclua dados da fonte sem verificar os dados carregados.
- Os metadados de arquivo e as permissões NTFS não são preservados quando os dados são carregados nos arquivos do Azure. Por exemplo, o *último atributo modificado* dos arquivos não será mantido quando os dados forem copiados.
