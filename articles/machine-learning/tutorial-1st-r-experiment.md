---
title: 'Tutorial: Modelo de regressão logística em R'
titleSuffix: Azure Machine Learning
description: Neste tutorial, você criará um modelo de regressão logística usando pacotes R azuremlsdk e caret para prever a probabilidade de uma fatalidade em um acidente de automóvel.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: sgilley
author: revodavid
ms.author: davidsmi
ms.date: 02/07/2020
ms.openlocfilehash: 37f2f98e594f558a9cd3c3e5994bf17a71ff1899
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191274"
---
# <a name="tutorial-create-a-logistic-regression-model-in-r-with-azure-machine-learning"></a>Tutorial: Criar um modelo de regressão logística em R com o Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste tutorial, você usará R e o Azure Machine Learning para criar um modelo de regressão logística que prevê a probabilidade de uma fatalidade em um acidente de automóvel. Depois de concluir este tutorial, você terá o conhecimento prático do SDK de R do Azure Machine Learning para encarar o desenvolvimento de experimentos e fluxos de trabalho mais complexos.

Neste tutorial, você executa as seguintes tarefas:
> [!div class="checklist"]
> * Criar um workspace do Azure Machine Learning
> * Clonar uma pasta de anotações com os arquivos necessários para executar este tutorial em seu workspace
> * Abrir o RStudio em seu workspace
> * Carregar dados e preparar para o treinamento
> * Carregar dados em um armazenamento de dados para que estejam disponíveis para treinamento remoto
> * Criar um recurso de computação para treinar remotamente o modelo
> * Treinar um modelo `caret` para prever a probabilidade de uma fatalidade
> * Implantar um ponto de extremidade de previsão
> * Testar o modelo do R

Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).


## <a name="create-a-workspace"></a>Criar um workspace

Um Workspace do Azure Machine Learning é o recurso fundamental na nuvem que você usa para experimentar, treinar e implantar modelos de machine learning. Ele vincula sua assinatura do Azure e o grupo de recursos a um objeto facilmente consumido no serviço. 

Você cria um workspace pelo portal do Azure, um console baseado na Web para gerenciar seus recursos do Azure. 

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Anote seu **workspace** e sua **assinatura**. Você precisará dessas informações para criar o experimento no local certo. 


## <a name="azure"></a>Clonar uma pasta do notebook

Este exemplo usa o servidor de notebook em nuvem em seu workspace para uma experiência pré-configurada e sem instalação. Use [seu próprio ambiente](how-to-configure-environment.md#local) se preferir ter controle sobre seu ambiente, pacotes e dependências.

Conclua a configuração do experimento a seguir e execute as etapas no Azure Machine Learning Studio, uma interface consolidada que inclui ferramentas de aprendizado de máquina para realizar cenários de ciência de dados para praticantes de todos os níveis de habilidade.

1. Entre no [Estúdio do Azure Machine Learning](https://ml.azure.com/).

1. Selecione a assinatura e o workspace criado.

1. Selecione **Notebooks** à esquerda.

1. Abra a pasta **samples**.

1. Abra a pasta **R**.

1. Abra a pasta com um número de versão.  Esse número representa a versão atual do SDK do R.

1. Selecione **"..."** à direita da pasta **vinhetas** e, depois, selecione **Clonar**.

    ![Clonar pasta](media/tutorial-1st-r-experiment/clone-folder.png)

1. Uma lista de pastas exibe cada usuário que acessa o espaço de trabalho.  Selecione uma pasta na qual clonar a pasta **vinhetas**.

## <a name="a-nameopenopen-rstudio"></a><a name="open">Abrir o RStudio

Use o RStudio em uma instância de computação ou VM do Notebook para executar este tutorial.  

1. Selecione **Computação** à esquerda.

1. Adicione um recurso de computação se ainda não existir um.

1. Depois que a computação estiver em execução, use o link **RStudio** para abrir o RStudio.

1. No RStudio, sua pasta *vinhetas* está alguns níveis abaixo de *Usuários* na seção **Arquivos** no canto inferior direito.  Sob a pasta *vinhetas*, selecione a pasta *train-and-deploy-to-aci* para localizar os arquivos necessários neste tutorial.

> [!Important]
> O restante deste artigo contém o mesmo conteúdo que você vê no arquivo *train-and-deploy-to-aci.Rmd*. Se você tiver experiência com o RMarkdown, fique à vontade para usar o código desse arquivo.  Ou você pode copiar/colar os code snippets de lá ou deste artigo para um script R ou para a linha de comando.  


## <a name="set-up-your-development-environment"></a>Configurar seu ambiente de desenvolvimento
A configuração do seu trabalho de desenvolvimento neste tutorial inclui as seguintes ações:

* Instalar os pacotes necessários
* Conectar-se ao workspace, para que sua instância de computação possa se comunicar com recursos remotos
* Criar um experimento para acompanhar suas execuções
* Criar um destino de computação remoto a ser usado para treinar

### <a name="install-required-packages"></a>Instalar os pacotes necessários
Este tutorial pressupõe que você já tem o SDK do Azure ML instalado. Vá em frente e importe o pacote **azuremlsdk**.

```R
library(azuremlsdk)
```

Os scripts de treinamento e pontuação (`accidents.R` e `accident_predict.R`) têm algumas dependências adicionais. Se você planeja executar esses scripts localmente, verifique se você tem esses pacotes necessários também.

### <a name="load-your-workspace"></a>Carregar seu workspace
Crie a instância de um objeto de workspace com base em seu workspace existente. O código a seguir carregará os detalhes do workspace do arquivo **config.json**. Você também pode recuperar um workspace usando [`get_workspace()`](https://azure.github.io/azureml-sdk-for-r/reference/get_workspace.html).

```R
ws <- load_workspace_from_config()
```

### <a name="create-an-experiment"></a>Criar uma experiência
Um experimento do Azure ML rastreia um agrupamento de execuções, normalmente do mesmo script de treinamento. Crie um experimento para rastrear as execuções para treinar o modelo caret nos dados de acidentes.

```R
experiment_name <- "accident-logreg"
exp <- experiment(ws, experiment_name)
```

### <a name="create-a-compute-target"></a>Criar um destino de computação
Usando a Computação do Azure Machine Learning (AmlCompute), um serviço gerenciado, os cientistas de dados podem treinar modelos de aprendizado de máquina em clusters de máquinas virtuais do Azure. Exemplos incluem máquinas virtuais com suporte a GPU. Neste tutorial, você criará um cluster AmlCompute de nó único como seu ambiente de treinamento. O código abaixo criará o cluster de cálculo se ele ainda não existir no seu workspace.

Talvez seja necessário aguardar poucos minutos para seu cluster de cálculo ser provisionado se ele ainda não existir.

```R
cluster_name <- "rcluster"
compute_target <- get_compute(ws, cluster_name = cluster_name)
if (is.null(compute_target)) {
  vm_size <- "STANDARD_D2_V2" 
  compute_target <- create_aml_compute(workspace = ws,
                                       cluster_name = cluster_name,
                                       vm_size = vm_size,
                                       max_nodes = 1)
}

wait_for_provisioning_completion(compute_target)
```

## <a name="prepare-data-for-training"></a>Preparar dados para treinamento
Este tutorial usa dados do [National Highway Traffic Safety Administration do Departamento de Transporte dos Estados Unidos](https://cdan.nhtsa.gov/tsftables/tsfar.htm) (agradecemos a [Mary C. Meyer e Tremika Finney](https://www.stat.colostate.edu/~meyer/airbags.htm)).
Esse conjunto de dados inclui dados de mais de 25 mil batidas de carro nos EUA, com variáveis que podem ser usadas para prever a probabilidade de uma fatalidade. Primeiro, importe os dados para o R e transforme-os em um novo dataframe `accidents` para análise e exporte-os para um arquivo `Rdata`.

```R
nassCDS <- read.csv("nassCDS.csv", 
                     colClasses=c("factor","numeric","factor",
                                  "factor","factor","numeric",
                                  "factor","numeric","numeric",
                                  "numeric","character","character",
                                  "numeric","numeric","character"))
accidents <- na.omit(nassCDS[,c("dead","dvcat","seatbelt","frontal","sex","ageOFocc","yearVeh","airbag","occRole")])
accidents$frontal <- factor(accidents$frontal, labels=c("notfrontal","frontal"))
accidents$occRole <- factor(accidents$occRole)
accidents$dvcat <- ordered(accidents$dvcat, 
                          levels=c("1-9km/h","10-24","25-39","40-54","55+"))

saveRDS(accidents, file="accidents.Rd")
```

### <a name="upload-data-to-the-datastore"></a>Carregar dados para o armazenamento de dados
Carregue dados para a nuvem para que eles possam ser acessados por seu ambiente de treinamento remoto. Cada workspace do Azure Machine Learning vem com um armazenamento de dados padrão que armazena informações de conexão no contêiner de blobs do Azure que é provisionado na conta de armazenamento anexada ao workspace. O código a seguir carregará os dados de acidentes que você criou em cima desse armazenamento de dados.

```R
ds <- get_default_datastore(ws)

target_path <- "accidentdata"
upload_files_to_datastore(ds,
                          list("./accidents.Rd"),
                          target_path = target_path,
                          overwrite = TRUE)
```


## <a name="train-a-model"></a>Treinar um modelo

Para este tutorial, ajuste um modelo de regressão lógica nos dados carregados usando seu cluster de cálculo remoto. Para enviar um trabalho, você precisa:

* Preparar o script de treinamento
* Criar um estimador
* Enviar o trabalho

### <a name="prepare-the-training-script"></a>Preparar o script de treinamento
Um script de treinamento chamado `accidents.R` foi fornecido para você no mesmo diretório que este tutorial. Observe os detalhes a seguir **dentro do script de treinamento** que foram feitos para usar o Azure Machine Learning para treinamento:

* O script de treinamento usa um argumento `-d` para localizar o diretório que contém os dados de treinamento. Quando você definir e enviar seu trabalho posteriormente, você apontará para o armazenamento de dados deste argumento. O Azure ML montará a pasta de armazenamento para o cluster remoto do trabalho de treinamento.
* O script de treinamento registra a precisão final como uma métrica no registro de execução no Azure ML usando `log_metric_to_run()`. O SDK do Azure ML fornece um conjunto de APIs de registro para registrar várias métricas durante execuções de treinamento. Essas métricas são registradas e continuam no registro de execução do experimento. As métricas podem ser acessadas a qualquer momento ou exibidas na página de detalhes da execução no [estúdio](https://ml.azure.com). Consulte a [referência](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-training-experimentation) para obter o conjunto completo de métodos de registro em log `log_*()`.
* O script de treinamento salva o modelo em um diretório chamado **outputs**. A pasta `./outputs` recebe tratamento especial pelo Azure ML. Durante o treinamento, os arquivos gravados no `./outputs` são carregados automaticamente em seu registro de execução pelo Azure ML e persistidos como artefatos. Ao salvar o modelo treinado em `./outputs`, você poderá acessar e recuperar seu arquivo de modelo mesmo depois que a execução terminar e você não tiver mais acesso ao seu ambiente de treinamento remoto.

### <a name="create-an-estimator"></a>Criar um estimador

Um estimador do Azure ML encapsula as informações de configuração de execução necessárias para executar um script de treinamento no destino de computação. As execuções do Azure ML são realizadas como trabalhos em contêineres no destino de computação especificado. Por padrão, a imagem do Docker criada para seu trabalho de treinamento incluirá o R, o SDK do Azure ML e um conjunto de pacotes R comumente usados. Consulte a lista completa de pacotes padrão incluídos aqui.

Para criar o estimador, defina:

* O diretório que contém seus scripts necessários para o treinamento (`source_directory`). Todos os arquivos neste diretório são carregados para os nós de cluster para execução. O diretório deve conter o script de treinamento e scripts adicionais necessários.
* O script de treinamento que será executado (`entry_script`).
* O destino de computação (`compute_target`), neste caso, o cluster AmlCompute criado anteriormente.
* Os parâmetros necessários do script de treinamento (`script_params`). O Azure ML executará o script de treinamento como um script de linha de comando com `Rscript`. Neste tutorial, você especifica um argumento para o script, o ponto de montagem do diretório de dados, que pode ser acessado com `ds$path(target_path)`.
* Dependências de ambiente necessárias para o treinamento. A imagem do Docker padrão criada para o treinamento já contém os três pacotes (`caret`, `e1071` e `optparse`) necessários no script de treinamento.  Portanto, você não precisa especificar informações adicionais. Se você estiver usando pacotes R não incluídos por padrão, use o parâmetro `cran_packages` do estimador para adicionar mais pacotes CRAN. Consulte a referência do [`estimator()`](https://azure.github.io/azureml-sdk-for-r/reference/estimator.html) para obter o conjunto completo de opções configuráveis.

```R
est <- estimator(source_directory = ".",
                 entry_script = "accidents.R",
                 script_params = list("--data_folder" = ds$path(target_path)),
                 compute_target = compute_target
                 )
```

### <a name="submit-the-job-on-the-remote-cluster"></a>Enviar o trabalho no cluster remoto

Por fim, envie o trabalho para ser executado no cluster. `submit_experiment()` retorna um objeto Run que você usa para fazer a interface com a execução. No total, a primeira execução leva **aproximadamente 10 minutos**. Mas, para execuções posteriores, a mesma imagem do Docker é reutilizada desde que as dependências do script não sejam alteradas.  Nesse caso, a imagem é armazenada em cache e o tempo de inicialização do contêiner é muito mais rápido.

```R
run <- submit_experiment(exp, est)
```

Você pode exibir os detalhes da execução do Visualizador do RStudio. Clicar no link “Exibição da Web” fornecido direcionará você para o estúdio do Azure Machine Learning, em que você pode monitorar a execução na interface do usuário.

```R
view_run_details(run)
```

O treinamento do modelo acontece em segundo plano. Aguarde até que o modelo tenha concluído o treinamento antes de você executar mais código.

```R
wait_for_run_completion(run, show_output = TRUE)
```

Você e seus colegas com acesso ao workspace podem enviar vários experimentos em paralelo, e o Azure ML cuidará do agendamento de tarefas no cluster de cálculo. Você poderá, inclusive, configurar o cluster para escalar verticalmente até vários nós e escalar de volta quando não houver mais nenhuma tarefa de computação na fila. Essa configuração é uma maneira econômica de as equipes compartilharem recursos de computação.

## <a name="retrieve-training-results"></a>Recuperar resultados do treinamento
Depois que o treinamento do modelo for concluído, você poderá acessar os artefatos do seu trabalho persistidos no registro de execução, incluindo métricas registradas e o modelo treinado final.

### <a name="get-the-logged-metrics"></a>Obter as métricas registradas em log
No script de treinamento `accidents.R`, você registrou em log uma métrica de seu modelo: a precisão das previsões nos dados de treinamento. Você pode ver métricas no [estúdio](https://ml.azure.com) ou extraí-las para a sessão local como uma lista do R da seguinte maneira:

```R
metrics <- get_run_metrics(run)
metrics
```

Se você tiver executado vários experimentos (digamos, usando diferentes variáveis, algoritmos ou hiperparâmetros), poderá usar as métricas de cada execução para comparar e escolher o modelo que você usará na produção.

### <a name="get-the-trained-model"></a>Obter o modelo treinado
Você pode recuperar o modelo treinado e examinar os resultados em sua sessão local do R. O código a seguir baixará o conteúdo do diretório `./outputs`, que inclui o arquivo de modelo.

```R
download_files_from_run(run, prefix="outputs/")
accident_model <- readRDS("outputs/model.rds")
summary(accident_model)
```

Você verá alguns fatores que contribuem para um aumento na probabilidade estimada de morte:

* maior velocidade de impacto 
* motorista homem
* ocupante mais velho
* passageiros

Você verá menores probabilidades de morte com:

* presença airbags
* presença de cintos de segurança
* colisão frontal 

O ano de fabricação do veículo não tem um efeito significativo.

Você pode usar esse modelo para fazer novas previsões:

```R
newdata <- data.frame( # valid values shown below
 dvcat="10-24",        # "1-9km/h" "10-24"   "25-39"   "40-54"   "55+"  
 seatbelt="none",      # "none"   "belted"  
 frontal="frontal",    # "notfrontal" "frontal"
 sex="f",              # "f" "m"
 ageOFocc=16,          # age in years, 16-97
 yearVeh=2002,         # year of vehicle, 1955-2003
 airbag="none",        # "none"   "airbag"   
 occRole="pass"        # "driver" "pass"
 )

## predicted probability of death for these variables, as a percentage
as.numeric(predict(accident_model,newdata, type="response")*100)
```

## <a name="deploy-as-a-web-service"></a>Implantar como um serviço Web

Com seu modelo, você pode prever o perigo de morte decorrente de uma colisão. Use o Azure ML para implantar seu modelo como um serviço de previsão. Neste tutorial, você implantará o serviço Web no ACI [(Instâncias de Contêiner do Azure)](https://docs.microsoft.com/azure/container-instances/).

### <a name="register-the-model"></a>Registre o modelo

Primeiro, registre o modelo baixado para seu workspace com [`register_model()`](https://azure.github.io/azureml-sdk-for-r/reference/register_model.html). Um modelo registrado pode ser qualquer coleção de arquivos, mas, nesse caso, o objeto de modelo de R é suficiente. O Azure ML usará o modelo registrado para implantação.

```R
model <- register_model(ws, 
                        model_path = "outputs/model.rds", 
                        model_name = "accidents_model",
                        description = "Predict probablity of auto accident")
```

### <a name="define-the-inference-dependencies"></a>Definir as dependências de inferência
Para criar um serviço Web para seu modelo, primeiro você precisa criar um script de pontuação (`entry_script`), um script R que usará os valores de variável de entrada (em formato JSON) e gerará uma previsão com base no seu modelo. Para este tutorial, use o arquivo de pontuação fornecido `accident_predict.R`. O script de pontuação deve conter um método `init()` que carrega seu modelo e retorna uma função que usa o modelo para fazer uma previsão com base nos dados de entrada. Consulte a [documentação](https://azure.github.io/azureml-sdk-for-r/reference/inference_config.html#details) para obter mais detalhes.

Em seguida, defina um **ambiente** do Azure ML para as dependências de pacote do script. Com um ambiente, você especifica os pacotes R (de CRAN ou outro lugar) necessários para que o script seja executado. Você também pode fornecer os valores de variáveis de ambiente que seu script pode referenciar para modificar seu comportamento. Por padrão, o Azure ML criará a mesma imagem do Docker padrão usada com o estimador para treinamento. Como o tutorial não tem requisitos especiais, crie um ambiente sem atributos especiais.

```R
r_env <- r_environment(name = "basic_env")
```

Se desejar usar sua própria imagem do Docker para implantação, especifique o parâmetro `custom_docker_image`. Consulte a referência do [`r_environment()`](https://azure.github.io/azureml-sdk-for-r/reference/r_environment.html) para obter o conjunto completo de opções configuráveis para definir um ambiente.

Agora você tem tudo de que precisa para criar uma **configuração de inferência** para encapsular seu script de pontuação e dependências de ambiente.

```R
inference_config <- inference_config(
  entry_script = "accident_predict.R",
  environment = r_env)
```

### <a name="deploy-to-aci"></a>Implantar no ACI
Neste tutorial, você implantará seu serviço no ACI. Esse código provisiona um único contêiner para responder a solicitações de entrada, adequado para teste e cargas leves. Consulte [`aci_webservice_deployment_config()`](https://azure.github.io/azureml-sdk-for-r/reference/aci_webservice_deployment_config.html) para obter opções configuráveis adicionais. (Para implantações em escala de produção, você também pode [implantar no Serviço de Kubernetes do Azure](https://azure.github.io/azureml-sdk-for-r/articles/deploy-to-aks/deploy-to-aks.html).)

``` R
aci_config <- aci_webservice_deployment_config(cpu_cores = 1, memory_gb = 0.5)
```

Agora você implanta seu modelo como um serviço Web. A implantação **pode levar vários minutos**. 

```R
aci_service <- deploy_model(ws, 
                            'accident-pred', 
                            list(model), 
                            inference_config, 
                            aci_config)

wait_for_deployment(aci_service, show_output = TRUE)
```

## <a name="test-the-deployed-service"></a>Testar o serviço implantado

Agora que seu modelo é implantado como um serviço, você pode testar o serviço do R usando [`invoke_webservice()`](https://azure.github.io/azureml-sdk-for-r/reference/invoke_webservice.html).  Forneça um novo conjunto de dados do qual prever, converta-o em JSON e envie-o para o serviço.

```R
library(jsonlite)

newdata <- data.frame( # valid values shown below
 dvcat="10-24",        # "1-9km/h" "10-24"   "25-39"   "40-54"   "55+"  
 seatbelt="none",      # "none"   "belted"  
 frontal="frontal",    # "notfrontal" "frontal"
 sex="f",              # "f" "m"
 ageOFocc=22,          # age in years, 16-97
 yearVeh=2002,         # year of vehicle, 1955-2003
 airbag="none",        # "none"   "airbag"   
 occRole="pass"        # "driver" "pass"
 )

prob <- invoke_webservice(aci_service, toJSON(newdata))
prob
```

Você também pode obter o ponto de extremidade HTTP do serviço Web, que aceita as chamadas do cliente REST. Você pode compartilhar esse ponto de extremidade com qualquer pessoa que queira testar o serviço Web ou integrá-lo a um aplicativo.

```R
aci_service$scoring_uri
```

## <a name="clean-up-resources"></a>Limpar os recursos

Exclua os recursos depois que não precisar mais deles. Não exclua nenhum recurso que você ainda planeja usar. 

Exclua o serviço Web:
```R
delete_webservice(aci_service)
```

Exclua o modelo registrado:
```R
delete_model(model)
```

Exclua o cluster de cálculo:
```R
delete_compute(compute)
```

### <a name="delete-everything"></a>Excluir tudo

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Você também pode manter o grupo de recursos, mas excluir um único workspace. Exiba as propriedades do workspace e, em seguida, selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

* Agora que você concluiu seu primeiro experimento do Azure Machine Learning no R, saiba mais sobre o [SDK do Azure Machine Learning para R](https://azure.github.io/azureml-sdk-for-r/index.html).

* Saiba mais sobre Azure Machine Learning com R mostrado nos exemplos na outra pasta *vinhetas*.
