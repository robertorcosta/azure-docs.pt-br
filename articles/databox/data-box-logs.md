---
title: Acompanhe e registre a Caixa de Dados do Azure, Azure Data Box Eventos pesados| Microsoft Docs
description: Descreve como rastrear e registrar eventos nas várias etapas da sua caixa de dados Azure e da caixa de dados do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 08/08/2019
ms.author: alkohli
ms.openlocfilehash: 74d38af4a64a184b26bd6ba1105db0d2530d8ba6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676401"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy"></a>Rastreamento e registro de eventos para sua Caixa de Dados Azure e Azure Data Box Heavy

Uma caixa de dados ou caixa de dados A ordem pesada passa pelas seguintes etapas: ordem, configuração, cópia de dados, retorno, upload para o Azure e verificação e eliminação de dados. Correspondente a cada etapa da ordem, você pode tomar várias ações para controlar o acesso à ordem, auditar os eventos, acompanhar a ordem e interpretar os vários logs gerados.

A tabela a seguir mostra um resumo das etapas de ordem Data Box ou Data Box Heavy e as ferramentas disponíveis para rastrear e auditar o pedido durante cada etapa.

| Estágio de pedido da Caixa de Dados       | Ferramenta para rastrear e auditar                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| Criar pedido               | [Configure o controle de acesso no pedido via RBAC](#set-up-access-control-on-the-order)                                                    |
| Pedido processado            | [Acompanhe a ordem](#track-the-order) através <ul><li> Portal do Azure </li><li> Site da transportadora de transporte </li><li>Notificações por email</ul> |
| Configurar o dispositivo              | As credenciais do dispositivo [acessam logs de atividade](#query-activity-logs-during-setup)                                              |
| Cópia de dados para dispositivo        | [Exibir *arquivos error.xml* ](#view-error-log-during-data-copy) para cópia de dados                                                             |
| Preparar para o envio            | [Inspecione os arquivos BOM](#inspect-bom-during-prepare-to-ship) ou os arquivos manifestos no dispositivo                                      |
| Upload de dados para o Azure       | [Revisar registros de cópia](#review-copy-log-during-upload-to-azure) para erros durante o upload de dados no data center do Azure                         |
| Eliminação de dados do dispositivo   | [Ver cadeia de registros de custódia,](#get-chain-of-custody-logs-after-data-erasure) incluindo registros de auditoria e histórico de pedidos                |

Este artigo descreve em detalhes os vários mecanismos ou ferramentas disponíveis para rastrear e auditar data box ou data box ordem pesada. As informações deste artigo se aplicam a ambos, Data Box e Data Box Heavy. Nas seções subseqüentes, quaisquer referências à Data Box também se aplicam ao Data Box Heavy.

## <a name="set-up-access-control-on-the-order"></a>Configure o controle de acesso na ordem

Você pode controlar quem pode acessar seu pedido quando o pedido é criado pela primeira vez. Configure funções rbac (Role-based Access Control, controle de acesso baseado em função) em vários escopos para controlar o acesso à ordem data box. Uma função RBAC determina o tipo de acesso – leitura-gravação, leitura-somente, leitura-gravação para um subconjunto de operações.

As duas funções que podem ser definidas para o serviço Azure Data Box são:

- **Data Box Reader** - tem acesso somente à leitura a um(s) pedido, conforme definido pelo escopo. Eles só podem ver detalhes de uma ordem. Eles não podem acessar quaisquer outros detalhes relacionados a contas de armazenamento ou editar os detalhes do pedido, como endereço e assim por diante.
- **Data Box Contributor** - só pode criar um pedido de transferência de dados para uma determinada conta de armazenamento *se eles já tiverem acesso à gravação de uma conta de armazenamento*. Se eles não tiverem acesso a uma conta de armazenamento, eles não podem sequer criar um pedido de Data Box para copiar dados para a conta. Essa função não define permissões relacionadas à conta de armazenamento nem concede acesso a contas de armazenamento.  

Para restringir o acesso a uma ordem, você pode:

- Atribua um papel em um nível de ordem. O usuário só tem essas permissões definidas pelas funções para interagir apenas com essa ordem específica da Data Box e nada mais.
- Atribuir uma função no nível de grupo de recursos, o usuário tem acesso a todas as ordens da Data Box dentro de um grupo de recursos.

Para obter mais informações sobre o uso sugerido do RBAC, consulte [As melhores práticas para o Azure RBAC](../role-based-access-control/best-practices.md).

## <a name="track-the-order"></a>Acompanhar o pedido

Você pode acompanhar seu pedido através do portal Azure e através do site da transportadora de transporte. Os seguintes mecanismos estão em vigor para rastrear a ordem da Caixa de dados a qualquer momento:

- Para rastrear o pedido quando o dispositivo estiver no datacenter do Azure ou em suas instalações, acesse o **pedido da Caixa de Dados > Visão Geral** no portal Azure.

    ![Exibir status do pedido e rastreamento não](media/data-box-logs/overview-view-status-1.png)

- Para acompanhar o pedido enquanto o dispositivo estiver em trânsito, acesse o site da operadora regional, por exemplo, site da UPS nos EUA. Forneça o número de rastreamento associado ao seu pedido.
- A Data Box também envia notificações de e-mail sempre que o status do pedido muda com base nos e-mails fornecidos quando o pedido foi criado. Para obter uma lista de todos os status de pedido da Caixa de dados, consulte [Exibir status do pedido](data-box-portal-admin.md#view-order-status). Para alterar as configurações de notificação associadas ao pedido, consulte [Editar detalhes de notificação](data-box-portal-admin.md#edit-notification-details).

## <a name="query-activity-logs-during-setup"></a>Registros de atividade de consulta durante a configuração

- Sua Data Box chega em suas instalações em um estado fechado. Você pode usar as credenciais do dispositivo disponíveis no portal Azure para o seu pedido.  

    Quando uma Caixa de Dados é configurada, você pode precisar saber quem todos acessaram as credenciais do dispositivo. Para descobrir quem acessou a lâmina de credenciais do **dispositivo,** você pode consultar os registros de atividade.  Qualquer ação que envolva acessar **detalhes do dispositivo >** `ListCredentials` a lâmina Credenciais é registrada nos registros de atividades como ação.

    ![Consultar logs de atividade](media/data-box-logs/query-activity-log-1.png)

- Cada sinal na Data Box é registrado em tempo real. No entanto, essas informações só estão disponíveis nos registros de [auditoria](#audit-logs) depois que o pedido for concluído com sucesso.

## <a name="view-error-log-during-data-copy"></a>Exibir registro de erro durante a cópia de dados

Durante a cópia de dados para Data Box ou Data Box Heavy, um arquivo de erro é gerado se houver algum problema com os dados sendo copiados.

### <a name="errorxml-file"></a>Arquivo Error.xml

Certifique-se de que os trabalhos de cópia terminaram sem erros. Se houver erros durante o processo de cópia, faça o download dos logs na página **Conectar e copiar**.

- Se você copiou um arquivo que não é 512 bytes alinhados a uma pasta de disco gerenciada em sua Data Box, o arquivo não será carregado como bolha de página para sua conta de armazenamento de estágio. Você verá um erro nos logs. Remova o arquivo e copiar um arquivo que tenha 512 bytes alinhados.
- Se você copiou um VHDX, ou um VHD dinâmico, ou um VHD diferenciado (esses arquivos não são suportados), você verá um erro nos logs.

Aqui está uma amostra do *error.xml* para diferentes erros ao copiar em discos gerenciados.

```xml
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\differencing-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\dynamic-vhd-022019.vhd</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidefixedvhdx-022019.vhdx</file>
<file error="ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED">\StandardHDD\testvhds\insidediffvhd-022019.vhd</file>
```

Aqui está uma amostra do *error.xml* para diferentes erros ao copiar para blobs de página.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File100Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File786Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File513Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File10Bytes</file>
<file error="ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT">\PageBlob512NotAligned\File500Bytes</file>
```


Aqui está uma amostra do *error.xml* para diferentes erros ao copiar para bloquear blobs.

```xml
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ni3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMy3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL">\InvalidUnicodeFiles\Ã.txt</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OS3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMi3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwNC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5OC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTMwMC3vv70=</file>
<file error="ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL" name_encoding="Base64">XEludmFsaWRVbmljb2RlRmlsZXNcU3BjQ2hhci01NTI5Ny3vv70=</file>
```

Aqui está uma amostra do *error.xml* para diferentes erros ao copiar para arquivos do Azure.

```xml
<file error="ERROR_BLOB_OR_FILE_SIZE_LIMIT">\AzFileMorethan1TB\AzFile1.2TB</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\testdirectory-~!@#$%^&amp;()_+{}</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\55555555--4444--3333--2222--111111111111</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\-startingwith-hyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\11111111-_2222-_3333-_4444-_555555555555</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH">\test--doublehyphen</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\ab</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\invalid dns name</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\test__doubleunderscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\morethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortestingmorethan63charactersfortesting</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\_startingwith_underscore</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_LENGTH">\1</file>
<file error="ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH">\Starting with Capital</file>
```

Em cada um dos casos acima, resolva os erros antes de prosseguir para a próxima etapa. Para obter mais informações sobre os erros recebidos durante a cópia de dados para data box via protocolos SMB ou NFS, vá para [Problemas de Caixa de Dados e Data Box Problemas pesados](data-box-troubleshoot.md). Para obter informações sobre erros recebidos durante a cópia de dados para data box via REST, vá para problemas de [armazenamento blob da caixa de](data-box-troubleshoot-rest.md)dados de solução de problemas .

## <a name="inspect-bom-during-prepare-to-ship"></a>Inspecione BOM durante a preparação para o navio

Durante a preparação para o navio, uma lista de arquivos conhecidos como A Declaração de Materiais (BOM) ou arquivo manifesto é criada.

- Use este arquivo para verificar os nomes reais e o número de arquivos que foram copiados para a Data Box.
- Use este arquivo para verificar os tamanhos reais dos arquivos.
- Verifique se o *crc64* corresponde a uma seqüência não-zero. <!--A null value for crc64 indicates that there was a reparse point error)-->

Para obter mais informações sobre os erros recebidos durante o preparo para o navio, vá para [Problemas de Caixa de Dados e Data Box Problemas pesados](data-box-troubleshoot.md).

### <a name="bom-or-manifest-file"></a>BOM ou arquivo manifesto

O arquivo BOM ou manifesto contém a lista de todos os arquivos copiados para o dispositivo Data Box. O arquivo BOM tem nomes de arquivo e os tamanhos correspondentes, bem como a soma de verificação. Um arquivo BOM separado é criado para os blobs de bloco, blobs de página, Arquivos Azure, para cópia através das APIs REST e para a cópia para discos gerenciados na Data Box. Você pode baixar os arquivos BOM da ui web local do dispositivo durante o preparo para enviar.

Esses arquivos também residem no dispositivo Data Box e são carregados na conta de armazenamento associada no data center do Azure.

### <a name="bom-file-format"></a>Formato de arquivo BOM

Bom ou arquivo manifesto tem o seguinte formato geral:

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

Aqui está uma amostra de um manifesto gerado quando os dados foram copiados para o compartilhamento de blob de bloco na Caixa de Dados.

```
<file size="10923" crc64="0x51c78833c90e4e3f">\databox\media\data-box-deploy-copy-data\connect-shares-file-explorer1.png</file>
<file size="15308" crc64="0x091a8b2c7a3bcf0a">\databox\media\data-box-deploy-copy-data\get-share-credentials2.png</file>
<file size="53486" crc64="0x053da912fb45675f">\databox\media\data-box-deploy-copy-data\nfs-client-access.png</file>
<file size="6093" crc64="0xadb61d0d7c6d4deb">\databox\data-box-cable-options.md</file>
<file size="6499" crc64="0x080add29add367d9">\databox\data-box-deploy-copy-data-via-nfs.md</file>
<file size="11089" crc64="0xc3ce6b13a4fe3001">\databox\data-box-deploy-copy-data-via-rest.md</file>
<file size="7749" crc64="0xd2e346a4588e307a">\databox\data-box-deploy-ordered.md</file>
<file size="14275" crc64="0x260296e5d1b1608a">\databox\data-box-deploy-copy-data.md</file>
<file size="4077" crc64="0x2bb0a170225bceec">\databox\data-box-deploy-picked-up.md</file>
<file size="15447" crc64="0xcec0ca8527720b3c">\databox\data-box-portal-admin.md</file>
<file size="9126" crc64="0x820856b5a54321ad">\databox\data-box-overview.md</file>
<file size="10963" crc64="0x5e9a14f9f4784fd8">\databox\data-box-safety.md</file>
<file size="5941" crc64="0x8631d62fbc038760">\databox\data-box-security.md</file>
<file size="12536" crc64="0x8c8ff93e73d665ec">\databox\data-box-system-requirements-rest.md</file>
<file size="3220" crc64="0x7257a263c434839a">\databox\data-box-system-requirements.md</file>
```

Os arquivos BOM ou manifesto também são copiados para a conta de armazenamento do Azure. Você pode usar os arquivos BOM ou manifesto para verificar se os arquivos carregados no Azure correspondem aos dados que foram copiados para a Data Box.

## <a name="review-copy-log-during-upload-to-azure"></a>Revisar o registro de cópia durante o upload para o Azure

Durante o upload de dados para o Azure, um registro de cópia é criado.

### <a name="copy-log"></a>Registro de cópia

Para cada pedido processado, o serviço Data Box cria o login de cópia na conta de armazenamento associada. O registro de cópia tem o número total de arquivos que foram carregados e o número de arquivos que foram errantes durante a cópia de dados da Data Box para sua conta de armazenamento Do Zure.

Um cálculo de Verificação de Redundância Cíclica (CRC) é feito durante o upload para o Azure. Os CRCs da cópia de dados e após o upload de dados são comparados. Uma incompatibilidade de CRC indica que os arquivos correspondentes falharam em carregar.

Por padrão, os logs são gravados em um contêiner denominado `copylog`. Os logs são armazenados com a seguinte convenção de nomeação:

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

O caminho do registro de cópia também é exibido na **lâmina Visão Geral** para o portal.

![Caminho para copiar login na lâmina visão geral quando concluído](media/data-box-logs/copy-log-path-1.png)

### <a name="upload-completed-successfully"></a>Upload concluído com sucesso 

A amostra a seguir descreve o formato geral de um registro de cópia para um upload da Data Box que foi concluído com sucesso:

```
<?xml version="1.0"?>
-<CopyLog Summary="Summary">
<Status>Succeeded</Status>
<TotalFiles>45</TotalFiles>
<FilesErrored>0</FilesErrored>
</CopyLog>
```

### <a name="upload-completed-with-errors"></a>Upload concluído com erros 

O upload para o Azure também pode ser concluído com erros.

![Caminho para copiar a lâmina de visão geral do login quando concluído com erros](media/data-box-logs/copy-log-path-2.png)

Aqui está um exemplo de um registro de cópia onde o upload concluído com erros:

```xml
<ErroredEntity Path="iso\samsungssd.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="iso\iSCSI_Software_Target_33.iso">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>409</ErrorCode>
  <ErrorMessage>The blob type is invalid for this operation.</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>72</TotalFiles_Blobs>
  <FilesErrored>2</FilesErrored>
</CopyLog>
```
### <a name="upload-completed-with-warnings"></a>Upload concluído com avisos

O upload para o Azure é concluído com avisos se seus dados tinham nomes de contêiner/blob/arquivo que não estavam de acordo com as convenções de nomeação do Azure e os nomes foram modificados para carregar os dados para o Azure.

![Caminho para copiar a lâmina de visão geral do login quando concluído com avisos](media/data-box-logs/copy-log-path-3.png)

Aqui está um exemplo de um registro de cópia onde os contêineres que não estavam de acordo com as convenções de nomeação do Azure foram renomeados durante o upload de dados para o Azure.

Os novos nomes exclusivos para `DataBox-GUID` contêineres estão no formato e os dados para o contêiner são colocados no novo contêiner renomeado. O registro de cópia especifica o nome antigo e o novo contêiner para contêiner.

```xml
<ErroredEntity Path="New Folder">
   <Category>ContainerRenamed</Category>
   <ErrorCode>1</ErrorCode>
   <ErrorMessage>The original container/share/blob has been renamed to: DataBox-3fcd02de-bee6-471e-ac62-33d60317c576 :from: New Folder :because either the name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>Container</Type>
</ErroredEntity>
```

Aqui está um exemplo de um registro de cópia onde os blobs ou arquivos que não estavam de acordo com as convenções de nomeação do Azure, foram renomeados durante o upload de dados para o Azure. Os novos nomes de blob ou arquivo são convertidos em SHA256 digest de caminho relativo ao contêiner e são carregados para o caminho com base no tipo de destino. O destino pode ser blobs de bloco, blobs de página ou Arquivos Azure.

O `copylog` especifica o antigo e o novo blob ou nome de arquivo e o caminho no Azure.

```xml
<ErroredEntity Path="TesDir028b4ba9-2426-4e50-9ed1-8e89bf30d285\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: PageBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDir9856b9ab-6acb-4bc3-8717-9a898bdb1f8c\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: AzureFile/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity Path="TesDirf92f6ca4-3828-4338-840b-398b967d810b\Ã">
  <Category>BlobRenamed</Category>
  <ErrorCode>1</ErrorCode>
  <ErrorMessage>The original container/share/blob has been renamed to: BlockBlob/DataBox-0xcdc5c61692e5d63af53a3cb5473e5200915e17b294683968a286c0228054f10e :from: Ã :because either name has invalid character(s) or length is not supported</ErrorMessage>
  <Type>File</Type>
</ErroredEntity>
```

## <a name="get-chain-of-custody-logs-after-data-erasure"></a>Obtenha cadeia de registros de custódia após a eliminação de dados

Depois que os dados são apagados dos discos da Caixa de Dados de acordo com as diretrizes da Revisão 1 do NIST SP 800-88, os registros de cadeia de custódia estão disponíveis. Esses registros incluem os registros de auditoria e o histórico do pedido. Os arquivos BOM ou manifesto também são copiados com os registros de auditoria.

### <a name="audit-logs"></a>Logs de auditoria

Os registros de auditoria contêm informações sobre como ligar e acessar compartilhamentos na Data Box ou Data Box Heavy quando está fora do data center do Azure. Estes registros estão localizados em:`storage-account/azuredatabox-chainofcustodylogs`

Aqui está uma amostra do registro de auditoria de uma caixa de dados:

```
9/10/2018 8:23:01 PM : The operating system started at system time ‎2018‎-‎09‎-‎10T20:23:01.497758400Z.
9/10/2018 8:23:42 PM : An account was successfully logged on.
Subject:
    Security ID:        S-1-5-18
    Account Name:       WIN-DATABOXADMIN
    Account Domain: Workgroup
    Logon ID:       0x3E7
Logon Information:
    Logon Type:     3
    Restricted Admin Mode:  -
    Virtual Account:        No
    Elevated Token:     No
Impersonation Level:        Impersonation
New Logon:
    Security ID:        S-1-5-7
    Account Name:       ANONYMOUS LOGON
    Account Domain: NT AUTHORITY
    Logon ID:       0x775D5
    Linked Logon ID:    0x0
    Network Account Name:   -
    Network Account Domain: -
    Logon GUID:     {00000000-0000-0000-0000-000000000000}
Process Information:
    Process ID:     0x4
    Process Name:       
Network Information:
    Workstation Name:   -
    Source Network Address: -
    Source Port:        -
Detailed Authentication Information:
    Logon Process:      NfsSvr
    Authentication Package:MICROSOFT_AUTHENTICATION_PACKAGE_V1_0
    Transited Services: -
    Package Name (NTLM only):   -
    Key Length:     0
This event is generated when a logon session is created. It is generated on the computer that was accessed. 
The subject fields indicate the account on the local system which requested the logon. This is most commonly a service such as the Server service, or a local process such as Winlogon.exe or Services.exe. 
The logon type field indicates the kind of logon that occurred. The most common types are 2 (interactive) and 3 (network).
The New Logon fields indicate the account for whom the new logon was created, i.e. the account that was logged on.
The network fields indicate where a remote logon request originated. Workstation name is not always available and may be left blank in some cases.
The impersonation level field indicates the extent to which a process in the logon session can impersonate.
The authentication information fields provide detailed information about this specific logon request.
    - Logon GUID is a unique identifier that can be used to correlate this event with a KDC event.
    - Transited services indicate which intermediate services have participated in this logon request.
    - Package name indicates which sub-protocol was used among the NTLM protocols.
    - Key length indicates the length of the generated session key. This will be 0 if no session key was requested.
9/10/2018 8:25:58 PM : An account was successfully logged on.
```


## <a name="download-order-history"></a>Baixar histórico de pedidos

O histórico do pedido está disponível no portal Azure. Se a ordem estiver concluída e a limpeza do dispositivo (eliminação de dados dos discos) estiver concluída, vá até a ordem do dispositivo e navegue até **os detalhes do Pedido**. A opção **Baixar histórico de pedidos** está disponível. Para obter mais informações, consulte [O histórico do pedido de download](data-box-portal-admin.md#download-order-history).

Se você percorrer o histórico da ordem, verá:

- Informações de rastreamento da operadora para o seu dispositivo.
- Eventos com atividade *SecureErase.* Esses eventos correspondem à eliminação dos dados no disco.
- Links de log da Data Box. Os caminhos para os *registros de auditoria,* registros de cópia e arquivos *BOM* são *apresentados.*

Aqui está uma amostra do registro do histórico de pedidos do portal Azure:

```
-------------------------------
Microsoft Data Box Order Report
-------------------------------
Name                                               : gus-poland                              
StartTime(UTC)                              : 9/19/2018 8:49:23 AM +00:00                       
DeviceType                                     : DataBox                                           
-------------------
Data Box Activities
-------------------
Time(UTC)                 | Activity                       | Status          | Description

9/19/2018 8:49:26 AM      | OrderCreated                   | Completed       |
10/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |
10/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 10/3/2018 1:36:43 PM at AMSTERDAM-NLD                                                                                
10/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 10/4/2018 8:23:30 PM at AMSTERDAM-NLD                                                                        
10/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time : 10/4/2018 11:43:34 PM at AMSTERDAM-NLD
10/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in BRIGHTON-GBR. Local Time : 10/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
10/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 10/5/2018 9:13:24 AM at BRIGHTON-GBR                                                                               
10/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 10/5/2018 12:03:04 PM at BRIGHTON-GBR                                                                          
1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25 PM at BRIGHTON-GBR                                                                                       
1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at BRIGHTON-GBR. Local Time : 1/25/2019 8:03:55 PM at LAMBETH-GBR                                                                            
1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in BRIGHTON-GBR. Local Time : 1/25/2019 8:04:58 PM at BRIGHTON-GBR                                                                    
1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR
1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD. Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019 9:07:57 AM at AMSTERDAM-NLD                                                                             
1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019 1:35:56 PM at AMSTERDAM-NLD                                                                            
1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019 2:57:48 PM at AMSTERDAM-NLD
1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |
1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully.
1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.
2/12/2019 10:37:03 PM     | DataCopy                       | Resumed         |
2/13/2019 12:05:15 AM     | DataCopy                       | Resumed         |
2/15/2019 7:07:34 PM      | DataCopy                       | Completed       | Copy Completed.
2/15/2019 7:47:32 PM      | SecureErase                    | Started         |
2/15/2019 8:01:10 PM      | SecureErase                    | Completed       | Azure Data Box:<Device-serial-no> has been sanitized according to NIST 800-88 Rev 1.
------------------
Data Box Log Links
------------------
Account Name         : gusacct
Copy Logs Path       : databoxcopylog/gus-poland_<Device-serial-no>_CopyLog_<GUID>.xml
Audit Logs Path      : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
BOM Files Path       : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
```

## <a name="next-steps"></a>Próximas etapas

- Saiba como [solucionar problemas em sua Caixa de Dados e Data Box Heavy](data-box-troubleshoot.md).
