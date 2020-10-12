---
title: Usar Importação/Exportação do Microsoft Azure para transferir dados para Arquivos do Azure | Microsoft Docs
description: Saiba como criar trabalhos de importação no Portal do Azure para transferir dados para Arquivos do Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 04/08/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: a88cf9981d4f3a69a503c9caa56be1b5f35029f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86105176"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Usar o serviço de importação/exportação do Microsoft Azure para importar dados para Arquivos do Azure

Este artigo fornece instruções passo a passo sobre como usar o serviço de Importação/Exportação do Microsoft Azure para importar com segurança grandes quantidades de dados de Arquivos do Azure. Para importar dados, o serviço exige o envio de unidades de disco suportadas contendo dados para um datacenter do Azure.  

O serviço de Importação/Exportação dá suporte somente à importação de Arquivos do Azure para o Armazenamento do Microsoft Azure. Não há suporte para a exportação de Arquivos do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar um trabalho de importação para transferir dados ao Arquivos do Azure, revise cuidadosamente e preencha a seguinte lista de pré-requisitos. Você deve:

- Ter uma assinatura ativa do Azure para usar com o serviço de Importação/Exportação.
- Ter pelo menos uma conta de Armazenamento do Microsoft Azure. Consulte a lista de [Contas de armazenamento e tipos de armazenamento com suporte para o serviço de Importação/Exportação](storage-import-export-requirements.md). Para obter informações sobre como criar uma nova conta de armazenamento, consulte [Como criar uma conta de armazenamento](storage-account-create.md).
- Ter o número adequado de discos de [Tipos com suporte](storage-import-export-requirements.md#supported-disks).
- Ter um sistema Windows executando uma [Versão do sistema operacional com suporte](storage-import-export-requirements.md#supported-operating-systems).
- [Baixe o WAImportExport versão 2](https://aka.ms/waiev2) no sistema Windows. Descompacte para a pasta padrão `waimportexport`. Por exemplo, `C:\WaImportExport`.
- Ter uma conta FedEx/DHL. Se você quiser usar uma operadora diferente de FedEx/DHL, contate a equipe de operações Azure Data Box em `adbops@microsoft.com` .  
    - A conta deve ser válida, deve ter saldo e ter recursos de devolução.
    - Gerar um número de controle para o trabalho de exportação.
    - Cada trabalho deve ter um número de controle separado. Não há suporte para vários trabalhos com o mesmo número de controle.
    - Se você não tiver uma conta de transportadora, vá para:
        - [Criar uma conta FedEX](https://www.fedex.com/en-us/create-account.html), ou
        - [Criar uma conta DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).



## <a name="step-1-prepare-the-drives"></a>Etapa 1: preparar as unidades

Essa etapa gera um arquivo de diário. O arquivo de diário armazena informações básicas como número de série da unidade, chave de criptografia e detalhes da conta de armazenamento.

Execute as etapas a seguir para preparar as unidades.

1. Conecte as unidades de disco ao sistema Windows via conectores SATA.
2. Crie um único volume NTFS em cada unidade. Atribua uma letra da unidade ao volume. Não use pontos de montagem.
3. Modificar o arquivo *dataset.csv* na pasta raiz onde a ferramenta reside. Dependendo de você querer importar um arquivo ou pasta ou ambos, adicione as entradas no arquivo *dataset.csv* semelhante aos exemplos a seguir.  

   - **Para importar um arquivo**: no exemplo a seguir, os dados a serem copiados residem na unidade F:. O arquivo *MyFile1.txt* é copiado para a raiz de *MyAzureFileshare1*. Se o *MyAzureFileshare1* não existir, ele será criado na conta de Armazenamento do Microsoft Azure. A estrutura de pastas é mantida.

       ```
           BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None

       ```
   - **Para importar uma pasta**: todos os arquivos e pastas em *MyFolder2* são copiados para o compartilhamento de arquivos de forma recursiva. A estrutura de pastas é mantida.

       ```
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Várias entradas podem ser feitas no mesmo arquivo correspondente a pastas ou arquivos que são importados.

       ```
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Saiba mais sobre [como preparar o arquivo CSV de conjunto de dados](storage-import-export-tool-preparing-hard-drives-import.md).


4. Modifique o arquivo *driveset.csv* na pasta raiz onde a ferramenta reside. Adicione as entradas no arquivo *driveset.csv* semelhante aos exemplos a seguir. O arquivo driveset tem a lista de discos e as letras de unidade correspondentes para que a ferramenta possa escolher corretamente a lista de discos a serem preparados.

    Este exemplo presume que dois discos estão vinculados e volumes NTFS básicos G:\ e H:\ foram criados. H:\is não está criptografado enquanto g já está criptografado. A ferramenta formata e criptografa o disco que hospeda apenas H:\ (e não G:\).

   - **Para um disco que não está criptografado**: especifique *Criptografar* para habilitar a criptografia BitLocker no disco.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       H,Format,SilentMode,Encrypt,
       ```

   - **Para um disco que já está criptografado**: especifique *AlreadyEncrypted* e forneça a chave do BitLocker.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
       ```

     Várias entradas podem ser feitas no mesmo arquivo correspondente a várias unidades. Saiba mais sobre [como preparar o arquivo CSV de driveset](storage-import-export-tool-preparing-hard-drives-import.md).

5. Use a opção `PrepImport` para copiar e preparar dados para a unidade de disco. Para a primeira sessão de cópia copiar diretórios e/ou arquivos com uma nova sessão de cópia, execute o seguinte comando:

    ```cmd
    .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

   Um exemplo de importação é mostrado abaixo.

    ```cmd
    .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
    ```

6. Um arquivo de diário com o nome fornecido com o parâmetro `/j:` é criado para cada execução da linha de comando. Cada unidade que você prepara tem um arquivo de diário que deve ser carregado quando você cria o trabalho de importação. Unidades sem arquivos de diário não são processadas.

    > [!IMPORTANT]
    > - Não modifique os dados em unidades de disco ou o arquivo de diário depois de concluir a preparação do disco.

Para exemplos adicionais, vá para [Exemplos de arquivos de diário](#samples-for-journal-files).

## <a name="step-2-create-an-import-job"></a>Etapa 2: criar um trabalho de importação

Execute as etapas a seguir para criar um trabalho de importação no portal do Azure.
1. Faça logon em https://portal.azure.com/.
2. Vá para **Todos os serviços > Armazenamento > Trabalhos de importação/exportação**.

    ![Ir para Importação/exportação](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Clique em **criar trabalho de importação/exportação**.

    ![Clique em Trabalho de importação/exportação](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. Em **Noções básicas**:

    - Selecione **Importar para o Azure**.
    - Digite um nome descritivo para o trabalho de importação. Use este nome para acompanhar os trabalhos enquanto eles estiverem em andamento e quando eles estiverem concluídos.
        -  Esse nome pode conter apenas letras minúsculas, números, hifens e sublinhados.
        -  O nome deve começar com uma letra e não pode conter espaços.
    - Selecione uma assinatura.
    - Selecione um grupo de recursos.

        ![Criar o trabalho de importação - Etapa 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. Em **Detalhes do trabalho**:

    - Carregue os arquivos de diário que você criou durante a [Etapa 1: preparar as unidades](#step-1-prepare-the-drives) anterior.
    - Selecione a conta de armazenamento na qual os dados serão importados.
    - O local final da corrida é preenchido automaticamente com base na região da conta de armazenamento selecionada.

       ![Criar trabalho de importação - Etapa 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. Em **Informações sobre a remessa de devolução**:

    - Selecione a operadora na lista suspensa. Se você quiser usar uma operadora diferente de FedEx/DHL, escolha uma opção existente na lista suspensa. Contate a equipe de operações Azure Data Box em `adbops@microsoft.com`  com as informações sobre a transportadora que você planeja usar.
    - Insira um número válido de conta de operadora que você criou com essa operadora. A Microsoft usará essa conta para enviar de volta as unidades para você após a conclusão do seu trabalho de importação.
    - Forneça um nome de contato completo e válido, telefone, email, endereço, cidade, CEP, estado/município e país/região.

        > [!TIP]
        > Em vez de especificar um endereço de email para um usuário único, forneça um email de grupo. Isso garante que você receba notificações mesmo que um administrador saia.

       ![Criar o trabalho de importação - Etapa 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)


5. No **Resumo**:

    - Forneça o endereço de envio do datacenter do Azure para envio de discos de volta ao Azure. Certifique-se de que o nome do trabalho e o endereço completo são mencionados no rótulo de envio.
    - Clique em **OK** para concluir a criação do trabalho de importação.

        ![Criar o trabalho de importação - Etapa 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>Etapa 3: enviar as unidades para o datacenter do Azure

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>Etapa 4: atualizar o trabalho com informações de rastreamento

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Etapa 5: verificar upload de dados para Azure

Acompanhe o trabalho até a conclusão. Quando o trabalho estiver concluído, verifique se os dados foram carregados no Azure. Exclua os dados locais somente depois de verificar se o upload foi realizado com êxito.

## <a name="samples-for-journal-files"></a>Exemplos de arquivos de diário

Para **adicionar mais unidades**, é possível criar um novo arquivo de driveset e executar o comando conforme exibido a seguir.

Para sessões de cópia subsequentes nas unidades de disco diferentes especificadas no arquivo *InitialDriveset .csv*, especifique um novo arquivo *.csv* de driveset e forneça-o como um valor para o parâmetro `AdditionalDriveSet`. Use o nome **mesmo arquivo de diário** e forneça uma **nova ID de sessão**. O formato do arquivo CSV AdditionalDriveset é igual ao formato InitialDriveSet.

```cmd
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
```

Um exemplo de importação é mostrado abaixo.

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
```


Para adicionar mais dados ao mesmo driveset, use o comando PrepImport para sessões de cópia subsequentes para copiar arquivos/diretórios adicionais.

Para as sessões de cópia subsequentes para a mesma unidade de disco rígido especificada no arquivo *InitialDriveset.csv*, especifique o mesmo nome do **arquivo de diário** e forneça uma **nova ID de sessão**. Não é preciso fornecer a chave da conta de armazenamento.

```cmd
WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
```

Um exemplo de importação é mostrado abaixo.

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

## <a name="next-steps"></a>Próximas etapas

* [Exibir o status do trabalho e da unidade](storage-import-export-view-drive-status.md)
* [Verificar os requisitos de Importação/Exportação](storage-import-export-requirements.md)
