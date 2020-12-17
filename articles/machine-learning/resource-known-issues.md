---
title: Problemas conhecidos e soluções
titleSuffix: Azure Machine Learning
description: Obtenha ajuda para localizar e corrigir erros ou falhas no Azure Machine Learning. Saiba mais sobre problemas conhecidos, solução de problemas e soluções alternativas.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
ms.custom: troubleshooting, contperf-fy20q4
ms.date: 11/09/2020
ms.openlocfilehash: aa0a14d57db932ef6cfb17df84b3204d3dec9e4d
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97616993"
---
# <a name="known-issues-and-troubleshooting-in-azure-machine-learning"></a>Problemas conhecidos e solução de problemas no Azure Machine Learning

Este artigo ajuda você a solucionar problemas conhecidos que você pode encontrar ao usar o Azure Machine Learning. 

Para obter mais informações sobre solução de problemas, consulte [próximas etapas](#next-steps) no final deste artigo.

> [!TIP]
> Erros ou outros problemas podem ser o resultado de [cotas de recursos](how-to-manage-quotas.md) que você encontrar ao trabalhar com Azure Machine Learning. 

## <a name="access-diagnostic-logs"></a>Acessar logs de diagnóstico

Às vezes, pode ser útil fornecer informações de diagnóstico ao pedir ajuda. Para ver alguns logs: 
1. Visite [Azure Machine Learning Studio](https://ml.azure.com). 
1. No lado esquerdo, selecione **experimento** 
1. Selecione um experimento.
1. Selecione uma execução.
1. Na parte superior, selecione **saídas + logs**.

> [!NOTE]
> Azure Machine Learning registra as informações de uma variedade de fontes durante o treinamento, como AutoML ou o contêiner do Docker que executa o trabalho de treinamento. Muitos desses logs não estão documentados. Se você encontrar problemas e entrar em contato com o suporte da Microsoft, eles poderão usar esses logs na solução.


## <a name="installation-and-import"></a>Instalação e importação
                           
* **Instalação de Pip: não há garantia de que as dependências sejam consistentes com a instalação de linha única:** 

   Essa é uma limitação conhecida do Pip, pois não tem um resolvedor de dependências em funcionamento quando você instala o como uma única linha. A primeira dependência exclusiva é a única que ela examina. 

   No código a seguir `azureml-datadrift` e `azureml-train-automl` são instalados usando uma instalação Pip de linha única. 
     ```
       pip install azureml-datadrift, azureml-train-automl
     ```
   Para este exemplo, digamos que o `azureml-datadrift` requer a versão > 1,0 e `azureml-train-automl` requer a versão < 1,2. Se a versão mais recente do `azureml-datadrift` for 1,3, ambos os pacotes serão atualizados para 1,3, independentemente do `azureml-train-automl` requisito de pacote para uma versão mais antiga. 

   Para garantir que as versões apropriadas sejam instaladas para seus pacotes, instale o usando várias linhas, como no código a seguir. A ordem não é um problema aqui, pois Pip explicitamente faz downgrade como parte da próxima chamada de linha. Portanto, as dependências de versão apropriadas são aplicadas.
    
     ```
        pip install azureml-datadrift
        pip install azureml-train-automl 
     ```
     
* **O pacote de explicação não tem garantia de ser instalado ao instalar o azureml-Train-automl-Client:** 
   
   Ao executar uma AutoML remota executar com a explicação do modelo habilitada, você verá uma mensagem de erro "Instale o azureml-explique-Package para obter explicações do modelo." Este é um problema conhecido. Como alternativa, siga uma das etapas abaixo:
  
  1. Instale o azureml-explique-Model localmente.
   ```
      pip install azureml-explain-model
   ```
  2. Desabilite inteiramente o recurso de explicação, passando model_explainability = false na configuração AutoML.
   ```
      automl_config = AutoMLConfig(task = 'classification',
                             path = '.',
                             debug_log = 'automated_ml_errors.log',
                             compute_target = compute_target,
                             run_configuration = aml_run_config,
                             featurization = 'auto',
                             model_explainability=False,
                             training_data = prepped_data,
                             label_column_name = 'Survived',
                             **automl_settings)
    ``` 
    
* **Erros do panda: normalmente vistos durante o experimento do AutoML:**
   
   Ao configurar manualmente seu ambiente usando Pip, você pode notar erros de atributo (especialmente do pandas) devido à instalação de versões de pacote sem suporte. Para evitar esses erros, [Instale o SDK do AutoML usando o automl_setup. cmd](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md):
   
    1. Abra um prompt do Anaconda e clone o repositório do GitHub para um conjunto de blocos de anotações de exemplo.

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```
    
    2. CD para a pasta como usar-azureml/Automated-Machine Learning em que os blocos de anotações de exemplo foram extraídos e, em seguida, executados:
    
    ```bash
    automl_setup
    ```
    
* **Keyerro: ' marca ' ao executar AutoML na computação local ou Azure Databricks cluster**

    Se um novo ambiente foi criado após 10 de junho de 2020, usando o SDK 1.7.0 ou anterior, o treinamento poderá falhar com esse erro devido a uma atualização no pacote py-cpuinfo. (Ambientes criados em ou antes de 10 de junho de 2020, não são afetados, já que as experiências são executadas na computação remota porque as imagens de treinamento em cache são usadas.) Para contornar esse problema, execute uma das duas etapas a seguir:
    
    * Atualize a versão do SDK para 1.8.0 ou posterior (isso também faz o downgrade de py-cpuinfo para 5.0.0):
    
      ```bash
      pip install --upgrade azureml-sdk[automl]
      ```
    
    * Faça o downgrade da versão instalada do py-cpuinfo para 5.0.0:
    
      ```bash
      pip install py-cpuinfo==5.0.0
      ```
  
* **Mensagem de erro: não é possível desinstalar 'PyYAML'**

    Azure Machine Learning SDK para Python: PyYAML é um `distutils` projeto instalado. Portanto, não é possível determinar com precisão quais arquivos pertencem a ele no caso de uma desinstalação parcial. Para continuar a instalação do SDK ignorando esse erro, use:
    
    ```Python
    pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
    ```

* **Falha na instalação do SDK do Azure Machine Learning com uma exceção: ModuleNotFoundError: nenhum módulo chamado ' ruamel ' ou ' ImportError: nenhum módulo chamado ruamel. YAML '**
   
   Esse problema está sendo encontrado com a instalação do SDK do Azure Machine Learning para Python no PIP (>20.1.1) mais recente no ambiente de base do Conda para todas as versões lançadas do SDK do Azure Machine Learning para Python. Consulte as seguintes soluções alternativas:

    * Evite instalar o SDK do Python no ambiente de base do Conda, em vez disso, crie seu ambiente do Conda e instale o SDK nesse ambiente de usuário recém-criado. O Pip mais recente deve funcionar no novo ambiente Conda.

    * Para criar imagens no Docker, onde você não pode sair do ambiente de base do Conda, fixe o Pip<= 20.1.1 no arquivo do Docker.

    ```Python
    conda install -c r -y conda python=3.6.2 pip=20.1.1
    ```
    
* **Falha do databricks ao instalar pacotes**

    A instalação do SDK do Azure Machine Learning falha em Azure Databricks quando mais pacotes são instalados. Alguns pacotes, como `psutil`, podem causar conflitos. Para evitar erros de instalação, instale pacotes congelando a versão da biblioteca. Esse problema está relacionado ao databricks e não ao SDK do Azure Machine Learning. Você também pode experimentar esse problema com outras bibliotecas. Exemplo:
    
    ```python
    psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
    ```

    Como alternativa, você pode usar scripts de inicialização se continuar enfrentando problemas de instalação com bibliotecas do Python. Essa abordagem não tem suporte oficial. Para obter mais informações, consulte [scripts de inicialização no escopo do cluster](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

* **Erro `Timedelta` de importação `pandas._libs.tslibs` do databricks: não é possível importar o nome de**: se você vir esse erro ao usar o aprendizado de máquina automatizado, execute as duas linhas a seguir no bloco de anotações:
    ```
    %sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
    %sh /databricks/python/bin/pip install pandas==0.23.4
    ```

* **Erro de importação do databricks: nenhum módulo chamado ' pandas. Core. Indexes '**: se você vir esse erro ao usar o aprendizado de máquina automatizado:

    1. Execute este comando para instalar dois pacotes no cluster de Azure Databricks:
    
       ```bash
       scikit-learn==0.19.1
       pandas==0.22.0
       ```
    
    1. Desanexe e anexe novamente o cluster ao seu bloco de anotações.
    
    Se essas etapas não resolverem o problema, tente reiniciar o cluster.

* **Databricks FailToSendFeather**: se você vir um `FailToSendFeather` erro ao ler dados no cluster Azure Databricks, consulte as seguintes soluções:
    
    * Atualize `azureml-sdk[automl]` o pacote para a versão mais recente.
    * Adicione a `azureml-dataprep` versão 1.1.8 ou superior.
    * Adicione a `pyarrow` versão 0,11 ou superior.
    
## <a name="create-and-manage-workspaces"></a>Crie e gerencie os workspaces

> [!WARNING]
> Não há suporte para a movimentação do workspace do Azure Machine Learning para outra assinatura nem para a movimentação da assinatura proprietária para um novo locatário. Se você fizer isso, poderá causar erros.

* **Portal do Azure**: 
  * Se você ir diretamente para seu espaço de trabalho de um link de compartilhamento do SDK ou do portal do Azure, não será possível exibir a página de **visão geral** padrão que tem informações de assinatura na extensão. Nesse cenário, você também não pode alternar para outro espaço de trabalho. Para exibir outro espaço de trabalho, vá diretamente para [Azure Machine Learning Studio](https://ml.azure.com) e pesquise o nome do espaço de trabalho.
  * Todos os ativos (conjuntos de valores, testes, computações e assim por diante) estão disponíveis apenas no [Azure Machine Learning Studio](https://ml.azure.com). Eles *não* estão disponíveis na portal do Azure.

* **Navegadores com suporte no portal da Web do Azure Machine Learning Studio**: Recomendamos que você use o navegador mais atualizado que é compatível com seu sistema operacional. Há suporte para os seguintes navegadores:
  * Microsoft Edge (o novo Microsoft Edge, versão mais recente. Não é herdado do Microsoft Edge)
  * Safari (última versão, apenas Mac)
  * Chrome (última versão)
  * Firefox (última versão)

## <a name="set-up-your-environment"></a>Configure seu ambiente

* **Problemas ao criar AmlCompute**: há uma chance rara de que alguns usuários que criaram seu espaço de trabalho Azure Machine Learning do portal do Azure antes da versão GA talvez não possam criar AmlCompute nesse espaço de trabalho. Você pode gerar uma solicitação de suporte em relação ao serviço ou criar um novo espaço de trabalho por meio do portal ou do SDK para desbloquear-se imediatamente.

* **Atualmente, o registro de contêiner do Azure não dá suporte a caracteres Unicode em nomes de grupos de recursos**: é possível que as solicitações ACR falhem porque o nome do grupo de recursos contém caracteres Unicode. Para atenuar esse problema, é recomendável criar um ACR em um grupo de recursos nomeado de forma diferente.

## <a name="work-with-data"></a>Trabalhar com dados

### <a name="overloaded-azurefile-storage"></a>Armazenamento do Azurefile sobrecarregado

Se você receber um erro `Unable to upload project files to working directory in AzureFile because the storage is overloaded` , aplique as seguintes soluções alternativas.

Se você estiver usando o compartilhamento de arquivos para outras cargas de trabalho, como a transferência de dados, a recomendação será usar BLOBs para que o compartilhamento de arquivos esteja livre para ser usado para o envio de execuções. Você também pode dividir a carga de trabalho entre dois espaços diferentes.

### <a name="passing-data-as-input"></a>Passando dados como entrada

*  **TypeError: fileNotFound: não há tal arquivo ou diretório**: esse erro ocorrerá se o caminho do arquivo fornecido não for onde o arquivo está localizado. Você precisa certificar-se de que a maneira como você se refere ao arquivo é consistente com o local em que você montou seu conjunto de seu destino de computação. Para garantir um estado determinístico, é recomendável usar o caminho abstrato ao montar um conjunto de um DataSet para um destino de computação. Por exemplo, no código a seguir, montamos o DataSet na raiz do sistema de arquivos do destino de computação, `/tmp` . 
    
    ```python
    # Note the leading / in '/tmp/dataset'
    script_params = {
        '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
    } 
    ```

    Se você não incluir a barra "/" à esquerda, será necessário prefixar o diretório de trabalho, por exemplo, `/mnt/batch/.../tmp/dataset` no destino de computação para indicar onde você deseja que o conjunto de os seja montado.

### <a name="mount-dataset"></a>Montar conjunto de um
* **Falha na inicialização do conjunto de pontos: aguardando o ponto de montagem estar pronto** expirado: 
  * Se você não tiver regras de [grupo de segurança de rede](https://docs.microsoft.com/azure/virtual-network/network-security-groups-overview) de saída e estiver usando `azureml-sdk>=1.12.0` , atualize `azureml-dataset-runtime` e suas dependências sejam as mais recentes para a versão secundária específica ou, se você a estiver usando em uma execução, recrie seu ambiente para que ele possa ter o patch mais recente com a correção. 
  * Se você estiver usando `azureml-sdk<1.12.0` o, atualize para a versão mais recente.
  * Se você tiver regras de NSG de saída, certifique-se de que haja uma regra de saída que permita todo o tráfego para a marca de serviço `AzureResourceMonitor` .

### <a name="data-labeling-projects"></a>Projetos de rotulagem de dados

|Problema  |Resolução  |
|---------|---------|
|Somente conjuntos de valores criados em armazenamentos de BLOBs podem ser usados.     |  Essa é uma limitação conhecida da versão atual.       |
|Após a criação, o projeto mostra "Inicializando" por um longo tempo.     | Atualize manualmente a página. A inicialização deve continuar em aproximadamente 20 pontos de extremidade por segundo. A falta de atualização autoatualizada é um problema conhecido.         |
|Ao revisar imagens, as imagens rotuladas recentemente não são mostradas.     |   Para carregar todas as imagens rotuladas, escolha o **primeiro** botão. O **primeiro** botão o levará de volta à frente da lista, mas carregará todos os dados rotulados.      |
|Pressionar a tecla ESC enquanto rotular para detecção de objeto cria um rótulo de tamanho zero no canto superior esquerdo. O envio de rótulos nesse estado falha.     |   Exclua o rótulo clicando na marca de cruz ao lado dele.  |

### <a name="data-drift-monitors"></a><a name="data-drift"></a> Monitores de descompasso de dados

Limitações e problemas conhecidos para monitores de descompasso de dados:

* O intervalo de tempo durante a análise de dados históricos é limitado a 31 intervalos da configuração de frequência do monitor. 
* Limitação de 200 recursos, a menos que uma lista de recursos não seja especificada (todos os recursos usados).
* O tamanho da computação deve ser grande o suficiente para lidar com os dados.
* Certifique-se de que o conjunto de dados tenha os dados dentro da data de início e de término de uma determinada execução do monitor.
* Os monitores de conjunto de registros só funcionarão em conjuntos de valores que contenham 50 linhas ou mais.
* Colunas ou recursos, no conjunto de linhas, são classificados como categóricos ou numéricos com base nas condições na tabela a seguir. Se o recurso não atender a essas condições, por exemplo, uma coluna do tipo cadeia de caracteres com >valores exclusivos de 100-o recurso será descartado do nosso algoritmo de descompasso de dados, mas ainda será criado um perfil. 

    | Tipo de recurso | Tipo de dados | Condição | Limitações | 
    | ------------ | --------- | --------- | ----------- |
    | Categóricos | Cadeia de caracteres, bool, int, float | O número de valores exclusivos no recurso é menor que 100 e menor que 5% do número de linhas. | NULL é tratado como sua própria categoria. | 
    | Numérico | int, float | Os valores no recurso são de um tipo de dados numérico e não atendem à condição de um recurso categórico. | Recurso Descartado se >15% dos valores forem nulos. | 

* Quando você tiver [criado um monitor de descompasso de dados](how-to-monitor-datasets.md) , mas não puder ver os dados na página de **monitores do DataSet** no Azure Machine Learning Studio, tente o seguinte.

    1. Verifique se você selecionou o intervalo de datas correto na parte superior da página.  
    1. Na guia **monitores do conjunto** de testes, selecione o link experimento para verificar o status da execução.  Esse link está na extrema direita da tabela.
    1. Se a execução for concluída com êxito, verifique os logs de driver para ver quantas métricas foram geradas ou se há alguma mensagem de aviso.  Localize os logs de driver na guia **saída + logs** depois de clicar em um experimento.

* Se a função SDK não `backfill()` gerar a saída esperada, isso pode ser devido a um problema de autenticação.  Quando você cria a computação para passar para essa função, não use `Run.get_context().experiment.workspace.compute_targets` .  Em vez disso, use [ServicePrincipalAuthentication](/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?preserve-view=true&view=azure-ml-py) como o seguinte para criar a computação que você passa para essa `backfill()` função: 

  ```python
   auth = ServicePrincipalAuthentication(
          tenant_id=tenant_id,
          service_principal_id=app_id,
          service_principal_password=client_secret
          )
   ws = Workspace.get("xxx", auth=auth, subscription_id="xxx", resource_group"xxx")
   compute = ws.compute_targets.get("xxx")
   ```

## <a name="azure-machine-learning-designer"></a>Designer do Azure Machine Learning

### <a name="dataset-visualization-in-the-designer"></a>Visualização de DataSet no designer

Depois de registrar um conjunto de registros na página de ativos de conjuntos de banco de **valores** ou usando o SDK, você pode encontrá-lo na categoria **conjuntos de valores** na lista à esquerda para a tela do designer.

No entanto, quando você arrasta o conjunto de itens para a tela e visualiza, pode não ser possível visualizar devido a alguns dos seguintes motivos:

- No momento, você só pode visualizar o conjunto de tabelas tabular no designer. Se você registrar um conjunto de registros de arquivo fora do designer, não será possível visualizá-lo na tela do designer.
- Seu conjunto de armazenamento é armazenado na rede virtual (VNet). Se você quiser Visualizar, precisará habilitar a identidade gerenciada do espaço de trabalho do repositório de armazenamento.
    1. Vá para o repositório de armazenamento relacionado e clique em **Atualizar credenciais** 
     :::image type="content" source="./media/resource-known-issues/datastore-update-credential.png" alt-text="Atualizar credenciais":::
    1. Selecione **Sim** para habilitar a identidade gerenciada do espaço de trabalho.
    :::image type="content" source="./media/resource-known-issues/enable-workspace-managed-identity.png" alt-text="Habilitar identidade gerenciada do espaço de trabalho":::

### <a name="long-compute-preparation-time"></a>Tempo de preparação de computação longa

Pode ser de alguns minutos ou ainda mais quando você se conecta pela primeira vez ao ou cria um destino de computação. 

No coletor de dados de modelo, pode levar até (mas geralmente menos de) 10 minutos para que os dados cheguem em sua conta de armazenamento de BLOBs. Aguarde 10 minutos para garantir que as células abaixo sejam executadas.

```python
import time
time.sleep(600)
```

### <a name="log-for-real-time-endpoints"></a>Log para pontos de extremidade em tempo real

Os logs de pontos de extremidade em tempo real são dados do cliente. Para solução de problemas de ponto de extremidade em tempo real, você pode usar o código a seguir para habilitar logs. 

Veja mais detalhes sobre como monitorar pontos de extremidade de serviço Web neste [artigo](./how-to-enable-app-insights.md#query-logs-for-deployed-models).

```python
from azureml.core import Workspace
from azureml.core.webservice import Webservice

ws = Workspace.from_config()
service = Webservice(name="service-name", workspace=ws)
logs = service.get_logs()
```
Se você tiver vários locatários, talvez seja necessário adicionar o seguinte código de autenticação antes `ws = Workspace.from_config()`

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="the tenant_id in which your workspace resides")
```

## <a name="train-models"></a>Treinar modelos

* **ModuleErrors (nenhum módulo chamado)**: se você estiver executando o ModuleErrors ao enviar experimentos no Azure ml, significa que o script de treinamento está esperando que um pacote seja instalado, mas não adicionado. Depois de fornecer o nome do pacote, o Azure ML instala o pacote no ambiente usado para sua execução de treinamento. 

    Se você estiver usando estimadores para enviar experimentos, poderá especificar um nome de pacote por meio do `pip_packages` `conda_packages` parâmetro ou no estimador com base em qual fonte você deseja instalar o pacote. Você também pode especificar um arquivo yml com todas as suas dependências usando `conda_dependencies_file` ou listar todos os seus requisitos de Pip em um arquivo txt usando o `pip_requirements_file` parâmetro. Se você tiver seu próprio objeto de ambiente do Azure ML que deseja substituir a imagem padrão usada pelo estimador, você poderá especificar esse ambiente por meio do `environment` parâmetro do Construtor estimador.

    O Azure ML também fornece avaliadores específicos da estrutura para TensorFlow, PyTorch, chainer e SKLearn. Usar esses estimadores garantirá que as principais dependências da estrutura sejam instaladas em seu nome no ambiente usado para treinamento. Você tem a opção de especificar dependências extras, conforme descrito acima. 
 
    As imagens do Docker mantidas pelo Azure ML e seu conteúdo podem ser vistos em [contêineres do AzureML](https://github.com/Azure/AzureML-Containers).
    As dependências específicas à estrutura são listadas no respectivo Framework Documentation- [Chainr](/python/api/azureml-train-core/azureml.train.dnn.chainer?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks), [PyTorch](/python/api/azureml-train-core/azureml.train.dnn.pytorch?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks), [TensorFlow](/python/api/azureml-train-core/azureml.train.dnn.tensorflow?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks), [SKLearn](/python/api/azureml-train-core/azureml.train.sklearn.sklearn?preserve-view=true&view=azure-ml-py#&preserve-view=trueremarks).

    > [!Note]
    > Se você considerar que um pacote específico é comum o suficiente para ser adicionado em ambientes e imagens mantidas do Azure ML, gere um problema do GitHub nos [contêineres do AzureML](https://github.com/Azure/AzureML-Containers). 
 
* **NameError (nome não definido), AttributeError (objeto sem atributo)**: essa exceção deve vir de seus scripts de treinamento. Você pode examinar os arquivos de log de portal do Azure para obter mais informações sobre o nome específico não definido ou erro de atributo. No SDK, você pode usar `run.get_details()` para examinar a mensagem de erro. Isso também listará todos os arquivos de log gerados para sua execução. Certifique-se de dar uma olhada no script de treinamento e corrija o erro antes de reenviar sua execução. 

* O **Horovod foi desligado**: na maioria dos casos, se você encontrar "AbortedError: Horovod foi desligado", essa exceção significa que houve uma exceção subjacente em um dos processos que fizeram com que o Horovod fosse desligado. Cada classificação no trabalho da MPI obtém seu próprio arquivo de log dedicado no Azure Machine Learning. Esses logs são nomeados `70_driver_logs`. No caso de treinamento distribuído, os nomes de log têm o sufixo `_rank` para facilitar a diferenciação dos logs. Para localizar o erro exato que fez com que o Horovod fosse desligado, percorra todos os arquivos de log e procure `Traceback` no final dos arquivos de driver_log. Um desses arquivos fornecerá a exceção subjacente real. 

* **Execução ou teste de exclusão**: os experimentos podem ser arquivados usando o método [experimento. Archive](/python/api/azureml-core/azureml.core.experiment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truearchive--) ou da exibição guia do experimento no cliente Azure Machine Learning Studio por meio do botão "arquivo de teste". Essa ação oculta o experimento de consultas de lista e exibições, mas não a exclui.

    Atualmente, não há suporte para a exclusão permanente de execuções ou experimentos individuais. Para obter mais informações sobre como excluir ativos de espaço de trabalho, consulte [exportar ou excluir seus dados de espaço de trabalho do serviço Machine Learning](how-to-export-delete-data.md).

* O **documento de métrica é muito grande**: Azure Machine Learning tem limites internos sobre o tamanho dos objetos de métrica que podem ser registrados de uma só vez a partir de uma execução de treinamento. Se você encontrar o erro "o Documento de Métrica é muito grande" ao registrar em log uma métrica com valor de lista, tente dividir a lista em partes menores, por exemplo:

    ```python
    run.log_list("my metric name", my_metric[:N])
    run.log_list("my metric name", my_metric[N:])
    ```

    Internamente, o Azure Machine Learning concatena os blocos com o mesmo nome de métrica em uma lista contígua.

## <a name="automated-machine-learning"></a>Machine Learning automatizado

* A **atualização recente das dependências do AutoML para versões mais recentes causará a compatibilidade**: a partir da versão 1.13.0 do SDK, os modelos não serão carregados em SDKs mais antigos devido à incompatibilidade entre as versões mais antigas que fixamos em nossos pacotes anteriores e as versões mais recentes que nós fixamos agora. Você verá um erro como:
  * Módulo não encontrado: ex. `No module named 'sklearn.decomposition._truncated_svd` ,
  * Erros de importação: ex. `ImportError: cannot import name 'RollingOriginValidator'` ,
  * Erros de atributo: ex. `AttributeError: 'SimpleImputer' object has no attribute 'add_indicator`
  
  Para contornar esse problema, execute uma das duas etapas a seguir, dependendo da sua versão de treinamento do SDK do AutoML:
  1. Se sua versão de treinamento do SDK do AutoML for maior que 1.13.0, você precisará de `pandas == 0.25.1` e `sckit-learn==0.22.1` . Se houver uma incompatibilidade de versão, atualize scikit-Learn e/ou pandas para corrigir a versão, conforme mostrado abaixo:
  
  ```bash
     pip install --upgrade pandas==0.25.1
     pip install --upgrade scikit-learn==0.22.1
  ```
  
  2. Se sua versão de treinamento do SDK do AutoML for menor ou igual a 1.12.0, você precisará de `pandas == 0.23.4` e `sckit-learn==0.20.3` . Se houver uma incompatibilidade de versão, faça o downgrade de scikit-Learn e/ou pandas para corrigir a versão, conforme mostrado abaixo:
  
  ```bash
    pip install --upgrade pandas==0.23.4
    pip install --upgrade scikit-learn==0.20.3
  ```

* **Falha na implantação**: para versões <= 1.18.0 do SDK, a imagem base criada para implantação pode falhar com o seguinte erro: "ImportError: não é possível importar o nome `cached_property` de `werkzeug` ". 

  As etapas a seguir podem contornar o problema:
  1. Baixar o pacote de modelo
  2. Descompactar o pacote
  3. Implantar usando os ativos não-zipados

* A **previsão da Pontuação do R2 é sempre zero**: esse problema ocorre se os dados de treinamento fornecidos tiverem uma série temporal que contém o mesmo valor para os últimos `n_cv_splits`  +  `forecasting_horizon` pontos de dados. Se esse padrão for esperado em sua série temporal, você poderá alternar a métrica primária para um erro de quadrado médio de raiz normalizado.
 
* **TensorFlow**: a partir da versão 1.5.0 do SDK, o Machine Learning automatizado não instala modelos TensorFlow por padrão. Para instalar o TensorFlow e usá-lo com seus experimentos de ML automatizados, instale TensorFlow = = 1.12.0 via CondaDependecies. 
 
   ```python
   from azureml.core.runconfig import RunConfiguration
   from azureml.core.conda_dependencies import CondaDependencies
   run_config = RunConfiguration()
   run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
  ```
* **Gráficos de experimento**: os gráficos de classificação binária (recall de precisão, Roc, curva de lucro, etc.) mostrados em iterações de experimento de ml automatizadas não são renderizados corretamente na interface do usuário desde 4/12. Atualmente, as plotagens de gráfico estão mostrando resultados inversos, onde os modelos de melhor desempenho são mostrados com resultados mais baixos. Uma resolução está sob investigação.

* **Databricks cancelar uma execução automatizada do Machine Learning**: quando você usa recursos automatizados de aprendizado de máquina no Azure Databricks, para cancelar uma execução e iniciar uma nova execução de experimento, reinicie o cluster Azure Databricks.

* **Databricks >10 iterações para o aprendizado de máquina automatizado**: em configurações automatizadas do Machine Learning, se você tiver mais de 10 iterações, defina `show_output` como `False` ao enviar a execução.

* **Widget do databricks para o SDK do Azure Machine Learning e o aprendizado de máquina automatizado**: o widget do sdk do Azure Machine Learning não tem suporte em um bloco de anotações do databricks porque os notebooks não podem analisar widgets HTML. Você pode exibir o widget no portal usando este código Python na célula Azure Databricks notebook:

    ```
    displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
    ```
* **automl_setup falha**: 
    * No Windows, execute automl_setup de um prompt do Anaconda. Use este link para [instalar o Miniconda](https://docs.conda.io/en/latest/miniconda.html).
    * Verifique se o Conda 64-bit está instalado, em vez de 32 bits executando o `conda info` comando. O `platform` deve ser `win-64` para Windows ou `osx-64` para Mac.
    * Verifique se o Conda 4.4.10 ou posterior está instalado. Você pode verificar a versão com o comando `conda -V` . Se você tiver uma versão anterior instalada, poderá atualizá-la usando o comando: `conda update conda` .
    * Linux `gcc: error trying to exec 'cc1plus'`
      *  Se o `gcc: error trying to exec 'cc1plus': execvp: No such file or directory` erro for encontrado, instale o princípios de Build usando o comando `sudo apt-get install build-essential` .
      * Passe um novo nome como o primeiro parâmetro para automl_setup para criar um novo ambiente Conda. Exibir ambientes Conda existentes usando `conda env list` e removê-los com o `conda env remove -n <environmentname>` .
      
* **automl_setup_linux. sh falhará**: se automl_setup_linus. sh falhar em Ubuntu Linux com o erro: `unable to execute 'gcc': No such file or directory`-
  1. Verifique se as portas de saída 53 e 80 estão habilitadas. Em uma VM do Azure, você pode fazer isso na portal do Azure selecionando a VM e clicando em rede.
  2. Execute o comando: `sudo apt-get update`
  3. Execute o comando: `sudo apt-get install build-essential --fix-missing`
  4. Executar `automl_setup_linux.sh` novamente

* **Configuration. ipynb falha**:
  * Para Conda locais, primeiro verifique se automl_setup foi executado com êxito.
  * Verifique se o subscription_id está correto. Localize os subscription_id na portal do Azure selecionando todos os serviços e, em seguida, assinaturas. Os caracteres "<" e ">" não devem ser incluídos no valor de subscription_id. Por exemplo, `subscription_id = "12345678-90ab-1234-5678-1234567890abcd"` tem o formato válido.
  * Verifique se o acesso de colaborador ou proprietário à assinatura.
  * Verifique se a região é uma das regiões com suporte:,,,,,, `eastus2` `eastus` `westcentralus` `southeastasia` `westeurope` `australiaeast` `westus2` , `southcentralus` .
  * Verifique o acesso à região usando o portal do Azure.
  
* **falha ao importar AutoMLConfig**: houve alterações de pacote na versão do Machine Learning automatizada 1.0.76, que requer que a versão anterior seja desinstalada antes de atualizar para a nova versão. Se o `ImportError: cannot import name AutoMLConfig` for encontrado após a atualização de uma versão do SDK antes de v 1.0.76 para v 1.0.76 ou posterior, resolva o erro executando: `pip uninstall azureml-train automl` e, em seguida `pip install azureml-train-auotml` . O script automl_setup. cmd faz isso automaticamente. 

* **Workspace.from_config falha**: se as chamadas ws = Workspace.from_config () ' falharem-
  1. Verifique se o bloco de anotações Configuration. ipynb foi executado com êxito.
  2. Se o bloco de anotações estiver sendo executado de uma pasta que não está sob a pasta em que o `configuration.ipynb` foi executado, copie a pasta aml_config e o config.jsde arquivo que ele contém para a nova pasta. Workspace.from_config lê o config.jsem para a pasta do bloco de anotações ou sua pasta pai.
  3. Se uma nova assinatura, um grupo de recursos, um espaço de trabalho ou uma região estiver sendo usada, certifique-se de executar o `configuration.ipynb` bloco de anotações novamente. Alterar config.jsdiretamente só funcionará se o espaço de trabalho já existir no grupo de recursos especificado na assinatura especificada.
  4. Se você quiser alterar a região, altere o espaço de trabalho, o grupo de recursos ou a assinatura. `Workspace.create` não criará ou atualizará um espaço de trabalho se ele já existir, mesmo se a região especificada for diferente.
  
* **Falha no bloco de anotações de exemplo**: se um bloco de anotações de exemplo falhar com um erro que a propriedade, o método ou a biblioteca não existe:
  * Verifique se o kernel correto foi selecionado no notebook jupyter. O kernel é exibido no canto superior direito da página do bloco de anotações. O padrão é azure_automl. Observe que o kernel é salvo como parte do bloco de anotações. Portanto, se você alternar para um novo ambiente Conda, terá que selecionar o novo kernel no bloco de anotações.
      * Por Azure Notebooks, ele deve ser Python 3,6. 
      * Para ambientes Conda locais, ele deve ser o nome do ambiente Conda que você especificou em automl_setup.
  * Verifique se o notebook é para a versão do SDK que você está usando. Você pode verificar a versão do SDK executando `azureml.core.VERSION` em uma célula do jupyter notebook. Você pode baixar a versão anterior dos blocos de anotações de exemplo do GitHub clicando no `Branch` botão, selecionando a `Tags` guia e, em seguida, selecionando a versão.

* **Falha na importação do numpy no Windows**: alguns ambientes do Windows veem um erro ao carregar o numpy com a versão mais recente do Python 3.6.8. Se você vir esse problema, tente com a versão 3.6.7 do Python.

* **Falha na importação do numpy**: Verifique a versão do TensorFlow no ambiente de Conda do ml automatizado. As versões com suporte são < 1,13. Desinstalar o TensorFlow do ambiente se a versão for >= 1,13, você poderá verificar a versão do TensorFlow e desinstalar da seguinte maneira-
  1. Inicie um shell de comando, ative o ambiente Conda onde os pacotes de ml automatizados são instalados.
  2. Insira `pip freeze` e procure `tensorflow` , se encontrado, a versão listada deve ser < 1,13
  3. Se a versão listada não for uma versão com suporte, `pip uninstall tensorflow` no Shell de comando e digite y para confirmação.

## <a name="model-explanations"></a>Explicações do modelo

* **Dados esparsos sem suporte**: o modelo explica a explicação do painel/diminui substancialmente com um grande número de recursos, portanto, no momento, não há suporte para o formato de dados esparsos. Além disso, problemas gerais de memória surgirão com grandes conjuntos de altos e um grande número de recursos. 

* **Modelos de previsão sem suporte com explicações de modelo**: interpretação, melhor explicação de modelo, não está disponível para experimentos de previsão de AutoML que recomendam os seguintes algoritmos como o melhor modelo: TCNForecaster, AutoArima, ExponentialSmoothing, Average, Naive, média sazonal e Naive sazonal. A previsão de AutoML tem modelos de regressão que dão suporte a explicações. No entanto, na explicação dashbord, a guia "importância de recurso individual" não tem suporte apenas para previsão devido à complexidade em seus pipelines de dados.

* **Explicação local para o índice de dados**: o painel de explicação não oferece suporte à relação de valores de importância local para um identificador de linha do DataSet de validação original se esse conjunto de dados for maior do que 5000 pontos de dados como o Dashboard downsamples aleatoriamente. No entanto, o painel mostra os valores de recurso de conjunto de forma bruto para cada DataPoint passado para o painel na guia importância de recurso individual. Os usuários podem mapear as importâncias locais de volta para o conjunto de recursos original, por meio da correspondência dos valores de recurso de DataSet bruto. Se o tamanho do conjunto de recursos de validação for inferior a 5000 amostras, o `index` recurso no AzureML Studio corresponderá ao índice no conjunto de recursos de validação.

* **Não há suporte para plotagens What-If/Ice no AML Studio**: What-If e gráficos de expectativa condicional (ICE) individuais não têm suporte no AzureML Studio na guia explicações, uma vez que a explicação carregada precisa de uma computação ativa para recalcular previsões e probabilidades de recursos de perturbed. No momento, ele tem suporte em notebooks Jupyter quando executado como um widget usando o SDK.

## <a name="deploy--serve-models"></a>Implantar e fornecer modelos

Execute estas ações para os seguintes erros:

|Erro  | Resolução  |
|---------|---------|
|Falha na criação da imagem ao implantar o serviço Web     |  Adicionar "pynacl = = 1.2.1" como uma dependência Pip ao arquivo Conda para configuração de imagem       |
|`['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`     |   Altere a SKU para VMs usadas em sua implantação para uma que tenha mais memória. |
|Falha de FPGA     |  Não será possível implantar modelos em FPGAs até que você tenha solicitado e recebido aprovação para cota de FPGA. Para solicitar acesso, preencha o formulário de solicitação de cota: https://aka.ms/aml-real-time-ai       |

### <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Atualizando Azure Machine Learning componentes no cluster AKS

As atualizações para Azure Machine Learning componentes instalados em um cluster do serviço kubernetes do Azure devem ser aplicadas manualmente. 

Você pode aplicar essas atualizações desanexando o cluster do espaço de trabalho Azure Machine Learning e anexando novamente o cluster ao espaço de trabalho. Se o TLS estiver habilitado no cluster, será necessário fornecer o certificado TLS/SSL e a chave privada ao anexar novamente o cluster. 

```python
compute_target = ComputeTarget(workspace=ws, name=clusterWorkspaceName)
compute_target.detach()
compute_target.wait_for_completion(show_output=True)

attach_config = AksCompute.attach_configuration(resource_group=resourceGroup, cluster_name=kubernetesClusterName)

## If SSL is enabled.
attach_config.enable_ssl(
    ssl_cert_pem_file="cert.pem",
    ssl_key_pem_file="key.pem",
    ssl_cname=sslCname)

attach_config.validate_configuration()

compute_target = ComputeTarget.attach(workspace=ws, name=args.clusterWorkspaceName, attach_configuration=attach_config)
compute_target.wait_for_completion(show_output=True)
```

Se você não tiver mais o certificado TLS/SSL e a chave privada, ou se estiver usando um certificado gerado pelo Azure Machine Learning, poderá recuperar os arquivos antes de desanexar o cluster conectando-se ao cluster usando `kubectl` e recuperando o segredo `azuremlfessl` .

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes armazena os segredos no formato codificado em base-64. Você precisará de base-64 decodificar `cert.pem` os `key.pem` componentes e dos segredos antes de fornecer a eles `attach_config.enable_ssl` . 

### <a name="detaching-azure-kubernetes-service"></a>Desanexando o serviço kubernetes do Azure

Usar o Azure Machine Learning Studio, o SDK ou a extensão CLI do Azure para o aprendizado de máquina para desanexar um cluster AKS não exclui o cluster AKS. Para excluir o cluster, consulte [usar o CLI do Azure com AKs](../aks/kubernetes-walkthrough.md#delete-the-cluster).

### <a name="webservices-in-azure-kubernetes-service-failures"></a>WebServices em falhas do serviço kubernetes do Azure

Muitas falhas de webservice no Serviço de Kubernetes do Azure podem ser depuradas conectando-se ao cluster com `kubectl`. Você pode obter o `kubeconfig.json` para um cluster do serviço kubernetes do Azure executando

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="authentication-errors"></a>Erros de autenticação

Se executar uma operação de gerenciamento em um destino de computação de um trabalho remoto, você receberá um dos seguintes erros: 

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Por exemplo, você receberá um erro se tentar criar ou anexar um destino de computação de um Pipeline de ML que é enviado para execução remota.

## <a name="missing-user-interface-items-in-studio"></a>Itens de interface do usuário ausentes no estúdio

O controle de acesso baseado em função do Azure pode ser usado para restringir ações que podem ser executadas com Azure Machine Learning. Essas restrições podem impedir que itens da interface do usuário sejam mostrados no Azure Machine Learning Studio. Por exemplo, se você receber uma função que não pode criar uma instância de computação, a opção para criar uma instância de computação não será exibida no estúdio.

Para obter mais informações, confira [Gerenciar usuários e funções](how-to-assign-roles.md).

## <a name="compute-cluster-wont-resize"></a>O cluster de computação não será redimensionado

Se o cluster de computação Azure Machine Learning aparecer preso no redimensionamento (0-> 0) para o estado do nó, isso pode ser causado por bloqueios de recursos do Azure.

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>Próximas etapas

Veja mais artigos de solução de problemas para Azure Machine Learning:

* [Solução de problemas de implantação do Docker com Azure Machine Learning](how-to-troubleshoot-deployment.md)
* [Depurar pipelines do Machine Learning](how-to-debug-pipelines.md)
* [Depurar a classe ParallelRunStep do SDK Azure Machine Learning](how-to-debug-parallel-run-step.md)
* [Depuração interativa de uma instância de computação do Machine Learning com VS Code](how-to-debug-visual-studio-code.md)
* [Usar Application Insights para depurar pipelines do Machine Learning](./how-to-log-pipelines-application-insights.md)
