---
title: Tutorial para exportar dados do Azure Data Box | Microsoft Docs
description: Conheça os pré-requisitos de implantação e como exportar dados de um Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 09/10/2020
ms.author: alkohli
ms.openlocfilehash: 5c3f87620c8a2a2d2438d7a5630541c0f76f9f17
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91575564"
---
# <a name="tutorial-create-export-order-for-azure-data-box-preview"></a>Tutorial: criar ordem de exportação para Azure Data Box (versão prévia)

Azure Data Box é uma solução híbrida que permite mover dados para fora do Azure para seu local. Este tutorial descreve como criar uma ordem de exportação para Azure Data Box. O principal motivo para criar uma ordem de exportação é para a recuperação de desastres, caso o armazenamento local seja comprometido e um backup precise ser restaurado.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
>
> * Pré-requisitos para exportar
> * Ordenar um Data Box para exportação
> * Acompanhar a ordem de exportação
> * Cancelar a ordem de exportação

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>Pré-requisitos

Conclua os seguintes pré-requisitos de configuração para Data Box serviço e dispositivo antes de solicitar o dispositivo.

### <a name="for-service"></a>Para o serviço

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

* Verifique se você tem um grupo de recursos existente que pode ser usado com sua Azure Data Box.

* Verifique se sua conta de armazenamento do Azure da qual você deseja exportar dados é um dos tipos de conta de armazenamento com suporte, conforme descrito [as contas de armazenamento com suporte para data Box](data-box-system-requirements.md#supported-storage-accounts).

### <a name="for-device"></a>Para dispositivo

Antes de começar, verifique se:

* Você deve ter um computador host conectado à rede do datacenter. Você copiará os dados de Azure Data Box para este computador. Seu computador host deve executar um sistema operacional com suporte, conforme descrito em [Requisitos de sistema do Azure Data Box](data-box-system-requirements.md).

* Seu datacenter deve ter uma rede de alta velocidade. É altamente recomendável que você tenha pelo menos uma conexão de 10 GbE. Se uma conexão de 10 GbE não estiver disponível, um link de dados de 1 GbE poderá ser usado, mas as velocidades de cópia serão afetadas.

## <a name="order-data-box-for-export"></a>Data Box de pedidos para exportação

Execute as etapas a seguir no portal do Azure para solicitar um dispositivo.

1. Use suas credenciais do Microsoft Azure para entrar nesta URL: [https://portal.azure.com](https://portal.azure.com).

2. Selecione **+ Criar um recurso** e pesquise *Azure Data Box*. Selecione **Azure Data Box**.

   ![Criar recurso](media/data-box-deploy-export-ordered/azure-data-box-export-00b.png)

3. Selecione **Criar**.

   ![Criar Azure Data Box](media/data-box-deploy-export-ordered/azure-data-box-export-00c.png)

4. Verifique se Azure Data Box serviço está disponível em sua região. Insira ou selecione as informações a seguir e selecione **Aplicar**.

    |Configuração  |Valor  |
    |---------|---------|
    |Tipo de transferência     | Selecione **exportar para o Azure**.        |
    |Subscription     | Selecione uma assinatura do EA, do CSP ou do Azure Sponsorship para o serviço Data Box. <br> A assinatura está vinculada à sua conta de cobrança.       |
    |Resource group     |    Selecione um grupo de recursos existente. <br> Um grupo de recursos é um contêiner lógico para os recursos que podem ser gerenciados ou implantados juntos.         |
    |Região do Azure de origem    |    Selecione a região do Azure na qual seus dados estão atualmente.         |
    |País de destino     |     Selecione o país em que deseja enviar o dispositivo.        |

   ![Selecione suas configurações de Data Box](media/data-box-deploy-export-ordered/azure-data-box-export-01.png)

5. Selecione **Data Box**. A capacidade máxima utilizável de um único pedido é de 80 TB. É possível criar vários pedidos para tamanhos maiores de dados.

   ![Selecione a capacidade de Data Box](media/data-box-deploy-export-ordered/azure-data-box-export-02b.png)

6. Em **ordem**, especifique os detalhes da ordem **básica** . Insira ou selecione as informações a seguir e selecione **Avançar**.

    |Configuração  |Valor  |
    |---------|---------|
    |Subscription     | A assinatura é preenchida automaticamente com base na seleção anterior.|
    |Resource group | O grupo de recursos selecionado anteriormente. |
    |Nome da ordem de exportação     |  Forneça um nome amigável para acompanhar o pedido. <br> O nome pode ter entre 3 e 24 caracteres que podem ser letras, números e hífens. <br> O nome deve começar e terminar com uma letra ou um número.      |

    ![Noções básicas de ordem de exportação](media/data-box-deploy-export-ordered/azure-data-box-export-03.png)

    Selecione **Avançar: seleção de dados** para continuar.

7. Em **seleção de dados**, selecione **adicionar conta de armazenamento e tipo de exportação**.

    ![Adicionar conta de armazenamento e tipo de exportação](media/data-box-deploy-export-ordered/azure-data-box-export-03b.png)

8. Na **opção Selecionar exportação**, especifique os detalhes da opção de exportação. Insira ou selecione as informações a seguir e selecione **Adicionar**.

    |Configuração  |Valor  |
    |---------|---------|
    |Conta de armazenamento     | A conta de armazenamento do Azure de onde você deseja exportar dados. |
    |Tipo de exportação     | Especifica o tipo de dados a serem exportados de **todos os objetos** e **usam o arquivo XML**.<ul><li> **Todos os objetos** – especifica que o trabalho exporta todos os dados dependendo da sua seleção para **Opções de transferência**.</li><li> **Usar arquivo XML** – especifica um arquivo XML que contém um conjunto de caminhos e prefixos para BLOBs e/ou arquivos a serem exportados da conta de armazenamento. O arquivo XML precisa estar no contêiner da conta de armazenamento selecionada e não há suporte para a seleção de compartilhamentos de arquivos. O arquivo precisa ser um arquivo. xml não vazio.</li></ul>        |
    |Opções de transferência     |  Especifica as opções de transferência de dados de **selecionar tudo**, **todos os BLOBs**e **todos os arquivos**. <ul><li> **Selecionar tudo** -especifica que todos os BLOBs e arquivos do Azure são exportados. Se você estiver usando uma conta de armazenamento que dá suporte apenas a BLOBs (conta de armazenamento de BLOB), a opção **todos os arquivos** não será selecionável.</li><li> **Todos os BLOBs** -especifica que somente os blobs de bloco e de página são exportados.</li><li> **Todos os arquivos** -especifica que todos os arquivos são exportados, excluindo BLOBs. O tipo de conta de armazenamento que você tem (GPv1 e GPv2, armazenamento Premium ou armazenamento de BLOBs) determina os tipos de dados que você pode exportar. Para obter mais informações, consulte [supported Storage accounts for Export](../storage/common/storage-import-export-requirements.md#supported-storage-types).</li></ul>         |
    |Incluir log detalhado     | Indica se você deseja um arquivo de log detalhado que contenha uma lista de todos os arquivos que foram exportados com êxito.        |

    > [!NOTE]
    >
    > Se você selecionar **usar arquivo XML** para a configuração de **tipo de exportação** , será necessário certificar-se de que o XML contém caminhos e/ou prefixos válidos. Você deve construir e fornecer o arquivo XML.  Se o arquivo for inválido ou nenhum dado corresponder aos caminhos especificados, a ordem será encerrada com dados parciais ou nenhum dado será exportado.

    Para ver como adicionar um arquivo XML a um contêiner, consulte [Exportar ordem usando o arquivo XML](data-box-deploy-export-ordered.md#export-order-using-xml-file).

   ![Selecionar opção de exportação](media/data-box-deploy-export-ordered/azure-data-box-export-04b.png)

   Para ver um exemplo da entrada XML, consulte [exemplo de entrada XML](data-box-deploy-export-ordered.md#sample-xml-file)

9. Em **seleção de dados**, examine as configurações e selecione **avançar: segurança>**.

   ![Detalhes do Contato](media/data-box-deploy-export-ordered/azure-data-box-export-05.png)

1. Em **segurança**, se você quiser habilitar a criptografia dupla baseada em software, selecione **habilitar criptografia dupla para o pedido**. 

   A criptografia baseada em software é executada além da criptografia AES-256 bits dos dados no Data Box.

   > [!NOTE]
   > Habilitar essa opção pode fazer com que o processamento de pedidos e a cópia de dados demorem mais. Você não pode alterar essa opção depois de criar seu pedido.

   ![Tela de segurança para importação de caixa de dados, criptografia dupla](media/data-box-deploy-export-ordered/azure-data-box-export-05b.png)

   Selecione **Avançar: Detalhes de contato** para continuar.

10. Nos **detalhes de contato**, selecione **+ Adicionar endereço de envio** para inserir suas informações de envio.

    ![Adicionar endereço de envio](media/data-box-deploy-export-ordered/azure-data-box-export-06.png)

11. Em **Adicionar endereço de envio**, forneça seu nome e sobrenome, nome e endereço postal da empresa e um número de telefone válido. Selecione **Validar**. O serviço valida o endereço de remessa para a disponibilidade do serviço. Se o serviço está disponível para o endereço de remessa especificado, você receberá uma notificação para esse efeito.

    ![Validar endereço de envio](media/data-box-deploy-export-ordered/azure-data-box-export-07.png)

    Se você estiver ordenando uma região em que o envio autogerenciado está disponível, você poderá selecionar essa opção. Para saber mais sobre a remessa autogerenciada, confira [Usar a remessa autogerenciada](data-box-portal-customer-managed-shipping.md).

12. Selecione **Adicionar endereço de envio** depois que os detalhes de envio tiverem sido validados com êxito.

13. Em **detalhes de contato**, revise seu endereço de envio e endereço de email. O serviço envia as notificações por email em relação a quaisquer atualizações do status do pedido para os endereços de email especificados.

    É recomendável usar um email de grupo para que você continue a receber notificações caso um administrador do grupo saia.

    ![Detalhes do pedido](media/data-box-deploy-export-ordered/azure-data-box-export-09.png)

14. Selecione **Avançar: revisar + ordem>**. Você deve aceitar os termos e condições para prosseguir com a criação do pedido.

15. Selecione **Pedido**. A criação do pedido demora alguns minutos.

    ![Ordem de confirmação](media/data-box-deploy-export-ordered/azure-data-box-export-10.png)

## <a name="export-order-using-xml-file"></a>Exportar ordem usando arquivo XML

Se você selecionar **usar arquivo XML**, poderá especificar contêineres e blobs específicos (página e bloco) que deseja exportar. Você precisará seguir as especificações de [tabela de arquivo XML de exemplo](#sample-xml-file) para formatar seu XML. As etapas a seguir mostram como usar um arquivo XML para exportar seus dados:

1. Para **tipo de exportação**, selecione **usar arquivo XML**. Esse é o arquivo XML que especifica BLOBs específicos e arquivos do Azure que você deseja exportar. Para adicionar o arquivo XML, selecione **clique aqui para selecionar um arquivo XML**.
     ![Captura de tela da opção Selecionar exportação com o clique aqui para selecionar uma opção de arquivo X M L chamada out.](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-01.png)

2. Selecione **+ contêiner** para criar um contêiner.
    ![Captura de tela da seção contêineres.](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-02.png)

3. Na **nova guia contêiner** que aparece no lado direito do portal do Azure, adicione um nome para o contêiner. O nome deve estar em letras minúsculas e você pode incluir números e traços '-'. Em seguida, selecione o **nível de acesso público** na caixa de listagem suspensa. Recomendamos que você escolha **privado (acesso não anônimo)** para impedir que outras pessoas acessem seus dados. Para obter mais informações sobre níveis de acesso de contêiner, consulte [permissões de acesso de contêiner](../storage/blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs).

   ![Captura de tela da nova guia contêiner mostrando a opção particular (sem acesso anônimo) selecionada.](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-04.png)

4. Selecione **Criar**.

   ![Captura de tela da guia novo contêiner com a opção criar chamada out.](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-07.png)

   Se o contêiner for criado com êxito, você receberá a seguinte mensagem:

   ![Captura de tela da mensagem que diz "contêiner de armazenamento criado com êxito".](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-09.png)

5. Selecione o contêiner que você criou e clique duas vezes nele.

   ![Captura de tela da seção contêineres com o contêiner meu contêiner de teste privado chamado.](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-08.png)

6. Clicar duas vezes no contêiner abrirá a exibição Propriedades do contêiner. Agora você deseja anexar (ou procurar) seu arquivo XML que contém a lista de BLOBs e/ou arquivos do Azure que você deseja exportar. Escolha **Carregar**.

   ![Captura de tela da caixa de diálogo Carregar blob com a opção de upload chamada out.](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-10c.png)

7. Você adicionou com êxito o arquivo XML ao contêiner. Somente os BLOBs e os arquivos do Azure especificados neste XML serão exportados.

   ![Captura de tela do assistente para pedidos com o próximo: opção de segurança chamada out.](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-12.png)

## <a name="track-the-order"></a>Acompanhar o pedido

Depois de fazer o pedido, você pode acompanhar o status do pedido no portal do Azure. Acesse seu pedido do Data Box e, em seguida, acesse **Visão Geral** para exibir o status. O portal mostra o pedido sob o estado **Pedido**.

Quando a preparação do dispositivo for concluída, a cópia de dados será iniciada a partir das contas de armazenamento selecionadas. O portal mostra a ordem em estado de **cópia de dados em andamento** .

![Data Box ordem de exportação processada](media/data-box-deploy-export-ordered/azure-data-box-export-15b.png)

Data Box copia dados das contas de armazenamento de origem. Depois que a cópia de dados for concluída, Data Box será bloqueada e o portal mostrará a ordem no estado de **cópia concluída** .

![Cópia de dados de exportação Data Box concluída](media/data-box-deploy-export-ordered/azure-data-box-export-15c.png)

Se o dispositivo não estiver disponível, você receberá uma notificação. Se o dispositivo estiver disponível, a Microsoft identificará o dispositivo para envio e o preparará. Durante a preparação do dispositivo, ocorrem as seguintes ações:

* Os compartilhamentos SMB são criados para cada conta de armazenamento associada ao dispositivo.
* Para cada compartilhamento, as credenciais de acesso, como nome de usuário e senha, são geradas.
* O dispositivo está bloqueado e pode ser acessado somente usando a senha de desbloqueio do dispositivo. Para recuperar a senha, você precisa fazer logon na sua conta do portal do Azure e selecionar os **detalhes do dispositivo**.

Em seguida, a Microsoft prepara e despacha seu dispositivo por meio de uma operadora regional. Após o envio do dispositivo, você receberá um número de acompanhamento. O portal mostra o pedido no estado **Despachado**.

![Data Box ordem de exportação expedida](media/data-box-deploy-export-ordered/azure-data-box-export-16.png)

Se a entrega autogerenciada foi selecionada, você receberá uma notificação por email com as próximas etapas quando o dispositivo estiver pronto para ser obtido do datacenter. Para obter mais informações relacionadas ao envio autogerenciado, consulte [entrega autogerenciada](data-box-portal-customer-managed-shipping.md).

![Envio autogerenciado pronto para retirada](media/data-box-deploy-export-ordered/azure-data-box-export-17.png)

## <a name="cancel-the-order"></a>Cancelar o pedido

Para cancelar esse pedido, no portal do Azure, vá para **Visão geral** e selecione **Cancelar** na barra de comandos.

Depois de fazer um pedido, você pode cancelá-lo a qualquer momento antes de o pedido começar a ser processado.

Para excluir um pedido cancelado, vá para **Visão geral** e selecione **Excluir** na barra de comandos.

## <a name="sample-xml-file"></a>Arquivo XML de exemplo

O XML a seguir mostra um exemplo de nomes de BLOB, prefixos de BLOB e arquivos do Azure contidos no formato XML que a ordem de exportação usa quando você seleciona a opção **usar arquivo XML** :

```xml
<?xml version="1.0" encoding="utf-8"?>
   <!-- BlobList/prefix/Container list for Blob storage for export  -->
   <BlobList>
      <BlobPath>/8tbpageblob/8tbpageblob/8tbpageblob</BlobPath>
      <BlobPathPrefix>/blockblob4dot75tbdata/</BlobPathPrefix>
      <BlobPathPrefix>/1tbfilepageblob</BlobPathPrefix>
      <BlobPathPrefix>/1tbfile/</BlobPathPrefix>
      <BlobPathPrefix>/8mbfiles/</BlobPathPrefix>
      <BlobPathPrefix>/64mbfiles/</BlobPathPrefix>
   </BlobList>
   <!-- FileList/prefix/Share list for Azure File storage for export  -->
   <AzureFileList>
      <FilePathPrefix>/64mbfiles/</FilePathPrefix>
      <FilePathPrefix>/4mbfiles/prefix2/subprefix</FilePathPrefix>
      <FilePathPrefix>/1tbfile/prefix</FilePathPrefix>
   </AzureFileList>
```

Alguns pontos importantes em relação aos arquivos XML:

* As marcas XML diferenciam maiúsculas de minúsculas e precisam corresponder exatamente conforme especificado no exemplo acima.
* Marcas de abertura e fechamento devem corresponder.
* Marcas XML incorretas ou formatação podem levar à falha de exportação de dados.
* Nenhum dado será exportado se o blob e/ou o prefixo do arquivo forem inválidos.

### <a name="examples-of-valid-blob-paths"></a>Exemplos de caminhos de blob válidos

A tabela a seguir mostra exemplos de caminhos de blob válidos:

   | Seletor | Caminho do Blob | Descrição |
   | --- | --- | --- |
   | Começa com |/ |Exporta todos os blobs na conta de armazenamento |
   | Começa com |/$root/ |Exporta todos os blobs no contêiner raiz |
   | Começa com |/containers |Exporta todos os BLOBs em qualquer contêiner que comece com **contêineres** de prefixo |
   | Começa com |/container-name/ |Exporta todos os BLOBs no contêiner **de contêiner-nome** |
   | Começa com |/container-name/prefix |Exporta todos os BLOBs no contêiner **de contêiner-nome** que começa com **prefixo** de prefixo |
   | Igual a |$root/logo.bmp |Exporta o blob **logo.bmp** no contêiner raiz |
   | Igual a |8tbpageblob/mydata.txt |Exporta **mydata.txt** de blob no contêiner **8tbpageblob** |

## <a name="sample-log-files"></a>Arquivos de log de exemplo

Esta seção fornece arquivos de log de exemplo que são gerados durante a exportação. Os logs de erros são gerados automaticamente. Para gerar o arquivo de log detalhado, você precisa selecionar **incluir log detalhado** em portal do Azure ao configurar a ordem de exportação.
Para obter mais informações sobre logs de cópia e detalhado, consulte [copiar logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box).

<!-- ### Verbose log

The following log files show examples of verbose logging when you select **Include verbose log**:

```xml
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3.4" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/helloEndWithDot..txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test..txt" Size="12" crc64="17906086011702236012"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/.......txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/copylogb08fa3095564421bb550d775fff143ed====..txt" Size="53638" crc64="1147139997367113454"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/testmaxChars-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-12345679" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file0" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file1" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000001" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000000" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed10.dat" Size="65536" crc64="10746682179555216785"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/LiveSiteReport_Oct.xlsx" Size="7028" crc64="6103506546789189963"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/NE_Oct_GeoReport.xlsx" Size="103197" crc64="13305485882546035852"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed1.dat" Size="65536" crc64="3140622834011462581"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-0" Size="1048576" crc64="16086591317856295272"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000001" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-0" Size="4194304" crc64="1339017920798612765"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000000" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-1" Size="8388608" crc64="3963298606737216548"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-1" Size="1048576" crc64="11061759121415905887"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/XLS-10MB.xls" Size="1199104" crc64="2218419493992437463"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-0" Size="8388608" crc64="1072783424245035917"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-1" Size="4194304" crc64="9991307204216370812"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/VL_Piracy_Negtive10_TPNameAndGCS.xlsx" Size="12398699" crc64="13526033021067702820"></File>
```

### Copy logs

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box). -->

<!-- The following xml shows an example of the copy log when the export is successful:

```xml
<CopyLog Summary="Summary">
  <Status>Succeeded</Status>
    <TotalFiles_Blobs>27</TotalFiles_Blobs>
    <FilesErrored>0</FilesErrored>
</CopyLog>
```

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box).

The following xml shows an example of the copy log when the export has errors:

```xml
<ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/wastorage.v140.3.0.2.nupkg">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444.hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444 (1).hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>4</TotalFiles_Blobs>
  <FilesErrored>3</FilesErrored>
</CopyLog>
``` -->

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre tópicos do Azure Data Box como:

> [!div class="checklist"]
>
> * Pré-requisitos para exportar
> * Ordenar um Data Box para exportação
> * Acompanhar a ordem de exportação
> * Cancelar a ordem de exportação

Avance para o próximo tutorial para saber como proteger seu Data Box.

> [!div class="nextstepaction"]
> [Configurar seu Azure Data Box](./data-box-deploy-set-up.md)
