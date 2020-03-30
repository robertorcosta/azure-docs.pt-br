---
title: Soluciona problemas em sua Caixa de Dados Azure, Azure Data Box Heavy
description: Descreve como solucionar problemas vistos na Caixa de Dados Azure e no Azure Data Box Heavy ao copiar dados para esses dispositivos.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 51935516e26f263e44a926bf9b7d7ec24a5eeb9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560058"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>Solucionando problemas relacionados à Caixa de Dados Do Azure e à Caixa de Dados Do Azure Heavy

Este artigo detalha informações sobre como solucionar problemas que você pode ver ao usar a Caixa de Dados Azure ou a Azure Data Box Heavy. O artigo inclui a lista de possíveis erros vistos quando os dados são copiados para a Data Box ou quando os dados são carregados da Data Box.

## <a name="error-classes"></a>Classes de erro

Os erros na Data Box e data box heavy são resumidos da seguinte forma:

| Categoria de erro*        | Descrição        | Ação recomendada    |
|----------------------------------------------|---------|--------------------------------------|
| Contêiner ou compartilhar nomes | Os nomes do contêiner ou do compartilhamento não seguem as regras de nomeação do Azure.  |Baixe as listas de erros. <br> Renomeie os contêineres ou ações. [Saiba mais](#container-or-share-name-errors).  |
| Limite de tamanho de contêiner ou de compartilhamento | O total de dados em contêineres ou ações excede o limite do Azure.   |Baixe as listas de erros. <br> Reduza os dados gerais no contêiner ou compartilhamento. [Saiba mais](#container-or-share-size-limit-errors).|
| Limite de tamanho de objeto ou arquivo | O objeto ou arquivos em contêineres ou compartilhamentos excede o limite do Azure.|Baixe as listas de erros. <br> Reduza o tamanho do arquivo no recipiente ou compartilhamento. [Saiba mais](#object-or-file-size-limit-errors). |    
| Tipo de dados ou arquivos | O formato de dados ou o tipo de arquivo não são suportados. |Baixe as listas de erros. <br> Para blobs de página ou discos gerenciados, certifique-se de que os dados estejam alinhados e copiados para as pastas pré-criadas. [Saiba mais](#data-or-file-type-errors). |
| Erros de blob ou arquivo não críticos  | Os nomes blob ou file não seguem as regras de nomeação do Azure ou o tipo de arquivo não é suportado. | Esses arquivos ou bolhas podem não ser copiados ou os nomes podem ser alterados. [Aprenda a corrigir esses erros](#non-critical-blob-or-file-errors). |

\*As quatro primeiras categorias de erro são erros críticos e devem ser corrigidas antes que você possa prosseguir a preparação para o navio.


## <a name="container-or-share-name-errors"></a>Erros de nome de contêiner ou compartilhamento

Estes são erros relacionados ao contêiner e nomes compartilhados.

### <a name="error_container_or_share_name_length"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**Descrição do erro:** O nome do contêiner ou de compartilhamento deve ter entre 3 e 63 caracteres. 

**Resolução sugerida:** A pasta o compartilhamento Data Box ou Data Box Heavy (SMB/NFS) para a qual você copiou dados torna-se um contêiner Azure em sua conta de armazenamento. 

- Na página **Conectar e copiar** a ui local do dispositivo, baixe e revise os arquivos de erro para identificar os nomes das pastas com problemas.
- Altere o nome da pasta no compartilhamento Data Box ou Data Box Heavy para ter certeza de que:

    - O nome tem entre 3 e 63 caracteres.
    - Os nomes só podem ter letras, números e hífens.
    - Os nomes não podem começar ou terminar com hífens.
    - Os nomes não podem ter hífens consecutivos.
    - Exemplos de nomes `my-folder-1`válidos: ,`my-really-extra-long-folder-111`
    - Exemplos de nomes que não `my-folder_1`são `my` `--myfolder`válidos: , , `myfolder--``myfolder!`

    Para obter mais informações, consulte as convenções de nomeação do Azure para [nomes de contêineres](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) e [nomes de compartilhamento](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).


### <a name="error_container_or_share_name_alpha_numeric_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**Descrição do erro:** O nome do contêiner ou de compartilhamento deve consistir apenas em letras, números ou hífens.

**Resolução sugerida:** A pasta o compartilhamento Data Box ou Data Box Heavy (SMB/NFS) para a qual você copiou dados torna-se um contêiner Azure em sua conta de armazenamento. 

- Na página **Conectar e copiar** a ui local do dispositivo, baixe e revise os arquivos de erro para identificar os nomes das pastas com problemas.
- Altere o nome da pasta no compartilhamento Data Box ou Data Box Heavy para ter certeza de que:

    - O nome tem entre 3 e 63 caracteres.
    - Os nomes só podem ter letras, números e hífens.
    - Os nomes não podem começar ou terminar com hífens.
    - Os nomes não podem ter hífens consecutivos.
    - Exemplos de nomes `my-folder-1`válidos: ,`my-really-extra-long-folder-111`
    - Exemplos de nomes que não `my-folder_1`são `my` `--myfolder`válidos: , , `myfolder--``myfolder!`

    Para obter mais informações, consulte as convenções de nomeação do Azure para [nomes de contêineres](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) e [nomes de compartilhamento](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

### <a name="error_container_or_share_name_improper_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**Descrição do erro:** Os nomes dos contêineres e nomes compartilhados não podem começar ou terminar com hífens e não podem ter hífens consecutivos.

**Resolução sugerida:** A pasta o compartilhamento Data Box ou Data Box Heavy (SMB/NFS) para a qual você copiou dados torna-se um contêiner Azure em sua conta de armazenamento. 

- Na página **Conectar e copiar** a ui local do dispositivo, baixe e revise os arquivos de erro para identificar os nomes das pastas com problemas.
- Altere o nome da pasta no compartilhamento Data Box ou Data Box Heavy para ter certeza de que:

    - O nome tem entre 3 e 63 caracteres.
    - Os nomes só podem ter letras, números e hífens.
    - Os nomes não podem começar ou terminar com hífens.
    - Os nomes não podem ter hífens consecutivos.
    - Exemplos de nomes `my-folder-1`válidos: ,`my-really-extra-long-folder-111`
    - Exemplos de nomes que não `my-folder_1`são `my` `--myfolder`válidos: , , `myfolder--``myfolder!`

    Para obter mais informações, consulte as convenções de nomeação do Azure para [nomes de contêineres](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) e [nomes de compartilhamento](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

## <a name="container-or-share-size-limit-errors"></a>Erros de limite de tamanho de contêiner ou de compartilhamento

Estes são erros relacionados a dados que excedam o tamanho dos dados permitidos em um contêiner ou um compartilhamento.

### <a name="error_container_or_share_capacity_exceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**Descrição do erro:** O compartilhamento de arquivos do Azure limita uma ação a 5 TB de dados. Esse limite excedeu para algumas ações.

**Resolução sugerida:** Na página **Conectar e copiar** a ui web local, baixe e revise os arquivos de erro.

Identifique as pastas que têm esse problema a partir dos registros de erro e certifique-se de que os arquivos nessa pasta estão abaixo de 5 TB.


## <a name="object-or-file-size-limit-errors"></a>Erros de limite de tamanho de objeto ou arquivo

Estes são erros relacionados a dados que excedam o tamanho máximo do objeto ou do arquivo permitido no Azure. 

### <a name="error_blob_or_file_size_limit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**Descrição do erro:** O tamanho do arquivo excede o tamanho máximo do arquivo para upload.

**Resolução sugerida:** A bolha ou os tamanhos dos arquivos excedem o limite máximo permitido para upload.

- Na página **Conectar e copiar** a ui web local, baixe e revise os arquivos de erro.
- Certifique-se de que os tamanhos de blob e arquivo não excedam os limites de tamanho do objeto Azure.

## <a name="data-or-file-type-errors"></a>Erros de dados ou tipos de arquivo

Estes são erros relacionados ao tipo de arquivo ou tipo de dados não suportado encontrado no contêiner ou compartilhamento. 

### <a name="error_blob_or_file_size_alignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**Descrição do erro:** A bolha ou arquivo está incorretamente alinhado.

**Resolução sugerida:** O compartilhamento de blob de página na Data Box ou Data Box Heavy só suporta arquivos que estão 512 bytes alinhados (por exemplo, VHD/VHDX). Todos os dados copiados para o compartilhamento de blob de página são carregados no Azure como blobs de página.

Remova quaisquer dados não-VHD/VHDX do compartilhamento de blob da página. Você pode usar compartilhamentos para arquivos block blob ou Azure para dados genéricos.

Para obter mais informações, consulte [Visão geral das bolhas de página](../storage/blobs/storage-blob-pageblob-overview.md).

### <a name="error_blob_or_file_type_unsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**Descrição do erro:** Um tipo de arquivo sem suporte está presente em um compartilhamento de disco gerenciado. Somente vHDs fixos são permitidos.

**Resolução sugerida:**

- Certifique-se de que você só carrega os VHDs fixos para criar discos gerenciados.
- Os arquivos VHDX ou VHDs **dinâmicos** e **differencing** não são suportados.

### <a name="error_directory_disallowed_for_type"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**Descrição do erro:** Um diretório não é permitido em nenhuma das pastas pré-existentes para os discos gerenciados. Somente VHDs fixos são permitidos nessas pastas.

**Resolução sugerida:** Para discos gerenciados, dentro de cada compartilhamento, são criadas as três pastas a seguir que correspondem a contêineres em sua conta de armazenamento: SSD Premium, HDD padrão e SSD padrão. Essas pastas correspondem ao nível de desempenho do disco gerenciado.

- Certifique-se de copiar seus dados de blob de página (VHDs) em uma dessas pastas existentes.
- Uma pasta ou diretório não é permitido nessas pastas existentes. Remova todas as pastas que você criou dentro das pastas pré-existentes.

Para obter mais informações, consulte [Copiar para discos gerenciados](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).

### <a name="reparse_point_error"></a>REPARSE_POINT_ERROR

**Descrição do erro:** Links simbólicos não são permitidos no Linux. 

**Resolução sugerida:** Os links simbólicos geralmente são links, tubos e outros arquivos desse tipo. Remova os links ou resolva os links e copie os dados.


## <a name="non-critical-blob-or-file-errors"></a>Erros de blob ou arquivo não críticos

Todos os erros não críticos relacionados a nomes de blobs, arquivos ou contêineres que são vistos durante a cópia de dados são resumidos na seção a seguir. Se esses erros estiverem presentes, os nomes serão modificados para se adequarem às convenções de nomeação do Azure. O status correspondente do pedido para upload de dados será **concluído com avisos**.  

### <a name="error_blob_or_file_name_character_control"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**Descrição do erro:** Os nomes blob ou file contêm caracteres de controle não suportados.

**Resolução sugerida:** As bolhas ou os arquivos que você copiou contêm nomes com caracteres não suportados.

Na página **Conectar e copiar** a ui web local, baixe e revise os arquivos de erro.
Remova ou renomeie os arquivos para remover caracteres sem suporte.

Para obter mais informações, consulte as convenções de nomeação do Azure para [nomes de bolhas](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e nomes de [arquivos](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).

### <a name="error_blob_or_file_name_character_illegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**Descrição do erro:** A bolha ou nomes de arquivos contêm caracteres ilegais.

**Resolução sugerida:** As bolhas ou os arquivos que você copiou contêm nomes com caracteres não suportados.

Na página **Conectar e copiar** a ui web local, baixe e revise os arquivos de erro.
Remova ou renomeie os arquivos para remover caracteres sem suporte.

Para obter mais informações, consulte as convenções de nomeação do Azure para [nomes de bolhas](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e nomes de [arquivos](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="error_blob_or_file_name_ending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**Descrição do erro:** A bolha ou nomes de arquivos estão terminando com personagens ruins.

**Resolução sugerida:** As bolhas ou os arquivos que você copiou contêm nomes com caracteres não suportados.

Na página **Conectar e copiar** a ui web local, baixe e revise os arquivos de erro.
Remova ou renomeie os arquivos para remover caracteres sem suporte.

Para obter mais informações, consulte as convenções de nomeação do Azure para [nomes de bolhas](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) e nomes de [arquivos](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="error_blob_or_file_name_segment_count"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**Descrição do erro:** A bolha ou nome do arquivo contém muitos segmentos de caminho.

**Resolução sugerida:** As bolhas ou os arquivos copiados excedem o número máximo de segmentos de caminho. Um segmento de caminho é a string entre caracteres delimitadores consecutivos, por exemplo, a barra para a frente /.

- Na página **Conectar e copiar** a ui web local, baixe e revise os arquivos de erro.
- Certifique-se [de](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) que os nomes e [nomes de arquivos](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) estejam em conformidade com as convenções de nomeação do Azure.

### <a name="error_blob_or_file_name_aggregate_length"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**Descrição do erro:** A bolha ou o nome do arquivo é muito longo.

**Resolução sugerida:** A bolha ou os nomes dos arquivos excedem o comprimento máximo.

- Na página **Conectar e copiar** a ui web local, baixe e revise os arquivos de erro.
- O nome blob não deve exceder 1.024 caracteres.
- Remova ou renomeie a bolha ou arquivos para que os nomes não excedam 1024 caracteres.

Para obter mais informações, consulte as convenções de nomeação do Azure para nomes de bolhas e nomes de arquivos.

### <a name="error_blob_or_file_name_component_length"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**Descrição do erro:** Um dos segmentos na bolha ou nome do arquivo é muito longo.

**Resolução sugerida:** Um dos segmentos de caminho no blob ou nome de arquivo excede o número máximo de caracteres. Um segmento de caminho é a string entre caracteres delimitadores consecutivos, por exemplo, a barra para a frente /.

- Na página **Conectar e copiar** a ui web local, baixe e revise os arquivos de erro.
- Certifique-se [de](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) que os nomes e [nomes de arquivos](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) estejam em conformidade com as convenções de nomeação do Azure.


### <a name="error_container_or_share_name_disallowed_for_type"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**Descrição do erro:** Nomes de contêineres inadequados são especificados para compartilhamentos de disco gerenciados.

**Resolução sugerida:** Para discos gerenciados, dentro de cada compartilhamento, são criadas as seguintes pastas que correspondem a contêineres em sua conta de armazenamento: SSD Premium, HDD padrão e SSD padrão. Essas pastas correspondem ao nível de desempenho do disco gerenciado.

- Certifique-se de copiar seus dados de blob de página (VHDs) em uma dessas pastas existentes. Somente os dados desses contêineres existentes são carregados no Azure.
- Qualquer outra pasta criada no mesmo nível do SSD Premium, HDD Padrão e SSD padrão não corresponde a um nível de desempenho válido e não pode ser usada.
- Remova arquivos ou pastas criadas fora dos níveis de desempenho.

Para obter mais informações, consulte [Copiar para discos gerenciados](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).


## <a name="next-steps"></a>Próximas etapas

- Conheça os requisitos do [sistema de armazenamento Data Box Blob](data-box-system-requirements-rest.md).
