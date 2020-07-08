---
title: Explorar dados e modelo no Windows
titleSuffix: Azure Data Science Virtual Machine
description: Executar tarefas de exploração e modelagem de dados no Máquina Virtual de Ciência de Dados do Windows.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
ms.custom: tracking-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 7d9aced42efefc8651605be44f0091b2f4f2815e
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2020
ms.locfileid: "85959272"
---
# <a name="ten-things-you-can-do-on-the-windows-data-science-virtual-machine"></a>Dez coisas que você pode fazer na Máquina Virtual de Ciência de Dados do Windows

O Windows Máquina Virtual de Ciência de Dados (DSVM) é um poderoso ambiente de desenvolvimento de ciência de dados, no qual você pode executar tarefas de exploração e modelagem de dados. O ambiente já vem criado e agrupado com várias ferramentas de análise de dados populares que facilitam a introdução à análise de implantações locais, na nuvem ou híbridas. 

O DSVM trabalha junto com os serviços do Azure. Ele pode ler e processar dados que já estão armazenados no Azure, no Azure SQL Data Warehouse, Azure Data Lake, armazenamento do Azure ou Azure Cosmos DB. Ele também pode aproveitar outras ferramentas de análise, como Azure Machine Learning e Azure Data Factory.

Neste artigo, você aprenderá a usar seu DSVM para executar tarefas de ciência de dados e interagir com outros serviços do Azure. Veja algumas tarefas que você pode realizar na DSVM:

- Explore dados e desenvolva modelos localmente no DSVM usando Microsoft Machine Learning Server e Python.
- Use um notebook Jupyter para experimentar seus dados em um navegador usando o Python 2, o Python 3 e o Microsoft R. (o Microsoft R é uma versão do R pronta para a empresa, projetada para o desempenho.)
- Implante modelos criados por meio de R e Python no Azure Machine Learning para que os aplicativos cliente possam acessar seus modelos usando uma interface de serviço Web simples.
- Administre seus recursos do Azure usando o portal do Azure ou o PowerShell.
- Estenda seu espaço de armazenamento e compartilhe conjuntos de arquivos/código em grande escala em toda a sua equipe criando um compartilhamento de arquivo do Azure como uma unidade montável em seu DSVM.
- Compartilhe código com sua equipe usando o GitHub. Acesse seu repositório usando os clientes git pré-instalados: git bash e git GUI.
- Acesse dados do Azure e serviços de análise como armazenamento de BLOBs do Azure, Azure Data Lake, Azure Cosmos DB, Azure SQL Data Warehouse e banco de dados SQL do Azure.
- Crie relatórios e um painel usando a instância de Power BI Desktop pré-instalada no DSVM e implante-os na nuvem.
- Dimensione dinamicamente seu DSVM para atender às necessidades do seu projeto.
- Instale ferramentas adicionais em sua máquina virtual.   

> [!NOTE]
> Encargos adicionais de uso se aplicam a muitos dos serviços de armazenamento e análise de dados listados neste artigo. Para obter detalhes, consulte a página de [preços do Azure](https://azure.microsoft.com/pricing/) .
> 
> 

## <a name="prerequisites"></a>Pré-requisitos

* É necessária uma assinatura do Azure. Você pode [se inscrever para uma avaliação gratuita](https://azure.microsoft.com/free/).
* As instruções para o provisionamento de um Máquina Virtual de Ciência de Dados no portal do Azure estão disponíveis na [criação de uma máquina virtual](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>Explorar dados e desenvolver modelos com Microsoft Machine Learning Server
Você pode usar linguagens como R e Python para fazer a análise de dados diretamente na DSVM.

Para o R, você pode usar um IDE como RStudio que pode ser encontrado no menu iniciar ou na área de trabalho. Ou você pode usar Ferramentas do R para Visual Studio. A Microsoft forneceu bibliotecas adicionais sobre o CRAN R de software livre para habilitar a análise escalonável e a capacidade de analisar dados maiores do que o tamanho da memória permitido na análise em partes paralela. 

Para Python, você pode usar um IDE como o Visual Studio Community Edition, que tem a extensão PTVS (Ferramentas Python para Visual Studio) pré-instalada. Por padrão, somente o Python 3,6, o ambiente de Conda raiz, é configurado em PTVS. Para habilitar o Anaconda Python 2,7, execute as seguintes etapas:

1. Crie ambientes personalizados para cada versão acessando **ferramentas**  >  **python**  >  **ambientes Python**e, em seguida, selecionando **+ personalizado** no Visual Studio Community Edition.
1. Forneça uma descrição e defina o caminho do prefixo do ambiente como **c:\anaconda\envs\python2** para anaconda Python 2,7.
1. Selecione **detecção automática**  >  **aplicar** para salvar o ambiente.

Consulte a [documentação do PTVS](https://aka.ms/ptvsdocs) para obter mais detalhes sobre como criar ambientes do Python.

Agora você está pronto para criar um novo projeto Python. Vá para **arquivo**  >  **novo**  >  **projeto**  >  **Python** e selecione o tipo de aplicativo Python que você está criando. Você pode definir o ambiente do Python para o projeto atual para a versão desejada (Python 2,7 ou 3,6) clicando com o botão direito do mouse em **ambientes do Python** e selecionando **Adicionar/remover ambientes Python**. Você pode encontrar mais informações sobre como trabalhar com o PTVS na [documentação do produto](https://aka.ms/ptvsdocs).

## <a name="use-jupyter-notebooks"></a>Usar notebooks Jupyter
O Jupyter Notebook fornece um IDE baseado em navegador para exploração e modelagem de dados. Você pode usar o Python 2, Python 3 ou R (software livre e Microsoft R Server) em um notebook Jupyter.

Para iniciar o Jupyter Notebook, selecione o ícone de **Jupyter Notebook** no menu **Iniciar** ou na área de trabalho. No prompt de comando do DSVM, você também pode executar o comando ```jupyter notebook``` do diretório em que você tem blocos de anotações existentes ou onde deseja criar novos blocos de anotações.  

Depois de iniciar o Jupyter, navegue até o `/notebooks` diretório de blocos de anotações de exemplo que são previamente empacotados no DSVM. Agora você pode:

* Selecione o bloco de anotações para ver o código.
* Execute cada célula selecionando Shift + Enter.
* Execute o bloco de anotações inteiro selecionando a execução da **célula**  >  **Run**.
* Crie um novo bloco de anotações selecionando o ícone de Jupyter (canto superior esquerdo), selecionando o botão **novo** à direita e escolhendo o idioma do notebook (também conhecido como kernels).   

> [!NOTE]
> Atualmente, há suporte para os kernels Python 2,7, Python 3,6, R, Julia e PySpark em Jupyter. O kernel do R dá suporte à programação no R de software livre e no Microsoft R.   
> 
> 

Quando você estiver no bloco de anotações, poderá explorar seus dados, criar o modelo e testar o modelo usando as bibliotecas de sua escolha.

## <a name="train-and-deploy-models-by-using-azure-machine-learning"></a>Treinar e implantar modelos usando o Azure Machine Learning
Depois de criar e validar seu modelo, a próxima etapa geralmente é implantá-lo na produção. Esta etapa permite que os aplicativos cliente invoquem as previsões de modelo em tempo real ou em modo de lote. O Machine Learning do Azure fornece um mecanismo para operacionalizar um modelo compilado em R ou Python.

Quando você operacionalizou seu modelo no Azure Machine Learning, um serviço Web é exposto. Ele permite que os clientes façam chamadas REST que passam parâmetros de entrada e recebam previsões do modelo como saídas.

### <a name="build-and-operationalize-python-models"></a>Crie e operacionalize modelos Python
Aqui está um trecho de código desenvolvido em um notebook Jupyter Python que cria um modelo simples usando a biblioteca Scikit-Learn:

```python
# IRIS classification
from sklearn import datasets
from sklearn import svm
clf = svm.SVC()
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf.fit(X, y)
```

O método usado para implantar seus modelos de Python em Azure Machine Learning encapsula a previsão do modelo em uma função e o decora com os atributos fornecidos pela biblioteca de Azure Machine Learning Python pré-instalada. Os atributos denotam sua ID de espaço de trabalho Azure Machine Learning, a chave de API e os parâmetros de entrada e de retorno.  

```python
from azureml import services
@services.publish(workspaceid, auth_token)
@services.types(sep_l=float, sep_w=float, pet_l=float, pet_w=float)
@services.returns(int)  # 0, or 1, or 2
def predictIris(sep_l, sep_w, pet_l, pet_w):
    inputArray = [sep_l, sep_w, pet_l, pet_w]


return clf.predict(inputArray)
```

Agora, um cliente pode fazer chamadas ao serviço Web. Os wrappers de conveniência constroem as solicitações da API REST. Aqui está o código de exemplo para consumir o serviço Web:

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

### <a name="build-and-operationalize-r-models"></a>Crie e operacionalize modelos de R
Você pode implantar modelos de R criados no Máquina Virtual de Ciência de Dados ou em outro lugar no Azure Machine Learning de forma semelhante a como ele é feito para o Python. Aqui estão as etapas para fazer isso:

1. Crie um settings.jsno arquivo para fornecer a ID do espaço de trabalho e o token de autenticação. 
2. Grave um wrapper para a função de previsão do modelo.
3. Chame ```publishWebService``` na biblioteca de Azure Machine Learning para passar o wrapper da função.  

Use o procedimento e os trechos de código a seguir para configurar, compilar, publicar e consumir um modelo como um serviço Web no Azure Machine Learning.

#### <a name="set-up"></a>Configuração

Crie um settings.jsno arquivo em um diretório chamado ```.azureml``` em seu diretório base. Insira os parâmetros do seu espaço de trabalho Azure Machine Learning.

Aqui está a settings.jsna estrutura de arquivos:

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
Para consumir o modelo de um aplicativo cliente, use a biblioteca Azure Machine Learning para pesquisar o serviço Web publicado por nome. Use a `services` chamada à API para determinar o ponto de extremidade. Em seguida, basta chamar a função `consume` e passar o quadro de dados a ser previsto.

Use o código a seguir para consumir o modelo publicado como um serviço Web Azure Machine Learning:

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

Veja mais informações sobre [pacotes de R no Machine Learning Studio](/azure/machine-learning/studio-module-reference/r-packages-supported-by-azure-machine-learning).

## <a name="manage-azure-resources"></a>Gerenciar recursos do Azure
O DSVM não permite apenas que você crie sua solução de análise localmente na máquina virtual. Ele também permite que você acesse serviços na plataforma de nuvem do Azure. O Azure fornece várias computação, armazenamento, análise de dados e outros serviços que você pode administrar e acessar de seu DSVM.

Para administrar seus recursos de nuvem e sua assinatura do Azure, você tem duas opções:
+ Use o navegador e vá para a [portal do Azure](https://portal.azure.com).

+ Use scripts do PowerShell. Execute Azure PowerShell de um atalho na área de trabalho ou no menu **Iniciar** . Consulte a [documentação do Microsoft Azure PowerShell](../../powershell-azure-resource-manager.md) para obter detalhes completos. 

## <a name="extend-storage-by-using-shared-file-systems"></a>Estender o armazenamento usando sistemas de arquivos compartilhados
Os cientistas de dados podem compartilhar grandes conjuntos de dados, códigos ou outros recursos dentro da equipe. O DSVM tem cerca de 45 GB de espaço disponível. Para estender o armazenamento, você pode usar os arquivos do Azure e montá-lo em uma ou mais instâncias do DSVM ou acessá-lo por meio de uma API REST. Você também pode usar o [portal do Azure](../../virtual-machines/windows/attach-managed-disk-portal.md) ou usar [Azure PowerShell](../../virtual-machines/windows/attach-disk-ps.md) para adicionar discos de dados dedicados extras. 

> [!NOTE]
> O espaço máximo no compartilhamento de arquivos do Azure é de 5 TB. O limite de tamanho para cada arquivo é 1 TB. 

Você pode usar esse script em Azure PowerShell para criar um compartilhamento de arquivos do Azure:

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

Agora que você criou um compartilhamento de arquivos do Azure, você pode montá-lo em qualquer máquina virtual no Azure. Recomendamos que você coloque a VM no mesmo datacenter do Azure que a conta de armazenamento para evitar a latência e encargos de transferência de dados. Aqui estão os comandos Azure PowerShell para montar a unidade no DSVM:

```powershell
# Get the storage key of the storage account that has the Azure Files share from the Azure portal. Store it securely on the VM to avoid being prompted in the next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure Files share as drive Z on the VM. You can choose another drive letter if you want.
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

Agora, você pode acessar essa unidade como faria com qualquer unidade normal na VM.

## <a name="share-code-in-github"></a>Compartilhar código no GitHub
O GitHub é um repositório de código no qual você pode encontrar exemplos de código e fontes para várias ferramentas usando tecnologias compartilhadas pela comunidade de desenvolvedores. Ele usa Git como a tecnologia para rastrear e armazenar versões dos arquivos de código. O GitHub também é uma plataforma na qual você pode criar seu próprio repositório para armazenar o código compartilhado e a documentação da sua equipe, implementar o controle de versão e controlar quem tem acesso para exibir e contribuir com código. 

Visite as [páginas de ajuda do GitHub](https://help.github.com/) para obter mais informações sobre como usar o Git. Você pode usar o GitHub como uma das maneiras de colaborar com sua equipe, usar o código desenvolvido pela Comunidade e contribuir com código de volta para a Comunidade.

O DSVM vem carregado com as ferramentas de cliente na linha de comando e na GUI para acessar o repositório GitHub. A ferramenta de linha de comando que funciona com o git e o GitHub é chamada git bash. O Visual Studio está instalado no DSVM e tem as extensões git. Você pode encontrar ícones para essas ferramentas no menu **Iniciar** e na área de trabalho.

Para baixar o código de um repositório GitHub, você usa o comando ```git clone```. Por exemplo, para baixar o repositório de ciência de dados publicado pela Microsoft no diretório atual, você pode executar o seguinte comando no git bash:

```bash
git clone https://github.com/Azure/DataScienceVM.git
```

No Visual Studio, você pode fazer a mesma operação de clonagem. A captura de tela a seguir mostra como acessar as ferramentas git e GitHub no Visual Studio:

![Captura de tela do Visual Studio com a conexão do GitHub exibida](./media/vm-do-ten-things/VSGit.PNG)

Você pode encontrar mais informações sobre como usar o Git para trabalhar com o repositório GitHub de recursos disponíveis em github.com. O [roteiro](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) é uma referência útil.

## <a name="access-azure-data-and-analytics-services"></a>Acessar dados e serviços de análise do Azure
### <a name="azure-blob-storage"></a>Armazenamento de Blobs do Azure
O armazenamento de BLOBs do Azure é um serviço de armazenamento em nuvem confiável e econômico para dados grandes e pequenos. Esta seção descreve como você pode mover dados para o armazenamento de BLOBs e acessar dados armazenados em um blob do Azure.

#### <a name="prerequisites"></a>Pré-requisitos

* Crie sua conta de armazenamento de BLOBs do Azure do [portal do Azure](https://portal.azure.com).

   ![Captura de tela do processo de criação da conta de armazenamento no portal do Azure](./media/vm-do-ten-things/create-azure-blob.png)

* Confirme se a ferramenta de linha de comando AzCopy está pré-instalada: ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe``` . O diretório que contém azcopy.exe já está em sua variável de ambiente PATH, portanto, você pode evitar digitar o caminho de comando completo ao executar essa ferramenta. Para obter mais informações sobre a ferramenta AzCopy, consulte a [documentação do AzCopy](../../storage/common/storage-use-azcopy.md).
* Inicie o Gerenciador de Armazenamento do Azure. Você pode baixá-lo na [página da web Gerenciador de armazenamento](https://storageexplorer.com/). 

   ![Captura de tela de Gerenciador de Armazenamento do Azure acessar uma conta de armazenamento](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>Mover dados de uma VM para um blob do Azure: AzCopy

Para mover dados entre seus arquivos locais e o armazenamento de BLOBs, você pode usar AzCopy na linha de comando ou no PowerShell:

```powershell
AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt
```

Substitua **C:\MyFolder** pelo caminho em que o arquivo está armazenado, **mystorageaccount** com o nome da conta de armazenamento de BLOBs, **MyContainer** com o nome do contêiner e a **chave da conta de armazenamento** com sua chave de acesso de armazenamento de BLOBs. Você pode encontrar suas credenciais de conta de armazenamento no [portal do Azure](https://portal.azure.com).

Execute o comando AzCopy no PowerShell ou em um prompt de comando. Veja alguns exemplos de uso do comando AzCopy:

```powershell
# Copy *.sql from a local machine to an Azure blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from an Azure blob container to a local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

Depois de executar o comando AzCopy para copiar para um blob do Azure, seu arquivo será exibido em Gerenciador de Armazenamento do Azure.

![Captura de tela da conta de armazenamento, exibindo o arquivo CSV carregado](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azure-storage-explorer"></a>Mover dados de uma VM para um blob do Azure: Gerenciador de Armazenamento do Azure

Você também pode carregar dados do arquivo local em sua VM usando Gerenciador de Armazenamento do Azure:

* Para carregar dados em um contêiner, selecione o contêiner de destino e selecione o botão **carregar** . ![ Captura de tela do botão carregar no Gerenciador de Armazenamento do Azure](./media/vm-do-ten-things/storage-accounts.png)
* Selecione as reticências (**...**) à direita da caixa **arquivos** , selecione um ou vários arquivos para carregar do sistema de arquivos e selecione **carregar** para começar a carregar os arquivos. ![ Captura de tela da caixa de diálogo carregar arquivos](./media/vm-do-ten-things/upload-files-to-blob.png)

#### <a name="read-data-from-an-azure-blob-machine-learning-reader-module"></a>Ler dados de um blob do Azure: módulo do leitor de Machine Learning

No Azure Machine Learning Studio, você pode usar o módulo importar dados para ler dados de seu BLOB.

![Captura de tela do módulo Importar Dados no Machine Learning Studio](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

#### <a name="read-data-from-an-azure-blob-python-odbc"></a>Ler dados de um blob do Azure: ODBC do Python

Você pode usar a biblioteca BlobService para ler dados diretamente de um blob em um notebook Jupyter ou em um programa Python.

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

Em seguida, conecte suas credenciais de conta de armazenamento de BLOBs e leia os dados do blob:

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
Azure Data Lake Storage é um repositório de hiperescala para cargas de trabalho de Big Data Analytics e é compatível com o Sistema de Arquivos Distribuído do Hadoop (HDFS). Ele funciona com Hadoop, Spark e Azure Data Lake Analytics. Nesta seção, você aprenderá como é possível mover dados para Azure Data Lake Storage e executar análises usando o Azure Data Lake Analytics.

#### <a name="prerequisites"></a>Pré-requisitos

* Crie sua instância de Azure Data Lake Analytics no [portal do Azure](https://portal.azure.com).

   ![Captura de tela da criação de uma instância de Data Lake Analytics da portal do Azure](./media/vm-do-ten-things/azure-data-lake-create-v3.png)

* As [ferramentas Azure data Lake e Stream Analytics para o plug-in do Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504) já estão instaladas no Visual Studio Community Edition na máquina virtual. Depois de iniciar o Visual Studio e entrar em sua assinatura do Azure, você deverá ver sua conta e armazenamento da análise de dados do Azure no painel esquerdo do Visual Studio.

   ![Captura de tela do plug-in para ferramentas de Data Lake no Visual Studio](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

#### <a name="move-data-from-a-vm-to-data-lake-azure-data-lake-explorer"></a>Mover dados de uma VM para Data Lake: Azure Data Lake Explorer

Você pode usar Azure Data Lake Explorer para [carregar dados dos arquivos locais em sua máquina virtual para data Lake Storage](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

Você também pode criar um pipeline de dados para colocar em operação a movimentação de dados de ou para Azure Data Lake usando [Azure data Factory](https://azure.microsoft.com/services/data-factory/). [Este artigo](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) orienta você pelas etapas para criar os pipelines de dados.

#### <a name="read-data-from-an-azure-blob-to-data-lake-u-sql"></a>Ler dados de um blob do Azure para Data Lake: U-SQL

Se os dados residirem no armazenamento de BLOBs do Azure, você poderá ler diretamente os dados de um blob do Azure em uma consulta U-SQL. Antes de compor sua consulta U-SQL, verifique se sua conta de armazenamento de BLOBs está vinculada à sua instância de Azure Data Lake. Vá para a portal do Azure, localize seu painel de Azure Data Lake Analytics, selecione **Adicionar fonte de dados**, selecione um tipo de armazenamento do **armazenamento do Azure**e conecte seu nome de conta de armazenamento do Azure e a chave. Em seguida, você pode fazer referência aos dados armazenados na conta de armazenamento.

![Captura de tela da caixa de diálogo Adicionar fonte de dados](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

No Visual Studio, você pode ler dados do armazenamento de BLOBs, manipular dados, recursos de engenharia e enviar os dados resultantes para Azure Data Lake ou para o armazenamento de BLOBs do Azure. Ao fazer referência aos dados no armazenamento de BLOBs, use **WASB://**. Ao fazer referência aos dados em Azure Data Lake, use **swbhdfs://**.

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

Depois que a consulta for enviada ao servidor, um diagrama mostrará o status do seu trabalho.

![Captura de tela do diagrama de status do trabalho](./media/vm-do-ten-things/USQL_Job_Status.PNG)

#### <a name="query-data-in-data-lake-u-sql"></a>Dados da consulta no Data Lake: U-SQL

Depois que o conjunto de dados é ingerido no Azure Data Lake, você pode usar a [linguagem U-SQL](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) para consultar e explorar os dados. A linguagem U-SQL é semelhante ao T-SQL, mas combina alguns recursos do C# para que os usuários possam escrever módulos personalizados e funções definidas pelo usuário. Você pode usar os scripts da etapa anterior.

Depois que a consulta for enviada ao servidor, tripdata_summary.CSV aparecerá no Azure Data Lake Explorer. Você pode visualizar os dados clicando com o botão direito do mouse no arquivo.

![Captura de tela do arquivo CSV no Data Lake Explorer](./media/vm-do-ten-things/USQL_create_summary.png)

As informações do arquivo são exibidas:

![Captura de tela das informações de resumo do arquivo](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="azure-sql-data-warehouse-and-databases"></a>SQL Data Warehouse e bancos de dados do Azure
O Azure SQL Data Warehouse é um data warehouse elástico como um serviço com uma experiência de SQL Server de classe empresarial.

Você pode provisionar seu data warehouse de SQL do Azure seguindo as instruções neste [artigo](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md). Depois de provisionar o SQL data warehouse, você pode usar [este passo a passos](../team-data-science-process/sqldw-walkthrough.md) para fazer upload, exploração e modelagem de dados usando dados dentro do data warehouse do SQL.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
O Azure Cosmos DB é um banco de dados NoSQL na nuvem. Você pode usá-lo para trabalhar com documentos como JSON e para armazenar e consultar os documentos.

Use as seguintes etapas de pré-requisito para acessar Azure Cosmos DB do DSVM:

1. O SDK do Python Azure Cosmos DB já está instalado no DSVM. Para atualizá-lo, execute ```pip install pydocumentdb --upgrade``` a partir de um prompt de comando.
2. Crie uma conta de Azure Cosmos DB e um banco de dados do [portal do Azure](https://portal.azure.com).
3. Baixe a ferramenta de migração de dados Azure Cosmos DB do [centro de download da Microsoft](https://www.microsoft.com/download/details.aspx?id=53595) e extraia para um diretório de sua escolha.
4. Importe dados JSON (dados de vulcão) armazenados em um [blob público](https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json) em Azure Cosmos DB com os seguintes parâmetros de comando para a ferramenta de migração. (Use dtui.exe no diretório em que você instalou a ferramenta de migração de dados do Azure Cosmos DB.) Insira a origem e o local de destino com estes parâmetros:
   
    `/s:JsonFile /s.Files:https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

Depois de importar os dados, você pode ir para Jupyter e abrir o notebook intitulado *documentdbsample,*. Ele contém código Python para acessar Azure Cosmos DB e fazer algumas consultas básicas. Você pode saber mais sobre Azure Cosmos DB visitando a [página de documentação](https://docs.microsoft.com/azure/cosmos-db/)do serviço.

## <a name="use-power-bi-reports-and-dashboards"></a>Usar Power BI relatórios e painéis 
Você pode visualizar o arquivo JSON vulcão do exemplo de Azure Cosmos DB anterior no Power BI Desktop para obter informações visuais sobre os dados. As etapas detalhadas estão disponíveis no [artigo sobre o Power BI](../../cosmos-db/powerbi-visualize.md). Aqui estão as etapas de alto nível:

1. Abra o Power BI Desktop e selecione **Obter dados**. Especifique a URL como: `https://cahandson.blob.core.windows.net/samples/volcano.json` .
2. Você deve ver os registros JSON importados como uma lista. Converta a lista em uma tabela para que Power BI possa trabalhar com ela.
4. Expanda as colunas selecionando o ícone expandir (seta).
5. Observe que o local é um campo de **registro** . Expanda o registro e selecione apenas as coordenadas. **Coordenar** é uma coluna de lista.
6. Adicione uma nova coluna para converter a coluna de coordenadas da lista em uma coluna **LatLong** separada por vírgulas. Concatene os dois elementos no campo lista de coordenadas usando a fórmula ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})``` .
7. Converta a coluna de **elevação** em decimal e selecione os botões **fechar** e **aplicar** .

Em vez de etapas anteriores, você pode colar o código a seguir. Ele gera scripts das etapas usadas no Editor Avançado em Power BI para gravar as transformações de dados em uma linguagem de consulta.

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

Agora você tem os dados no modelo de dados do Power BI. Sua instância de Power BI Desktop deve aparecer da seguinte maneira:

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

Você pode começar a criar relatórios e visualizações usando o modelo de dados. Você pode seguir as etapas neste [Power bi artigo](../../cosmos-db/powerbi-visualize.md#build-the-reports) para criar um relatório.

## <a name="scale-the-dsvm-dynamically"></a>Dimensionar o DSVM dinamicamente 
Você pode escalar e reduzir verticalmente o DSVM para atender às necessidades do seu projeto. Se você não precisar usar a VM na noite ou nos finais de semana, poderá desligar a VM do [portal do Azure](https://portal.azure.com).

> [!NOTE]
> Você incorrerá em encargos de computação se usar apenas o botão de desligamento para o sistema operacional na VM.  
> 
> 

Talvez seja necessário lidar com uma análise em larga escala e precisa de mais capacidade de CPU, memória ou disco. Nesse caso, você pode encontrar uma opção de tamanhos de VM em termos de núcleos de CPU, instâncias baseadas em GPU para aprendizado profundo, capacidade de memória e tipos de disco (incluindo unidades de estado sólido) que atendam às suas necessidades de computação e orçamento. A lista completa de VMs, juntamente com seus preços de computação por hora, está disponível na página de [preços de máquinas virtuais do Azure](https://azure.microsoft.com/pricing/details/virtual-machines/) .

Da mesma forma, sua necessidade de capacidade de processamento de VM pode diminuir. (Por exemplo: você moveu uma carga de trabalho principal para um cluster Hadoop ou Spark.) Em seguida, você pode reduzir o cluster do [portal do Azure](https://portal.azure.com) e ir para as configurações da sua instância de VM. 

## <a name="add-more-tools"></a>Adicionar mais ferramentas
As ferramentas predefinidas no DSVM podem atender a muitas necessidades comuns de análise de dados. Isso poupa tempo porque você não precisa instalar e configurar seus ambientes um a um. Ele também economiza dinheiro, pois você paga apenas pelos recursos que usa.

Você pode usar outros serviços de análise e dados do Azure com o perfil criado neste artigo para aprimorar seu ambiente de análise. Em alguns casos, talvez você precise de ferramentas adicionais, incluindo algumas ferramentas de parceiros de propriedade. Você tem acesso administrativo total na máquina virtual para instalar novas ferramentas de que precisa. Também é possível instalar pacotes adicionais no Python e no R que não foram pré-instalados. Para Python, você pode usar o ```conda``` ou o ```pip``` . Para o r, você pode usar o ```install.packages()``` no console do r ou usar o IDE e selecionar **pacotes**  >  **instalar pacotes**.

## <a name="deep-learning"></a>Aprendizado

Além dos exemplos baseados em estrutura, você pode obter um conjunto de orientações abrangentes que foram validadas no DSVM. Essas orientações ajudam a recomeçar seu desenvolvimento de aplicativos de aprendizado profundo em domínios como reconhecimento de imagem e texto/linguagem.   


- [Executando redes neurais em diferentes estruturas](https://github.com/ilkarman/DeepLearningFrameworks): este passo a passos mostra como migrar o código de uma estrutura para outra. Ele também demonstra como comparar modelos e desempenho de tempo de execução entre estruturas. 

- [Um guia de instruções para criar uma solução de ponta a ponta para detectar produtos em imagens](https://github.com/Azure/cortana-intelligence-product-detection-from-images): a detecção de imagem é uma técnica que pode localizar e classificar objetos em imagens. Essa tecnologia tem o potencial de trazer enormes recompensas em muitos domínios de negócios da vida real. Por exemplo, os varejistas podem usar essa técnica para determinar qual produto um cliente retirou da prateleira. Por sua vez, essas informações ajudam as lojas a gerenciar o estoque do produtos. 

- [Aprendizado profundo de áudio](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/): Este tutorial mostra como treinar um modelo de aprendizado profundo para detecção de eventos de áudio no conjunto de informações de [sons urbanas](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html). Ele também fornece uma visão geral de como trabalhar com dados de áudio.

- [Classificação de documentos de texto](https://github.com/anargyri/lstm_han): Este tutorial demonstra como criar e treinar duas arquiteturas de rede neural: rede de atenção hierárquica e LSTM (memória de longa duração curta). Essas redes neurais usam a API Keras para aprendizagem profunda para classificar documentos de texto. O Keras é um front-end para três das estruturas mais populares de aprendizado profundo: Microsoft Cognitive Toolkit, TensorFlow e Theano.

## <a name="summary"></a>Resumo
Este artigo descreveu algumas das coisas que você pode fazer no Microsoft Máquina Virtual de Ciência de Dados. Há muito mais coisas que você pode fazer para tornar o DSVM um ambiente de análise eficaz.

