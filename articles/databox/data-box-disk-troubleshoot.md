---
title: Solução de problemas do Azure Data Box Disk | Microsoft Docs
description: Descreve como solucionar os problemas observados no Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: f8116ec0836623adf803991017950ddc7f960923
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67805718"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>Use logs para solucionar problemas de validação no Disco de caixa de dados do Azure

Este artigo se aplica ao Microsoft Azure Data Box Disk. O artigo descreve como usar os logs para solucionar problemas de validação que você pode ver ao implantar essa solução.

## <a name="validation-tool-log-files"></a>Arquivos de registro de ferramentas de validação

Quando você valida os dados nos discos usando a [ferramenta de validação,](data-box-disk-deploy-copy-data.md#validate-data)um *erro.xml* é gerado para registrar quaisquer erros. O arquivo de registro `Drive:\DataBoxDiskImport\logs` está localizado na pasta da unidade. Um link para o registro de erro é fornecido quando você executa a validação.

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

Se você executar várias sessões para validação, então um registro de erro será gerado por sessão.

- Aqui está uma amostra do registro de erro `PageBlob` quando os dados carregados na pasta não estão alinhados com 512 bytes. Qualquer dado carregado no PageBlob deve estar alinhado com 512 bytes, por exemplo, um VHD ou VHDX. Os erros neste arquivo `<Errors>` estão no `<Warnings>`e avisos em .

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
            <SessionId>session#1</SessionId>
            <ItemType>PageBlob</ItemType>
            <SourceDirectory>D:\Dataset\TestDirectory</SourceDirectory>
            <Errors>
                <Error Code="Not512Aligned">
                    <Description>The file is not 512 bytes aligned.</Description>
                    <List>
                        <File Path="\Practice\myScript.ps1" />
                    </List>
                    <Count>1</Count>
                </Error>
            </Errors>
            <Warnings />
        </ErrorLog>
    ```

- Aqui está uma amostra do registro de erro quando o nome do contêiner não é válido. A pasta em `BlockBlob`que `PageBlob`você `AzureFile` cria em , ou pastas no disco torna-se um contêiner em sua conta de armazenamento Azure. O nome do contêiner deve seguir as [convenções de nomeação do Azure.](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)

    ```xml
        <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
          <SessionId>bbsession</SessionId>
          <ItemType>BlockBlob</ItemType>
          <SourceDirectory>E:\BlockBlob</SourceDirectory>
          <Errors>
            <Error Code="InvalidShareContainerFormat">
              <List>
                <Container Name="Azu-reFile" />
                <Container Name="bbcont ainer1" />
              </List>
              <Count>2</Count>
            </Error>
          </Errors>
          <Warnings />
    </ErrorLog>
    ```

## <a name="validation-tool-errors"></a>Erros de ferramenta de validação

Os erros contidos no *error.xml* com as ações recomendadas correspondentes são resumidos na tabela a seguir.

| Código do erro| Descrição                       | Ações recomendadas               |
|------------|--------------------------|-----------------------------------|
| `None` | Validou com sucesso os dados. | Nenhuma ação é necessária. |
| `InvalidXmlCharsInPath` |Não foi possível criar um arquivo manifesto, pois o caminho do arquivo tem caracteres que não são válidos. | Remova esses caracteres para prosseguir.  |
| `OpenFileForReadFailed`| Não foi possível processar o arquivo. Isso pode ser devido a um problema de acesso ou corrupção no sistema de arquivos.|Não foi possível ler o arquivo devido a um erro. Os detalhes do erro estão na exceção. |
| `Not512Aligned` | Este arquivo não está em um formato válido para pasta PageBlob.| Apenas o upload de dados que são `PageBlob` 512 bytes alinhados à pasta. Remova o arquivo da pasta PageBlob ou mova-o para a pasta BlockBlob. Tente novamente a validação.|
| `InvalidBlobPath` | O caminho do arquivo não mapeia um caminho blob válido na nuvem, de acordo com as convenções de nomeação do Azure Blob.|Siga as diretrizes de nomeação do Azure para renomear o caminho do arquivo. |
| `EnumerationError` | Não foi possível enumerar o arquivo para validação. |Pode haver várias razões para este erro. Uma razão mais provável é o acesso ao arquivo. |
| `ShareSizeExceeded` | Este arquivo fez com que o tamanho do compartilhamento de arquivos do Azure excedesse o limite do Azure de 5 TB.|Reduza o tamanho dos dados no compartilhamento para que ele esteja em conformidade com os [limites de tamanho do objeto Azure](data-box-disk-limits.md#azure-object-size-limits). Tente novamente a validação. |
| `AzureFileSizeExceeded` | O tamanho do arquivo excede os limites de tamanho do Arquivo Azure.| Reduza o tamanho do arquivo ou dos dados para que ele esteja em conformidade com os [limites de tamanho do objeto Azure](data-box-disk-limits.md#azure-object-size-limits). Tente novamente a validação.|
| `BlockBlobSizeExceeded` | O tamanho do arquivo excede os limites de tamanho do Azure Block Blob. | Reduza o tamanho do arquivo ou dos dados para que ele esteja em conformidade com os [limites de tamanho do objeto Azure](data-box-disk-limits.md#azure-object-size-limits). Tente novamente a validação. |
| `ManagedDiskSizeExceeded` | O tamanho do arquivo excede os limites de tamanho do disco gerenciado do Azure. | Reduza o tamanho do arquivo ou dos dados para que ele esteja em conformidade com os [limites de tamanho do objeto Azure](data-box-disk-limits.md#azure-object-size-limits). Tente novamente a validação. |
| `PageBlobSizeExceeded` | O tamanho do arquivo excede os limites de tamanho do disco gerenciado do Azure. | Reduza o tamanho do arquivo ou dos dados para que ele esteja em conformidade com os [limites de tamanho do objeto Azure](data-box-disk-limits.md#azure-object-size-limits). Tente novamente a validação. |
| `InvalidShareContainerFormat`  |Os nomes dos diretórios não estão de acordo com as convenções de nomeação do Azure para contêineres ou ações.         |A primeira pasta criada as pastas pré-existentes no disco torna-se um contêiner em sua conta de armazenamento. Este nome de ação ou contêiner não está de acordo com as convenções de nomeação do Azure. Renomeie o arquivo para que ele esteja em conformidade com as [convenções de nomeação do Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Tente novamente a validação.   |
| `InvalidBlobNameFormat` | O caminho do arquivo não mapeia um caminho blob válido na nuvem, de acordo com as convenções de nomeação do Azure Blob.|Renomeie o arquivo para que ele esteja em conformidade com as [convenções de nomeação do Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Tente novamente a validação. |
| `InvalidFileNameFormat` | O caminho do arquivo não mapeia um caminho de arquivo válido na nuvem, de acordo com as convenções de nomeação do Arquivo Azure. |Renomeie o arquivo para que ele esteja em conformidade com as [convenções de nomeação do Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Tente novamente a validação. |
| `InvalidDiskNameFormat` | O caminho do arquivo não mapeia um nome de disco válido na nuvem, de acordo com as convenções de nomeação do Disco Gerenciado do Azure. |Renomeie o arquivo para que ele esteja em conformidade com as [convenções de nomeação do Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Tente novamente a validação.       |
| `NotPartOfFileShare` | O caminho de upload para arquivos não é válido. Faça upload dos arquivos para uma pasta no Azure Files.   | Remova os arquivos em erro e carregue esses arquivos para uma pasta pré-criada. Tente novamente a validação. |
| `NonVhdFileNotSupportedForManagedDisk` | Um arquivo não-VHD não pode ser carregado como um disco gerenciado. |Remova os arquivos não-VHD da `ManagedDisk` pasta, pois estes `PageBlob` não são suportados ou mova esses arquivos para uma pasta. Tente novamente a validação. |


## <a name="next-steps"></a>Próximas etapas

- Solucionar [problemas de upload de dados](data-box-disk-troubleshoot-upload.md).
