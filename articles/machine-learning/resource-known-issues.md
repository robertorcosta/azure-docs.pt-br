---
title: Problemas conhecidos e soluções
titleSuffix: Azure Machine Learning
description: Obtenha uma lista dos problemas conhecidos, solução de solução e solução de problemas para o Azure Machine Learning.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d5525c02edb30eff0ee8971a382f2acb8f2e57ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455716"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning"></a>Problemas conhecidos e solução de problemas do Azure Machine Learning

Este artigo ajuda você a encontrar e corrigir erros ou falhas encontradas ao usar o Azure Machine Learning.

## <a name="sdk-installation-issues"></a>Problemas de instalação do SDK

**Mensagem de erro: não é possível desinstalar 'PyYAML'**

Azure Machine Learning SDK for Python: PyYAML é um projeto instalado distutils. Portanto, não é possível determinar com precisão quais arquivos pertencem a ele no caso de uma desinstalação parcial. Para continuar a instalação do SDK ignorando esse erro, use:

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

**Mensagem de erro: `ERROR: No matching distribution found for azureml-dataprep-native`**

A distribuição Python 3.7.4 da Anaconda tem um bug que quebra a instalação azureml-sdk. Este problema é discutido neste [problema do GitHub](https://github.com/ContinuumIO/anaconda-issues/issues/11195) Isso pode ser trabalhado criando um novo Ambiente Conda usando este comando:
```bash
conda create -n <env-name> python=3.7.3
```
O que cria um Ambiente Conda usando python 3.7.3, que não tem o problema de instalação presente no 3.7.4.

## <a name="training-and-experimentation-issues"></a>Questões de treinamento e experimentação

### <a name="metric-document-is-too-large"></a>Documento métrico é muito grande
O Azure Machine Learning tem limites internos sobre o tamanho dos objetos métricos que podem ser registrados de uma só vez a partir de uma corrida de treinamento. Se você encontrar um erro "Documento métrico é muito grande" ao registrar uma métrica avaliada em lista, tente dividir a lista em pedaços menores, por exemplo:

```python
run.log_list("my metric name", my_metric[:N])
run.log_list("my metric name", my_metric[N:])
```

Internamente, o Azure ML concatena os blocos com o mesmo nome métrico em uma lista contígua.

### <a name="moduleerrors-no-module-named"></a>Erros de módulo (Nenhum módulo nomeado)
Se você estiver executando ModuleErrors enquanto submete experimentos no Azure ML, isso significa que o script de treinamento está esperando que um pacote seja instalado, mas não é adicionado. Depois de fornecer o nome do pacote, o Azure ML instalará o pacote no ambiente usado para sua execução de treinamento. 

Se você estiver usando [Estimadores](concept-azure-machine-learning-architecture.md#estimators) para enviar experimentos, você `pip_packages` `conda_packages` pode especificar um nome de pacote via ou parâmetro no estimador com base na origem que deseja instalar o pacote. Você também pode especificar um arquivo yml com todas as suas dependências `conda_dependencies_file` `pip_requirements_file` usando ou listar todos os seus requisitos pip em um arquivo txt usando parâmetro. Se você tiver seu próprio objeto Azure ML Environment que deseja substituir a imagem padrão usada `environment` pelo estimador, você pode especificar esse ambiente através do parâmetro do construtor estimador.

O Azure ML também fornece estimadores específicos da estrutura para Tensorflow, PyTorch, Chainer e SKLearn. O uso desses estimadores garantirá que as dependências da estrutura central sejam instaladas em seu nome no ambiente usado para treinamento. Você tem a opção de especificar dependências extras como descrito acima. 
 
Azure ML manteve imagens docker e seu conteúdo pode ser visto em [Contêineres AzureML](https://github.com/Azure/AzureML-Containers).
As dependências específicas do framework estão listadas na respectiva documentação-quadro - [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py#remarks), [PyTorch,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py#remarks) [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py#remarks), [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py#remarks).

> [!Note]
> Se você acha que um pacote específico é comum o suficiente para ser adicionado em imagens e ambientes mantidos pelo Azure ML, por favor, levante um problema do GitHub em [Contêineres AzureML](https://github.com/Azure/AzureML-Containers). 
 
### <a name="nameerror-name-not-defined-attributeerror-object-has-no-attribute"></a>Erro de nome (Nome não definido), Erro de atributo (Objeto não tem atributo)
Essa exceção deve vir de seus scripts de treinamento. Você pode olhar os arquivos de log do portal Azure para obter mais informações sobre o nome específico não definido ou erro de atributo. A partir do SDK, você pode usar `run.get_details()` para olhar para a mensagem de erro. Isso também listará todos os arquivos de log gerados para sua execução. Por favor, certifique-se de dar uma olhada no seu script de treinamento e corrigir o erro antes de reenviar sua corrida. 

### <a name="horovod-has-been-shut-down"></a>Horovod foi desligado.
Na maioria dos casos, se você encontrar "AbortedError: Horovod foi desligado" esta exceção significa que houve uma exceção subjacente em um dos processos que fez horovod desligar. Cada classificação no trabalho de MPI obtém seu próprio arquivo de log dedicado no Azure ML. Esses registros são `70_driver_logs`nomeados . Em caso de treinamento distribuído, os nomes `_rank` dos registros são sufixos para facilitar a diferenciação dos logs. Para encontrar o erro exato que fez horovod desligar, passar `Traceback` por todos os arquivos de log e procurar no final dos arquivos driver_log. Um desses arquivos lhe dará a exceção básica. 

### <a name="sr-iov-availability-on-ncv3-machines-in-amlcompute-for-distributed-training"></a>Disponibilidade sr-IOV em máquinas NCv3 em AmlCompute para treinamento distribuído
A Azure Compute está lançando uma [atualização SR-IOV](https://azure.microsoft.com/updates/sriov-availability-on-ncv3-virtual-machines-sku/) de máquinas NCv3, que os clientes podem aproveitar com a oferta de computação gerenciada do Azure ML (AmlCompute). As atualizações permitirão o suporte de toda a pilha de MPI e o uso da rede INfiniband RDMA para melhorar o desempenho de treinamento distribuído em vários lados, especialmente para o aprendizado profundo.

Veja o [cronograma de atualização](https://azure.microsoft.com/updates/sr-iov-availability-schedule-on-ncv3-virtual-machines-sku/) para ver quando o suporte será implantado para sua região.

### <a name="run-or-experiment-deletion"></a>Exclusão de execução ou experimento
Os experimentos podem ser arquivados usando o método [Experiment.archive](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#archive--) ou a partir da exibição da guia Experiment no cliente do estúdio Azure Machine Learning através do botão "Experiência de arquivamento". Esta ação oculta o experimento de consultas e visualizações de listas, mas não o exclui.

A exclusão permanente de experimentos ou executações individuais não é suportada no momento. Para obter mais informações sobre a exclusão de ativos do Workspace, consulte [Exportar ou excluir os dados do espaço de trabalho do serviço de aprendizado de máquina](how-to-export-delete-data.md).

## <a name="azure-machine-learning-compute-issues"></a>Problemas de Computação de Aprendizado de Máquina do Azure
Problemas conhecidos com o uso do Azure Machine Learning Compute (AmlCompute).

### <a name="trouble-creating-amlcompute"></a>Problemas na criação do AmlCompute

Há uma rara chance de que alguns usuários que criaram seu espaço de trabalho Azure Machine Learning a partir do portal Azure antes da versão ga pode não ser capaz de criar AmlCompute nesse espaço de trabalho. Você pode levantar uma solicitação de suporte contra o serviço ou criar um novo espaço de trabalho através do portal ou do SDK para desbloquear-se imediatamente.

### <a name="outage-sr-iov-upgrade-to-ncv3-machines-in-amlcompute"></a>Paralisação: atualização sr-IOV para máquinas NCv3 em AmlCompute

O Azure Compute atualizará as SKUs NCv3 a partir do início de novembro de 2019 para suportar todas as implementações e versões de MPI, e verbos RDMA para máquinas virtuais equipadas com InfiniBand. Isso exigirá um curto tempo de inatividade - [leia mais sobre a atualização do SR-IOV](https://azure.microsoft.com/updates/sriov-availability-on-ncv3-virtual-machines-sku).

Como cliente da oferta de computação gerenciada do Azure Machine Learning (AmlCompute), você não é obrigado a fazer nenhuma alteração neste momento. Com base no [cronograma de atualização,](https://azure.microsoft.com/updates/sr-iov-availability-schedule-on-ncv3-virtual-machines-sku) você precisaria planejar uma pequena pausa em seu treinamento. O serviço assumirá a responsabilidade de atualizar as imagens vm em seus nós de cluster e escalar automaticamente seu cluster. Uma vez que a atualização seja concluída, você pode ser capaz de usar todas as outras distribuições de MPI (como OpenMPI com Pytorch) além de obter maior largura de banda InfiniBand, latências mais baixas e melhor desempenho de aplicativos distribuídos.

## <a name="azure-machine-learning-designer-issues"></a>Problemas de designer de Machine Learning do Azure

Problemas conhecidos com o designer.

### <a name="long-compute-preparation-time"></a>Longo tempo de preparação da computação

Criar uma nova computação ou evocar deixar a computação leva tempo, podendo ser alguns minutos ou até mais. A equipe está trabalhando para a otimização.


### <a name="cannot-run-an-experiment-only-contains-a-dataset"></a>Não é possível executar um experimento apenas contém um conjunto de dados 

Você pode querer executar um experimento que só contém conjunto de dados para visualizar o conjunto de dados. No entanto, não é permitido executar um experimento apenas contém conjunto de dados hoje. Estamos resolvendo ativamente esse problema.
 
Antes da correção, você pode conectar o conjunto de dados a qualquer módulo de transformação de dados (Selecionar Colunas no conjunto de dados, editar metadados, dividir dados etc.) e executar o experimento. Então você pode visualizar o conjunto de dados. 

Imagem abaixo mostra ![como: visulize-data](./media/resource-known-issues/aml-visualize-data.png)

## <a name="image-building-failure"></a>Falha na criação da imagem

Falha na criação da imagem ao implantar o serviço Web. Uma solução alternativa é adicionar “pynacl==1.2.1” como uma dependência de pip para o arquivo Conda da imagem de configuração.

## <a name="deployment-failure"></a>Falha na implantação

Caso veja `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`, altere o SKU das VMs usadas na implantação para uma que tenha mais memória.

## <a name="fpgas"></a>FPGAs

Não será possível implantar modelos em FPGAs até que você tenha solicitado e recebido aprovação para cota de FPGA. Para solicitar acesso, preencha o formulário de solicitação de cota: https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>Machine Learning automatizado

O aprendizado de máquina automatizado tensor Flow não suporta atualmente a versão 1.13 do fluxo de tensor. A instalação desta versão fará com que as dependências do pacote parem de funcionar. Estamos trabalhando para corrigir esse problema em um lançamento futuro.

### <a name="experiment-charts"></a>Gráficos de Experimentos

Gráficos de classificação binários (precisão-recall, ROC, curva de ganho etc.) mostrados em iterações automatizadas de experimentos ML não estão renderizando corretamente na interface do usuário desde 4/12. Os gráficos estão atualmente mostrando resultados inversos, onde modelos de melhor desempenho são mostrados com resultados mais baixos. Uma resolução está investigação.

## <a name="datasets-and-data-preparation"></a>Conjuntos de dados e preparação de dados

Estes são problemas conhecidos para conjuntos de dados de aprendizado de máquina do Azure.

### <a name="typeerror-filenotfound-no-such-file-or-directory"></a>Erro de tipo: FileNotFound: Nenhum arquivo ou diretório

Esse erro ocorre se o caminho do arquivo que você fornece não é onde o arquivo está localizado. Você precisa ter certeza de que a maneira como você se refere ao arquivo é consistente com onde você montou seu conjunto de dados no seu alvo de computação. Para garantir um estado determinístico, recomendamos usar o caminho abstrato ao montar um conjunto de dados em um alvo de computação. Por exemplo, no código a seguir, montamos o conjunto de dados `/tmp`sob a raiz do sistema de arquivos do destino de computação, . 

```python
# Note the leading / in '/tmp/dataset'
script_params = {
    '--data-folder': dset.as_named_input('dogscats_train').as_mount('/tmp/dataset'),
} 
```

Se você não incluir a barra de avanço principal, '/', você precisará prefixar o diretório de trabalho, por exemplo, `/mnt/batch/.../tmp/dataset` no alvo de computação para indicar onde você deseja que o conjunto de dados seja montado.

### <a name="fail-to-read-parquet-file-from-http-or-adls-gen-2"></a>Não leia o arquivo Parquet do HTTP ou Do ADLS Gen 2

Há um problema conhecido no AzureML DataPrep SDK versão 1.1.25 que causa uma falha ao criar um conjunto de dados lendo arquivos Parquet de HTTP ou ADLS Gen 2. Vai falhar `Cannot seek once reading started.`com. Para corrigir esse problema, atualize `azureml-dataprep` para uma versão superior a 1.1.26 ou faça downgrade para uma versão inferior a 1.1.24.

```python
pip install --upgrade azureml-dataprep
```

### <a name="typeerror-mount-got-an-unexpected-keyword-argument-invocation_id"></a>TypeError: mount() tem um argumento de palavra-chave inesperado 'invocation_id'

Este erro ocorre se você tiver `azureml-core` `azureml-dataprep`uma versão incompatível entre e . Se você vir esse `azureml-dataprep` erro, atualize o pacote para uma versão mais recente (maior ou igual a 1.1.29).

```python
pip install --upgrade azureml-dataprep
```

## <a name="databricks"></a>Databricks

Problemas do Databricks e do Azure Machine Learning.

### <a name="failure-when-installing-packages"></a>Falha ao instalar pacotes

A instalação do Azure Machine Learning SDK falha nos tijolos de dados do Azure quando mais pacotes são instalados. Alguns pacotes, como `psutil`, podem causar conflitos. Para evitar erros de instalação, instale pacotes congelando a versão da biblioteca. Este problema está relacionado com databricks e não com o Azure Machine Learning SDK. Você pode experimentar esse problema com outras bibliotecas, também. Exemplo:

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

Alternativamente, você pode usar scripts init se continuar enfrentando problemas de instalação com bibliotecas Python. Esta abordagem não é oficialmente apoiada. Para obter mais informações, consulte [scripts init com escopo de cluster](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

### <a name="cancel-an-automated-machine-learning-run"></a>Cancele uma corrida automatizada de aprendizado de máquina

Quando você usar recursos automatizados de aprendizado de máquina no Azure Databricks, para cancelar uma execução e iniciar uma nova execução de experimentos, reinicie o cluster Azure Databricks.

### <a name="10-iterations-for-automated-machine-learning"></a>>10 iterações para aprendizado automático de máquina

Em configurações automatizadas de aprendizado de máquina, se você `show_output` `False` tiver mais de 10 iterações, defina para quando você enviar a execução.

### <a name="widget-for-the-azure-machine-learning-sdk-and-automated-machine-learning"></a>Widget para o Azure Machine Learning SDK e aprendizado automático de máquina

O widget Azure Machine Learning SDK não é suportado em um notebook Databricks porque os notebooks não podem analisar widgets HTML. Você pode visualizar o widget no portal usando este código Python em sua célula de notebook Azure Databricks:

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-cannot-import-name-timedelta-from-pandas_libstslibs"></a>Erro de importação: não é possível importar o nome 'Timedelta' de 'pandas._libs.tslibs'

Se você vir esse erro ao usar aprendizado automático de máquina, execute as duas linhas a seguir em seu notebook:
```
%sh rm -rf /databricks/python/lib/python3.7/site-packages/pandas-0.23.4.dist-info /databricks/python/lib/python3.7/site-packages/pandas
%sh /databricks/python/bin/pip install pandas==0.23.4
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>Erro de importação: Nenhum módulo chamado 'pandas.core.indexes'

Se você vir esse erro ao usar aprendizado automático de máquina:

1. Execute este comando para instalar dois pacotes no cluster Azure Databricks:

   ```bash
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. Desconecte-se e, em seguida, reconecte o cluster ao seu notebook.

Se essas etapas não resolverem o problema, tente reiniciar o cluster.

### <a name="failtosendfeather"></a>FailToSendFeather

Se você `FailToSendFeather` vir um erro ao ler dados no cluster Azure Databricks, consulte as seguintes soluções:

* Upgrade `azureml-sdk[automl]` pacote para a versão mais recente.
* Adicione `azureml-dataprep` a versão 1.1.8 ou superior.
* Adicione `pyarrow` a versão 0.11 ou superior.

## <a name="azure-portal"></a>Portal do Azure

Se você for diretamente exibir seu workspace a partir de um link de compartilhamento do SDK ou do portal, não poderá exibir a página de Visão Geral normal com as informações de assinatura na extensão. Você também não poderá alternar para outro workspace. Se você precisar ver outro espaço de trabalho, a solução é ir diretamente ao [estúdio azure Machine Learning](https://ml.azure.com) e procurar o nome do espaço de trabalho.

## <a name="diagnostic-logs"></a>Logs de diagnóstico

Às vezes, pode ser útil fornecer informações de diagnóstico ao pedir ajuda. Para ver alguns registros, visite [o estúdio Azure Machine Learning](https://ml.azure.com) e vá ao seu espaço de trabalho e selecione **Workspace > Experiment > Run > Logs**.  

> [!NOTE]
> O Azure Machine Learning registra informações de várias fontes durante o treinamento, como o AutoML ou o contêiner Docker que executa o trabalho de treinamento. Muitos desses registros não estão documentados. Se você encontrar problemas e entrar em contato com o suporte da Microsoft, eles poderão usar esses logs durante a solução de problemas.

## <a name="activity-logs"></a>Logs de atividade

Algumas ações dentro do espaço de trabalho Azure Machine Learning não registram informações no __registro de atividades__. Por exemplo, iniciar uma corrida de treinamento ou registrar um modelo.

Algumas dessas ações aparecem na área de __Atividades__ do seu espaço de trabalho, porém não indicam quem iniciou a atividade.

## <a name="resource-quotas"></a>Cotas de recursos

Saiba mais sobre as [cotas de recursos](how-to-manage-quotas.md) que você pode encontrar ao trabalhar com o Azure Machine Learning.

## <a name="authentication-errors"></a>Erros de autenticação

Se executar uma operação de gerenciamento em um destino de computação de um trabalho remoto, você receberá um dos seguintes erros:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Por exemplo, você receberá um erro se tentar criar ou anexar um destino de computação de um Pipeline de ML que é enviado para execução remota.

## <a name="overloaded-azurefile-storage"></a>Armazenamento sobrecarregado do AzureFile

Se você receber `Unable to upload project files to working directory in AzureFile because the storage is overloaded`um erro, aplique seguindo as contrações.

Se você estiver usando o compartilhamento de arquivos para outras cargas de trabalho, como transferência de dados, a recomendação é usar blobs para que o compartilhamento de arquivos seja livre para ser usado para enviar corridas. Você também pode dividir a carga de trabalho entre dois espaços de trabalho diferentes.

## <a name="webservices-in-azure-kubernetes-service-failures"></a>Serviços web em falhas de serviço do Azure Kubernetes

Muitas falhas no serviço de webservice no Azure Kubernetes Service `kubectl`podem ser depuradas conectando-se ao cluster usando . Você pode `kubeconfig.json` obter o para um Cluster de Serviço sazure Kubernetes executando

```azurecli-interactive
az aks get-credentials -g <rg> -n <aks cluster name>
```

## <a name="updating-azure-machine-learning-components-in-aks-cluster"></a>Atualizando os componentes de aprendizado de máquina do Azure no cluster AKS

Atualizações para os componentes de Aprendizado de Máquina do Azure instalados em um cluster Azure Kubernetes Service devem ser aplicadas manualmente. 

Você pode aplicar essas atualizações desvinculando o cluster do espaço de trabalho azure Machine Learning e, em seguida, reanexando o cluster ao espaço de trabalho. Se o SSL estiver habilitado no cluster, você precisará fornecer o certificado SSL e a chave privada ao reconectar o cluster. 

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

Se você não tiver mais o certificado SSL e a chave privada, ou estiver usando um certificado gerado pelo Azure Machine `kubectl` Learning, você `azuremlfessl`poderá recuperar os arquivos antes de separar o cluster conectando-se ao cluster usando e recuperando o segredo .

```bash
kubectl get secret/azuremlfessl -o yaml
```

>[!Note]
>Kubernetes armazena os segredos em formato codificado base-64. Você precisará decodificar a base-64 e `cert.pem` `key.pem` os componentes `attach_config.enable_ssl`dos segredos antes de fornecê-los para . 

## <a name="labeling-projects-issues"></a>Problemas de projetos de rotulagem

Problemas conhecidos com projetos de rotulagem.

### <a name="only-datasets-created-on-blob-datastores-can-be-used"></a>Apenas conjuntos de dados criados em datastores blob podem ser usados

Esta é uma limitação conhecida da versão atual.

### <a name="after-creation-the-project-shows-initializing-for-a-long-time"></a>Após a criação, o projeto mostra "Inicialização" por um longo tempo

Atualize manualmente a página. A inicialização deve prosseguir em cerca de 20 pontos de dados por segundo. A falta de autoatualização é um problema conhecido. 

### <a name="when-reviewing-images-newly-labeled-images-are-not-shown"></a>Ao revisar imagens, imagens recém-rotuladas não são mostradas

Para carregar todas as imagens rotuladas, escolha o **primeiro** botão. O **primeiro** botão o levará de volta à frente da lista, mas carrega todos os dados rotulados.

### <a name="pressing-esc-key-while-labeling-for-object-detection-creates-a-zero-size-label-on-the-top-left-corner-submitting-labels-in-this-state-fails"></a>Pressionar a tecla Esc durante a rotulagem para detecção de objetos cria uma etiqueta de tamanho zero no canto superior esquerdo. O envio de etiquetas neste estado falha.

Exclua a etiqueta clicando na marca cruzada ao lado.

## <a name="moving-the-workspace"></a>Movendo o espaço de trabalho

> [!WARNING]
> A mudança do espaço de trabalho do Azure Machine Learning para uma assinatura diferente, ou a mudança da assinatura própria para um novo inquilino, não é suportada. Fazer isso pode causar erros.
