---
title: Solucionando problemas de uploadde dados usando logs
titleSuffix: Azure Data Box Disk
description: Descreve como usar os logs e solucionar problemas vistos ao carregar dados no Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: 7c14988706ef193ef5da868c55f6c4f55e7d98f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260131"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Entenda os logs para solucionar problemas de upload de dados no Disco da Caixa de Dados do Azure

Este artigo se aplica ao Microsoft Azure Data Box Disk e descreve os problemas que você vê ao enviar dados para o Azure.

## <a name="about-upload-logs"></a>Sobre logs de upload

Quando os dados são carregados para o `_error.xml` Azure no data center, e `_verbose.xml` os arquivos são gerados para cada conta de armazenamento. Esses logs são carregados para a mesma conta de armazenamento que foi usada para carregar dados. 

Ambos os logs estão no mesmo formato e contêm descrições XML dos eventos ocorridos enquanto copiam os dados do disco para a conta do Azure Storage.

O registro verbose contém informações completas sobre o status da operação de cópia para cada bolha ou arquivo, enquanto o registro de erro contém apenas as informações para blobs ou arquivos que encontraram erros durante o upload.

O registro de erro tem a mesma estrutura do registro verboso, mas filtra as operações bem sucedidas.

## <a name="download-logs"></a>baixar logs

Tome as seguintes etapas para localizar os registros de upload.

1. Se houver algum erro ao enviar os dados para o Azure, o portal exibirá um caminho para a pasta onde os registros de diagnóstico saem.

    ![Link para logs no portal](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. Vá para **Waies.**

    ![logs de erro e verbos](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

Em cada caso, você vê os registros de erro e os registros verbos. Selecione cada registro e baixe uma cópia local.

## <a name="sample-upload-logs"></a>Registros de upload de amostras

Uma amostra `_verbose.xml` do é mostrado abaixo. Neste caso, a ordem foi concluída com sucesso sem erros.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Blob Status="Completed">
    <BlobPath>$root/botetapageblob.vhd</BlobPath>
    <FilePath>\PageBlob\botetapageblob.vhd</FilePath>
    <Length>1073742336</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <PageRangeList>
      <PageRange Offset="0" Length="4194304" Status="Completed" />
      <PageRange Offset="4194304" Length="4194304" Status="Completed" />
      <PageRange Offset="8388608" Length="4194304" Status="Completed" />
      --------CUT-------------------------------------------------------
      <PageRange Offset="1061158912" Length="4194304" Status="Completed" />
      <PageRange Offset="1065353216" Length="4194304" Status="Completed" />
      <PageRange Offset="1069547520" Length="4194304" Status="Completed" />
      <PageRange Offset="1073741824" Length="512" Status="Completed" />
    </PageRangeList>
  </Blob>
  <Blob Status="Completed">
    <BlobPath>$root/botetablockblob.txt</BlobPath>
    <FilePath>\BlockBlob\botetablockblob.txt</FilePath>
    <Length>19</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <BlockList>
      <Block Offset="0" Length="19" Status="Completed" />
    </BlockList>
  </Blob>
  <File Status="Completed">
    <FileStoragePath>botetaazurefilesfolder/botetaazurefiles.txt</FileStoragePath>
    <FilePath>\AzureFile\botetaazurefilesfolder\botetaazurefiles.txt</FilePath>
    <Length>20</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <FileRangeList>
      <FileRange Offset="0" Length="20" Status="Completed" />
    </FileRangeList>
  </File>
  <Status>Completed</Status>
</DriveLog>
```

Para a mesma ordem, `_error.xml` uma amostra do é mostrado abaixo.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Summary>
    <ValidationErrors>
      <None Count="3" />
    </ValidationErrors>
    <CopyErrors>
      <None Count="3" Description="No errors encountered" />
    </CopyErrors>
  </Summary>
  <Status>Completed</Status>
</DriveLog>
```

Uma amostra `_error.xml` do é mostrado abaixo onde a ordem foi concluída com erros. 

O arquivo de erro `Summary` neste caso tem uma seção e outra seção que contém todos os erros de nível de arquivo. 

O `Summary` contém `ValidationErrors` o `CopyErrors`e o . Neste caso, 8 arquivos ou pastas foram carregados no Azure e não houve erros de validação. Quando os dados foram copiados para a conta do Azure Storage, 5 arquivos ou pastas foram carregados com sucesso. Os 3 arquivos ou pastas restantes foram renomeados de acordo com as convenções de nomeação de contêineres do Azure e, em seguida, carregados com sucesso para o Azure.

O status de `BlobStatus` nível de arquivo está no que descreve quaisquer ações tomadas para carregar as bolhas. Neste caso, três contêineres são renomeados porque as pastas para as quais os dados foram copiados não estavam de acordo com as convenções de nomeação do Azure para contêineres. Para as bolhas carregadas nesses contêineres, o novo nome do contêiner, o caminho da bolha no Azure, o caminho original do arquivo inválido e o tamanho da bolha estão incluídos.
    
```xml
 <?xml version="1.0" encoding="utf-8"?>
    <DriveLog Version="2018-10-01">
      <DriveId>18041C582D7E</DriveId>
      <Summary>
     <!--Summary for validation and data copy to Azure -->
        <ValidationErrors>
          <None Count="8" />
        </ValidationErrors>
        <CopyErrors>
          <Completed Count="5" Description="No errors encountered" />
          <ContainerRenamed Count="3" Description="Renamed the container as the original container name does not follow Azure conventions." />
        </CopyErrors>
      </Summary>
    <!--List of renamed containers with the new names, new file path in Azure, original invalid file path, and size -->
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/private.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\private.vhd</OriginalFilePath>
        <SizeInBytes>10490880</SizeInBytes>
      </Blob>
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/resource.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\resource.vhd</OriginalFilePath>
        <SizeInBytes>71528448</SizeInBytes>
      </Blob>
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/role.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\role.vhd</OriginalFilePath>
        <SizeInBytes>10490880</SizeInBytes>
      </Blob>
      <Status>CompletedWithErrors</Status>
    </DriveLog>
```

## <a name="data-upload-errors"></a>Erros de upload de dados

Os erros gerados ao enviar os dados para o Azure são resumidos na tabela a seguir.

| Código do erro | Descrição                   |
|-------------|------------------------------|
|`None` |  Concluído com sucesso.           |
|`Renamed` | Renomeada com sucesso para blob.   |
|`CompletedWithErrors` | Upload concluído com erros. Os detalhes dos arquivos em erro estão incluídos no arquivo de registro.  |
|`Corrupted`|O CRC calculado durante a ingestão de dados não corresponde ao CRC calculado durante o upload.  |  
|`StorageRequestFailed` | Falha no pedido de armazenamento do Azure.   |     
|`LeasePresent` | Este item é alugado e está sendo usado por outro usuário. |
|`StorageRequestForbidden` |Não foi possível fazer o upload devido a problemas de autenticação. |
|`ManagedDiskCreationTerminalFailure` | Não foi possível carregar como discos gerenciados. Os arquivos estão disponíveis na conta de armazenamento de encenação como blobs de página. Você pode converter manualmente blobs de página em discos gerenciados.  |
|`DiskConversionNotStartedTierInfoMissing` | Como o arquivo VHD foi copiado fora das pastas de nível pré-criadas, um disco gerenciado não foi criado. O arquivo é carregado como bolha de página para a conta de armazenamento de encenação, conforme especificado durante a criação do pedido. Você pode convertê-lo manualmente em um disco gerenciado.|
|`InvalidWorkitem` | Não foi possível carregar os dados, pois não está de acordo com a nomeação do Azure e limita as convenções.|
|`InvalidPageBlobUploadAsBlockBlob` | Carregado como blobs de bloco `databoxdisk-invalid-pb-`em um recipiente com prefixo .|
|`InvalidAzureFileUploadAsBlockBlob` | Carregado como blobs de bloco `databoxdisk-invalid-af`em um recipiente com prefixo -.|
|`InvalidManagedDiskUploadAsBlockBlob` | Carregado como blobs de bloco `databoxdisk-invalid-md`em um recipiente com prefixo -.|
|`InvalidManagedDiskUploadAsPageBlob` |Carregado como bolhas de página em `databoxdisk-invalid-md-`um recipiente com prefixo . |
|`MovedToOverflowShare` |Arquivos enviados para uma nova parte, pois o tamanho original da ação excedeu o limite máximo de tamanho do Azure. O novo nome de compartilhamento de arquivos `-2`tem o nome original sufixo com .   |
|`MovedToDefaultAzureShare` |Arquivos enviados que não eram parte de qualquer pasta para um compartilhamento padrão. O nome da `databox-`ação começa com . |
|`ContainerRenamed` |O contêiner para esses arquivos não estava de acordo com as convenções de nomeação do Azure e foi renomeado. O novo nome `databox-` começa com e é sufixo com o hash SHA1 do nome original |
|`ShareRenamed` |A parte desses arquivos não estava de acordo com as convenções de nomeação do Azure e foi renomeada. O novo nome `databox-` começa com e é sufixo com o hash SHA1 do nome original. |
|`BlobRenamed` |Esses arquivos não estavam de acordo com as convenções de nomeação do Azure e foram renomeados. Verifique `BlobPath` o campo para o novo nome. |
|`FileRenamed` |Esses arquivos não estavam de acordo com as convenções de nomeação do Azure e foram renomeados. Verifique `FileStoragePath` o campo para o novo nome. |
|`DiskRenamed` |Esses arquivos não estavam de acordo com as convenções de nomeação do Azure e foram renomeados. Verifique `BlobPath` o campo para o novo nome. |


## <a name="next-steps"></a>Próximas etapas

- [Abra um bilhete de suporte para problemas de disco de caixa de dados](data-box-disk-contact-microsoft-support.md).
