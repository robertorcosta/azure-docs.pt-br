---
title: Migre da loja HDFS on-prem para o armazenamento do Azure com a caixa de dados do Azure
description: Migrar dados de uma loja HDFS no local para o Armazenamento Azure
author: normesta
ms.service: storage
ms.date: 02/14/2019
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: c0c6a8637223727a9b0c88245d939605f6a8530e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301993"
---
# <a name="migrate-from-on-prem-hdfs-store-to-azure-storage-with-azure-data-box"></a>Migre da loja HDFS on-prem para o armazenamento do Azure com a caixa de dados do Azure

Você pode migrar dados de uma loja HDFS no local do seu cluster Hadoop para o Azure Storage (blob storage ou Data Lake Storage Gen2) usando um dispositivo Data Box. Você pode escolher entre uma caixa de dados de 80 TB ou uma caixa de dados de 770 TB heavy.

Este artigo ajuda você a completar essas tarefas:

> [!div class="checklist"]
> * Prepare-se para migrar seus dados.
> * Copie seus dados para uma Caixa de Dados ou um dispositivo Data Box Heavy.
> * Mande o dispositivo de volta para a Microsoft.
> * Aplicar permissões de acesso a arquivos e diretórios (somente Data Lake Storage Gen2)

## <a name="prerequisites"></a>Pré-requisitos

Você precisa dessas coisas para completar a migração.

* Uma conta de armazenamento do Azure.

* Um cluster Hadoop no local que contém seus dados de origem.

* Um [dispositivo Azure Data Box](https://azure.microsoft.com/services/storage/databox/).

  * [Solicite sua Caixa de Dados](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) ou Data Box [Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered). 

  * Cabo e conecte sua [Caixa de Dados](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) ou Caixa de Dados [Pesada](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) a uma rede local.

Se você está pronto, vamos começar.

## <a name="copy-your-data-to-a-data-box-device"></a>Copie seus dados para um dispositivo Data Box

Se seus dados se encaixam em um único dispositivo Data Box, então você copiará os dados para o dispositivo Data Box. 

Se o tamanho dos dados exceder a capacidade do dispositivo Data Box, use o [procedimento opcional para dividir os dados em vários dispositivos data box](#appendix-split-data-across-multiple-data-box-devices) e, em seguida, execute esta etapa. 

Para copiar os dados da sua loja HDFS no local para um dispositivo Data Box, você definirá algumas coisas e, em seguida, usará a ferramenta [DistCp.](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)

Siga estas etapas para copiar os dados através das APIs REST do armazenamento Blob/Object para o dispositivo Data Box. A interface rest API fará com que o dispositivo apareça como uma loja HDFS para o seu cluster.

1. Antes de copiar os dados via REST, identifique os primitivos de segurança e conexão para se conectar à interface REST na Data Box ou Data Box Heavy. Faça login na ui web local da Data Box e vá para **Conectar e copiar** página. Contra a conta de armazenamento Do Zure para o seu dispositivo, em **Configurações de Acesso,** localize e selecione **REST**.

    ![Página "Conectar e copiar"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. Na caixa de diálogo de armazenamento de acesso e upload de dados, copie o ponto final do **serviço Blob** e a **chave da conta de armazenamento**. Do ponto final de serviço blob, omita a `https://` barra e a barra de arrasto.

    Neste caso, o ponto `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`final é: . A parte hospedeira do URI que `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`você usará é: . Por exemplo, veja como [conectar ao REST em http](/azure/databox/data-box-deploy-copy-data-via-rest). 

     ![Diálogo "Acessar conta de armazenamento e carregar dados"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. Adicione o ponto final e o endereço IP do `/etc/hosts` nó pesado da Caixa de Dados ou caixa de dados em cada nó.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    Se você estiver usando algum outro mecanismo para DNS, você deve garantir que o ponto final da Caixa de Dados possa ser resolvido.

4. Defina a `azjars` variável shell `hadoop-azure` para `azure-storage` a localização dos arquivos e frasco. Você pode encontrar esses arquivos o diretório de instalação hadoop.

    Para determinar se esses arquivos existem, use o seguinte comando: `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`. Substitua `<hadoop_install_dir>` o espaço reservado pelo caminho para o diretório onde você instalou Hadoop. Certifique-se de usar caminhos totalmente qualificados.

    Exemplos:

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. Crie o recipiente de armazenamento que você deseja usar para cópia de dados. Você também deve especificar um diretório de destino como parte deste comando. Este pode ser um diretório de destino neste momento.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Substitua `<blob_service_endpoint>` o espaço reservado pelo nome do seu ponto final de serviço blob.

    * Substitua `<account_key>` o espaço reservado pela chave de acesso da sua conta.

    * Substitua `<container-name>` o espaço reservado pelo nome do seu recipiente.

    * Substitua `<destination_directory>` o espaço reservado pelo nome do diretório para o que deseja copiar seus dados.

6. Execute um comando de lista para garantir que seu contêiner e diretório foram criados.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * Substitua `<blob_service_endpoint>` o espaço reservado pelo nome do seu ponto final de serviço blob.

   * Substitua `<account_key>` o espaço reservado pela chave de acesso da sua conta.

   * Substitua `<container-name>` o espaço reservado pelo nome do seu recipiente.

7. Copie os dados do armazenamento Hadoop HDFS para Data Box Blob, no contêiner que você criou anteriormente. Se o diretório em que você está copiando não for encontrado, o comando o criará automaticamente.

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Substitua `<blob_service_endpoint>` o espaço reservado pelo nome do seu ponto final de serviço blob.

    * Substitua `<account_key>` o espaço reservado pela chave de acesso da sua conta.

    * Substitua `<container-name>` o espaço reservado pelo nome do seu recipiente.

    * Substitua `<exlusion_filelist_file>` o espaço reservado pelo nome do arquivo que contém sua lista de exclusões de arquivos.

    * Substitua `<source_directory>` o espaço reservado pelo nome do diretório que contém os dados que deseja copiar.

    * Substitua `<destination_directory>` o espaço reservado pelo nome do diretório para o que deseja copiar seus dados.

    A `-libjars` opção é usada `hadoop-azure*.jar` para `azure-storage*.jar` disponibilizar os `distcp`arquivos e os arquivos dependentes para . Isso já pode ocorrer para alguns clusters.

    O exemplo a `distcp` seguir mostra como o comando é usado para copiar dados.

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```
  
    Para melhorar a velocidade da cópia:

    * Tente alterar o número de mapeadores. (O exemplo `m` acima usa = 4 mapeadores.)

    * Tente executar `distcp` vários em paralelo.

    * Lembre-se que arquivos grandes têm melhor desempenho do que pequenos arquivos.

## <a name="ship-the-data-box-to-microsoft"></a>Enviar a caixa de dados para a Microsoft

Siga estas etapas para preparar e enviar o dispositivo Data Box para a Microsoft.

1. Primeiro, [prepare-se para enviar em sua Caixa de Dados ou Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest).

2. Depois que a preparação do dispositivo estiver concluída, baixe os arquivos BOM. Você usará esses arquivos BOM ou manifesto mais tarde para verificar os dados enviados ao Azure.

3. Desligue o dispositivo e remova os cabos.

4. Agende uma retirada com a UPS.

    * Para dispositivos Data Box, consulte [Enviar sua Caixa de Dados](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up).

    * Para dispositivos Data Box Heavy, consulte [Enviar sua caixa de dados pesada](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up).

5. Depois que a Microsoft recebe seu dispositivo, ele é conectado à rede do data center e os dados são carregados na conta de armazenamento que você especificou quando fez o pedido do dispositivo. Verifique com os arquivos BOM se todos os seus dados são carregados para o Azure. 

## <a name="apply-access-permissions-to-files-and-directories-data-lake-storage-gen2-only"></a>Aplicar permissões de acesso a arquivos e diretórios (somente Data Lake Storage Gen2)

Você já tem os dados em sua conta do Azure Storage. Agora você aplicará permissões de acesso a arquivos e diretórios.

> [!NOTE]
> Esta etapa só é necessária se você estiver usando o Azure Data Lake Storage Gen2 como seu armazenamento de dados. Se você estiver usando apenas uma conta de armazenamento blob sem namespace hierárquico como seu armazenamento de dados, você pode pular esta seção.

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>Crie um princípio de serviço para sua conta Azure Data Lake Storage Gen2

Para criar um diretor de serviço, consulte [Como: Usar o portal para criar um aplicativo e serviço AD do Azure que possa acessar recursos](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

* Ao executar as etapas da seção [Atribuir o aplicativo a uma função](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) do artigo, atribua a função **Colaborador dos Dados do Storage Blob** à entidade de serviço.

* Ao executar as etapas no [Obter valores para assinar na](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) seção do artigo, salve o ID do aplicativo e os valores secretos do cliente em um arquivo de texto. Você precisará deles em breve.

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>Gerar uma lista de arquivos copiados com suas permissões

A partir do cluster Hadoop no local, execute este comando:

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

Este comando gera uma lista de arquivos copiados com suas permissões.

> [!NOTE]
> Dependendo do número de arquivos no HDFS, este comando pode levar um longo tempo para ser executado.

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>Gere uma lista de identidades e mapeie-as para as identidades do Azure Active Directory (ADD)

1. Baixe `copy-acls.py` o roteiro. Consulte os scripts de ajuda de download e configure seu nó de [borda para executá-los](#download-helper-scripts) seção deste artigo.

2. Execute este comando para gerar uma lista de identidades exclusivas.

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   Esse script gera `id_map.json` um arquivo chamado que contém as identidades que você precisa mapear para identidades baseadas em ADD.

3. Abra o arquivo `id_map.json` em um editor de texto.

4. Para cada objeto JSON que aparece `target` no arquivo, atualize o atributo de um Nome Principal de Usuário AAD (UPN) ou ObjectId (OID), com a identidade mapeada apropriada. Depois de terminar, salve o arquivo. Você vai precisar deste arquivo na próxima etapa.

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>Aplique permissões para arquivos copiados e aplique mapeamentos de identidade

Execute este comando para aplicar permissões aos dados que você copiou na conta Data Lake Storage Gen2:

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* Substitua o espaço reservado `<storage-account-name>` pelo nome da sua conta de armazenamento.

* Substitua `<container-name>` o espaço reservado pelo nome do seu recipiente.

* Substitua `<application-id>` `<client-secret>` os e espaços reservados pelo ID do aplicativo e o segredo do cliente que você coletou quando criou o principal do serviço.

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>Apêndice: Dividir dados em vários dispositivos da Data Box

Antes de mover seus dados para um dispositivo Data Box, você precisará baixar alguns scripts auxiliares, garantir que seus dados estão organizados para se encaixar em um dispositivo Data Box e excluir quaisquer arquivos desnecessários.

<a id="download-helper-scripts" />

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>Baixe scripts auxiliares e configure seu nó de borda para executá-los

1. A partir de sua borda ou nó de cabeça do seu cluster Hadoop no local, execute este comando:

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   Este comando clona o repositório GitHub que contém os scripts do ajudante.

2. Certifique-se de que o pacote [jq](https://stedolan.github.io/jq/) está instalado no seu computador local.

   ```bash
   
   sudo apt-get install jq
   ```

3. Instale o pacote [python Solicitações.](https://2.python-requests.org/en/master/)

   ```bash
   
   pip install requests
   ```

4. Defina as permissões de execução nos scripts necessários.

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>Certifique-se de que seus dados estão organizados para se encaixar em um dispositivo Data Box

Se o tamanho de seus dados exceder o tamanho de um único dispositivo Data Box, você pode dividir arquivos em grupos que você pode armazenar em vários dispositivos data box.

Se seus dados não excederem o tamanho de um dispositivo singe Data Box, você pode prosseguir para a próxima seção.

1. Com permissões elevadas, `generate-file-list` execute o script que você baixou seguindo a orientação na seção anterior.

   Aqui está uma descrição dos parâmetros de comando:

   ```
   sudo -u hdfs ./generate-file-list.py [-h] [-s DATABOX_SIZE] [-b FILELIST_BASENAME]
                    [-f LOG_CONFIG] [-l LOG_FILE]
                    [-v {DEBUG,INFO,WARNING,ERROR}]
                    path

   where:
   positional arguments:
   path                  The base HDFS path to process.

   optional arguments:
   -h, --help            show this help message and exit
   -s DATABOX_SIZE, --databox-size DATABOX_SIZE
                        The size of each Data Box in bytes.
   -b FILELIST_BASENAME, --filelist-basename FILELIST_BASENAME
                        The base name for the output filelists. Lists will be
                        named basename1, basename2, ... .
   -f LOG_CONFIG, --log-config LOG_CONFIG
                        The name of a configuration file for logging.
   -l LOG_FILE, --log-file LOG_FILE
                        Name of file to have log output written to (default is
                        stdout/stderr)
   -v {DEBUG,INFO,WARNING,ERROR}, --log-level {DEBUG,INFO,WARNING,ERROR}
                        Level of log information to output. Default is 'INFO'.
   ```

2. Copie as listas de arquivos gerados para o HDFS para que elas estejam acessíveis ao [trabalho do DistCp.](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>Excluir arquivos desnecessários

Você precisará excluir alguns diretórios do trabalho do DisCp. Por exemplo, exclua diretórios que contenham informações de estado que mantenham o cluster em execução.

No cluster Hadoop no local, onde você planeja iniciar o trabalho do DistCp, crie um arquivo que especifica a lista de diretórios que você deseja excluir.

Aqui está um exemplo:

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>Próximas etapas

Saiba como o Data Lake Storage Gen2 funciona com clusters HDInsight. Consulte [Usar o Azure Data Lake Storage Gen2 com clusters de HDInsight do Azure](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).
