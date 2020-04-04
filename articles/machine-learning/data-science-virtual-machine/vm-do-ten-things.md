---
title: Explorar dados e modelos no Windows
titleSuffix: Azure Data Science Virtual Machine
description: Execute tarefas de exploração e modelagem de dados na Máquina Virtual de Data Science do Windows.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 8da8cd7110cd17d0aadd52cce1263c7c0fcfdf5c
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632167"
---
# <a name="ten-things-you-can-do-on-the-windows-data-science-virtual-machine"></a>Dez coisas que você pode fazer na Máquina Virtual de Ciência de Dados do Windows

O Windows Data Science Virtual Machine (DSVM) é um poderoso ambiente de desenvolvimento de ciência de dados onde você pode executar tarefas de exploração e modelagem de dados. O ambiente vem já construído e empacotado com várias ferramentas populares de análise de dados que facilitam a sua análise para implantações locais, em nuvem ou híbridas. 

A DSVM trabalha em estreita colaboração com os serviços do Azure. Ele pode ler e processar dados que já estão armazenados no Azure, no Azure SQL Data Warehouse, Azure Data Lake, Azure Storage ou Azure Cosmos DB. Ele também pode aproveitar outras ferramentas de análise, como o Azure Machine Learning e o Azure Data Factory.

Neste artigo, você aprenderá a usar seu DSVM para executar tarefas de ciência de dados e interagir com outros serviços do Azure. Veja algumas tarefas que você pode realizar na DSVM:

- Explore dados e desenvolva modelos localmente no DSVM usando o Microsoft Machine Learning Server e o Python.
- Use um notebook Jupyter para experimentar seus dados em um navegador usando Python 2, Python 3 e Microsoft R. (o Microsoft R é uma versão pronta para a empresa do R projetada para desempenho.)
- Implantar modelos construídos através de R e Python no Azure Machine Learning para que os aplicativos clientes possam acessar seus modelos usando uma simples interface de serviço web.
- Administre seus recursos do Azure usando o portal Azure ou powerShell.
- Amplie seu espaço de armazenamento e compartilhe conjuntos de dados/código em larga escala em toda a sua equipe, criando um compartilhamento de Arquivos Azure como uma unidade montável em seu DSVM.
- Compartilhe código com sua equipe usando o GitHub. Acesse seu repositório usando os clientes Git pré-instalados: Git Bash e Git GUI.
- Acesse os serviços de análise e dados do Azure, como armazenamento Azure Blob, Azure Data Lake, Azure HDInsight (Hadoop), Azure Cosmos DB, Azure SQL Data Warehouse e Azure SQL Database.
- Crie relatórios e um painel usando a instância power bi desktop pré-instalada no DSVM e implante-os na nuvem.
- Dimensione dinamicamente seu DSVM para atender às necessidades do seu projeto.
- Instale ferramentas adicionais em sua máquina virtual.   

> [!NOTE]
> Taxas adicionais de uso se aplicam a muitos dos serviços de armazenamento e análise de dados listados neste artigo. Para obter detalhes, consulte a página de preços do [Azure.](https://azure.microsoft.com/pricing/)
> 
> 

## <a name="prerequisites"></a>Pré-requisitos

* É necessária uma assinatura do Azure. Você pode [se inscrever para uma avaliação gratuita](https://azure.microsoft.com/free/).
* Instruções para o provisionamento de uma Máquina Virtual de Ciência de Dados no portal Azure estão disponíveis na [Criação de uma máquina virtual](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>Explore dados e desenvolva modelos com o Microsoft Machine Learning Server
Você pode usar linguagens como R e Python para fazer a análise de dados diretamente na DSVM.

Para R, você pode usar um IDE como RStudio que pode ser encontrado no menu iniciar ou no desktop. Ou você pode usar Ferramentas R para O Visual Studio. A Microsoft forneceu bibliotecas adicionais em cima do CRAN R de código aberto para permitir análises escaláveis e a capacidade de analisar dados maiores do que o tamanho de memória permitido em análises em blocos paralelos. 

Para Python, você pode usar um IDE como o Visual Studio Community Edition, que tem a extensão PTVS (Ferramentas Python para Visual Studio) pré-instalada. Por padrão, apenas o Python 3.6, o ambiente Conda raiz, é configurado no PTVS. Para habilitar o Anaconda Python 2.7, dê as seguintes etapas:

1. Crie ambientes personalizados para cada versão indo para **ferramentas** > **Python Tools** > **Python Environments**e, em seguida, selecionando **+ Custom** in Visual Studio Community Edition.
1. Dê uma descrição e defina o caminho do prefixo do ambiente como **c:\anaconda\envs\python2** para Anaconda Python 2.7.
1. Selecione **'Detectar automaticamente'** > **para** salvar o ambiente.

Consulte a [documentação PTVS](https://aka.ms/ptvsdocs) para obter mais detalhes sobre como criar ambientes Python.

Agora você está configurado para criar um novo projeto Python. Vá para **Arquivo** > **Novo** > **Projeto** > **Python** e selecione o tipo de aplicativo Python que você está construindo. Você pode definir o ambiente Python para o projeto atual para a versão desejada (Python 2.7 ou 3.6) clicando com o botão direito do **mouse em ambientes Python** e, em seguida, selecionando **Adicionar/Remover Ambientes Python**. Você pode encontrar mais informações sobre como trabalhar com PTVS na documentação do [produto.](https://aka.ms/ptvsdocs)

## <a name="use-jupyter-notebooks"></a>Usar notebooks Jupyter
O Jupyter Notebook fornece um IDE baseado em navegador para exploração e modelagem de dados. Você pode usar Python 2, Python 3 ou R (tanto de código aberto quanto do Microsoft R Server) em um notebook Jupyter.

Para iniciar o Jupyter Notebook, selecione o ícone **Do notebook Jupyter** no menu **Iniciar** ou na área de trabalho. No prompt de comando DSVM, você ```jupyter notebook``` também pode executar o comando a partir do diretório onde você tem notebooks existentes ou onde você deseja criar novos notebooks.  

Depois de iniciar o Jupyter, você deve ver um diretório que contém alguns notebooks de exemplo que são pré-embalados no DSVM. Agora você pode:

* Selecione o caderno para ver o código.
* Execute cada célula selecionando Shift+Enter.
* Execute o notebook inteiro selecionando **Cell** > **Run**.
* Crie um novo notebook selecionando o ícone Jupyter (canto superior esquerdo), selecionando o botão **Novo** à direita e, em seguida, escolhendo o idioma do notebook (também conhecido como kernels).   

> [!NOTE]
> Atualmente, os núcleos Python 2.7, Python 3.6, R, Julia e PySpark em Jupyter são suportados. O kernel R suporta programação tanto em r de código aberto quanto em Microsoft R.   
> 
> 

Quando você está no notebook, você pode explorar seus dados, construir o modelo e testar o modelo usando sua escolha de bibliotecas.

## <a name="train-and-deploy-models-by-using-azure-machine-learning"></a>Treine e implante modelos usando o Azure Machine Learning
Depois de construir e validar seu modelo, o próximo passo é geralmente implantá-lo em produção. Esta etapa permite que seus aplicativos clientes invoque as previsões do modelo em tempo real ou em um modo de lote. O Machine Learning do Azure fornece um mecanismo para operacionalizar um modelo compilado em R ou Python.

Quando você operacionaliza seu modelo no Azure Machine Learning, um serviço web é exposto. Permite que os clientes façam chamadas REST que passam em parâmetros de entrada e recebam previsões do modelo como saídas.

### <a name="build-and-operationalize-python-models"></a>Construir e operacionalizar modelos Python
Aqui está um trecho de código desenvolvido em um notebook Python Jupyter que constrói um modelo simples usando a biblioteca scikit-learn:

```python
# IRIS classification
from sklearn import datasets
from sklearn import svm
clf = svm.SVC()
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf.fit(X, y)
```

O método usado para implantar seus modelos Python no Azure Machine Learning envolve a previsão do modelo em uma função e o decora com atributos fornecidos pela biblioteca Python de aprendizado de máquina azure pré-instalada. Os atributos denotam seu ID de espaço de trabalho de aprendizado de máquina do Azure, a chave aPI e os parâmetros de entrada e retorno.  

```python
from azureml import services
@services.publish(workspaceid, auth_token)
@services.types(sep_l=float, sep_w=float, pet_l=float, pet_w=float)
@services.returns(int)  # 0, or 1, or 2
def predictIris(sep_l, sep_w, pet_l, pet_w):
    inputArray = [sep_l, sep_w, pet_l, pet_w]


return clf.predict(inputArray)
```

Agora, um cliente pode fazer chamadas ao serviço Web. Os invólucros de conveniência constroem as solicitações de API REST. Aqui está o código de exemplo para consumir o serviço web:

```python
# Consume through web service URL and keys
from azureml import services
@services.service(url, api_key)
@services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
@services.returns(float)
def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
pass

IrisPredictor(3,2,3,4)
```

> [!NOTE]
> Atualmente, a biblioteca Azure Machine Learning é suportada apenas no Python 2.7.   
> 
> 

### <a name="build-and-operationalize-r-models"></a>Construir e operacionalizar modelos R
Você pode implantar modelos R construídos na Máquina Virtual de Data Science ou em outros lugares no Azure Machine Learning de uma maneira semelhante à forma como é feito para Python. Siga estas etapas:

1. Crie um arquivo settings.json para fornecer seu ID de espaço de trabalho e token de autenticação. 
2. Escreva um invólucro para a função de previsão do modelo.
3. Chame ```publishWebService``` a biblioteca azure Machine Learning para passar no invólucro da função.  

Use os seguintes trechos de procedimento e código para configurar, construir, publicar e consumir um modelo como um serviço web no Azure Machine Learning.

#### <a name="set-up"></a>Configuração

Crie um arquivo settings.json sob ```.azureml``` um diretório chamado sob seu diretório inicial. Digite os parâmetros do espaço de trabalho Azure Machine Learning.

Aqui está a estrutura do arquivo settings.json:

```json
{"workspace":{
"id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
"authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
}}
```

#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>Compilar um modelo em R e publicá-lo no Azure Machine Learning

```r
library(AzureML)
ws <- workspace(config="~/.azureml/settings.json")

if(!require("lme4")) install.packages("lme4")
library(lme4)
set.seed(1)
train <- sleepstudy[sample(nrow(sleepstudy), 120),]
m <- lm(Reaction ~ Days + Subject, data = train)

# Define a prediction function to publish based on the model:
sleepyPredict <- function(newdata){
        predict(m, newdata=newdata)
}

ep <- publishWebService(ws, fun = sleepyPredict, name="sleepy lm", inputSchema = sleepstudy, data.frame=TRUE)
```

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>Consumir o modelo implantado no Azure Machine Learning
Para consumir o modelo de um aplicativo cliente, use a biblioteca Azure Machine Learning para procurar o serviço web publicado pelo nome. Use `services` a chamada aPI para determinar o ponto final. Em seguida, basta chamar a função `consume` e passar o quadro de dados a ser previsto.

Use o seguinte código para consumir o modelo publicado como um serviço web azure Machine Learning:

```r
library(AzureML)
library(lme4)
ws <- workspace(config="~/.azureml/settings.json")

s <-  services(ws, name = "sleepy lm")
s <- tail(s, 1) # use the last published function, in case of duplicate function names

ep <- endpoints(ws, s)

# Try this out, and compare with raw data
ans = consume(ep, sleepstudy)$ans
```

Veja mais informações sobre [pacotes R no Machine Learning Studio](/azure/machine-learning/studio-module-reference/r-packages-supported-by-azure-machine-learning).

## <a name="manage-azure-resources"></a>Gerenciar recursos do Azure
O DSVM não permite apenas que você construa sua solução de análise localmente na máquina virtual. Ele também permite que você acesse serviços na plataforma de nuvem DoZure. O Azure fornece vários serviços de computação, armazenamento, análise de dados e outros serviços que você pode administrar e acessar a partir do seu DSVM.

Para administrar sua assinatura do Azure e recursos na nuvem, você tem duas opções:
+ Use seu navegador e vá para o [portal Azure](https://portal.azure.com).

+ Use scripts PowerShell. Execute o Azure PowerShell a partir de um atalho na área de trabalho ou no menu **Iniciar.** Consulte a documentação do [Microsoft Azure PowerShell](../../powershell-azure-resource-manager.md) para obter detalhes completos. 

## <a name="extend-storage-by-using-shared-file-systems"></a>Estender o armazenamento usando sistemas de arquivos compartilhados
Os cientistas de dados podem compartilhar grandes conjuntos de dados, códigos ou outros recursos dentro da equipe. O DSVM tem cerca de 45 GB de espaço disponível. Para estender seu armazenamento, você pode usar arquivos Azure e montá-lo em uma ou mais instâncias DSVM ou acessá-lo através de uma API REST. Você também pode usar o [portal Azure](../../virtual-machines/windows/attach-managed-disk-portal.md) ou usar [o Azure PowerShell](../../virtual-machines/windows/attach-disk-ps.md) para adicionar discos de dados extras dedicados. 

> [!NOTE]
> O espaço máximo no compartilhamento de Arquivos Azure é de 5 TB. O limite de tamanho para cada arquivo é de 1 TB. 

Você pode usar este script no Azure PowerShell para criar um compartilhamento de arquivos do Azure:

```powershell
# Authenticate to Azure.
Connect-AzAccount
# Select your subscription
Get-AzSubscription –SubscriptionName "<your subscription name>" | Select-AzSubscription
# Create a new resource group.
New-AzResourceGroup -Name <dsvmdatarg>
# Create a new storage account. You can reuse existing storage account if you want.
New-AzStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
# Set your current working storage account
Set-AzCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

# Create an Azure Files share
$s = New-AzStorageShare <<teamsharename>>
# Create a directory under the file share. You can give it any name
New-AzStorageDirectory -Share $s -Path <directory name>
# List the share to confirm that everything worked
Get-AzStorageFile -Share $s
```

Agora que você criou um compartilhamento de Arquivos Azure, você pode montá-lo em qualquer máquina virtual no Azure. Recomendamos que você coloque o VM no mesmo datacenter do Azure que a conta de armazenamento, para evitar taxas de latência e transferência de dados. Aqui estão os comandos Azure PowerShell para montar a unidade no DSVM:

```powershell
# Get the storage key of the storage account that has the Azure Files share from the Azure portal. Store it securely on the VM to avoid being prompted in the next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure Files share as drive Z on the VM. You can choose another drive letter if you want.
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

Agora, você pode acessar essa unidade como faria com qualquer unidade normal na VM.

## <a name="share-code-in-github"></a>Compartilhar código no GitHub
GitHub é um repositório de código onde você pode encontrar amostras de código e fontes para várias ferramentas usando tecnologias compartilhadas pela comunidade de desenvolvedores. Ele usa Git como a tecnologia para rastrear e armazenar versões dos arquivos de código. O GitHub também é uma plataforma onde você pode criar seu próprio repositório para armazenar o código e documentação compartilhados da sua equipe, implementar o controle de versão e controlar quem tem acesso a exibir e contribuir com código. 

Visite as [páginas de ajuda do GitHub](https://help.github.com/) para obter mais informações sobre como usar o Git. Você pode usar o GitHub como uma das maneiras de colaborar com sua equipe, usar o código desenvolvido pela comunidade e contribuir com o código de volta para a comunidade.

O DSVM vem carregado com ferramentas do cliente na linha de comando e na GUI para acessar o repositório GitHub. A ferramenta de linha de comando que funciona com o Git e o GitHub é chamada Git Bash. O Visual Studio está instalado no DSVM e possui as extensões Git. Você pode encontrar ícones para essas ferramentas no menu **Iniciar** e na área de trabalho.

Para baixar o código de um repositório GitHub, você usa o comando ```git clone```. Por exemplo, para baixar o repositório de ciência de dados publicado pela Microsoft no diretório atual, você pode executar o seguinte comando no Git Bash:

    git clone https://github.com/Azure/DataScienceVM.git

No Visual Studio, você pode fazer a mesma operação de clonagem. A captura de tela a seguir mostra como acessar as ferramentas Git e GitHub no Visual Studio:

![Captura de tela do Visual Studio com a conexão do GitHub exibida](./media/vm-do-ten-things/VSGit.PNG)

Você pode encontrar mais informações sobre o uso do Git para trabalhar com seu repositório GitHub a partir de recursos disponíveis no github.com. O [roteiro](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) é uma referência útil.

## <a name="access-azure-data-and-analytics-services"></a>Acesse os serviços de análise e dados do Azure
### <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure
O armazenamento Azure Blob é um serviço de armazenamento em nuvem confiável e econômico para dados grandes e pequenos. Esta seção descreve como você pode mover dados para o armazenamento Blob e acessar dados armazenados em uma bolha do Azure.

#### <a name="prerequisites"></a>Pré-requisitos

* Crie sua conta de armazenamento Azure Blob a partir do [portal Azure](https://portal.azure.com).

   ![Captura de tela do processo de criação de contas de armazenamento no portal Azure](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Confirme se a ferramenta AzCopy da linha ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```de comando está pré-instalada: . O diretório que contém azcopy.exe já está na variável de ambiente PATH, para que você possa evitar digitar o caminho de comando completo ao executar esta ferramenta. Para obter mais informações sobre a ferramenta AzCopy, consulte a [documentação do AzCopy](../../storage/common/storage-use-azcopy.md).
* Inicie o Gerenciador de Armazenamento do Azure. Você pode baixá-lo na [página do Storage Explorer](https://storageexplorer.com/). 

   ![Captura de tela do Azure Storage Explorer acessando uma conta de armazenamento](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>Mova dados de uma VM para uma bolha do Azure: AzCopy

Para mover dados entre seus arquivos locais e o armazenamento Blob, você pode usar o AzCopy na linha de comando ou no PowerShell:

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

Substitua **C:\myfolder** pelo caminho em que seu arquivo está armazenado, **minha conta de armazenamento** com o nome da conta de armazenamento Blob, meu **contêiner** com o nome do contêiner e a chave da conta **de armazenamento** com a chave de acesso de armazenamento Blob. Você pode encontrar suas credenciais de conta de armazenamento no [portal Azure](https://portal.azure.com).

Execute o comando AzCopy no PowerShell ou a partir de um prompt de comando. Aqui está um exemplo de uso do comando AzCopy:

```powershell
# Copy *.sql from a local machine to an Azure blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from an Azure blob container to a local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

Depois de executar o comando AzCopy para copiar para uma bolha do Azure, seu arquivo aparecerá no Azure Storage Explorer.

![Captura de tela da conta de armazenamento, exibindo o arquivo CSV enviado](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azure-storage-explorer"></a>Mova dados de uma VM para uma bolha do Azure: Azure Storage Explorer

Você também pode carregar dados do arquivo local em sua VM usando o Azure Storage Explorer:

* Para carregar dados em um contêiner, selecione o contêiner de destino e selecione o botão **Upload.** ![Captura de tela do botão de upload no Azure Storage Explorer](./media/vm-do-ten-things/storage-accounts.png)
* Selecione a elipse (**...**) à direita da caixa **Arquivos,** selecione um ou vários arquivos para carregar no sistema de arquivos e selecione **Upload** para começar a carregar os arquivos. ![Captura de tela da caixa de diálogo Upload arquivos](./media/vm-do-ten-things/upload-files-to-blob.png)

#### <a name="read-data-from-an-azure-blob-machine-learning-reader-module"></a>Leia dados de um blob Azure: Módulo de leitor de aprendizado de máquina

No Azure Machine Learning Studio, você pode usar o módulo Importar dados para ler dados da sua bolha.

![Captura de tela do módulo Importar Dados no Machine Learning Studio](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

#### <a name="read-data-from-an-azure-blob-python-odbc"></a>Leia dados de uma bolha do Azure: Python ODBC

Você pode usar a biblioteca BlobService para ler dados diretamente de uma bolha em um notebook Jupyter ou em um programa Python.

Primeiro, importe os pacotes necessários:

```python
import pandas as pd
from pandas import Series, DataFrame
import numpy as np
import matplotlib.pyplot as plt
from time import time
import pyodbc
import os
from azure.storage.blob import BlobService
import tables
import time
import zipfile
import random
```

Em seguida, conecte as credenciais da conta de armazenamento Blob e leia os dados da bolha:

```python
CONTAINERNAME = 'xxx'
STORAGEACCOUNTNAME = 'xxxx'
STORAGEACCOUNTKEY = 'xxxxxxxxxxxxxxxx'
BLOBNAME = 'nyctaxidataset/nyctaxitrip/trip_data_1.csv'
localfilename = 'trip_data_1.csv'
LOCALDIRECTORY = os.getcwd()
LOCALFILE =  os.path.join(LOCALDIRECTORY, localfilename)

#download from blob
t1 = time.time()
blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILE)
t2 = time.time()
print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))

#unzip downloaded files if needed
#with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
#    z.extractall(LOCALDIRECTORY)

df1 = pd.read_csv(LOCALFILE, header=0)
df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
print 'the size of the data is: %d rows and  %d columns' % df1.shape
```

Os dados são lidos como um quadro de dados:

![Captura de tela das primeiras 10 linhas de dados](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
O Azure Data Lake Storage é um repositório de hiperescala para cargas de trabalho de análise de big data e é compatível com o Hadoop Distributed File System (HDFS). Ele funciona com Hadoop, Spark e Azure Data Lake Analytics. Nesta seção, você aprenderá como mover dados para o Azure Data Lake Storage e executar análises usando o Azure Data Lake Analytics.

#### <a name="prerequisites"></a>Pré-requisitos

* Crie a instância do Azure Data Lake Analytics no [portal Azure](https://portal.azure.com).

   ![Captura de tela da criação de uma instância do Data Lake Analytics a partir do portal Azure](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* O [Azure Data Lake e stream analytics Tools para visual studio plug-in](https://www.microsoft.com/download/details.aspx?id=49504) já está instalado no Visual Studio Community Edition na máquina virtual. Depois de iniciar o Visual Studio e entrar na sua assinatura do Azure, você deve ver sua conta e armazenamento do Azure Data Analytics no painel esquerdo do Visual Studio.

   ![Captura de tela das ferramentas do Data Lake para Data Lake no Visual Studio](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

#### <a name="move-data-from-a-vm-to-data-lake-azure-data-lake-explorer"></a>Mova dados de uma VM para Data Lake: Azure Data Lake Explorer

Você pode usar o Azure Data Lake Explorer para [carregar dados dos arquivos locais em sua máquina virtual para o Data Lake Storage](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

Você também pode construir um pipeline de dados para operacionalizar sua movimentação de dados para ou a partir do Azure Data Lake usando [o Azure Data Factory](https://azure.microsoft.com/services/data-factory/). [Este artigo](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) orienta você através das etapas para construir os pipelines de dados.

#### <a name="read-data-from-an-azure-blob-to-data-lake-u-sql"></a>Leia dados de uma bolha do Azure para Data Lake: U-SQL

Se seus dados residirem no armazenamento Azure Blob, você poderá ler diretamente dados de uma bolha do Azure em uma consulta U-SQL. Antes de compor sua consulta U-SQL, certifique-se de que sua conta de armazenamento Blob esteja vinculada à instância do Lago de Dados do Azure. Vá ao portal do Azure, encontre o painel do Azure Data Lake Analytics, selecione Adicionar fonte de **dados,** selecione um tipo de armazenamento do **Azure Storage**e conecte o nome e a chave da sua conta de armazenamento Azure. Em seguida, você pode referenciar os dados armazenados na conta de armazenamento.

![Captura de tela da caixa de diálogo Adicionar fonte de dados](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

No Visual Studio, você pode ler dados do armazenamento Blob, manipular dados, projetar recursos e enviar os dados resultantes para o armazenamento Azure Data Lake ou Azure Blob. Ao fazer referência aos dados no armazenamento Blob, use **wasb://**. Ao fazer referência aos dados no Lago de Dados Do Azure, use **swbhdfs://**.

Você pode usar as seguintes consultas U-SQL no Visual Studio:

```usql
@a =
    EXTRACT medallion string,
            hack_license string,
            vendor_id string,
            rate_code string,
            store_and_fwd_flag string,
            pickup_datetime string,
            dropoff_datetime string,
            passenger_count int,
            trip_time_in_secs double,
            trip_distance double,
            pickup_longitude string,
            pickup_latitude string,
            dropoff_longitude string,
            dropoff_latitude string

    FROM "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Input Data File Name>"
    USING Extractors.Csv();

@b =
    SELECT vendor_id,
    COUNT(medallion) AS cnt_medallion,
    SUM(passenger_count) AS cnt_passenger,
    AVG(trip_distance) AS avg_trip_dist,
    MIN(trip_distance) AS min_trip_dist,
    MAX(trip_distance) AS max_trip_dist,
    AVG(trip_time_in_secs) AS avg_trip_time
    FROM @a
    GROUP BY vendor_id;

OUTPUT @b   
TO "swebhdfs://<Azure Data Lake Storage Account Name>.azuredatalakestore.net/<Folder Name>/<Output Data File Name>"
USING Outputters.Csv();

OUTPUT @b   
TO "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Output Data File Name>"
USING Outputters.Csv();
```

Depois que sua consulta é enviada ao servidor, um diagrama mostra o status do seu trabalho.

![Captura de tela do diagrama de status do trabalho](./media/vm-do-ten-things/USQL_Job_Status.PNG)

#### <a name="query-data-in-data-lake-u-sql"></a>Dados da consulta no Data Lake: U-SQL

Depois que o conjunto de dados for ingerido no Azure Data Lake, você pode usar o [idioma U-SQL](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) para consultar e explorar os dados. O idioma U-SQL é semelhante ao T-SQL, mas combina alguns recursos de C# para que os usuários possam escrever módulos personalizados e funções definidas pelo usuário. Você pode usar os scripts da etapa anterior.

Depois que a consulta é submetida ao servidor, tripdata_summary. CSV aparece no Azure Data Lake Explorer. Você pode visualizar os dados clicando com o botão direito do mouse no arquivo.

![Captura de tela do arquivo CSV no Data Lake Explorer](./media/vm-do-ten-things/USQL_create_summary.png)

As informações do arquivo são exibidas:

![Captura de tela das informações de resumo do arquivo](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>Clusters HDInsight Hadoop
O Azure HDInsight é um serviço gerenciado do Apache Hadoop, Spark, HBase e Storm na nuvem. Você pode trabalhar facilmente com clusters Azure HDInsight a partir da Máquina Virtual de Ciência de Dados.

#### <a name="prerequisites"></a>Pré-requisitos

* Crie sua conta de armazenamento Azure Blob a partir do [portal Azure](https://portal.azure.com). Essa conta de armazenamento é usada para armazenar dados dos clusters HDInsight.

   ![Captura de tela da criação de uma conta de armazenamento do portal Azure](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Personalize os clusters Azure HDInsight Hadoop do [portal Azure](../team-data-science-process/customize-hadoop-cluster.md).
  
   Vincule a conta de armazenamento criada com seu cluster HDInsight quando for criada. Essa conta de armazenamento é usada para acessar dados que podem ser processados dentro do cluster.

   ![Seleções para vincular a conta de armazenamento criada com um cluster HDInsight](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* Habilite o acesso remoto da área de trabalho ao nó principal do cluster depois de criado. Lembre-se das credenciais de acesso remoto que você especifica aqui, porque você precisará delas no procedimento subsequente.

   ![Botão de desktop remoto para habilitar acesso remoto ao cluster HDInsight](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Criar um Workspace de Azure Machine Learning. Seus experimentos de Aprendizado de Máquina são armazenados neste espaço de trabalho de Aprendizado de Máquina. Selecione as opções destacadas no portal, conforme mostrado na captura de tela a seguir:

   ![Criar um workspace do Azure Machine Learning](./media/vm-do-ten-things/Create_ML_Space.PNG)

* Digite os parâmetros do seu espaço de trabalho.

   ![Insira os parâmetros do Workspace do Machine Learning](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* Faça upload de dados usando o IPython Notebook. Importe pacotes necessários, conecte credenciais, crie um banco de dados em sua conta de armazenamento e, em seguida, carregue dados em clusters HDI.

```python
# Import required packages
import pyodbc
import time as time
import json
import os
import urllib
import urllib2
import warnings
import re
import pandas as pd
import matplotlib.pyplot as plt
from azure.storage.blob import BlobService
warnings.filterwarnings("ignore", category=UserWarning, module='urllib2')


# Create the connection to Hive by using ODBC
SERVER_NAME = 'xxx.azurehdinsight.net'
DATABASE_NAME = 'nyctaxidb'
USERID = 'xxx'
PASSWORD = 'xxxx'
DB_DRIVER = 'Microsoft Hive ODBC Driver'
driver = 'DRIVER={' + DB_DRIVER + '}'
server = 'Host=' + SERVER_NAME + ';Port=443'
database = 'Schema=' + DATABASE_NAME
hiveserv = 'HiveServerType=2'
auth = 'AuthMech=6'
uid = 'UID=' + USERID
pwd = 'PWD=' + PASSWORD
CONNECTION_STRING = ';'.join(
    [driver, server, database, hiveserv, auth, uid, pwd])
connection = pyodbc.connect(CONNECTION_STRING, autocommit=True)
cursor = connection.cursor()


# Create the Hive database and tables
queryString = "create database if not exists nyctaxidb;"
cursor.execute(queryString)

queryString = """
                create external table if not exists nyctaxidb.trip
                (
                    medallion string,
                    hack_license string,
                    vendor_id string,
                    rate_code string,
                    store_and_fwd_flag string,
                    pickup_datetime string,
                    dropoff_datetime string,
                    passenger_count int,
                    trip_time_in_secs double,
                    trip_distance double,
                    pickup_longitude double,
                    pickup_latitude double,
                    dropoff_longitude double,
                    dropoff_latitude double)  
                PARTITIONED BY (month int)
                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');
            """
cursor.execute(queryString)

queryString = """
                create external table if not exists nyctaxidb.fare
                (
                    medallion string,
                    hack_license string,
                    vendor_id string,
                    pickup_datetime string,
                    payment_type string,
                    fare_amount double,
                    surcharge double,
                    mta_tax double,
                    tip_amount double,
                    tolls_amount double,
                    total_amount double)
                PARTITIONED BY (month int)
                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');
            """
cursor.execute(queryString)


# Upload data from Blob storage to an HDI cluster
for i in range(1, 13):
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxitripraw2/trip_data_%d.csv' INTO TABLE nyctaxidb2.trip PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxifareraw2/trip_fare_%d.csv' INTO TABLE nyctaxidb2.fare PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
```

Alternativamente, você pode seguir [este passo a passo](../team-data-science-process/hive-walkthrough.md) para carregar dados do NYC Taxi para o cluster HDI. As principais etapas incluem:
  
* Use o AzCopy para baixar CSVs com zíper da bolha pública para sua pasta local.
* Use o AzCopy para carregar CSVs sem zíper da pasta local para um cluster HDI.
* Faça login no nó principal do cluster Hadoop e prepare-se para análise exploratória de dados.

Depois que os dados forem carregados no cluster HDI, você pode verificar seus dados no Azure Storage Explorer. E o banco de dados nyctaxidb foi criado no cluster HDI.

#### <a name="data-exploration-hive-queries-in-python"></a>Exploração de dados: consultas Hive no Python

Como os dados estão em um cluster Hadoop, você pode usar o pacote pyodbc para se conectar a clusters hadoop e bancos de dados de consulta usando hive para fazer exploração e engenharia de recursos. Você pode visualizar as tabelas existentes que você criou na etapa pré-requisito.

```python
queryString = """
    show tables in nyctaxidb2;
    """
pd.read_sql(queryString, connection)
```

![Exibir tabelas existentes](./media/vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

Vamos examinar o número de registros em cada mês e as frequências de gorjetas na tabela de corridas:

```python
queryString = """
    select month, count(*) from nyctaxidb.trip group by month;
    """
results = pd.read_sql(queryString,connection)

%matplotlib inline

results.columns = ['month', 'trip_count']
df = results.copy()
df.index = df['month']
df['trip_count'].plot(kind='bar')
```

![Gráfico do número de registros a cada mês](./media/vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)

```python
queryString = """
    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;
    """
results = pd.read_sql(queryString, connection)

results.columns = ['tipped', 'trip_count']
df = results.copy()
df.index = df['tipped']
df['trip_count'].plot(kind='bar')
```

![Gráfico das frequências de dicas](./media/vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

Você também pode calcular a distância entre o local de retirada e o local de entrega e, em seguida, compará-lo com a distância da viagem.

```python
queryString = """
                select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
                    3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
                    *radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
                    *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
                    /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
                    +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*
                    pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance
                    from nyctaxidb.trip
                    where month=1
                        and pickup_longitude between -90 and -30
                        and pickup_latitude between 30 and 90
                        and dropoff_longitude between -90 and -30
                        and dropoff_latitude between 30 and 90;
            """
results = pd.read_sql(queryString, connection)
results.head(5)
```

![Principais filas da mesa de coleta e entrega](./media/vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

```python
results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                   'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
df = results.loc[results['trip_distance'] <= 100]  # remove outliers
df = df.loc[df['direct_distance'] <= 100]  # remove outliers
plt.scatter(df['direct_distance'], df['trip_distance'])
```

![Gráfico da distância de retirada/entrega à distância da viagem](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

Agora vamos preparar um conjunto de dados (1 por cento) para modelagem. Você pode usar esses dados no módulo de leitor de Aprendizado de Máquina.

```python
queryString = """
create  table if not exists nyctaxi_downsampled_dataset_testNEW (
medallion string,
hack_license string,
vendor_id string,
rate_code string,
store_and_fwd_flag string,
pickup_datetime string,
dropoff_datetime string,
pickup_hour string,
pickup_week string,
weekday string,
passenger_count int,
trip_time_in_secs double,
trip_distance double,
pickup_longitude double,
pickup_latitude double,
dropoff_longitude double,
dropoff_latitude double,
direct_distance double,
payment_type string,
fare_amount double,
surcharge double,
mta_tax double,
tip_amount double,
tolls_amount double,
total_amount double,
tipped string,
tip_class string
)
row format delimited fields terminated by ','
lines terminated by '\\n'
stored as textfile;
"""
cursor.execute(queryString)
```

Agora insira o conteúdo da parte na tabela interna anterior.

```python
queryString = """
insert overwrite table nyctaxi_downsampled_dataset_testNEW
select
t.medallion,
t.hack_license,
t.vendor_id,
t.rate_code,
t.store_and_fwd_flag,
t.pickup_datetime,
t.dropoff_datetime,
hour(t.pickup_datetime) as pickup_hour,
weekofyear(t.pickup_datetime) as pickup_week,
from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
t.passenger_count,
t.trip_time_in_secs,
t.trip_distance,
t.pickup_longitude,
t.pickup_latitude,
t.dropoff_longitude,
t.dropoff_latitude,
t.direct_distance,
f.payment_type,
f.fare_amount,
f.surcharge,
f.mta_tax,
f.tip_amount,
f.tolls_amount,
f.total_amount,
if(tip_amount>0,1,0) as tipped,
if(tip_amount=0,0,
if(tip_amount>0 and tip_amount<=5,1,
if(tip_amount>5 and tip_amount<=10,2,
if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class
from
(
select
medallion,
hack_license,
vendor_id,
rate_code,
store_and_fwd_flag,
pickup_datetime,
dropoff_datetime,
passenger_count,
trip_time_in_secs,
trip_distance,
pickup_longitude,
pickup_latitude,
dropoff_longitude,
dropoff_latitude,
3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
/sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
+cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance,
rand() as sample_key

from trip
where pickup_latitude between 30 and 90
    and pickup_longitude between -90 and -30
    and dropoff_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
)t
join
(
select
medallion,
hack_license,
vendor_id,
pickup_datetime,
payment_type,
fare_amount,
surcharge,
mta_tax,
tip_amount,
tolls_amount,
total_amount
from fare
)f
on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
where t.sample_key<=0.01
"""
cursor.execute(queryString)
```

Depois de um tempo, você pode ver que os dados foram carregados em clusters Hadoop:

```python
queryString = """
    select * from nyctaxi_downsampled_dataset limit 10;
    """
cursor.execute(queryString)
pd.read_sql(queryString, connection)
```

![Linhas superiores de dados da tabela](./media/vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

### <a name="azure-sql-data-warehouse-and-databases"></a>Azure SQL Data Warehouse e bancos de dados
O Azure SQL Data Warehouse é um data warehouse elástico como um serviço com uma experiência de SQL Server de classe empresarial.

Você pode provisionar seu data warehouse Azure SQL seguindo as instruções [deste artigo](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md). Depois de provisionar seu data warehouse SQL, você pode usar [este passo a passo](../team-data-science-process/sqldw-walkthrough.md) para fazer upload, exploração e modelagem de dados usando dados dentro do data warehouse SQL.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
O Azure Cosmos DB é um banco de dados NoSQL na nuvem. Você pode usá-lo para trabalhar com documentos como JSON, e para armazenar e consultar os documentos.

Use as seguintes etapas pré-requisitos para acessar o Azure Cosmos DB a partir do DSVM:

1. O Azure Cosmos DB Python SDK já está instalado no DSVM. Para atualizá-lo, execute ```pip install pydocumentdb --upgrade``` a partir de um prompt de comando.
2. Crie uma conta e um banco de dados do Azure Cosmos DB a partir do [portal Azure](https://portal.azure.com).
3. Baixe a ferramenta de migração de dados Do Azure Cosmos DB do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) e extraia para um diretório de sua escolha.
4. Importe os dados JSON (dados vulcânicos) armazenados em uma [bolha pública](https://cahandson.blob.core.windows.net/samples/volcano.json) no Azure Cosmos DB com os seguintes parâmetros de comando para a ferramenta de migração. (Use dtui.exe do diretório onde você instalou a Ferramenta de Migração de Dados Azure Cosmos DB.) Digite a origem e o local de destino com esses parâmetros:
   
    `/s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

Depois de importar os dados, você pode ir ao Jupyter e abrir o notebook intitulado *DocumentDBSample*. Ele contém código Python para acessar o Azure Cosmos DB e fazer algumas consultas básicas. Você pode aprender mais sobre o Azure Cosmos DB visitando a página de [documentação](https://docs.microsoft.com/azure/cosmos-db/)do serviço .

## <a name="use-power-bi-reports-and-dashboards"></a>Use relatórios e painéis de power bi 
Você pode visualizar o arquivo Volcano JSON do exemplo anterior do Azure Cosmos DB no Power BI Desktop para obter insights visuais sobre os dados. As etapas detalhadas estão disponíveis no [artigo sobre o Power BI](../../cosmos-db/powerbi-visualize.md). Aqui estão as etapas de alto nível:

1. Abra o Power BI Desktop e selecione **Obter dados**. Especifique `https://cahandson.blob.core.windows.net/samples/volcano.json`a URL como: .
2. Você deve ver os registros JSON importados como uma lista. Converta a lista em uma tabela para que o Power BI possa trabalhar com ela.
4. Expanda as colunas selecionando o ícone expandir (seta).
5. Observe que o local é um campo **De** Registro. Expanda o registro e selecione apenas as coordenadas. **Coordenada** é uma coluna de lista.
6. Adicione uma nova coluna para converter a coluna de coordenadas da lista em uma coluna **LatLong** separada por comuma comuma. Concatenaos os dois elementos no campo ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```de lista de coordenadas usando a fórmula .
7. Converta a coluna **Elevação** em decimal e selecione os botões **Fechar** e **Aplicar.**

Em vez de etapas anteriores, você pode colar o seguinte código. Ele roteirizar as etapas usadas no Advanced Editor in Power BI para escrever as transformações de dados em um idioma de consulta.

```pqfl
let
    Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
    #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
    #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
    #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
    #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
in
    #"Changed Type"
```

Agora você tem os dados no modelo de dados do Power BI. A instância do Power BI Desktop deve aparecer da seguinte forma:

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

Você pode começar a construir relatórios e visualizações usando o modelo de dados. Você pode seguir as etapas [deste artigo do Power BI](../../cosmos-db/powerbi-visualize.md#build-the-reports) para construir um relatório.

## <a name="scale-the-dsvm-dynamically"></a>Dimensione o DSVM dinamicamente 
Você pode escalar o DSVM para atender às necessidades do seu projeto. Se você não precisar usar a VM à noite ou nos fins de semana, você pode desligar a VM do [portal Azure](https://portal.azure.com).

> [!NOTE]
> Você incorre em taxas de computação se usar apenas o botão de desligamento do sistema operacional na VM.  
> 
> 

Você pode precisar lidar com alguma análise em larga escala e precisar de mais capacidade de CPU, memória ou disco. Se assim for, você pode encontrar uma escolha de tamanhos de VM em termos de núcleos de CPU, instâncias baseadas em GPU para aprendizado profundo, capacidade de memória e tipos de disco (incluindo drives de estado sólido) que atendam às suas necessidades computacionais e orçamentárias. A lista completa de VMs, juntamente com seus preços de computação por hora, está disponível na página [de preços da Azure Virtual Machines.](https://azure.microsoft.com/pricing/details/virtual-machines/)

Da mesma forma, sua necessidade de capacidade de processamento de VM pode diminuir. (Por exemplo: você moveu uma carga de trabalho principal para um cluster Hadoop ou Spark.) Em seguida, você pode reduzir o cluster a partir do [portal Azure](https://portal.azure.com) e ir para as configurações da sua instância de VM. 

## <a name="add-more-tools"></a>Adicione mais ferramentas
Ferramentas pré-incorporadas ao DSVM podem atender a muitas necessidades comuns de análise de dados. Isso economiza tempo porque você não precisa instalar e configurar seus ambientes um a um. Ele também economiza dinheiro, porque você paga apenas pelos recursos que você usa.

Você pode usar outros serviços de dados e análises do Azure perfilados neste artigo para melhorar seu ambiente de análise. Em alguns casos, você pode precisar de ferramentas adicionais, incluindo algumas ferramentas de parceiros proprietários. Você tem acesso administrativo completo na máquina virtual para instalar novas ferramentas que você precisa. Também é possível instalar pacotes adicionais no Python e no R que não foram pré-instalados. Para Python, você ```conda``` pode ```pip```usar qualquer um ou . Para R, você ```install.packages()``` pode usar no console R, ou usar o IDE e selecionar **Pacotes** > **instalar pacotes**.

## <a name="deep-learning"></a>Aprendizado

Além das amostras baseadas em framework, você pode obter um conjunto de passos abrangentes que foram validados no DSVM. Esses passoados ajudam você a iniciar o desenvolvimento de aplicativos de aprendizagem profunda em domínios como a compreensão de imagem e texto/idioma.   


- [Executando redes neurais em diferentes estruturas](https://github.com/ilkarman/DeepLearningFrameworks): Este passo a passo mostra como migrar código de uma estrutura para outra. Ele também demonstra como comparar modelos e desempenho em tempo de execução entre frameworks. 

- [Um guia de instruções para criar uma solução de ponta a ponta para detectar produtos em imagens](https://github.com/Azure/cortana-intelligence-product-detection-from-images): a detecção de imagem é uma técnica que pode localizar e classificar objetos em imagens. Essa tecnologia tem o potencial de trazer enormes recompensas em muitos domínios de negócios da vida real. Por exemplo, os varejistas podem usar essa técnica para determinar qual produto um cliente retirou da prateleira. Por sua vez, essas informações ajudam as lojas a gerenciar o estoque do produtos. 

- [Aprendizado profundo para áudio](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/): Este tutorial mostra como treinar um modelo de aprendizagem profunda para detecção de eventos de áudio no [conjunto de dados de sons urbanos](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html). Ele também fornece uma visão geral de como trabalhar com dados de áudio.

- [Classificação de documentos de texto](https://github.com/anargyri/lstm_han): Este passo a passo demonstra como construir e treinar duas arquiteturas de rede neural: Rede de Atenção Hierárquica e Rede de Memória de Longo Prazo (LSTM). Essas redes neurais usam a API Keras para aprendizagem profunda para classificar documentos de texto. Keras é um front end para três dos quadros de aprendizagem profunda mais populares: Microsoft Cognitive Toolkit, TensorFlow e Theano.

## <a name="summary"></a>Resumo
Este artigo descreveu algumas das coisas que você pode fazer na Máquina Virtual de Data Science da Microsoft. Há muito mais coisas que você pode fazer para tornar o DSVM um ambiente de análise eficaz.

