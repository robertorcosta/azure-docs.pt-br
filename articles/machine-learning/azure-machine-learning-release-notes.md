---
title: Novidades na versão
titleSuffix: Azure Machine Learning
description: Conheça as últimas atualizações do Azure Machine Learning e os SDKs Python de aprendizado de máquina e preparação de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 03/10/2020
ms.openlocfilehash: b55c351927a56afce697d07f41bfbe668144d68d
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475523"
---
# <a name="azure-machine-learning-release-notes"></a>Notas de lançamento do Azure Machine Learning

Neste artigo, conheça os lançamentos do Azure Machine Learning.  Para obter o conteúdo completo de referência do SDK, visite a página principal de referência do Azure Machine Learning [**para python.**](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)

Veja [a lista de problemas conhecidos](resource-known-issues.md) para aprender sobre erros e soluções conhecidas.

## <a name="2020-03-23"></a>2020-03-23

### <a name="azure-machine-learning-sdk-for-python-v120"></a>Azure Machine Learning SDK para Python v1.2.0

+ **Alterações da falha**
  + Suporte de queda para python 2.7

+ **Correções e melhorias de bugs**
  + **azure-cli-ml**
    + Adiciona "--assinatura-id" `az ml model/computetarget/service` aos comandos na CLI
    + Adicionando suporte para passar vault_url, key_name e key_version de vault_url de chave gerenciada pelo cliente (CMK) para implantação de ACI
  + **azureml-automl-core** 
    + Imputação personalizada habilitada com valor constante para tarefas de previsão de dados X e Y.
    + Corrigimos o problema com a exibição de mensagens de erro ao usuário.    
  + **azureml-automl-runtime**
    + Corrigimos o problema com a previsão dos conjuntos de dados, contendo grãos com apenas uma linha
    + Diminuiu a quantidade de memória exigida pelas tarefas de previsão.
    + Adicionado mensagens de erro melhores se a coluna tempo tiver formato incorreto.
    + Imputação personalizada habilitada com valor constante para tarefas de previsão de dados X e Y.
  + **azureml-core**
    + Adicionar suporte para carregar o ServicePrincipal a partir de variáveis de ambiente: AZUREML_SERVICE_PRINCIPAL_ID, AZUREML_SERVICE_PRINCIPAL_TENANT_ID e AZUREML_SERVICE_PRINCIPAL_PASSWORD
    + Introduziu um novo `support_multi_line` `Dataset.Tabular.from_delimited_files`parâmetro para`support_multi_line=False`: Por padrão (), todas as quebras de linha, incluindo as dos valores de campo citados, serão interpretadas como uma quebra de recorde. A leitura de dados dessa forma é mais rápida e otimizada para execução paralela em vários núcleos de CPU. No entanto, pode resultar em produzir silenciosamente mais registros com valores de campo desalinhados. Isso deve ser `True` definido para quando os arquivos delimitados são conhecidos por conter quebras de linha citadas.
    + Adicionado a capacidade de registrar ODLS Gen2 na CLI de aprendizado de máquina do Azure
    + Parâmetro renomeado 'fine_grain_timestamp' para 'carimbo de tempo' e parâmetro 'coarse_grain_timestamp' para 'partition_timestamp' para o método with_timestamp_columns() no TabularDataset para refletir melhor o uso dos parâmetros.
    + Aumento do comprimento máximo do nome do experimento para 255.
  + **azureml-interpret**
    + Interpretação azureml atualizada para interpretar-comunidade 0.7.*
  + **azureml-sdk**
    + Alterando para dependências com a versão compatível Tilde para o suporte de patches em versões pré-lançamento e estáveis.


## <a name="2020-03-11"></a>2020-03-11

### <a name="azure-machine-learning-sdk-for-python-v115"></a>Azure Machine Learning SDK para Python v1.1.5

+ **Depreciação de recursos**
  + **Python 2,7**
    + Última versão para suportar python 2.7

+ **Alterações da falha**
  + **Controle de Versão Semântico 2.0.0**
    + A partir da versão 1.1 O Azure ML Python SDK adota a Versão Semântica 2.0.0. [Leia mais aqui](https://semver.org/). Todas as versões subseqüentes seguirão novo esquema de numeração e contrato de versão semântica. 

+ **Correções e melhorias de bugs**
  + **azure-cli-ml**
    + Altere o nome de comando CLI de ponto final de 'az ml endpoint aks' para 'az ml endpoint realtime' para obter consistência.
    + atualizar as instruções de instalação da CLI para cli ramo estável e experimental
    + O perfil de instância única foi fixado para produzir uma recomendação e foi disponibilizado no núcleo sdk.
  + **azureml-automl-core**
    + Habilitou a inferência do modo lote (tomando várias linhas uma vez) para os modelos ONNX automáticos
    + Melhora da detecção de frequência nos conjuntos de dados, falta de dados ou contendo pontos de dados irregulares
    + Adicionada a capacidade de remover pontos de dados que não estão em conformidade com a freqüência dominante.
    + Alterou a entrada do construtor para fazer uma lista de opções para aplicar as opções de imputação para colunas correspondentes.
    + O registro de erros foi melhorado.
  + **azureml-automl-runtime**
    + Corrigimos o problema com o erro jogado se o grão que não estava presente no conjunto de treinamento sumisse no conjunto de testes
    + Removeu a exigência de y_query durante a pontuação no serviço de previsão
    + Corrigimos o problema com a previsão quando o conjunto de dados contém grãos curtos com intervalos de tempo longos.
    + Corrigimos o problema quando o horizonte max automático é ligado e a coluna de data contém datas em forma de strings. Mensagens adequadas de conversão e erro foram adicionadas para quando a conversão para data não é possível
    + Usando numpy nativo e SciPy para serializar e desserializar dados intermediários para FileCacheStore (usado para executões automáticos locais)
    + Corrigimos um bug onde corridas de crianças com falha podem ficar presas no estado de execução.
    + Aumento da velocidade de featurização.
    + Corrigimos a verificação de freqüência durante a pontuação, agora as tarefas de previsão não exigem equivalência de freqüência estrita entre o trem e o conjunto de testes.
    + Alterou a entrada do construtor para fazer uma lista de opções para aplicar as opções de imputação para colunas correspondentes.
    + Corrigimos erros relacionados à seleção do tipo de atraso.
    + Corrigimos o erro não classificado levantado nos conjuntos de dados, tendo grãos com a linha única
    + Corrigimos o problema com lentidão na detecção de frequência.
    + Corrige um bug no manuseio de exceção AutoML que fez com que a verdadeira razão para a falha de treinamento fosse substituída por um AttributeError.
  + **azureml-cli-comum**
    + O perfil de instância única foi fixado para produzir uma recomendação e foi disponibilizado no núcleo sdk.
  + **azureml-contrib-mir**
    + Adiciona funcionalidade na classe MirWebservice para recuperar o Token de acesso
    + Use o token auth for MirWebservice por padrão durante a chamada MirWebservice.run() - Somente atualize se a chamada falhar
    + A implantação do webservice mir agora requer Skus adequados [Standard_DS2_v2, Standard_F16, Standard_A2_v2] em vez de [Ds2v2, A2v2 e F16] respectivamente.
  + **azureml-contrib-pipeline-passos**
    + Parâmetro opcional side_inputs adicionado ao ParallelRunStep. Este parâmetro pode ser usado para montar pasta no recipiente. Atualmente, os tipos suportados são DataReference e PipelineData.
    + Os parâmetros aprovados no ParallelRunConfig podem ser substituídos passando parâmetros de pipeline agora. Novos parâmetros de pipeline suportados aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count e aml_process_count_per_node já fazem parte da versão anterior).
  + **azureml-core**
    + Os webservices do AzureML `INFO` implantados agora serão padrão para o registro. Isso pode ser controlado `AZUREML_LOG_LEVEL` definindo a variável ambiente no serviço implantado.
    + Python sdk usa serviço de descoberta para usar o ponto final 'api' em vez de 'pipelines'.
    + Troque para as novas rotas em todas as chamadas SDK
    + Altera o roteamento de chamadas para o ModelManagementService para uma nova estrutura unificada
      + Tornou o método de atualização do espaço de trabalho disponível publicamente.
      + Adicionado image_build_compute parâmetro no método de atualização do espaço de trabalho para permitir que o usuário atualize a computação para construção de imagens
    +  Adicionadas mensagens de depreciação ao antigo fluxo de trabalho de criação de perfil. Limites fixos de criação de perfil da CPU e da memória
    + Adicionado RSection como parte do Ambiente para executar trabalhos R
    +  Validação adicionada `Dataset.mount` para levantar erro quando a fonte do conjunto de dados não estiver acessível ou não contiver nenhum dado.
    + Adicionado `--grant-workspace-msi-access` como um parâmetro adicional para o CLI datastore para registrar o Azure Blob Container que permitirá que você registre o Blob Container que está atrás de um VNet
    + O perfil de instância única foi fixado para produzir uma recomendação e foi disponibilizado no núcleo sdk.
    + Corrigimos o problema em _deploy aks.py
    + Valida a integridade dos modelos que estão sendo carregados para evitar falhas silenciosas de armazenamento.
    + O usuário agora pode especificar um valor para a chave auth ao regenerar chaves para webservices.
    + Bug corrigido onde letras maiúsculas não podem ser usadas como nome de entrada do conjunto de dados
  + **azureml-defaults**
    + `azureml-dataprep`agora será instalado como `azureml-defaults`parte de . Não é mais necessário instalar o dataprep[fusível] manualmente em metas de computação para montar conjuntos de dados.
  + **azureml-interpret**
    + Interpretação azureml atualizada para interpretar-comunidade 0.6.*
    + A interpretação azureml atualizada para depender da interpretação-comunidade 0.5.0
    + Adicionado exceções ao estilo azureml à interpretação azureml
    + Serialização fixa do DeepScoringExplainer para modelos de keras
  + **azureml-mlflow**
    + Adicione suporte para nuvens soberanas ao azureml.mlflow
  + **azureml-pipeline-core**
    + Notebook de pontuação em lote pipeline agora usa ParallelRunStep
    + Corrigimos um bug no qual os resultados do PythonScriptStep poderiam ser reutilizados incorretamente, apesar de alterar a lista de argumentos
    + Adicionada a capacidade de definir o tipo de colunas ao chamar os métodos parse_* em`PipelineOutputFileDataset`
  + **azureml-pipeline-steps**
    + Mudou `AutoMLStep` o `azureml-pipeline-steps` pacote para o pacote. Preterido `AutoMLStep` o `azureml-train-automl-runtime`interior.
    + Exemplo de documentação adicionado para conjunto de dados como entrada PythonScriptStep
  + **azureml-tensorboard**
    + azureml-tensorboard atualizado para suportar tensorflow 2.0
    + Mostrar o número correto da porta ao usar uma porta Tensorboard personalizada em uma instância de computação
  + **azureml-train-automl-client**
    + Corrigimos um problema no qual certos pacotes podem ser instalados em versões incorretas em corridas remotas.
    + featurization fixoProblema de substituição que filtra config de featurização personalizada.
  + **azureml-trem-automl-runtime**
    + Corrigimos o problema com detecção de frequência nas corridas remotas
    + Moveu `AutoMLStep` o `azureml-pipeline-steps` pacote. Preterido `AutoMLStep` o `azureml-train-automl-runtime`interior.
  + **azureml-train-core**
    + Suporte pyTorch versão 1.4 no PyTorch Estimator
  
## <a name="2020-03-02"></a>2020-03-02

### <a name="azure-machine-learning-sdk-for-python-v112rc0-pre-release"></a>Azure Machine Learning SDK para Python v1.1.2rc0 (Pré-lançamento)

+ **Correções e melhorias de bugs**
  + **azureml-automl-core**
    + Habilitou a inferência do modo lote (tomando várias linhas uma vez) para os modelos ONNX automáticos
    + Melhora da detecção de frequência nos conjuntos de dados, falta de dados ou contendo pontos de dados irregulares
    + Adicionada a capacidade de remover pontos de dados que não estão em conformidade com a freqüência dominante.
  + **azureml-automl-runtime**
    + Corrigimos o problema com o erro jogado se o grão que não estava presente no conjunto de treinamento sumisse no conjunto de testes
    + Removeu a exigência de y_query durante a pontuação no serviço de previsão
  + **azureml-contrib-mir**
    + Adiciona funcionalidade na classe MirWebservice para recuperar o Token de acesso
  + **azureml-core**
    + Os webservices do AzureML `INFO` implantados agora serão padrão para o registro. Isso pode ser controlado `AZUREML_LOG_LEVEL` definindo a variável ambiente no serviço implantado.
    + Corrija `Dataset.get_all` a iteração para retornar todos os conjuntos de dados registrados no espaço de trabalho.
    + Melhore a mensagem de `path` erro quando o tipo inválido for passado para o argumento das APIs de criação de conjuntos de dados.
    + Python sdk usa serviço de descoberta para usar o ponto final 'api' em vez de 'pipelines'.
    + Troque para as novas rotas em todas as chamadas SDK
    + Altera o roteamento de chamadas para o ModelManagementService para uma nova estrutura unificada
      + Tornou o método de atualização do espaço de trabalho disponível publicamente.
      + Adicionado image_build_compute parâmetro no método de atualização do espaço de trabalho para permitir que o usuário atualize a computação para construção de imagens
    +  Adicionadas mensagens de depreciação ao antigo fluxo de trabalho de criação de perfil. Limites fixos de criação de perfil da CPU e da memória
  + **azureml-interpret**
    + atualizar azureml-interpret para interpretar-comunidade 0.6.*
  + **azureml-mlflow**
    + Adicione suporte para nuvens soberanas ao azureml.mlflow
  + **azureml-pipeline-steps**
    + Mudou `AutoMLStep` o `azureml-pipeline-steps package`para o . Preterido `AutoMLStep` o `azureml-train-automl-runtime`interior.
  + **azureml-train-automl-client**
    + Corrigimos um problema no qual certos pacotes podem ser instalados em versões incorretas em corridas remotas.
  + **azureml-trem-automl-runtime**
    + Corrigimos o problema com detecção de frequência nas corridas remotas
    + Mudou `AutoMLStep` o `azureml-pipeline-steps package`para o . Preterido `AutoMLStep` o `azureml-train-automl-runtime`interior.
  + **azureml-train-core**
    + Mudou `AutoMLStep` o `azureml-pipeline-steps package`para o . Preterido `AutoMLStep` o `azureml-train-automl-runtime`interior.

## <a name="2020-02-18"></a>2020-02-18

### <a name="azure-machine-learning-sdk-for-python-v111rc0-pre-release"></a>Azure Machine Learning SDK para Python v1.1.1rc0 (Pré-lançamento)

+ **Correções e melhorias de bugs**
  + **azure-cli-ml**
    + O perfil de instância única foi fixado para produzir uma recomendação e foi disponibilizado no núcleo sdk.
  + **azureml-automl-core**
    + O registro de erros foi melhorado.
  + **azureml-automl-runtime**
    + Corrigimos o problema com a previsão quando o conjunto de dados contém grãos curtos com intervalos de tempo longos.
    + Corrigimos o problema quando o horizonte max automático é ligado e a coluna de data contém datas em forma de strings. Adicionamos conversão adequada e erro sensato se a conversão até a data não for possível
    + Usando numpy nativo e SciPy para serializar e desserializar dados intermediários para FileCacheStore (usado para executões automáticos locais)
    + Corrigimos um bug onde corridas de crianças com falha podem ficar presas no estado de execução.
  + **azureml-cli-comum**
    + O perfil de instância única foi fixado para produzir uma recomendação e foi disponibilizado no núcleo sdk.
  + **azureml-core**
    + Adicionado `--grant-workspace-msi-access` como um parâmetro adicional para o CLI datastore para registrar o Azure Blob Container que permitirá que você registre o Blob Container que está atrás de um VNet
    + O perfil de instância única foi fixado para produzir uma recomendação e foi disponibilizado no núcleo sdk.
    + Corrigimos o problema em _deploy aks.py
    + Valida a integridade dos modelos que estão sendo carregados para evitar falhas silenciosas de armazenamento.
  + **azureml-interpret**
    + adicionado exceções estilo azureml para azureml-interpretar
    + serialização deepscoringexplicador fixa para modelos keras
  + **azureml-pipeline-core**
    + Notebook de pontuação em lote pipeline agora usa ParallelRunStep
  + **azureml-pipeline-steps**
    + Moveu `AutoMLStep` o `azureml-pipeline-steps` pacote. Preterido `AutoMLStep` o `azureml-train-automl-runtime`interior.
  + **azureml-contrib-pipeline-passos**
    + Parâmetro opcional side_inputs adicionado ao ParallelRunStep. Este parâmetro pode ser usado para montar pasta no recipiente. Atualmente, os tipos suportados são DataReference e PipelineData.
  + **azureml-tensorboard**
    + azureml-tensorboard atualizado para suportar tensorflow 2.0
  + **azureml-train-automl-client**
    + featurization fixoProblema de substituição que filtra config de featurização personalizada.
  + **azureml-trem-automl-runtime**
    + Moveu `AutoMLStep` o `azureml-pipeline-steps` pacote. Preterido `AutoMLStep` o `azureml-train-automl-runtime`interior.
  + **azureml-train-core**
    + Suporte pyTorch versão 1.4 no PyTorch Estimator
  
## <a name="2020-02-04"></a>2020-02-04

### <a name="azure-machine-learning-sdk-for-python-v110rc0-pre-release"></a>Azure Machine Learning SDK para Python v1.1.0rc0 (Pré-lançamento)

+ **Alterações da falha**
  + **Controle de Versão Semântico 2.0.0**
    + A partir da versão 1.1 O Azure ML Python SDK adota a Versão Semântica 2.0.0. [Leia mais aqui](https://semver.org/). Todas as versões subseqüentes seguirão novo esquema de numeração e contrato de versão semântica. 
  
+ **Correções e melhorias de bugs**
  + **azureml-automl-runtime**
    + Aumento da velocidade de featurização.
    + Corrigimos a verificação de freqüência durante a pontuação, agora nas tarefas de previsão não exigimos equivalência de freqüência estrita entre o trem e o conjunto de testes.
  + **azureml-core**
    + O usuário agora pode especificar um valor para a chave auth ao regenerar chaves para webservices.
  + **azureml-interpret**
    + A interpretação azureml atualizada para depender da interpretação-comunidade 0.5.0
  + **azureml-pipeline-core**
    + Corrigimos um bug no qual os resultados do PythonScriptStep poderiam ser reutilizados incorretamente, apesar de alterar a lista de argumentos
  + **azureml-pipeline-steps**
    + Exemplo de documentação adicionado para conjunto de dados como entrada PythonScriptStep
  + **azureml-contrib-pipeline-passos**
    + Os parâmetros aprovados no ParallelRunConfig podem ser substituídos passando parâmetros de pipeline agora. Novos parâmetros de pipeline suportados aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count e aml_process_count_per_node já fazem parte da versão anterior).
  
## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>Azure Machine Learning SDK para Python v1.0.85

+ **Novos recursos**
  + **azureml-core**
    + Obtenha o uso central atual e limitação de cotas para recursos AmlCompute em um determinado espaço de trabalho e assinatura
  
  + **azureml-contrib-pipeline-passos**
    + Permitir que o usuário passe o conjunto de dados tabular como resultado intermediário da etapa anterior para a etapa paralela

+ **Correções e melhorias de bugs**
  + **azureml-automl-runtime**
    + Retirou a exigência de y_query coluna na solicitação ao serviço de previsão implantado. 
    + O 'y_query' foi removido da seção de solicitação de serviço de notebook Saque Laranja da Dominick.
    + Corrigimos o bug impedindo a previsão nos modelos implantados, operando em conjuntos de dados com colunas de data.
    + Adicionado Matthews Correlation Coeficiente como uma métrica de classificação, tanto para classificação binária quanto multiclasse.
  + **azureml-contrib-interpretar**
    + Os explicadores de texto removidos da interpretação azureml-contrib como explicação de texto foram movidos para o repo interpret-texto que será lançado em breve.
  + **azureml-core**
    + Conjunto de dados: os usos para conjunto de dados de arquivos não dependem mais de numpy e pandas para serem instalados no env python.
    + Alterou localWebservice.wait_for_deployment() para verificar o status do contêiner Docker local antes de tentar pingar seu ponto final de saúde, reduzindo consideravelmente o tempo necessário para relatar uma implantação falha.
    + Corrigimos a inicialização de uma propriedade interna usada no LocalWebservice.reload() quando o objeto de serviço é criado a partir de uma implantação existente usando o construtor LocalWebservice().
    + Mensagem de erro editada para esclarecimento.
    + Adicionou um novo método chamado get_access_token() ao AksWebservice que retornará o objeto AksServiceAccessToken, que contém token de acesso, atualização após carimbo de tempo, expiração no carimbo de tempo e tipo de token. 
    + Método de get_token () preterido no AksWebservice à medida que o novo método retorna todas as informações que este método retorna.
    + Saída modificada do comando az ml get-access-token de serviço. Token renomeado para acessarToken e atualizarPor atualizaçãoApós. Adicionado expiraçãoPropriedades on e tokenType.
    + get_active_runs fixo
  + **azureml-explain-model**
    + shap atualizado para 0.33.0 e interprete-comunidade para 0.4.*
  + **azureml-interpret**
    + shap atualizado para 0.33.0 e interprete-comunidade para 0.4.*
  + **azureml-trem-automl-runtime**
    + Adicionado Matthews Correlation Coeficiente como uma métrica de classificação, tanto para classificação binária quanto multiclasse.
    + Depreciar o sinalizador de pré-processo do código e substituído por featurização -featurization está ligado por padrão

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>Azure Machine Learning SDK para Python v1.0.83

+ **Novos recursos**
  + Conjunto de dados: `on_error` `out_of_range_datetime` Adicione `to_pandas_dataframe` duas opções e para falhar `None`quando os dados têm valores de erro em vez de preenchê-los com .
  + Espaço de trabalho: Adicionou o `hbi_workspace` sinalizador para espaços de trabalho com dados confidenciais que permitem mais criptografia e desativa diagnósticos avançados em espaços de trabalho. Também adicionamos suporte para trazer suas próprias chaves para a `cmk_keyvault` `resource_cmk_uri` instância do Cosmos DB associada, especificando os parâmetros e parâmetros ao criar um espaço de trabalho, que cria uma instância do Cosmos DB em sua assinatura enquanto provisiona seu espaço de trabalho. [Leia mais aqui.](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#azure-cosmos-db)

+ **Correções e melhorias de bugs**
  + **azureml-automl-runtime**
    + Corrigimos uma regressão que fez com que um TypeError fosse levantado ao executar o AutoML em versões Python abaixo de 3.5.4.
  + **azureml-core**
    + Corrigido bug `datastore.upload_files` em que o caminho `./` relativo que não começou não foi capaz de ser usado.
    + Adicionadomensagens de depreciação para todos os codepaths de classe de imagem
    + Construção de URL de gerenciamento de modelo fixo para a região de Mooncake.
    + Corrigimos problema sumido no qual os modelos que usam source_dir não podiam ser embalados para funções do Azure.    
    + Adicionada uma opção ao [Environment.build_local()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) para empurrar uma imagem para o registro de contêiner do espaço de trabalho AzureML
    + Atualizei o SDK para usar a nova biblioteca de tokens no azure sinapse de forma compatível com as costas.
  + **azureml-interpret**
    + Corrigido bug onde Nenhum foi devolvido quando nenhuma explicação estava disponível para download. Agora levanta uma exceção, combinando comportamento em outro lugar.
  + **azureml-pipeline-steps**
    + Proibido a `DatasetConsumptionConfig`passagem `Estimator`para `inputs` o parâmetro `Estimator` quando o será `EstimatorStep`usado em um .
  + **azureml-sdk**
    + Adicionado cliente AutoML ao pacote azureml-sdk, permitindo que as corridas automáticas de AutoML sejam enviadas sem instalar o pacote AutoML completo.
  + **azureml-train-automl-client**
    + Alinhamento corrigido na saída do console para executões de automl
    + Corrigimos um bug onde a versão incorreta de pandas pode ser instalada em amlcomputa remota.

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>Azure Machine Learning SDK para Python v1.0.81

+ **Correções e melhorias de bugs**
  + **azureml-contrib-interpretar**
    + adiar dependência shap para interpretar-comunidade de azureml-interpretar
  + **azureml-core**
    + O destino de cálculo agora pode ser especificado como um parâmetro para os objetos de configuração de implantação correspondentes. Este é especificamente o nome do alvo de computação para implantar, não o objeto SDK.
    + Adicionado'CreateBy informações aos objetos Model e Service. Pode ser acessado <var>através de .created_by
    + Fixed ContainerImage.run(), que não estava configurando corretamente a porta HTTP do contêiner Docker.
    + Tornar `azureml-dataprep` opcional `az ml dataset register` para o comando cli
    + Corrigimos um `TabularDataset.to_pandas_dataframe` bug onde incorretamente voltaria para um leitor alternativo e imprimiria um aviso.
  + **azureml-explain-model**
    + adiar dependência shap para interpretar-comunidade de azureml-interpretar
  + **azureml-pipeline-core**
    + Adicionada nova `NotebookRunnerStep`etapa de pipeline, para executar um notebook local como um passo no pipeline.
    + Removido get_all funções preteridas para PublicPipelines, Schedules e PipelineEndpoints
  + **azureml-train-automl-client**
    + Começou a depreciação de data_script como uma entrada para o AutoML.


## <a name="2019-12-09"></a>09/12/2019

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>Azure Machine Learning SDK para Python v1.0.79

+ **Correções e melhorias de bugs**
  + **azureml-automl-core**
    + Featurization removidaConfig a ser registrada
      + Registro atualizado para registrar apenas "auto"/"off"/"personalizado".
  + **azureml-automl-runtime**
    + Adicionado apoio para pandas. Séries e pandas. Categórico para detectar o tipo de dados da coluna. Anteriormente, apenas suportado numpy.ndarray
      + Adicionado alterações de código relacionadas para lidar com dtype categórico corretamente.
    + A interface da função de previsão foi melhorada: o parâmetro y_pred tornou-se opcional. - As cordas foram melhoradas.
  + **azureml-contrib-dataset**
    + Corrigimos um bug onde os conjuntos de dados rotulados não podiam ser montados.
  + **azureml-core**
    + Correção `Environment.from_existing_conda_environment(name, conda_environment_name)`de bugs para . O usuário pode criar uma instância de Ambiente que é uma réplica exata do ambiente local
    + Métodos de conjuntos de `include_boundary=True` dados relacionados à série de tempo alterados para por padrão.
  + **azureml-train-automl-client**
    + Problema corrigido quando os resultados de validação não são impressos quando a saída do show é definida como falsa.


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>Azure Machine Learning SDK para Python v1.0.76

+ **Alterações da falha**
  + Problemas de atualização Azureml-Train-AutoML
    + A atualização para azureml-train-automl>=1.0.76 do azureml-train-automl<1.0.76 pode causar instalações parciais, fazendo com que algumas importações automáticas falhem. Para resolver isso, você pode executar https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmdo script de configuração encontrado em . Ou se você estiver usando pip diretamente você pode:
      + "pip install --upgrade azureml-train-automl"
      + "pip install --ignore-ignorar-instalado azureml-train-automl-client"
    + ou você pode desinstalar a versão antiga antes de atualizar
      + "pip desinstalar azureml-train-automl"
      + "pip instalar azureml-train-automl"

+ **Correções e melhorias de bugs**
  + **azureml-automl-runtime**
    + O AutoML agora levará em conta classes verdadeiras e falsas ao calcular métricas escalares médias para tarefas de classificação binária.
    + Movido Código de treinamento e aprendizado de máquina no AzureML-AutoML-Core para um novo pacote AzureML-AutoML-Runtime.
  + **azureml-contrib-dataset**
    + Ao `to_pandas_dataframe` ligar para um conjunto de dados rotulado com a opção de download, agora você pode especificar se deve substituir os arquivos existentes ou não.
    + Ao `keep_columns` ligar `drop_columns` ou que resulte em uma série de tempo, rótulo ou coluna de imagem sendo descartado, os recursos correspondentes serão descartados para o conjunto de dados também.
    + Corrigimos um problema com o carregador de pitorespara a tarefa de detecção de objetos.
  + **azureml-contrib-interpretar**
    + Removeu o widget do painel de explicação da interpretação azureml-contrib, mudou o pacote para referenciar o novo em interpret_community
    + Versão atualizada da interpret-community para 0.2.0
  + **azureml-core**
    + Melhorar o `workspace.datasets`desempenho de .
    + Adicionado a capacidade de registrar o Azure SQL Database Datastore usando o nome de usuário e autenticação de senha
    + Corrija para carregar Configurações de execução de caminhos relativos.
    + Ao `keep_columns` ligar `drop_columns` ou que resulte em uma coluna de séries temporais sendo descartada, os recursos correspondentes serão descartados para o conjunto de dados também.
  + **azureml-interpret**
    + versão atualizada da interpret-comunidade para 0.2.0
  + **azureml-pipeline-steps**
    + Valores suportados `runconfig_pipeline_params` documentados para as etapas do pipeline de aprendizado de máquina do azul.
  + **azureml-pipeline-core**
    + Adicionada opção CLI para baixar a saída no formato json para comandos Pipeline.
  + **azureml-trem-automl**
    + Divida AzureML-Train-AutoML em 2 pacotes, um pacote cliente AzureML-Train-AutoML-Client e um pacote de treinamento ML AzureML-Train-AutoML-Runtime
  + **azureml-train-automl-client**
    + Adicionou um cliente fino para enviar experimentos AutoML sem precisar instalar quaisquer dependências de aprendizado de máquina localmente.
    + Registro fixo de lags detectados automaticamente, tamanhos de janela de rolamento e horizontes máximos nas corridas remotas.
  + **azureml-trem-automl-runtime**
    + Adicionou um novo pacote AutoML para isolar o aprendizado de máquina e os componentes de tempo de execução do cliente.
  + **azureml-contrib-train-rl**
    + Adicionado suporte de aprendizagem de reforço no SDK.
    + Adicionado suporte amlWindowsCompute no RL SDK.


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>Azure Machine Learning SDK para Python v1.0.74

  + **Versão prévia dos recursos**
    + **azureml-contrib-dataset**
      + Depois de importar conjunto de dados azureml-contrib, você pode ligar `Dataset.Labeled.from_json_lines` em vez de criar um conjunto de `._Labeled` dados rotulado.
      + Ao `to_pandas_dataframe` ligar para um conjunto de dados rotulado com a opção de download, agora você pode especificar se deve substituir os arquivos existentes ou não.
      + Ao `keep_columns` ligar `drop_columns` ou que resulte em uma série temporal, rótulo ou coluna de imagem sendo descartado, os recursos correspondentes serão descartados para o conjunto de dados também.
      + Corrigimos problemas com o `dataset.to_torchvision()`carregador PyTorch ao ligar .

+ **Correções e melhorias de bugs**
  + **azure-cli-ml**
    + Adicionado Perfil de Modelo à pré-visualização CLI.
    + Corrige a alteração de quebra no Armazenamento Azure fazendo com que o AzureML CLI falhe.
    + Adicionado tipo de balanceador de carga ao MLC para tipos AKS
  + **azureml-automl-core**
    + Corrigimos o problema com a detecção de horizonte máximo em séries tempois, tendo valores ausentes e múltiplos grãos.
    + Corrigimos o problema com falhas durante a geração de divisões de validação cruzada.
    + Substitua esta seção por uma mensagem em formato de marcação para aparecer nas notas de versão: -Melhor manuseio de grãos curtos nos conjuntos de dados de previsão.
    + Corrigimos o problema com a mascaração de algumas informações do usuário durante o registro. -Registro aprimorado dos erros durante as corridas de previsão.
    + Adicionando psutil como uma dependência de conda ao arquivo de implantação yml gerado automaticamente.
  + **azureml-contrib-mir**
    + Corrige a alteração de quebra no Armazenamento Azure fazendo com que o AzureML CLI falhe.
  + **azureml-core**
    + Corrige um bug que fez com que os modelos implantados nas funções do Azure produzissem 500s.
    + Corrigimos um problema no qual o arquivo amlignore não era aplicado em instantâneos.
    + Adicionado um novo API amlcompute.get_active_runs que retorna um gerador para funcionamento e enfileirado funciona em uma determinada amlcompute.
    + Adicionado Tipo de Balanceador de Carga ao MLC para tipos AKS.
    + Adicionado append_prefix parâmetro bool para download_files em run.py e download_artifacts_from_prefix em artifacts_client. Este sinalizador é usado para achatar seletivamente o caminho de arquivo de origem para que apenas o nome do arquivo ou da pasta seja adicionado ao output_directory
    + Corrigir problema de `run_config.yml` desserialização para com o uso do conjunto de dados.
    + Ao `keep_columns` ligar `drop_columns` ou que resulte em uma coluna de séries temporais sendo descartada, os recursos correspondentes serão descartados para o conjunto de dados também.
  + **azureml-interpret**
    + Versão atualizada da comunidade de interpretação para 0.1.0.3
  + **azureml-trem-automl**
    + Corrigimos um problema no qual automl_step pode não imprimir problemas de validação.
    + Fixoregister_model ter sucesso mesmo que o ambiente do modelo esteja faltando dependências localmente.
    + Corrigimos um problema no qual algumas corridas remotas não eram habilitadas para docker.
    + Adicione o registro da exceção que está fazendo com que uma corrida local falhe prematuramente.
  + **azureml-train-core**
    + Considere resume_from executa no cálculo de ajuste automatizado de hiperparâmetro melhor corrida infantil.
  + **azureml-pipeline-core**
    + Manuseio de parâmetros fixos na construção de argumentos de tubulação.
    + Adicionada descrição do gasoduto e parâmetro de inhame tipo passo.
    + Novo formato de inhame para etapa pipeline e adicionado aviso de depreciação para o formato antigo.



## <a name="2019-11-04"></a>2019-11-04

### <a name="web-experience"></a>Experiência na Web

A página de aterrissagem colaborativa do espaço de trabalho [https://ml.azure.com](https://ml.azure.com) foi aprimorada e renomeada como o estúdio Azure Machine Learning (pré-visualização).

A partir do estúdio, você pode treinar, testar, implantar e gerenciar ativos de Machine Learning do Azure, como conjuntos de dados, pipelines, modelos, endpoints e muito mais.

Acesse as seguintes ferramentas de autoria baseadas na Web do estúdio:

| Ferramenta baseada na Web | Descrição | Edition |
|-|-|-|
| Notebook VM(visualização) | Estação de trabalho totalmente gerenciada em nuvem | Empresa & Básica |
| [Aprendizado automático de máquina](tutorial-first-experiment-automated-ml.md) (visualização) | Nenhuma experiência de código para automatizar o desenvolvimento de modelos de aprendizagem de máquina | Enterprise |
| [Designer](concept-designer.md) (visualização) | Ferramenta de modelagem de aprendizado de máquina de arrastar e soltar anteriormente conhecida como o designer | Enterprise |


### <a name="azure-machine-learning-designer-enhancements"></a>Aprimoramentos do designer de Machine Learning do Azure

+ Anteriormente conhecida como interface visual 
+    11 novos [módulos,](algorithm-module-reference/module-reference.md) incluindo recomendadores, classificadores e utilitários de treinamento, incluindo engenharia de recursos, validação cruzada e transformação de dados.

### <a name="r-sdk"></a>SDK do R 
 
Cientistas de dados e desenvolvedores de IA usam o [Azure Machine Learning SDK para R](tutorial-1st-r-experiment.md) para construir e executar fluxos de trabalho de aprendizado de máquina com o Azure Machine Learning.

O Azure Machine Learning SDK `reticulate` for R usa o pacote para se ligar ao Python SDK. Ao vincular diretamente ao Python, o SDK for R permite que você tenha acesso a objetos e métodos principais implementados no Python SDK de qualquer ambiente R que você escolher.

As principais capacidades do SDK incluem:

+    Gerenciar recursos de nuvem para monitorar, registrar em log e organizar seus experimentos de machine learning.
+    Treinar modelos usando recursos em nuvem, incluindo treinamento de modelo acelerado por GPU.
+    Implante seus modelos como webservices no Azure Container Instances (ACI) e no Azure Kubernetes Service (AKS).

Consulte o site do [pacote](https://azure.github.io/azureml-sdk-for-r) para obter a documentação completa.

### <a name="azure-machine-learning-integration-with-event-grid"></a>Integração de Machine Learning do Azure com a Event Grid 

O Azure Machine Learning agora é um provedor de recursos para a Event Grid, você pode configurar eventos de aprendizado de máquina através do portal Azure ou do Azure CLI. Os usuários podem criar eventos para conclusão de execução, registro de modelo, implantação de modelos e deriva de dados detectados. Esses eventos podem ser encaminhados para manipuladores de eventos suportados pela Event Grid para consumo. Consulte [esquemade](https://docs.microsoft.com/azure/event-grid/event-schema-machine-learning)eventos de aprendizagem de máquina, [conceitos](https://docs.microsoft.com/azure/machine-learning/concept-event-grid-integration) e artigos [tutoriais](https://docs.microsoft.com/azure/machine-learning/how-to-use-event-grid) para obter mais detalhes.

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>Azure Machine Learning SDK para Python v1.0.72

+ **Novos recursos**
  + Monitores adicionais de conjunto de dados através do pacote [**azureml-datadrift,**](https://docs.microsoft.com/python/api/azureml-datadrift) permitindo o monitoramento de conjuntos de dados de séries tempontos para deriva de dados ou outras alterações estatísticas ao longo do tempo. Alertas e eventos podem ser acionados se a deriva for detectada ou outras condições nos dados forem atendidas. Veja [nossa documentação](https://aka.ms/datadrift) para obter detalhes.
  + Anunciando duas novas edições (também referidas como SKU intercambiavelmente) no Azure Machine Learning. Com esta versão, agora você pode criar um espaço de trabalho básico ou enterprise Azure Machine Learning. Todos os espaços de trabalho existentes serão padrão para a edição Básica, e você pode ir para o portal Azure ou para o estúdio para atualizar o espaço de trabalho a qualquer momento. Você pode criar um espaço de trabalho básico ou corporativo a partir do portal Azure. Leia [nossa documentação](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) para saber mais. A partir do SDK, a edição do seu espaço de trabalho pode ser determinada usando a propriedade "sku" do seu objeto de espaço de trabalho.
  + Também fizemos melhorias no Azure Machine Learning Compute - agora você pode visualizar métricas para seus clusters (como nós totais, nós em execução, cota total do núcleo) no Azure Monitor, além de visualizar registros de diagnóstico para depuração. Além disso, você também pode visualizar atualmente executando ou enfileirado executões em seu cluster e detalhes como os IPs dos vários nós em seu cluster. Você pode visualizá-los no portal ou usando funções correspondentes no SDK ou CLI.

  + **Versão prévia dos recursos**
    + Estamos liberando suporte de visualização para criptografia de disco do seu SSD local no Azure Machine Learning Compute. Por favor, levante um bilhete de suporte técnico para obter sua assinatura listada em branco para usar este recurso.
    + Visualização pública da inferência em lote de aprendizagem de máquina do Azure. A Inferência de Lote de Aprendizado de Máquina do Azure tem como alvo grandes trabalhos de inferência que não são sensíveis ao tempo. A Inferência em Lote fornece dimensionamento de inferência econômica, com throughput incomparável para aplicações assíncronas. É otimizado para alta emissão de through-through, inferência de fogo e esquecimento sobre grandes coletas de dados.
    + [**azureml-contrib-dataset**](https://docs.microsoft.com/python/api/azureml-contrib-dataset)
        + Funcionalidades habilitadas para conjunto de dados rotulado
        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore, Dataset
        import azureml.contrib.dataset
        from azureml.contrib.dataset import FileHandlingOption, LabeledDatasetTask

        # create a labeled dataset by passing in your JSON lines file
        dataset = Dataset._Labeled.from_json_lines(datastore.path('path/to/file.jsonl'), LabeledDatasetTask.IMAGE_CLASSIFICATION)

        # download or mount the files in the `image_url` column
        dataset.download()
        dataset.mount()

        # get a pandas dataframe
        from azureml.data.dataset_type_definitions import FileHandlingOption
        dataset.to_pandas_dataframe(FileHandlingOption.DOWNLOAD)
        dataset.to_pandas_dataframe(FileHandlingOption.MOUNT)

        # get a Torchvision dataset
        dataset.to_torchvision()
        ```

+ **Correções e melhorias de bugs**
  + **azure-cli-ml**
    + A CLI agora suporta a embalagem do modelo.
    + Adicionado conjunto de dados CLI. Para mais informações:`az ml dataset --help`
    + Adicionado suporte para implantação e embalagem de modelos suportados (ONNX, scikit-learn e TensorFlow) sem uma instância InferenceConfig.
    + Adicionado sinalizador de sobregravação para implantação de serviço (ACI e AKS) em SDK e CLI. Se fornecido, substituirá o serviço existente se o serviço com nome já existir. Se o serviço não existir, criará um novo serviço.
    + Os modelos podem ser registrados com duas novas estruturas, Onnx e Tensorflow. - O registro do modelo aceita dados de entrada de amostra, dados de saída de amostra e configuração de recursos para o modelo.
  + **azureml-automl-core**
    + Treinar uma iteração seria executado em um processo infantil somente quando as restrições de tempo de execução estão sendo definidas.
    + Adicione um guardrail para tarefas de previsão, para verificar se um max_horizon especificado causará um problema de memória na máquina dada ou não. Se isso for, uma mensagem guardrail será exibida.
    + Adicionado suporte para frequências complexas como 2 anos e 1 mês. -Mensagem de erro compreensível adicionada se a freqüência não puder ser determinada.
    + Adicionar azureml-defaults ao conda env gerado automaticamente para resolver a falha de implantação do modelo
    + Permitir que dados intermediários no Azure Machine Learning Pipeline sejam `AutoMLStep`convertidos em conjunto de dados tabulares e usados em .
    + Implementou atualização de propósito da coluna para streaming.
    + Implementou a atualização do parâmetro do transformador para Imputer e HashOneHotEncoder para streaming.
    + Adicionou o tamanho atual dos dados e o tamanho mínimo de dados necessário às mensagens de erro de validação.
    + Atualizei o tamanho mínimo de dados necessário para validação cruzada para garantir um mínimo de duas amostras em cada dobra de validação.
  + **azureml-cli-comum**
    + A CLI agora suporta a embalagem do modelo.
    + Os modelos podem ser registrados com duas novas estruturas, Onnx e Tensorflow.
    + O registro do modelo aceita dados de entrada de amostra, dados de saída de amostra e configuração de recursos para o modelo.
  + **azureml-contrib-gbdt**
    + corrigiu o canal de liberação para o notebook
    + Adicionado um aviso para meta de computação não AmlCompute que não suportamos
    + Adicionado LightGMB Estimador ao pacote azureml-contrib-gbdt
  + [**azureml-core**](https://docs.microsoft.com/python/api/azureml-core)
    + A CLI agora suporta a embalagem do modelo.
    + Adicione aviso de depreciação para APIs de conjunto de dados depreciados. Consulte o aviso de https://aka.ms/tabular-datasetalteração da API do conjunto de dados em .
    + Alterar [`Dataset.get_by_id`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-) para retornar nome de registro e versão se o conjunto de dados estiver registrado.
    + Corrija um bug que scriptRunConfig com conjunto de dados como argumento não pode ser usado repetidamente para enviar a execução do experimento.
    + Os conjuntos de dados recuperados durante uma execução serão rastreados [`run.get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--) e podem ser vistos na página de detalhes da execução ou ligando após a execução ser concluída.
    + Permitir que dados intermediários no Azure Machine Learning Pipeline sejam [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep)convertidos em conjunto de dados tabulares e usados em .
    + Adicionado suporte para implantação e embalagem de modelos suportados (ONNX, scikit-learn e TensorFlow) sem uma instância InferenceConfig.
    + Adicionado sinalizador de sobregravação para implantação de serviço (ACI e AKS) em SDK e CLI. Se fornecido, substituirá o serviço existente se o serviço com nome já existir. Se o serviço não existir, criará um novo serviço.
    +  Os modelos podem ser registrados com duas novas estruturas, Onnx e Tensorflow. O registro do modelo aceita dados de entrada de amostra, dados de saída de amostra e configuração de recursos para o modelo.
    + Adicionado novo datastore para O Banco de Dados Azure para MySQL. Adicionado exemplo para usar o Banco de Dados Azure para MySQL em DataTransferStep em Pipelines de Aprendizado de Máquina do Azure.
    + Funcionalidade adicionada para adicionar e remover tags de experimentos Funcionalidade adicionada para remover tags de executações
    + Adicionado sinalizador de sobregravação para implantação de serviço (ACI e AKS) em SDK e CLI. Se fornecido, substituirá o serviço existente se o serviço com nome já existir. Se o serviço não existir, criará um novo serviço.
  + [**azureml-datadrift**](https://docs.microsoft.com/python/api/azureml-datadrift)
    + Movido `azureml-contrib-datadrift` de para`azureml-datadrift`
    + Suporte adicional para o monitoramento de conjuntos de dados de séries tempois para deriva e outras medidas estatísticas
    + Novos `create_from_model()` métodos `create_from_dataset()` e [`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) para a classe. O `create()` método será preterido.
    + Ajustes nas visualizações em Python e UI no estúdio Azure Machine Learning.
    + Suporte ao agendamento semanal e mensal do monitor, além de diariamente para monitores de conjunto de dados.
    + Suporte ao backup de métricas do monitor de dados para analisar dados históricos para monitores de conjunto de dados.
    + Correções de vários bugs
  + [**azureml-pipeline-core**](https://docs.microsoft.com/python/api/azureml-pipeline-core)
    + azureml-dataprep não é mais necessário para enviar um Azure Machine Learning Pipeline executado a partir do arquivo pipeline. `yaml`
  + [**azureml-trem-automl**](/python/api/azureml-train-automl-runtime/)
    + Adicionar azureml-defaults ao conda env gerado automaticamente para resolver a falha de implantação do modelo
    + O treinamento remoto AutoML agora inclui azureml-defaults para permitir a reutilização do env de treinamento para inferência.
  + **azureml-train-core**
    + Adicionado suporte PyTorch 1.3 no [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch) estimador

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>Interface visual (visualização)

+ A interface visual (visualização) do Azure Machine Learning foi revisada para ser executada nos [pipelines do Azure Machine Learning](concept-ml-pipelines.md). Os pipelines (anteriormente conhecidos como experimentos) de autoria na interface visual estão agora totalmente integrados com a experiência de Machine Learning do Núcleo Azure.
  + Experiência de gestão unificada com ativos SDK
  + Versão e rastreamento para modelos de interface visual, pipelines e endpoints
  + Interface do usuário reprojetada
  + Implantação adicionada de inferência em lote
  + Adicionado suporte ao Azure Kubernetes Service (AKS) para metas de computação de inferência
  + Novo fluxo de trabalho de autoria de pipeline python-step
  + Nova [página de aterrissagem](https://ml.azure.com) para ferramentas de autoragem visual

+ **Novos módulos**
  + Aplicar operação matemática
  + Aplicar a transformação SQL
  + Valores do clipe
  + Resumir dados
  + Importação do banco de dados SQL

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Azure Machine Learning SDK para Python v1.0.69

+ **Correções e melhorias de bugs**
  + **azureml-automl-core**
    + Limitar as explicações do modelo para melhor executar em vez de computar explicações para cada corrida. Fazendo com que essa mudança de comportamento seja para local, remoto e ADB.
    + Suporte adicional para explicações do modelo sob demanda para a UI
    + Adicionado psutil como uma `automl` dependência de e incluiu psutil como uma dependência conda em amlcompute.
    + Corrigimos o problema com lags heurísticos e tamanhos de janelas de rolagem nos conjuntos de dados de previsão, algumas séries podem causar erros de álgebra linear
      + Adicionado impressão para os parâmetros heturisticamente determinados nas corridas de previsão.
  + **azureml-contrib-datadrift**
    + Adicionado proteção ao criar métricas de saída se a deriva do nível do conjunto de dados não estiver na primeira seção.
  + **azureml-contrib-interpretar**
    + azureml-contrib-explain-model pacote foi renomeado para azureml-contrib-interpret
  + **azureml-core**
    + Adicionado API a conjuntos de dados não registrados. `dataset.unregister_all_versions()`
    + o pacote azureml-contrib-explain-model foi renomeado para azureml-contrib-interpret.
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core)**
    + Adicionado API a conjuntos de dados não registrados. Dataset. [unregister_all_versions( )](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--)
    + API adicionada do conjunto de dados para verificar o tempo alterado de dados. `dataset.data_changed_time`.
    + Ser capaz `FileDataset` de `TabularDataset` consumir e `PythonScriptStep` `EstimatorStep`como `HyperDriveStep` insumos para , e em Azure Machine Learning Pipeline
    + O `FileDataset.mount` desempenho foi melhorado para pastas com um grande número de arquivos
    + Ser capaz de consumir [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset) e [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset) como entradas para [PythonScriptStep,](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep) [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)e [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) no Azure Machine Learning Pipeline.
    + Desempenho do FileDataset. [montagem ()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none----kwargs-) foi melhorada para pastas com um grande número de arquivos
    + Adicionado URL às recomendações de erro conhecidas em detalhes de execução.
    + Corrigimos um bug em run.get_metrics onde os pedidos falhariam se uma corrida tivesse muitos filhos
    + Corrigimos um bug em [run.get_metrics](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) onde os pedidos falhariam se uma corrida tivesse muitos filhos
    + Adicionado suporte para autenticação no cluster Arcadia.
    + Criar um objeto Experimento obtém ou cria o experimento no espaço de trabalho Azure Machine Learning para o rastreamento do histórico de execução. O ID do experimento e o tempo arquivado são preenchidos no objeto Experimento na criação. Exemplo: experimento = Experimento (espaço de trabalho, "Novo Experimento") experiment_id = experiment.id arquivamento() e reativar() são funções que podem ser chamadas em um experimento para ocultar e restaurar o experimento de ser mostrado no UX ou retornado por padrão em uma chamada para listar experimentos. Se um novo experimento for criado com o mesmo nome de um experimento arquivado, você pode renomear o experimento arquivado ao reativar passando um novo nome. Só pode haver um experimento ativo com um determinado nome. Exemplo: experimento1 = Experimento (espaço de trabalho, "Experimento Ativo") experimento1.archive() # Criar um novo experimento ativo com o mesmo nome do arquivado. experimento2. = Experimento (espaço de trabalho, "Experimento Ativo") experimento1.reactivate (new_name="Experimento Ativo Anterior") A lista de métodos estáticos() no Experimento pode levar um filtro de nome e filtro ViewType. Os valores do ViewType são "ACTIVE_ONLY", "ARCHIVED_ONLY" e "ALL" Exemplo: archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY") all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
    + Suporte ao uso do ambiente para implantação de modelos e atualização de serviços
  + **azureml-datadrift**
    + O atributo show da classe DataDriftDector não suportará mais o argumento opcional 'with_details' mais. O atributo show só apresentará coeficiente de deriva de dados e contribuição de deriva de dados de colunas de recursos.
    + Alterações de comportamento do dataDriftDetector 'get_output':
      + Parâmetro de entrada start_time, end_time são opcionais em vez de obrigatórios;
      + A entrada de start_time e/ou end_time específicos com um run_id específico na mesma invocação resultará em exceção de erro de valor, pois são mutuamente exclusivas
      + Por meio de start_time e/ou end_time específicos de entrada, somente os resultados das corridas programadas serão devolvidos;
      + Parâmetro 'daily_latest_only' é preterido.
    + Suporte à recuperação de saídas de deriva de dados baseadas em dataset.
  + **azureml-explain-model**
    + Renomeie o pacote de modelo explicativo do AzureML para a interpretação do AzureML, mantendo o pacote antigo para compatibilidade reversa por enquanto
    + bug `automl` fixo com explicações brutas definidas para a tarefa de classificação em vez de regressão por padrão no download de ExplanationClient
    + Adicionar suporte `ScoringExplainer` para ser criado diretamente usando`MimicWrapper`
  + **azureml-pipeline-core**
    + Desempenho melhorado para a criação de grandes pipelines
  + **azureml-train-core**
    + Adicionado suporte ao TensorFlow 2.0 no TensorFlow Estimador
  + **azureml-trem-automl**
    + Criar um objeto [Experimento](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment) obtém ou cria o experimento no espaço de trabalho Azure Machine Learning para o rastreamento do histórico de execução. O ID do experimento e o tempo arquivado são preenchidos no objeto Experimento na criação. Exemplo:

        ```py
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        [arquivamento()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#archive--) e [reativado()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) são funções que podem ser chamadas em um experimento para ocultar e restaurar o experimento de ser mostrado no UX ou retornado por padrão em uma chamada para listar experimentos. Se um novo experimento for criado com o mesmo nome de um experimento arquivado, você pode renomear o experimento arquivado ao reativar passando um novo nome. Só pode haver um experimento ativo com um determinado nome. Exemplo:

        ```py
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        A lista de métodos [estáticos()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-) no Experiment pode pegar um filtro de nome e filtro ViewType. Os valores do ViewType são "ACTIVE_ONLY", "ARCHIVED_ONLY" e "ALL". Exemplo:

        ```py
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + Suporte ao uso do ambiente para implantação de modelos e atualização de serviços.
  + **[azureml-datadrift](https://docs.microsoft.com/python/api/azureml-datadrift)**
    + O atributo show da classe [DataDriftDetector](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) não suportará mais o argumento opcional 'with_details' mais. O atributo show só apresentará coeficiente de deriva de dados e contribuição de deriva de dados de colunas de recursos.
    + Mudanças de comportamentohttps://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#get-output-start-time-none--end-time-none--run-id-none-) da função DataDriftDetector [get_output]:
      + Parâmetro de entrada start_time, end_time são opcionais em vez de obrigatórios;
      + A entrada de start_time e/ou end_time específicos com uma run_id específica na mesma invocação resultará em exceção de erro de valor, por serem mutuamente exclusivas;
      + Por meio de start_time e/ou end_time específicos de entrada, somente os resultados das corridas programadas serão devolvidos;
      + Parâmetro 'daily_latest_only' é preterido.
    + Suporte à recuperação de saídas de deriva de dados baseadas em dataset.
  + **[azureml-explain-model](https://docs.microsoft.com/python/api/azureml-explain-model)**
    + Renomeie o pacote de modelo explicativo AzureML para a interpretação do AzureML, mantendo o pacote antigo para compatibilidade reversa por enquanto.
    + bug AutoML fixo com explicações brutas definidas para a tarefa de classificação em vez de regressão por padrão no download do ExplanationClient.
    + Adicionar suporte para [ScoringExplainer](/python/api/azureml-interpret/azureml.interpret.scoring.scoring_explainer.scoringexplainer?view=azure-ml-py) a ser criado diretamente usando [MimicWrapper](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic_wrapper.mimicwrapper)
  + **[azureml-pipeline-core](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Melhor desempenho para a criação de grandes pipelines.
  + **[azureml-train-core](https://docs.microsoft.com/python/api/azureml-train-core)**
    + Adicionado suporte ao TensorFlow 2.0 no [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow) Estimator.
  + **[azureml-trem-automl](/python/api/azureml-train-automl-runtime/)**
    + A execução dos pais não será mais falhada quando a iteração de configuração falhar, já que a orquestração já cuida dela.
    + Adicionado suporte local-docker e local-conda para experimentos AutoML
    + Adicionado suporte local-docker e local-conda para experimentos AutoML.


## <a name="2019-10-08"></a>08-10-2019

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nova experiência na Web (visualização) para espaços de trabalho de Machine Learning do Azure

A guia Experimento no [novo portal do espaço de trabalho](https://ml.azure.com) foi atualizada para que os cientistas de dados possam monitorar experimentos de forma mais performática. Você pode explorar os seguintes recursos:
+ Metadados de experimentos para filtrar e classificar facilmente sua lista de experimentos
+ Páginas de detalhes de experimento simplificadas e performáticas que permitem visualizar e comparar suas corridas
+ Novo design para executar páginas de detalhes para entender e monitorar suas corridas de treinamento

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>Azure Machine Learning SDK para Python v1.0.65

  + **Novos recursos**
    + Adicionadoambientes com curadoria. Esses ambientes foram pré-configurados com bibliotecas para tarefas comuns de aprendizado de máquina e foram pré-construídos e armazenados em cache como imagens Docker para execução mais rápida. Eles aparecem por padrão na lista de ambientes do Workspace, com prefixo "AzureML".
    + Adicionadoambientes com curadoria. Esses ambientes foram pré-configurados com bibliotecas para tarefas comuns de aprendizado de máquina e foram pré-construídos e armazenados em cache como imagens Docker para execução mais rápida. Eles aparecem por padrão na lista de ambientes do [Workspace,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29)com prefixo "AzureML".

  + **azureml-trem-automl**
  + **[azureml-trem-automl](/python/api/azureml-train-automl-runtime/)**
    + Adicionado o suporte de conversão ONNX para o ADB e HDI

+ **Versão prévia dos recursos**
  + **azureml-trem-automl**
  + **[azureml-trem-automl](/python/api/azureml-train-automl-runtime/)**
    + BERT e BiLSTM suportados como featurizer de texto (somente visualização)
    + Personalização de featurização suportada para fins de coluna e parâmetros de transformador (somente visualização)
    + Explicações brutas suportadas quando o usuário habilita a explicação do modelo durante o treinamento (somente visualização)
    + Profeta adicionado `timeseries` para previsão como um gasoduto treinável (somente visualização)

  + **azureml-contrib-datadrift**
    + Pacotes realocados de azureml-contrib-datadrift para azureml-datadrift; o `contrib` pacote será removido em uma versão futura

+ **Correções e melhorias de bugs**
  + **azureml-automl-core**
    + Introduziu featurizationConfig para AutoMLConfig e AutoMLBaseSettings
    + Introduziu featurizationConfig para [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) e AutoMLBaseSettings
      + Substituir o propósito da coluna para a featurização com determinada coluna e tipo de recurso
      + Parâmetros do transformador de substituição
    + Mensagem de depreciação adicionada para explain_model() e retrieve_model_explanations()
    + Profeta adicionado como um gasoduto treinável (somente visualização)
    + Adicionada mensagem de depreciação para explain_model() e retrieve_model_explanations().
    + Adicionado Prophet como um gasoduto treinável (somente visualização).
    + Adicionado suporte para detecção automática de defasagem de alvo, tamanho da janela de rolamento e horizonte máximo. Se um dos target_lags, target_rolling_window_size ou max_horizon for definido como "automático", a heurística será aplicada para estimar o valor do parâmetro correspondente com base nos dados de treinamento.
    + Previsão fixa no caso quando o conjunto de dados contém uma coluna de grãos, este grão é de um tipo numérico e há uma lacuna entre o trem e o conjunto de testes
    + Corrigimos a mensagem de erro sobre o índice duplicado na execução remota em tarefas de previsão
    + Previsão fixa no caso quando o conjunto de dados contém uma coluna de grãos, este grão é de um tipo numérico e há uma lacuna entre o trem e o conjunto de testes.
    + Corrigimos a mensagem de erro sobre o índice duplicado na execução remota em tarefas de previsão.
    + Adicione um guardrail para verificar se um conjunto de dados está desequilibrado ou não. Se for, uma mensagem guardrail seria escrita para o console.
  + **azureml-core**
    + Adicionada capacidade de recuperar url SAS para modelar no armazenamento através do objeto do modelo. Ex: model.get_sas_url()
    + Introduzir `run.get_details()['datasets']` para obter conjuntos de dados associados à execução enviada
    + Adicionar API `Dataset.Tabular.from_json_lines_files` para criar um Conjunto TabularDataset a partir de arquivos JSON Lines. Para saber mais sobre esses dados tabulares nos arquivos JSON Lines no TabularDataset, visite https://aka.ms/azureml-data a documentação.
    + Adicionado seleções adicionais de tamanho vm (disco do sistema operacional, número de GPUs) à função supported_vmsizes ()
    + Adicionado campos adicionais à função list_nodes () para mostrar a execução, o IP privado e público, a porta etc.
    + Capacidade de especificar um novo campo durante o provisionamento de cluster --remotelogin_port_public_access que pode ser configurado para habilitado ou desativado, dependendo se você gostaria de deixar a porta SSH aberta ou fechada no momento da criação do cluster. Se você não especificar, o serviço abrirá ou fechará a porta de forma inteligente, dependendo se você estiver implantando o cluster dentro de um VNet.
  + **azureml-explain-model**
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core/azureml.core)**
    + Adicionada capacidade de recuperar url SAS para modelar no armazenamento através do objeto do modelo. Ex: modelo. [get_sas_url](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + Introduza a corrida. [get_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--)['conjuntos de dados'] para obter conjuntos de dados associados à execução enviada
    + Adicionar API `Dataset.Tabular`. [from_json_lines_files()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) para criar um Conjunto DeDados Tabular a partir de arquivos JSON Lines. Para saber mais sobre esses dados tabulares nos arquivos JSON Lines no TabularDataset, visite https://aka.ms/azureml-data a documentação.
    + Adicionado seleções adicionais de tamanho vm (disco do SISTEMA OPERACIONAL, número de GPUs) à função [supported_vmsizes()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)
    + Adicionado campos adicionais à função [list_nodes()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) para mostrar a execução, o IP privado e público, a porta etc.
    + Capacidade de especificar um novo campo durante o [provisionamento de](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--) `--remotelogin_port_public_access` clusters que pode ser definido como ativado ou desativado, dependendo se você gostaria de deixar a porta SSH aberta ou fechada no momento da criação do cluster. Se você não especificar, o serviço abrirá ou fechará a porta de forma inteligente, dependendo se você estiver implantando o cluster dentro de um VNet.
  + **[azureml-explain-model](https://docs.microsoft.com/python/api/azureml-explain-model)**
    + Documentação melhorada para saídas de explicação no cenário de classificação.
    + Adicionada a capacidade de carregar os valores y previstos na explicação para os exemplos de avaliação. Desbloqueia visualizações mais úteis.
    + Adicionado a propriedade explicativa ao MimicWrapper para permitir obter o MimicExplainer subjacente.
  + **azureml-pipeline-core**
    + Notebook adicionado para descrever Módulo, MóduloVersão e ModuleStepStep
  + **azureml-pipeline-steps**
    + Adicionado RScriptStep para suportar a execução de script R via pipeline AML.
    + Os parâmetros de metadados fixos analisados no AzureBatchStep, que estava causando a mensagem de erro "atribuição para parâmetro SsubscriptionId não é especificado".
  + **azureml-trem-automl**
    + Suporte training_data, validation_data, label_column_name weight_column_name como formato de entrada de dados
    + Mensagem de depreciação adicionada para explain_model() e retrieve_model_explanations()
  + **[azureml-pipeline-core](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Adicionado um [notebook](https://aka.ms/pl-modulestep) para descrever [Módulo,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.module(class)) [ModuleVersion](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.moduleversion) e [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep).
  + **[azureml-pipeline-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps)**
    + Adicionado [RScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) para suportar a execução de script R via pipeline AML.
    + Os parâmetros de metadados fixos analisados no [AzureBatchStep,](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep) que estava causando a mensagem de erro "atribuição para parâmetro SsubscriptionId não está especificado".
  + **[azureml-trem-automl](/python/api/azureml-train-automl-runtime/)**
    + Suporte training_data, validation_data, label_column_name, weight_column_name como formato de entrada de dados.
    + Adicionada mensagem de depreciação para [explain_model()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) e [retrieve_model_explanations()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-).


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Azure Machine Learning SDK para Python v1.0.62

+ **Novos recursos**
  + Introduziu `timeseries` a característica no TabularDataset. Essa característica permite uma filtragem fácil de carimbo de tempo em dados de um TabularDataset, como pegar todos os dados entre um intervalo de tempo ou os dados mais recentes. Para saber mais `timeseries` sobre isso, o traço https://aka.ms/azureml-data no TabularDataset, visite a documentação ou https://aka.ms/azureml-tsd-notebook para um caderno de exemplo.
  + Treinamento habilitado com TabularDataset e FileDataset. Por https://aka.ms/dataset-tutorial favor, visite um caderno de exemplo.

  + **azureml-train-core**
      + Adicionado `Nccl` `Gloo` e suporte no estimador PyTorch

+ **Correções e melhorias de bugs**
  + **azureml-automl-core**
    + Preterido a configuração AutoML 'lag_length' e o Defasador.
    + Corrigimos a validação correta dos dados de entrada se eles forem especificados em um formato de fluxo de dados
    + Modificou o fit_pipeline.py para gerar o gráfico json e carregar para artefatos.
    + Renderizado o `userrun` gráfico `Cytoscape`usando .
  + **azureml-core**
    + Revisitei o tratamento de exceção no código ADB e faça alterações para, conforme novo tratamento de erro
    + Adicionado autenticação automática de MSI para VMs de notebook.
    + Corre bug onde modelos corrompidos ou vazios podem ser carregados por causa de tentativas fracassadas.
    + Corrigimos o `DataReference` bug onde `DataReference` o nome muda quando `as_upload`o `as_download`modo `as_mount`muda (por exemplo, ao ligar , ou ).
    + Faça `mount_point` `target_path` e `FileDataset.mount` seja `FileDataset.download`opcional para e .
    + A exceção de que a coluna de carimbo de tempo não pode ser encontrada será descartada se a API relacionada a séries de horas for chamada sem a coluna de carimbo de tempo fina atribuída ou as colunas de carimbo de tempo atribuídas forem retiradas.
    + As colunas de séries de tempo devem ser atribuídas com a coluna cujo tipo é Data, caso contrário, espera-se exceção
    + Colunas de séries de tempo atribuindo a API 'with_timestamp_columns' podem levar nenhum nome de coluna de carimbo de tempo fino/grosseiro, que limpará colunas de carimbo de tempo previamente atribuídas.
    + A exceção será descartada quando o grão grosso ou a coluna de carimbo de tempo de grãofino fino for descartada com indicação para o usuário de que a queda pode ser feita após a exclusão da coluna de carimbo de tempo na lista de queda ou a chamada with_time_stamp com nenhum valor para liberar colunas de carimbo de tempo
    + A exceção será descartada quando a coluna de grãos grossos ou de granulado fino não estiver incluída na lista de colunas de manutenção com indicação de que a manutenção pode ser feita após incluir a coluna de carimbo de tempo na lista de colunas ou with_time_stamp de chamada com nenhum valor para liberar colunas de carimbo de tempo.
    + Adicionado registro para o tamanho de um modelo registrado.
  + **azureml-explain-model**
    + Aviso fixo impresso no console quando o pacote python "embalagem" não estiver instalado: "Usando a versão mais antiga do lightgbm suportada, atualize para a versão maior que 2.2.1"
    + Explicação do modelo de download fixo com fragmentação para explicações globais com muitas características
    + Explicador de mímica fixa faltando exemplos de inicialização na explicação de saída
    + Corrigimos erro imutável nas propriedades do conjunto ao fazer upload com o cliente explicante usando dois tipos diferentes de modelos
    + Adicionado um get_raw param ao explicador de pontuação .explain() para que um explicador de pontuação possa retornar tanto os valores projetados quanto os valores brutos.
  + **azureml-trem-automl**
    + Introduziu APIs públicas do AutoML `automl` para dar explicações de suporte ao Explicação SDK - Nova maneira de suportar explicações AutoML ao desacoplar a featurização AutoML e explicar SDK - Suporte integrado de explicação bruta do azureml explicasse SDK para modelos AutoML.
    + Removendo azureml-defaults de ambientes de treinamento remoto.
    + Alterou o local da loja de cache padrão do FileCacheStore baseado em um para OZureFileCacheStore um para AutoML no caminho de código Dozure Databricks.
    + Corrigimos a validação correta dos dados de entrada se eles forem especificados em um formato de fluxo de dados
  + **azureml-train-core**
    + Reverteu source_directory_data_store depreciação.
    + Capacidade adicional para substituir as versões do pacote instalado do Azureml.
    + Adicionado suporte de `environment_definition` arquivo docker em parâmetro em estimadores.
    + Parâmetros de treinamento distribuídos simplificados em estimadores.

         ```py
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nova experiência na Web (visualização) para espaços de trabalho de Machine Learning do Azure
A nova experiência na Web permite que cientistas de dados e engenheiros de dados completem seu ciclo de vida de aprendizado de máquina de ponta a ponta, desde a preparação e visualização de dados até o treinamento e a implantação de modelos em um único local.

![UI do azure Machine Learning (visualização)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**Principais características:**

Usando esta nova interface de Aprendizado de Máquina do Azure, você pode agora:
+ Gerencie seus notebooks ou vincule-se ao Jupyter
+ [Execute experimentos ML automatizados](tutorial-first-experiment-automated-ml.md)
+ [Criar conjuntos de dados a partir de arquivos locais, datastores, arquivos & web](how-to-create-register-datasets.md)
+ Explore & preparar conjuntos de dados para a criação de modelos
+ Monitore a deriva de dados para seus modelos
+ Exibir recursos recentes de um painel

No momento desta versão, os seguintes navegadores são suportados: Chrome, Firefox, Safari e Microsoft Edge Preview.

**Problemas conhecidos:**

1. Atualize seu navegador se vir "Algo deu errado! Erro de carregamento de arquivos de bloco" quando a implantação está em andamento.

1. Não é possível excluir ou renomear arquivo em Notebooks e Arquivos. Durante a Visualização Pública, você pode usar jupyter UI ou Terminal em Notebook VM para executar operações de arquivo de atualização. Por ser um sistema de arquivos de rede montado, todas as alterações que você faz no Notebook VM são imediatamente refletidas no Espaço de Trabalho do Notebook.

1. Para SSH no Notebook VM:
   1. Encontre as teclas SSH que foram criadas durante a configuração da VM. Ou, encontre as chaves no espaço de trabalho azure Machine Learning > guia de computação aberta > localizar o Notebook VM na lista > abrir suas propriedades : copie as chaves da caixa de diálogo.
   1. Importe essas chaves SSH públicas e privadas para sua máquina local.
   1. Use-os para SSH no Notebook VM.

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Azure Machine Learning SDK para Python v1.0.60

+ **Novos recursos**
  + Introduzido FileDataset, que faz referência a arquivos únicos ou múltiplos em seus datastores ou urls públicos. Os arquivos podem ser de qualquer formato. FileDataset fornece a você a capacidade de baixar ou montar os arquivos para o seu cálculo. Para saber mais sobre FileDataset, visite https://aka.ms/file-dataset.
  + Adicionado suporte ao Yaml pipeline para etapa PythonScript, Passo Adla, Passo de Databricks, Passo de Transferência de Dados e Etapa AzureBatch

+ **Correções e melhorias de bugs**
  + **azureml-automl-core**
    + AutoArima é agora um gasoduto sugestionável apenas para visualização.
    + Relatório de erro melhorado para previsão.
    + Melhorou o registro usando exceções personalizadas em vez de genéricas nas tarefas de previsão.
    + Removeu a verificação na max_concurrent_iterations ser menor que o número total de iterações.
    + Os modelos AutoML agora retornam AutoMLExceptions
    + Esta versão melhora o desempenho de execução de corridas locais automatizadas de aprendizado de máquina.
  + **azureml-core**
    + Introduza Dataset.get_all(workspace), que `TabularDataset` retorna `FileDataset` um dicionário e objetos com chave pelo nome de registro.

    ```py
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + Apresentar `parition_format` como `Dataset.Tabular.from_delimited_files` argumento `Dataset.Tabular.from_parquet.files`para e . As informações de partição de cada caminho de dados serão extraídas em colunas com base no formato especificado. '{column_name}' cria coluna de cordas e '{column_name:yyyy/MM/dd/HH/mm/ss}' cria coluna de data,onde 'yyyy', 'MM', 'dd', 'HH', 'mm' e 'ss' são usados para extrair ano, mês, dia, hora, minuto e segundo para o tipo de data. O partition_format deve começar da posição da primeira tecla de partição até o fim do caminho do arquivo. Por exemplo, dado o caminho.. /USA/2019/01/01/data.csv' onde a partição é por país e tempo, partition_format='/{Country}/{PartitionDate:yyyy/MM/dd}/data.csv' cria a coluna de string 'Country' com valor 'USA' e coluna de data', 'PartitionDate' com valor '2019-01-01'.
        ```py
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + Apresentar `partition_format` como `Dataset.Tabular.from_delimited_files` argumento `Dataset.Tabular.from_parquet.files`para e . As informações de partição de cada caminho de dados serão extraídas em colunas com base no formato especificado. '{column_name}' cria coluna de cordas e '{column_name:yyyy/MM/dd/HH/mm/ss}' cria coluna de data,onde 'yyyy', 'MM', 'dd', 'HH', 'mm' e 'ss' são usados para extrair ano, mês, dia, hora, minuto e segundo para o tipo de data. O partition_format deve começar da posição da primeira tecla de partição até o fim do caminho do arquivo. Por exemplo, dado o caminho.. /USA/2019/01/01/data.csv' onde a partição é por país e tempo, partition_format='/{Country}/{PartitionDate:yyyy/MM/dd}/data.csv' cria a coluna de string 'Country' com valor 'USA' e coluna de data', 'PartitionDate' com valor '2019-01-01'.
    + `to_csv_files`e `to_parquet_files` métodos foram `TabularDataset`adicionados a . Esses métodos permitem `TabularDataset` a `FileDataset` conversão entre a e a convertendo os dados em arquivos do formato especificado.
    + Faça login automaticamente no registro de imagens base ao salvar um arquivo Docker gerado pelo Model.package().
    + 'gpu_support' não é mais necessário; A AML agora detecta e usa automaticamente a extensão nvidia docker quando está disponível. Ela será removida em uma versão futura.
    + Adicionamos suporte para criar, atualizar e usar PipelineDrafts.
    + Esta versão melhora o desempenho de execução de corridas locais automatizadas de aprendizado de máquina.
    + Os usuários podem consultar métricas do histórico de execução por nome.
    + Melhorou o registro usando exceções personalizadas em vez de genéricas nas tarefas de previsão.
  + **azureml-explain-model**
    + Adicionado feature_maps parâmetro ao novo MimicWrapper, permitindo que os usuários obtenham explicações de recursos brutos.
    + Os uploads do conjunto de dados estão agora desligados por padrão para upload de explicação, e podem ser reativados com upload_datasets=True
    + Adicionado parâmetros de filtragem "is_law" à lista de explicações e funções de download.
    + Adiciona `get_raw_explanation(feature_maps)` método a objetos de explicação globais e locais.
    + Verificação de versão adicionada ao lightgbm com aviso impresso se abaixo da versão suportada
    + Uso otimizado da memória ao lotear explicações
    + Os modelos AutoML agora retornam AutoMLExceptions
  + **azureml-pipeline-core**
    + O suporte adicionado para criar, atualizar e usar PipelineDrafts - pode ser usado para manter definições de pipeline mutáveis e usá-las interativamente para executar
  + **azureml-trem-automl**
    + Recurso criado para instalar versões específicas do pytorch v1.1.0, toolkit 9.0, :::no-loc text="cuda"::: pytorch-transformers, que é necessário para ativar BERT/ XLNet no ambiente remoto python runtime.
  + **azureml-train-core**
    + Falha inicial de alguns erros de definição de espaço de hiperparâmetro diretamente no sdk em vez do lado do servidor.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure Machine Learning Data Prep SDK v1.1.14
+ **Correções e melhorias de bugs**
  + Escrita habilitada para ADLS/ADLSGen2 usando caminho bruto e credenciais.
  + Corrigimos um `include_path=True` bug que `read_parquet`não funcionava para.
  + Falha `to_pandas_dataframe()` fixa causada pela exceção "Valor de propriedade inválido: hostSecret".
  + Corrigimos um bug onde os arquivos não podiam ser lidos no modo DBFS no modo Spark.

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Azure Machine Learning SDK para Python v1.0.57
+ **Novos recursos**
  + Habilitado `TabularDataset` para ser consumido pelo AutomatedML. Para saber `TabularDataset`mais sobre, por favor, visite https://aka.ms/azureml/howto/createdatasets.

+ **Correções e melhorias de bugs**
  + **cliente-cliente-core-nativeclient**
    + Corrigimos o erro, levantado quando os rótulos de treinamento e/ou validação (y e y_valid) são fornecidos na forma de dataframe pandas, mas não como matriz numpy.
    + Interface atualizada para `RawDataContext` criar a para `AutoMLBaseSettings` exigir apenas os dados e o objeto.
    +  Permitir que os usuários do AutoML abandonem séries de treinamento que não são longas o suficiente durante a previsão. - Permitir que os usuários do AutoML soltem grãos do conjunto de testes que não existem no conjunto de treinamento suspreviamente previsto.
  + **azure-cli-ml**
    + Agora você pode atualizar o certificado TLS/SSL para o ponto final de pontuação implantado no cluster AKS tanto para o certificado gerado pela Microsoft quanto para o certificado do cliente.
  + **azureml-automl-core**
    + Corrigimos um problema no AutoML onde as linhas com etiquetas ausentes não foram removidas corretamente.
    + Registro de erros aprimorado no AutoML; mensagens de erro completas serão agora sempre escritas no arquivo de log.
    + O AutoML atualizou a `azureml-defaults`fixação do pacote para incluir , `azureml-explain-model`e `azureml-dataprep`. O AutoML não avisará mais sobre `azureml-train-automl` incompatibilidades de pacote (exceto para o pacote).
    + Corrigimos um `timeseries` problema em que as divisões de cv são de tamanho desigual, fazendo com que o cálculo do bin fracasse.
    + Ao executar a iteração do conjunto para o tipo de treinamento de Validação Cruzada, se acabamos tendo problemas para baixar os modelos treinados em todo o conjunto de dados, estávamos tendo uma inconsistência entre os pesos do modelo e os modelos que estavam sendo alimentados no conjunto de votação.
    + Corrigimos o erro, levantado quando os rótulos de treinamento e/ou validação (y e y_valid) são fornecidos na forma de dataframe pandas, mas não como matriz numpy.
    + Corrigimos o problema com as tarefas de previsão quando Nenhum foi encontrado nas colunas booleanas das tabelas de entrada.
    + Permitir que os usuários do AutoML abandonem séries de treinamento que não são longas o suficiente durante a previsão. - Permitir que os usuários do AutoML soltem grãos do conjunto de testes que não existem no conjunto de treinamento suspreviamente previsto.
  + **azureml-core**
    + Corrigido problema com blob_cache_timeout pedido de parâmetro.
    + Adicionado ajuste externo e transformar tipos de exceção para erros do sistema.
    + Adicionado suporte para segredos do Key Vault para corridas remotas. Adicione a zureml.core.keyvault.Keyvault class para adicionar, obter e listar segredos do cofre de chaves associados ao seu espaço de trabalho. As operações suportadas são:
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.keyvault.Keyvault.set_secret (nome, valor)
      + azureml.core.keyvault.Keyvault.set_secrets(secrets_dict)
      + azureml.core.keyvault.Keyvault.get_secret(nome)
      + azureml.core.keyvault.Keyvault.get_secrets(secrets_list)
      + azureml.core.keyvault.Keyvault.list_secrets()
    + Métodos adicionais para obter o cofre de chaves padrão e obter segredos durante a execução remota:
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.run.Run.get_secret(nome)
      + azureml.core.run.run.get_secrets(secrets_list)
    + Adicionado parâmetros adicionais de substituição ao comando CLI de envio-hyperdrive.
    + Melhorar a confiabilidade das chamadas de API está expandindo as repetições para exceções comuns da biblioteca de solicitações.
    + Adicione suporte para envio de executões a partir de uma execução submetida.
    + Corrigimos o problema do token SAS expirando no FileWatcher, o que fez com que os arquivos parassem de ser carregados após o término do token inicial.
    + Suportado importar arquivos HTTP csv/tsv no conjunto de dados Python SDK.
    + Preterido o método Workspace.setup(). A mensagem de aviso mostrada aos usuários sugere o uso de criar() ou obter()/from_config() em vez disso.
    + Adicionado Ambiente.add_private_pip_wheel(), que permite carregar pacotes de python personalizados privados `whl`para o espaço de trabalho e usá-los com segurança para construir/materializar o ambiente.
    + Agora você pode atualizar o certificado TLS/SSL para o ponto final de pontuação implantado no cluster AKS tanto para o certificado gerado pela Microsoft quanto para o certificado do cliente.
  + **azureml-explain-model**
    + Adicionado parâmetro para adicionar um ID de modelo às explicações no upload.
    + Adicionado `is_raw` marcação às explicações na memória e upload.
    + Adicionado suporte de pitoresco e testes para o pacote azureml-explain-model.
  + **azureml-opendatasets**
    + Suporte a detectar e registrar o ambiente de teste automático.
    + Adicionado classes para obter a população dos EUA por condado e zip.
  + **azureml-pipeline-core**
    + Adicionada propriedade de rótulo às definições de porta de entrada e saída.
  + **azureml-telemetria**
    + Corrigimos uma configuração de telemetria incorreta.
  + **azureml-trem-automl**
    + Corrigimos o bug onde na falha de configuração, o erro não estava sendo registrado no campo "erros" para a execução da configuração e, portanto, não foi armazenado em "erros" de execução dos pais.
    + Corrigimos um problema no AutoML onde as linhas com etiquetas ausentes não foram removidas corretamente.
    + Permitir que os usuários do AutoML abandonem séries de treinamento que não são longas o suficiente durante a previsão.
    + Permitir que os usuários do AutoML soltem grãos do conjunto de testes que não existem no conjunto de treinamento quando a previsão.
    + Agora, o AutoMLStep passa por `automl` config para backend para evitar quaisquer problemas sobre alterações ou adições de novos parâmetros de configuração.
    + AutoML Data Guardrail está agora em visualização pública. O usuário verá um relatório do Data Guardrail (para tarefas de classificação/regressão) após o treinamento e também poderá acessá-lo através da API SDK.
  + **azureml-train-core**
    + Adicionado suporte à tocha 1.2 no PyTorch Estimator.
  + **azureml-widgets**
    + Gráficos matriciais de confusão melhorados para treinamento de classificação.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning Data Prep SDK v1.1.12
+ **Novos recursos**
  + Listas de strings agora podem ser `read_*` passadas como entrada para métodos.

+ **Correções e melhorias de bugs**
  + O desempenho `read_parquet` de foi significativamente melhorado ao correr em Spark.
  + Corrigimos um `column_type_builder` problema em que falhou no caso de uma única coluna com formatos de data ambíguos.

### <a name="azure-portal"></a>Portal do Azure
+ **Recurso de visualização**
  + O streaming de arquivos de log e saída já está disponível para páginas de detalhes em execução. Os arquivos transmitirão atualizações em tempo real quando o alternador de visualização estiver ligado.
  + A capacidade de definir a cota em um nível de espaço de trabalho é liberada na pré-visualização. As cotas amlCompute são alocadas no nível de assinatura, mas agora permitimos que você distribua essa cota entre espaços de trabalho e aloque-a para compartilhamento justo e governança. Basta clicar na lâmina **Usos+Cotas** na barra de navegação esquerda do seu espaço de trabalho e selecionar a guia **Configurar cotas.** Observe que você deve ser um administrador de assinatura para poder definir cotas no nível do espaço de trabalho, uma vez que esta é uma operação de espaço de trabalho.

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Azure Machine Learning SDK para Python v1.0.55

+ **Novos recursos**
  + A autenticação baseada em token satisfaz agora as chamadas feitas para o ponto final de pontuação implantado no AKS. Continuaremos a suportar a autenticação baseada em chave atual e os usuários podem usar um desses mecanismos de autenticação por vez.
  + Capacidade de registrar um armazenamento blob que está por trás da rede virtual (VNet) como um datastore.

+ **Correções e melhorias de bugs**
  + **azureml-automl-core**
    + Corrige um bug onde o tamanho da validação para divisões cv é pequeno e resulta em gráficos preditos mal preditos versus gráficos verdadeiros para regressão e previsão.
    + O registro de tarefas de previsão nas corridas remotas melhorou, agora o usuário é fornecido com uma mensagem de erro abrangente se a execução foi falhada.
    + Falhas corrigidas de `Timeseries` se o sinalizador de pré-processo é verdadeiro.
    + Tornou algumas mensagens de erro de validação de dados de previsão mais acionáveis.
    + O consumo reduzido de memória do AutoML é executado por queda e/ou carga preguiçosa de conjuntos de dados, especialmente entre desovas de processos
  + **azureml-contrib-explicar-modelo**
    + Adicionado model_task bandeira aos explicadores para permitir que o usuário anule a lógica de inferência automática padrão para o tipo de modelo
    + Alterações do widget: `contrib`instala automaticamente `nbextension` com , não mais instalar/habilitar - explicação de suporte com apenas importância global de recursos (por exemplo, Permutative)
    + Alterações no painel: - Parcelas de `beeswarm` caixa e parcelas de violino, `beeswarm` além de plotagem na página de resumo - Rerenderização muito mais rápida do enredo no controle deslizante 'Top -k' - mensagem útil explicando como top-k é computado - Mensagens personalizáveis úteis no lugar de gráficos quando os dados não fornecidos
  + **azureml-core**
    + Adicionado método Model.package() para criar imagens Docker e arquivos Docker que encapsulam modelos e suas dependências.
    + Webservices locais atualizados para aceitar InferenceConfigs contendo objetos de ambiente.
    + Modelo fixo.register() produzindo modelos inválidos quando '.' (para o diretório atual) é passado como o parâmetro model_path.
    + Adicionar Run.submit_child, a funcionalidade espelha experiment.submit enquanto especifica a execução como o pai da execução do filho submetido.
    + Suporte às opções de configuração do Model.register no Run.register_model.
    + Capacidade de executar trabalhos JAR em clusters existentes.
    + Agora suportando instance_pool_id e cluster_log_dbfs_path parâmetros.
    + Adicionado suporte para usar um objeto Ambiente ao implantar um modelo em um Webservice. O objeto Ambiente agora pode ser fornecido como parte do objeto InferenceConfig.
    + Adicionar mapeamento appinsifht para novas regiões - centralus - westus - northcentralus
    + Adicionado documentação para todos os atributos em todas as classes datastore.
    + Adicionado parâmetro blob_cache_timeout `Datastore.register_azure_blob_container`a .
    + Adicionados métodos save_to_directory e load_from_directory ao azureml.core.environment.Environment.
    + Adicionou os comandos "az ml environment download" e "az ml environment register" à CLI.
    + Adicionado método Environment.add_private_pip_wheel.
  + **azureml-explain-model**
    + Adicionado rastreamento de conjunto de dados para explicações usando o serviço Dataset (visualização).
    + Diminuiu o tamanho do lote padrão ao transmitir explicações globais de 10k para 100.
    + Adicionado model_task bandeira aos explicadores para permitir que o usuário anule a lógica de inferência automática padrão para o tipo de modelo.
  + **azureml-mlflow**
    + Corrigido bug em mlflow.azureml.build_image onde diretórios aninhados são ignorados.
  + **azureml-pipeline-steps**
    + Capacidade adicional de executar trabalhos JAR no cluster Azure Databricks existente.
    + Adicionado seleção instance_pool_id e cluster_log_dbfs_path parâmetros para a etapa DatabricksStep.
    + Adicionado suporte para parâmetros de pipeline na etapa DatabricksStep.
  + **azureml-trem-automl**
    + Adicionado `docstrings` para os arquivos relacionados ao Ensemble.
    + Docs atualizados para `max_cores_per_iteration` linguagem mais apropriada para e`max_concurrent_iterations`
    + O registro de tarefas de previsão nas corridas remotas melhorou, agora o usuário é fornecido com uma mensagem de erro abrangente se a execução foi falhada.
    + Removido get_data `automlstep` do notebook de tubulação.
    + Iniciado `dataprep` o `automlstep`suporte em .

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning Data Prep SDK v1.1.10

+ **Novos recursos**
  + Agora você pode solicitar a execução de inspetores específicos (por exemplo, histograma, gráfico de dispersão, etc.) em colunas específicas.
  + Adicionado um argumento `append_columns`de paralelização a . Se True, os dados serão carregados na memória, mas a execução será executada em paralelo; se Falso, a execução será streaming, mas com um único fio.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Azure Machine Learning SDK para Python v1.0.53

+ **Novos recursos**
  + O Automated Machine Learning agora suporta o treinamento de modelos ONNX no alvo da computação remota
  + O Azure Machine Learning agora fornece a capacidade de retomar o treinamento de uma execução anterior, ponto de verificação ou arquivos de modelo.
    + Saiba como [usar os avaliadores para retomar](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb) o treinamento de uma corrida anterior

+ **Correções e melhorias de bugs**
  + **cliente-cliente-core-nativeclient**
    + Corrigir o bug sobre a desadesão de tipos de colunas após a transformação (bug linked);
    + Permitir que y_query seja um tipo de objeto contendo Nenhum(s) no início (#459519).
  + **azure-cli-ml**
    + Os comandos CLI "model deploy" e "service update" agora aceitam parâmetros, arquivos de configuração ou uma combinação dos dois. Os parâmetros têm precedência sobre atributos em arquivos.
    + A descrição do modelo agora pode ser atualizada após o registro
  + **azureml-automl-core**
    + Atualize a dependência do NimbusML para a versão 1.2.0 (atual mais recente).
    + Adicionando suporte para estimadores NimbusML & pipelines a serem usados dentro de estimadores AutoML.
    + Corrigindo um bug no processo de seleção do Ensemble que estava crescendo desnecessariamente o conjunto resultante, mesmo que as pontuações permanecessem constantes.
    + Habilite a reutilização de algumas featurizações em Splits de CV para tarefas de previsão. Isso acelera o tempo de execução da configuração executado por aproximadamente um fator de n_cross_validations para featurizações caras como lags e janelas de rolamento.
    + Abordando um problema se o tempo estiver fora do intervalo de tempo suportado por pandas. Agora levantamos uma DataException se o tempo for menor que pd. Carimbo de hora ou maior que pd. Carimbo de tempo.max
    + A previsão agora permite diferentes freqüências em conjuntos de trens e testes se eles podem ser alinhados. Por exemplo, "trimestral a partir de janeiro" e "trimestral a partir de outubro" podem ser alinhados.
    + Os "parâmetros" da propriedade foram adicionados ao TimeSeriesTransformer.
    + Remova as velhas classes de exceção.
    + Na previsão de `target_lags` tarefas, o parâmetro agora aceita um único valor inteiro ou uma lista de inteiros. Se o inteiro foi fornecido, apenas um lag será criado. Se uma lista for fornecida, os valores únicos de lags serão tomados. target_lags=[1, 2, 2, 4] criará lags de um, 2 e 4 períodos.
    + Corrija o bug sobre a perda de tipos de colunas após a transformação (bug linked);
    + Em `model.forecast(X, y_query)`, permita que y_query seja um tipo de objeto contendo Nenhum(s) no início (#459519).
    + Adicionar valores `automl` esperados à saída
  + **azureml-contrib-datadrift**
    +  Melhorias no notebook de exemplo, incluindo a mudança para conjuntos de dados abertos azureml em vez de conjuntos de dados abertos azureml-contrib e melhorias de desempenho ao enriquecer dados
  + **azureml-contrib-explicar-modelo**
    + Argumento de transformações fixas para explicador lime para importância de recurso bruto no pacote azureml-contrib-explain-model
    + Adicionados segmentações às explicações de imagem no explicador de imagem para o pacote azureML-contrib-explain-model
    + Adicionar suporte esparso para LimeExplainer
    + Adicionado `batch_size` para imitar `include_local=False`explicadores quando, para streaming de explicações globais em lotes para melhorar o tempo de execução de DecisionTreeExplainableModel
  + **azureml-contrib-featureengineering**
    + Correção para chamar set_featurizer_timeseries_params(): alteração do tipo de `timeseries` valor dict e verificação nula - Adicionar notebook para featurizer
    + Atualize a dependência do NimbusML para a versão 1.2.0 (atual mais recente).
  + **azureml-core**
    + Adicionado a capacidade de anexar datastores DBFS no Cli AzureML
    + Corrigimos o bug com o upload do `target_path` datastore onde uma pasta vazia é criada se iniciada com`/`
    + Corrigida `deepcopy` problema no ServicePrincipalAuthentication.
    + Adicionou os comandos "az ml environment show" e "az ml environment list" à CLI.
    + Os ambientes agora suportam especificar uma base_dockerfile como uma alternativa a um base_image já construído.
    + A configuração runconfiguration não utilizada auto_prepare_environment foi marcada como depreciada.
    + A descrição do modelo agora pode ser atualizada após o registro
    + Bugfix: A exclusão de modelo e imagem agora fornece mais informações sobre como recuperar objetos upstream que dependem deles se a exclusão falhar devido a uma dependência upstream.
    + Corrigimos o bug que imprimia duração em branco para implantações que ocorrem ao criar um espaço de trabalho para alguns ambientes.
    + O espaço de trabalho melhorado cria exceções de falha. De tal forma que os usuários não vêem "Incapaz de criar espaço de trabalho. Incapaz de encontrar..." como a mensagem e, em vez disso, ver a falha de criação real.
    + Adicione suporte para autenticação de token sustais.
    + Adicionar `get_token()` método `Webservice` aos objetos.
    + Adicionado suporte cli para gerenciar conjuntos de dados de aprendizado de máquina.
    + `Datastore.register_azure_blob_container`agora, opcionalmente, leva um `blob_cache_timeout` valor (em segundos) que configura os parâmetros de montagem do blobfuse para permitir a expiração de cache para este datastore. O padrão não é um intervalo, ou seja, quando uma bolha é lida, ela permanecerá no cache local até que o trabalho seja concluído. A maioria dos trabalhos prefere essa configuração, mas alguns trabalhos precisam ler mais dados de um grande conjunto de dados do que caberão em seus nós. Para esses trabalhos, afinar este parâmetro irá ajudá-los a ter sucesso. Tome cuidado ao ajustar este parâmetro: definir o valor muito baixo pode resultar em um desempenho ruim, pois os dados usados em uma época podem expirar antes de serem usados novamente. Isso significa que todas as leituras serão feitas a partir do armazenamento blob (ou seja, da rede) em vez do cache local, o que impacta negativamente os tempos de treinamento.
    + A descrição do modelo agora pode ser atualizada corretamente após o registro
    + A exclusão de modelos e imagens agora fornece mais informações sobre objetos upstream que dependem deles, o que faz com que a exclusão falhe
    + Melhore a utilização de recursos de corridas remotas usando azureml.mlflow.
  + **azureml-explain-model**
    + Argumento de transformações fixas para explicador lime para importância de recurso bruto no pacote azureml-contrib-explain-model
    + adicionar suporte esparso para LimeExplainer
    + adicionado shape explicador explicor, bem como outro nível para explicador tabular para explicar modelos lineares
    + para imitar explicador na biblioteca de modelos explica, erro fixo quando include_local=Falso para entrada de dados esparsas
    + adicionar valores `automl` esperados para a saída
    + permutação fixa característica importância quando o argumento transformações fornecido para obter importância recurso bruto
    + adicionado `batch_size` para imitar `include_local=False`explicador quando , para streaming explicações globais em lotes para melhorar o tempo de execução de DecisionTreeExplainableModel
    + para a biblioteca de explicabilidade de modelos, explicadores fixos de caixa preta onde a entrada do quadro de dados pandas é necessária para a previsão
    + Corrigimos um `explanation.expected_values` bug onde às vezes retornava um carro alegórico em vez de uma lista com um carro alegórico nele.
  + **azureml-mlflow**
    + Melhorar o desempenho do mlflow.set_experiment(experiment_name)
    + Corrigir bug no uso de InteractiveLoginAuthentication para mlflow tracking_uri
    + Melhore a utilização de recursos de corridas remotas usando azureml.mlflow.
    + Melhorar a documentação do pacote azureml-mlflow
    + O bug de correção onde mlflow.log_artifacts("my_dir") salvaria artefatos sob "my_dir/<caminhos de artefatos>" em vez de "<caminhos de artefatos>"
  + **azureml-opendatasets**
    + `pyarrow` Pino `opendatasets` de versões antigas (<0.14.0) por causa do problema de memória recém-introduzido lá.
    + Mova conjuntos de dados abertos azureml-contrib para conjuntos de dados abertos azureml.
    + Permita que classes abertas de conjunto de dados sejam registradas no espaço de trabalho do Azure Machine Learning e aproveite perfeitamente os recursos do conjunto de dados AML.
    + Melhorar o NoaaIsdWeather enriquece significativamente o desempenho na versão não-SPARK.
  + **azureml-pipeline-steps**
    + O DBFS Datastore agora é suportado para entradas e saídas em DatabricksStep.
    + Documentação atualizada para Azure Batch Step em relação a entradas/saídas.
    + Em AzureBatchStep, alterado *delete_batch_job_after_finish* valor padrão para *true*.
  + **azureml-telemetria**
    +  Mova conjuntos de dados abertos azureml-contrib para conjuntos de dados abertos azureml.
    + Permita que classes abertas de conjunto de dados sejam registradas no espaço de trabalho do Azure Machine Learning e aproveite perfeitamente os recursos do conjunto de dados AML.
    + Melhorar o NoaaIsdWeather enriquece significativamente o desempenho na versão não-SPARK.
  + **azureml-trem-automl**
    + Documentação atualizada sobre get_output para refletir o tipo de retorno real e fornecer notas adicionais sobre a recuperação de propriedades-chave.
    + Atualize a dependência do NimbusML para a versão 1.2.0 (atual mais recente).
    + adicionar valores `automl` esperados para a saída
  + **azureml-train-core**
    + As strings agora são aceitas como alvo de computação para ajuste automatizado de hiperparâmetro
    + A configuração runconfiguration não utilizada auto_prepare_environment foi marcada como depreciada.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure Machine Learning Data Prep SDK v1.1.9

+ **Novos recursos**
  + Adicionado suporte para ler um arquivo diretamente de uma url http ou https.

+ **Correções e melhorias de bugs**
  + Mensagem de erro melhorada ao tentar ler um conjunto de dados parquet a partir de uma fonte remota (que não é suportada no momento).
  + Corrigimos um bug ao escrever para o formato de arquivo Parquet no ADLS Gen 2 e atualizando o nome do contêiner ADLS Gen 2 no caminho.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>Interface Visual
+ **Versão prévia dos recursos**
  + Adicionado módulo "Execute R script" na interface visual.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Azure Machine Learning SDK para Python v1.0.48

+ **Novos recursos**
  + **azureml-opendatasets**
    + **azureml-contrib-opendatasets** está agora disponível como **conjuntos de dados abertos azureml**. O pacote antigo ainda pode funcionar, mas recomendamos que você use **conjuntos de dados abertos azureml** avançando para recursos e melhorias mais ricos.
    + Esse novo pacote permite que você registre conjuntos de dados abertos como conjunto de dados no espaço de trabalho do Azure Machine Learning e aproveite quaisquer funcionalidades que o Dataset ofereça.
    + Ele também inclui recursos existentes, como consumir conjuntos de dados abertos como dataframes Pandas/SPARK, e se junta a localização para alguns conjuntos de dados, como o clima.

+ **Versão prévia dos recursos**
    + O HyperDriveConfig agora pode aceitar o objeto do pipeline como parâmetro para suportar o ajuste de hiperparâmetros usando um pipeline.

+ **Correções e melhorias de bugs**
  + **azureml-trem-automl**
    + Corrigimos o bug sobre a perda de tipos de colunas após a transformação.
    + Corrigimos o bug para permitir que y_query fosse um tipo de objeto contendo None(s) no início.
    + Corrigimos o problema no processo de seleção do Ensemble, que estava crescendo desnecessariamente o conjunto resultante, mesmo que as pontuações permanecessem constantes.
    + Corrigimos o problema com as configurações de whitelist_models e blacklist_models no AutoMLStep.
    + Corrigimos o problema que impedia o uso de pré-processamento quando o AutoML teria sido usado no contexto do Azure ML Pipelines.
  + **azureml-opendatasets**
    + Moveu conjuntos de dados abertos azureml-contrib para conjuntos de dados abertos azureml.
    + Permitiu que classes abertas de conjunto de dados fossem registradas no espaço de trabalho do Azure Machine Learning e aproveitasse perfeitamente os recursos do conjunto de dados AML.
    + NoaaIsdWeather melhorado enriquecem significativamente o desempenho na versão não-SPARK.
  + **azureml-explain-model**
    + Documentação on-line atualizada para objetos de interpretação.
    + Adicionado `batch_size` para imitar `include_local=False`explicadores quando, para transmitir explicações globais em lotes para melhorar o tempo de execução de DecisionTreeExplainableModel para biblioteca de explicabilidade de modelo.
    + Corrigia o `explanation.expected_values` problema onde às vezes retornava um carro alegórico em vez de uma lista com um carro alegórico nele.
    + Adicionados valores esperados à `automl` saída para imitar explicador na biblioteca do modelo explice.
    + A permutação fixa característica importância quando o argumento de transformações fornecido para obter importância de recurso bruto.
  + **azureml-core**
    + Adicionada a capacidade de anexar armazenamentos de dados DBFS no Cli AzureML.
    + Corrigimos o problema com o upload do `target_path` datastore `/`onde uma pasta vazia é criada se iniciada com .
    + Comparação ativada de dois conjuntos de dados.
    + A exclusão de modelo e imagem agora fornece mais informações sobre como recuperar objetos a montante que dependem deles se a exclusão falhar devido a uma dependência upstream.
    + Preterido a configuração de execução não utilizada em auto_prepare_environment.
  + **azureml-mlflow**
    + Utilização aprimorada de recursos de executações remotas que usam azureml.mlflow.
    + Melhorou a documentação do pacote azureml-mlflow.
    + Corrigimos o problema em que mlflow.log_artifacts("my_dir") salvaria artefatos em "my_dir/caminhos de artefatos" em vez de "caminhos de artefatos".
  + **azureml-pipeline-core**
    + O parâmetro hash_paths para todas as etapas do gasoduto é preterido e será removido no futuro. Pelo conteúdo padrão do source_directory é hashed (exceto arquivos listados em .amlignore ou .gitignore)
    + Continuação do aprimoramento módulo e móduloStep para suportar módulos específicos do tipo de computação, para se preparar para a integração RunConfiguration e outras alterações para desbloquear o uso de módulos específicos do tipo de computação em pipelines.
  + **azureml-pipeline-steps**
    + AzureBatchStep: Documentação melhorada em relação às entradas/saídas.
    + AzureBatchStep: Alterado delete_batch_job_after_finish valor padrão para true.
  + **azureml-train-core**
    + As strings agora são aceitas como alvo de computação para ajuste automatizado de hiperparâmetros.
    + Preterido a configuração de execução não utilizada em auto_prepare_environment.
    + Parâmetros `conda_dependencies_file_path` depreciados `pip_requirements_file_path` e a `conda_dependencies_file` `pip_requirements_file` favor e, respectivamente, a favor e, respectivamente.
  + **azureml-opendatasets**
    + Melhorar o NoaaIsdWeather enriquece significativamente o desempenho na versão não-SPARK.

### <a name="azure-machine-learning-data-prep-sdk-v118"></a>Azure Machine Learning Data Prep SDK v1.1.8

+ **Novos recursos**
 + Os objetos de fluxo de dados agora podem ser iterados, produzindo uma seqüência de registros. Consulte a `Dataflow.to_record_iterator`documentação para .
  + Os objetos de fluxo de dados agora podem ser iterados, produzindo uma seqüência de registros. Consulte a `Dataflow.to_record_iterator`documentação para .

+ **Correções e melhorias de bugs**
 + Aumentou a robustez do DataPrep SDK.
 + Melhor manuseio de dataframes pandas com índices de coluna não-string.
 + Melhorou o `to_pandas_dataframe` desempenho dos conjuntos de dados.
 + Corrigimos um bug no qual a execução de Spark de Conjuntos de Dados falhou quando executada em um ambiente de vários álos.
  + Aumentou a robustez do DataPrep SDK.
  + Melhor manuseio de dataframes pandas com índices de coluna não-string.
  + Melhorou o `to_pandas_dataframe` desempenho dos conjuntos de dados.
  + Corrigimos um bug no qual a execução de Spark de Conjuntos de Dados falhou quando executada em um ambiente de vários álos.

## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>Azure Machine Learning Data Prep SDK v1.1.7

Revertemos uma mudança que melhorou o desempenho, pois estava causando problemas para alguns clientes que usam o Azure Databricks. Se você teve um problema no Azure Databricks, você pode atualizar para a versão 1.1.7 usando um dos métodos abaixo:
1. Execute este script para atualizar:`%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
2. Recriar o cluster, que instalará a versão mais recente do Data Prep SDK.

## <a name="2019-06-25"></a>2019-06-25

### <a name="azure-machine-learning-sdk-for-python-v1045"></a>Azure Machine Learning SDK para Python v1.0.45

+ **Novos recursos**
  + Adicionar modelo de substituto de árvore de decisão para imitar explicador no pacote azureml-explain-model
  + Capacidade de especificar uma versão CUDA a ser instalada em imagens de inferência. Suporte para CUDA 9.0, 9.1 e 10.0.
  + Informações sobre as imagens da base de treinamento do Azure ML estão agora disponíveis no [Azure ML Containers GitHub Repository](https://github.com/Azure/AzureML-Containers) e [dockerHub](https://hub.docker.com/_/microsoft-azureml)
  + Adicionado suporte cli para o cronograma do gasoduto. Execute "az ml pipeline -h" para saber mais
  + Adicionado parâmetro de namespace personalizado do Kubernetes à configuração de implantação do webservice aks e à configuração DE CLI.
  + Parâmetro de hash_paths preterido para todas as etapas do gasoduto
  + Model.register agora suporta o registro de vários arquivos individuais `child_paths` como um único modelo com o uso do parâmetro.

+ **Versão prévia dos recursos**
    + Os explicadores de pontuação agora podem, opcionalmente, salvar informações de conda e pip para serialização e desserialização mais confiáveis.
    + Correção de erro para seletor de recursos automáticos.
    + Os bugs atualizados mlflow.azureml.build_image para a nova api, corrigidos pela nova implementação.

+ **Correções e melhorias de bugs**
  + Removido `paramiko` a dependência do núcleo azureml. Adicionados avisos de depreciação para métodos de anexação de destino de computação legado.
  + Melhorar o desempenho do run.create_children
  + Na imitação explicadora com classificador binário, corrija a ordem de probabilidades quando a probabilidade do professor é usada para dimensionar valores de forma.
  + Melhor manuseio de erros e mensagem para aprendizado automático de máquina.
  + Corrigimos o problema do tempo de iteração para aprendizado automático de máquina.
  + Melhorou o desempenho de transformação em séries tempométricas para aprendizado automático de máquina.

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>Azure Machine Learning Data Prep SDK v1.1.6

+ **Novos recursos**
  + Funções de resumo adicionadas`SummaryFunction.TOPVALUES`para valores`SummaryFunction.BOTTOMVALUES`superiores ( ) e valores inferiores ( ).

+ **Correções e melhorias de bugs**
  + Melhorou significativamente o `read_pandas_dataframe`desempenho de .
  + Corrigimos um bug `get_profile()` que causaria falha em um fluxo de dados que apontava para arquivos binários.
  + Exposto `set_diagnostics_collection()` para permitir a habilitação programática/desativação da coleta de telemetria.
  + Mudou o `get_profile()`comportamento de . Os valores da NaN são agora ignorados por Min, Mean, Std e Sum, que se alinha mao comportamento dos Pandas.


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>Azure Machine Learning SDK para Python v1.0.43

+ **Novos recursos**
  + O Azure Machine Learning agora oferece suporte de primeira classe para a popular estrutura de aprendizado de máquina e análise de dados Scikit-learn. Usando [ `SKLearn` o estimador,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)os usuários podem facilmente treinar e implantar modelos scikit-learn.
    + Aprenda a executar a [sintonia de hiperparâmetros com o Scikit-learn usando HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb).
  + Adicionado suporte para criar ModuleStep em pipelines, juntamente com as classes Module e ModuleVersion para gerenciar unidades de computação reutilizáveis.
  + Os webservices da ACI agora suportam scoring_uri persistentes através de atualizações. O scoring_uri mudará de IP para FQDN. O dns name label for FQDN pode ser configurado definindo o dns_name_label em deploy_configuration.
  + Novos recursos automatizados de aprendizado de máquina:
    + Featurizer STL para previsão
    + O clustering KMeans está habilitado para varrer recursos
  + As aprovações da Cota AmlCompute se tornaram mais rápidas! Agora automatizamos o processo para aprovar suas solicitações de cotas dentro de um limite. Para obter mais informações sobre como funcionam as cotas, saiba [como gerenciar cotas.](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas)

+ **Versão prévia dos recursos**
    + Integração com o rastreamento [do MLflow](https://mlflow.org) 1.0.0 através do pacote azureml-mlflow[(notebooks exemplo).](https://aka.ms/azureml-mlflow-examples)
    + Envie o caderno Jupyter como uma corrida. [Documentação de Referência da API](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + Visualização pública do [Data Drift Detector](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) através do pacote azureml-contrib-datadrift[(notebooks exemplo).](https://aka.ms/azureml-datadrift-example) O Data Drift é uma das principais razões pelas quais a precisão do modelo se degrada ao longo do tempo. Isso acontece quando os dados servidos para modelar na produção são diferentes dos dados em que o modelo foi treinado. O detector AML Data Drift ajuda o cliente a monitorar a deriva de dados e envia alerta sempre que o drift é detectado.

+ **Alterações da falha**

+ **Correções e melhorias de bugs**
  + ExecutarConfiguração de carga e salvar suportes especificando um caminho completo de arquivo com full back-compat para comportamento anterior.
  + Adicionado cache no ServicePrincipalAuthentication, desligado por padrão.
  + Habilite o registro de várias parcelas com o mesmo nome métrico.
  + Classe modelo agora devidamente importada a partir`from azureml.core import Model`de azureml.core ( ).
  + Em etapas `hash_path` de gasodutos, o parâmetro está agora preterido. O novo comportamento é fazer hash completo source_directory, exceto arquivos listados em .amlignore ou .gitignore.
  + Em pacotes de `get_all` `get_all_*` gasodutos, vários e métodos `list` foram `list_*`preteridos em favor e, respectivamente.
  + azureml.core.get_run não exige mais que as classes sejam importadas antes de retornar o tipo de execução original.
  + Corrigimos um problema no qual algumas chamadas para o WebService Update não acionam uma atualização.
  + O tempo de pontuação nos webservices aks deve ser entre 5ms e 300000ms. Max permitiu que scoring_timeout_ms para pedidos de pontuação foi aumentado de 1 min para 5 min.
  + Os objetos localWebservice agora têm `scoring_uri` e `swagger_uri` propriedades.
  + Criação de diretório de saídas movida e envio de saídas do diretório para fora do processo do usuário. Habilitado o histórico de execução SDK para ser executado em todos os processos de usuário. Isso deve resolver alguns problemas de sincronização experimentados por corridas de treinamento distribuídas.
  + O nome do registro azureml escrito a partir do nome do processo de usuário incluirá agora nome do processo (apenas para treinamento distribuído) e PID.

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>Azure Machine Learning Data Prep SDK v1.1.5

+ **Correções e melhorias de bugs**
  + Para valores de data-hora interpretados que tenham um formato de ano de 2 dígitos, o intervalo de anos válidos foi atualizado para corresponder ao Lançamento de Maio do Windows. A gama foi alterada de 1930-2029 para 1950-2049.
  + Ao ler em um `handleQuotedLineBreaks=True` `\r` arquivo e configuração, será tratado como uma nova linha.
  + Corrigimos um `read_pandas_dataframe` bug que causou falha em alguns casos.
  + Melhor desempenho `get_profile`de .
  + Mensagens de erro melhoradas.

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>Azure Machine Learning Data Prep SDK v1.1.4

+ **Novos recursos**
  + Agora você pode usar as seguintes funções de linguagem de expressão para extrair e analisar valores de data-hora em novas colunas.
    + `RegEx.extract_record()`extrai elementos de data-hora em uma nova coluna.
    + `create_datetime()`cria objetos de data-hora a partir de elementos de data separados.
  + Ao `get_profile()`ligar, agora você pode ver que as colunas quantile são rotuladas como (est.) para indicar claramente que os valores são aproximações.
  + Agora você pode usar ** globbing ao ler do Azure Blob Storage.
    + Por exemplo: `dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **Correções**
  + Corrigimos um bug relacionado à leitura de um arquivo Parquet de uma fonte remota (Azure Blob).

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>Azure Machine Learning SDK para Python v1.0.39
+ **Alterações**
  + Executar configuração auto_prepare_environment opção está sendo depreciada, com a preparação automática se tornando o padrão.

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>Azure Machine Learning Data Prep SDK v1.1.3

+ **Novos recursos**
  + Adicionado suporte para ler a partir de um banco de dados PostgresSQL, seja ligando para read_postgresql ou usando um Datastore.
    + Veja exemplos em guias de como fazer:
      + [Caderno de Ingestão de Dados](https://aka.ms/aml-data-prep-ingestion-nb)
      + [Notebook datastore](https://aka.ms/aml-data-prep-datastore-nb)

+ **Correções e melhorias de bugs**
  + Corrigido sustam problemas com conversão de tipo de coluna:
  + Agora converte corretamente uma coluna booleana ou numérica em uma coluna booleana.
  + Agora não falha ao tentar definir uma coluna de data para ser do tipo de data.
  + Tipos de JoinType aprimorados e documentação de referência que acompanha. Ao juntar dois fluxos de dados, agora você pode especificar um desses tipos de adesão:
    + NENHUM, FÓSFORO, INTERIOR, INCOMPARÁVEL ESQUERDA, ESQUERDA, ESQUERDA, INCOMPARÁVELDIREITA, DIREITA ANTI, DIREITA, FULLANTI, FULL.
  + Inferência aprimorada do tipo de dados para reconhecer mais formatos de data.

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Portal do Azure

No portal Azure, você pode agora:
+ Crie e execute experimentos ml automatizados
+ Crie um Notebook VM para experimentar a amostra de cadernos Jupyter ou o seu próprio.
+ Nova seção de Criação (Preview) no espaço de trabalho Azure Machine Learning, que inclui Aprendizado automatizado de máquina, interface visual e VMs de notebook hospedado
    + Crie automaticamente um modelo usando aprendizado de máquina automatizado
    + Use uma interface visual de arrastar e soltar para executar experimentos
    + Crie um Notebook VM para explorar dados, criar modelos e implantar serviços.
+ Gráfico ao vivo e atualização métrica em relatórios de execução e páginas de detalhes de execução
+ Visualizador de arquivos atualizado para logs, saídas e instantâneos em páginas de detalhes executar.
+ Experiência de criação de relatórios novos e melhorados na guia Experimentos.
+ Capacidade adicional de baixar o arquivo config.json da página Visão Geral do espaço de trabalho Azure Machine Learning.
+ Apoie a criação de espaço de trabalho de aprendizado de máquina do Azure Databricks.

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>Azure Machine Learning SDK para Python v1.0.33
+ **Novos recursos**
  + O método _Workspace.create_ agora aceita configurações padrão de cluster para clusters de CPU e GPU.
  + Se a criação do espaço de trabalho falhar, os recursos confiáveis serão limpos.
  + O Default Azure Container Registry SKU foi alterado para básico.
  + O Registro de Contêineres do Azure é criado preguiçosamente, quando necessário para execução ou criação de imagens.
  + Suporte para Ambientes para corridas de treinamento.

### <a name="notebook-virtual-machine"></a>Máquina virtual do notebook 

Use um Notebook VM como um ambiente de hospedagem seguro e pronto para a empresa para notebooks Jupyter no qual você pode programar experimentos de aprendizado de máquina, implantar modelos como pontos finais da Web e executar todas as outras operações suportadas pelo Azure Machine Learning SDK usando Python.Ele fornece vários recursos:
+ [Gire rapidamente um VM](tutorial-1st-experiment-sdk-setup.md) de notebook pré-configurado que tenha a versão mais recente do Azure Machine Learning SDK e pacotes relacionados.
+ O acesso é protegido por meio de tecnologias comprovadas, como HTTPS, autenticação e autorização do Azure Active Directory.
+ Armazenamento em nuvem confiável de notebooks e código em sua conta de armazenamento blob de aprendizado de máquina do Azure.Você pode excluir com segurança o seu notebook VM sem perder o seu trabalho.
+ Notebooks de exemplo pré-instalados para explorar e experimentar os recursos do Azure Machine Learning.
+ Recursos completos de personalização de VMs Azure, qualquer tipo de VM, quaisquer pacotes, quaisquer drivers. 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning SDK para Python v1.0.33 lançado.

+ Azure ML Hardware Accelerated Models on [FPGAs](how-to-deploy-fpga-web-service.md) está geralmente disponível.
  + Agora você pode [usar o pacote azureml-accel-models](how-to-deploy-fpga-web-service.md) para:
    + Treine os pesos de uma rede neural profunda suportada (ResNet 50, ResNet 152, DenseNet-121, VGG-16 e SSD-VGG)
    + Use o aprendizado de transferência com o DNN suportado
    + Registre o modelo com O Serviço de Gerenciamento de Modelos e contêiner o modelo
    + Implantar o modelo em um VM Azure com um FPGA em um cluster Azure Kubernetes Service (AKS)
  + Implante o contêiner em um dispositivo de servidor [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)
  + Marque seus dados com o ponto final do gRPC com esta [amostra](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Automatizado de Machine Learning

+ Varredura de recursos para :::no-loc text="featurizers"::: permitir a adição dinâmica para otimização de desempenho. Novo: :::no-loc text="featurizers":::incorporações de trabalho, peso de evidências, codificações de destino, codificação de destino de texto, distância de cluster
+ Cv inteligente para lidar com frações de trem/válido dentro de ML automatizado
+ Poucas mudanças de otimização de memória e melhoria do desempenho em tempo de execução
+ Melhoria de desempenho na explicação do modelo
+ Conversão do modelo ONNX para execução local
+ Suporte adicionado de subamostragem
+ Parada Inteligente quando nenhum critério de saída definido
+ Conjuntos empilhados

+ Previsão de série temporal
  + Nova função de previsão de previsão
  + Agora você pode usar validação cruzada de origem rolando em dados de séries tempois
  + Nova funcionalidade adicionada para configurar lags de séries de tempo
  + Nova funcionalidade adicionada para suportar recursos agregados de janela de rolagem
  + Nova detecção de Holiday e featurizer quando o código do país é definido em configurações de experimento

+ Azure Databricks
  + Previsão de séries temporiais habilitadas e capacidade de explicação/interpretação do modelo
  + Agora você pode cancelar e retomar (continuar) experimentos automatizados ml
  + Suporte adicionado para processamento multinúcleo

### <a name="mlops"></a>MLOps
+ **Implantação local & depuração para pontuação de contêineres**<br/> Agora você pode implantar um modelo ML localmente e iterar rapidamente em seu arquivo de pontuação e dependências para garantir que eles se comportem como esperado.

+ **Introduced InferenceConfig & Model.deploy()**<br/> A implantação do modelo agora suporta especificar uma pasta de origem com um script de entrada, o mesmo que um RunConfig.  Além disso, a implantação do modelo foi simplificada para um único comando.

+ **Rastreamento de referência git**<br/> Os clientes vêm solicitando recursos básicos de integração do Git há algum tempo, pois isso ajuda a manter uma trilha de auditoria de ponta a ponta. Implementamos o rastreamento entre as principais entidades do Azure ML para metadados relacionados ao Git (repo, commit, clean state). Essas informações serão coletadas automaticamente pelo SDK e pela CLI.

+ **Serviço de validação de & de perfil de modelo**<br/> Os clientes frequentemente reclamam da dificuldade de dimensionar adequadamente o cálculo associado ao seu serviço de inferência. Com nosso serviço de criação de perfil de modelo, o cliente pode fornecer entradas de amostra e faremos um perfil em 16 configurações diferentes de CPU/memória para determinar o dimensionamento ideal para implantação.

+ **Traga sua própria imagem base para inferência**<br/> Outra queixa comum foi a dificuldade em passar da experimentação para a inferência de dependências de compartilhamento de RE. Com nosso novo recurso básico de compartilhamento de imagens, agora você pode reutilizar suas imagens base de experimentação, dependências e tudo mais, para inferência. Isso deve acelerar as implantações e reduzir a lacuna do circuito interno para o exterior.

+ **Experiência aprimorada de geração de esquema swagger**<br/> Nosso método anterior de geração de swagger era propenso a erros e impossível de automatizar. Temos uma nova maneira em linha de gerar swagger schemas de qualquer função Python através de decoradores. Temos esse código de código aberto e nosso protocolo de geração de esquemas não está acoplado à plataforma Azure ML.

+ **Acli ml do Azure está geralmente disponível (GA)**<br/> Os modelos agora podem ser implantados com um único comando CLI. Temos feedback comum do cliente de que ninguém implanta um modelo ML a partir de um notebook Jupyter. A [**documentação de referência da CLI**](https://aka.ms/azmlcli) foi atualizada.


## <a name="2019-04-22"></a>2019-04-22

Azure Machine Learning SDK para Python v1.0.30 lançado.

O [`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py) foi introduzido para adicionar uma nova versão de um pipeline publicado, mantendo o mesmo ponto final.

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Azure Machine Learning Data Prep SDK v1.1.2

Nota: Data Prep Python SDK não instalará `numpy` mais e `pandas` empacotes. Consulte [as instruções de instalação atualizadas](https://github.com/Microsoft/AMLDataPrepDocs).

+ **Novos recursos**
  + Agora você pode usar a transformação pivô.
    + Guia de como fazer: [Notebook pivô](https://aka.ms/aml-data-prep-pivot-nb)
  + Agora você pode usar expressões regulares em funções nativas.
    + Exemplos:
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + Agora você `to_upper`  pode `to_lower`  usar e funções na linguagem de expressão.
  + Agora você pode ver o número de valores únicos de cada coluna em um perfil de dados.
  + Para alguns dos passos de leitor comumente `infer_column_types` usados, agora você pode passar no argumento. Se estiver configurado para `True`, O Data Prep tentará detectar e converter automaticamente os tipos de colunas.
    + `inference_arguments`agora é preterido.
  + Agora você `Dataflow.shape`pode ligar.

+ **Correções e melhorias de bugs**
  + `keep_columns` agora aceita um argumento `validate_column_exists`opcional adicional, que `keep_columns` verifica se o resultado conterá quaisquer colunas.
  + Todos os passos do leitor (que lêem `verify_exists`a partir de um arquivo) agora aceitam um argumento opcional adicional .
  + Melhor desempenho da leitura do dataframe dos pandas e obtenção de perfis de dados.
  + Corrigimos um bug em que cortar um único passo de um Fluxo de Dados falhou com um único índice.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Portal do Azure
  + Agora você pode reenviar uma execução de Script existente em um cluster de computação remota existente.
  + Agora você pode executar um pipeline publicado com novos parâmetros na guia Pipelines.
  + Os detalhes de execução agora suportam um novo visualizador de arquivo Snapshot. Você pode visualizar um instantâneo do diretório quando você enviou uma execução específica. Você também pode baixar o notebook que foi enviado para iniciar a execução.
  + Agora você pode cancelar as corridas dos pais do portal Azure.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK para Python v1.0.23

+ **Novos recursos**
  + O Azure Machine Learning SDK agora suporta Python 3.7.
  + Os estimadores DNN de aprendizado de máquina do Azure agora fornecem suporte integrado em várias versões. Por exemplo, `TensorFlow`  o estimador `framework_version` agora aceita um parâmetro, e os usuários podem especificar a versão '1.10' ou '1.12'. Para obter uma lista das versões suportadas `get_supported_versions()` pela versão atual do SDK, ligue para a classe de quadro desejada (por exemplo, `TensorFlow.get_supported_versions()`).
  Para obter uma lista das versões suportadas pela versão mais recente do SDK, consulte a documentação do [Estimador DNN](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py).

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Azure Machine Learning Data Prep SDK v1.1.1

+ **Novos recursos**
  + Você pode ler várias fontes datastore/DataPath/DataReference usando read_* transformações.
  + Você pode realizar as seguintes operações em colunas para criar uma nova coluna: divisão, piso, módulo, potência, comprimento.
  + O Data Prep agora faz parte do pacote de diagnósticos do Azure ML e registrará as informações de diagnóstico por padrão.
    + Para desativar isso, defina essa variável de ambiente como verdadeira: DISABLE_DPREP_LOGGER

+ **Correções e melhorias de bugs**
  + Documentação de código melhorada para classes e funções comumente usadas.
  + Corrigimos um bug no auto_read_file que não conseguiu ler arquivos do Excel.
  + Opção adicionada para substituir a pasta em read_pandas_dataframe.
  + Melhor desempenho da instalação de dependência dotnetcore2 e suporte adicional para fedora 27/28 e Ubuntu 1804.
  + Melhorou o desempenho da leitura do Azure Blobs.
  + A detecção do tipo de coluna agora suporta colunas do tipo Longo.
  + Corrigimos um bug no qual alguns valores de data estavam sendo exibidos como carimbos de tempo em vez de objetos de data-hora python.
  + Corrigimos um bug onde alguns tipos de contagens estavam sendo exibidos como duplos em vez de inteiros.


## <a name="2019-03-25"></a>25-03-2019

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK para Python v1.0.21

+ **Novos recursos**
  + O método *azureml.core.Run.create_children* permite a criação de múltiplas corridas de crianças com uma única chamada.

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Azure Machine Learning Data Prep SDK v1.1.0

+ **Alterações da falha**
  + O conceito do Pacote de Preparação de Dados foi preterido e não é mais suportado. Em vez de persistir vários fluxos de dados em um pacote, você pode persistir fluxos de dados individualmente.
    + Guia de como fazer: [Abrindo e salvando o notebook Dataflows](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **Novos recursos**
  + O Data Prep agora pode reconhecer colunas que correspondem a um tipo semântico específico e dividir de acordo. Os STypes atualmente suportados incluem: endereço de e-mail, coordenadas geográficas (latitude & longitude), endereços IPv4 e IPv6, número de telefone dos EUA e CEP dos EUA.
    + Guia de como fazer: [Notebook Tipos Semânticos](https://aka.ms/aml-data-prep-semantic-types-nb)
  + O Data Prep agora suporta as seguintes operações para gerar uma coluna resultante a partir de duas colunas numéricas: subtrair, multiplicar, dividir e modulo.
  + Você pode `verify_has_data()` chamar um Fluxo de Dados para verificar se o Fluxo de Dados produziria registros se executado.

+ **Correções e melhorias de bugs**
  + Agora você pode especificar o número de caixas a serem usados em um histograma para perfis de colunas numéricas.
  + A `read_pandas_dataframe` transformação agora requer que o DataFrame tenha nomes de coluna sinuosos ou bytes.
  + Corrigimos um `fill_nulls` bug na transformação, onde os valores não eram preenchidos corretamente se a coluna estivesse faltando.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK para Python v1.0.18

 + **Alterações**
   + O pacote azureml-tensorboard substitui azureml-contrib-tensorboard.
   + Com esta versão, você pode configurar uma conta de usuário em seu cluster de computação gerenciada (amlcompute), enquanto cria. Isso pode ser feito passando essas propriedades na configuração de provisionamento. Você pode encontrar mais detalhes na documentação de referência do [SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--).

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning Data Prep SDK v1.0.17

+ **Novos recursos**
  + Agora suporta adicionar duas colunas numéricas para gerar uma coluna resultante usando a linguagem de expressão.

+ **Correções e melhorias de bugs**
  + Melhorou a documentação e a verificação de parâmetros para random_split.

## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning Data Prep SDK v1.0.16

+ **Correção de bugs**
  + Corrigimos um problema de autenticação do Service Principal causado por uma alteração de API.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK para Python v1.0.17

+ **Novos recursos**

  + O Azure Machine Learning agora oferece suporte de primeira classe para o popular Chainer de estrutura DNN. O [`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) uso de usuários de classe pode facilmente treinar e implantar modelos Chainer.
    + Saiba como [executar treinamento distribuído com chainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + Aprenda a executar ajuste [de hiperparâmetro com Chainer usando HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + O Azure Machine Learning Pipelines adicionou capacidade de ativar uma execução do Pipeline com base em modificações de armazenamento de dados. O [notebook de programação](https://aka.ms/pl-schedule) de pipeline é atualizado para mostrar este recurso.

+ **Correções e melhorias de bugs**
  + Adicionamos suporte nos pipelines de Machine Learning do Azure para definir a propriedade source_directory_data_store em um armazenamento de dados desejado (como um armazenamento blob) em [Configurações de execução fornecidas](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) ao [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py). Por padrão, as etapas usam o armazenamento de arquivos Azure como o armazenamento de dados de backup, que pode ter problemas de estrangulamento quando um grande número de etapas são executadas simultaneamente.

### <a name="azure-portal"></a>Portal do Azure

+ **Novos recursos**
  + Nova experiência de editor de mesa de arrastar e soltar para relatórios. Os usuários podem arrastar uma coluna do poço para a área da tabela onde uma visualização da tabela será exibida. As colunas podem ser reorganizadas.
  + Novo visualizador de arquivos Logs
  + Links para testes executados, computados, modelos, imagens e implantações da guia de atividades

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Azure Machine Learning Data Prep SDK v1.0.15

+ **Novos recursos**
  + O Data Prep agora suporta a gravação de fluxos de arquivos a partir de um fluxo de dados. Também fornece a capacidade de manipular os nomes do fluxo de arquivos para criar novos nomes de arquivo.
    + Guia de como fazer: [Trabalhando com o notebook Fluxos de Arquivos](https://aka.ms/aml-data-prep-file-stream-nb)

+ **Correções e melhorias de bugs**
  + Melhor desempenho do t-Digest em grandes conjuntos de dados.
  + O Data Prep agora suporta a leitura de dados de um DataPath.
  + Uma codificação quente agora funciona em colunas booleanas e numéricas.
  + Outras correções diversas de erros.

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>SDK do Azure Machine Learning para Python v1.0.15

+ **Novos recursos**
  + O Azure Machine Learning Pipelines adicionou o AzureBatchStep[(notebook),](https://aka.ms/pl-azbatch)o HyperDriveStep (notebook) e a funcionalidade de agendamento baseada no tempo[(notebook).](https://aka.ms/pl-schedule)
  +  DataTranferStep atualizado para funcionar com o Azure SQL Server e banco de dados do Azure para PostgreSQL ([notebook](https://aka.ms/pl-data-trans)).

+ **Alterações**
  + `PublishedPipeline.get_published_pipeline` preterido em favor de `PublishedPipeline.get`.
  + `Schedule.get_schedule` preterido em favor de `Schedule.get`.

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>SDK de Preparação de Dados do Azure Machine Learning v1.0.12

+ **Novos recursos**
  + A preparação de dados agora oferece suporte à leitura de um banco de dados SQL do Azure usando o armazenamento de dados.

+ **Alterações**
  + Melhorou o desempenho da memória de certas operações em dados grandes.
  + `read_pandas_dataframe()` agora exige que `temp_folder` seja especificado.
  + A propriedade `name` em `ColumnProfile` foi preterida - usar `column_name` em vez disso.

## <a name="2019-01-28"></a>28/01/2019

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>SDK do Azure Machine Learning para Python v1.0.10

+ **Alterações:**
  + O SDK do Azure Machine Learning não tem mais pacotes azure-cli como dependência. Especificamente, as dependências azure-cli-core e azure-cli-profile foram removidas de azureml-core. Estas são alterações que afetam o usuário:
      + Se você estiver executando o "login az" e, em seguida, usando azureml-sdk, o SDK fará o login de código do navegador ou do dispositivo mais uma vez. Ele não usará nenhum estado de credencial criado por "az login".
    + Para autenticação de CLI do Azure, assim como usando "az login", use a classe _azureml.core.authentication.AzureCliAuthentication_. Para a autenticação de CLI do Azure, execute _pip install azure-cli_ no ambiente do Python em que você instalou o azureml-sdk.
    + Se você estiver usando "az login" e usando uma entidade de serviço para a automação, é recomendável usar a classe _azureml.core.authentication.ServicePrincipalAuthentication_, pois o azureml-sdk não usará o estado de credenciais criado pela CLI do Azure.

+ **Correções de**bugs : Esta versão contém principalmente pequenas correções de bugs

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>SDK de Preparação de Dados do Azure Machine Learning v1.0.8

+ **Correções**
  + Melhorou o desempenho da obtenção de perfis de dados.
  + Bugs secundários relacionados ao relatório de erros foram corrigidos.

### <a name="azure-portal-new-features"></a>Portal do Azure: novos recursos
+ Nova experiência de criação de gráficos de arrastar e soltar para relatórios. Os usuários podem arrastar uma coluna ou um atributo do poço à área do gráfico, na qual o sistema selecionará automaticamente um tipo de gráfico apropriado para o usuário com base no tipo de dados. Os usuários podem alterar o tipo de gráfico para outros tipos aplicáveis ou adicionar outros atributos.

    Tipos de gráfico compatíveis:
    - Gráfico de linhas
    - Histograma
    - Gráfico de barras empilhadas
    - Gráfico de caixa
    - Dispersão
    - Gráfico de bolhas
+ O portal agora gera relatórios dinamicamente para experimentos. Quando um usuário envia uma execução a um experimento, um relatório será gerado automaticamente com métricas registradas em log e gráficos para permitir a comparação entre diferentes execuções.

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>SDK do Azure Machine Learning para Python v1.0.8

+ **Correções de**bugs : Esta versão contém principalmente pequenas correções de bugs

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>SDK de Preparação de Dados do Azure Machine Learning v1.0.7

+ **Novos recursos**
  + Aprimoramentos do repositório de dados (documentado em [Guia de instruções do repositório de dados](https://aka.ms/aml-data-prep-datastore-nb))
    + Capacidade adicional de ler e gravar no compartilhamento de Arquivo do Azure e em Repositórios de Dados do ADLS em expansão.
    + Ao usar repositórios de dados, a preparação de dados agora dá suporte ao uso à autenticação de entidade de serviço em vez da autenticação interativa.
    + Adicionado suporte para URLs de wasb e wasbs.

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>SDK de Preparação de Dados do Azure Machine Learning v1.0.6

+ **Correções**
  + Corrigido o bug com a leitura de contêineres de Blob do Azure legíveis públicos no Spark

## <a name="2018-12-20"></a>2018-12-20

### <a name="azure-machine-learning-sdk-for-python-v106"></a>SDK do Azure Machine Learning para Python v1.0.6
+ **Correções de**bugs : Esta versão contém principalmente pequenas correções de bugs

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>SDK de preparação de dados do Azure Machine Learning v1.0.4

+ **Novos recursos**
  + A função `to_bool` agora permite que os valores incompatíveis sejam convertidos em valores de erro. Esse é o novo comportamento de incompatibilidade de padrão para `to_bool` e `set_column_types`, enquanto o comportamento padrão anterior era para converter valores incompatíveis como False.
  + Ao chamar `to_pandas_dataframe`, há uma nova opção para interpretar valores ausente/nulos em colunas numéricas como NaN.
  + Capacidade adicional para verificar o tipo de retorno de algumas expressões para garantir a consistência de tipo e falha antecipada.
  + Agora você pode chamar `parse_json` para analisar valores em uma coluna como objetos JSON e expandi-las em várias colunas.

+ **Correções**
  + Corrigido um bug que travou `set_column_types` no Python 3.5.2.
  + Corrigido um bug que falhou ao se conectar ao armazenamento de dados usando uma imagem do AML.

+ **Atualizações**
  * [Notebooks de exemplo](https://aka.ms/aml-data-prep-notebooks) para obter tutoriais de Introdução, estudos de caso e guias de instruções.

## <a name="2018-12-04-general-availability"></a>2018-12-04: Disponibilidade Geral

O Azure Machine Learning agora está disponível em geral.

### <a name="azure-machine-learning-compute"></a>Computação do Azure Machine Learning
Com esta versão, estamos anunciando uma nova experiência de computação gerenciada por meio da [Computação do Machine Learning](how-to-set-up-training-targets.md#amlcompute). Esse destino de computação substitui a computação do IA do Lote do Azure para o Azure Machine Learning.

O destino de computação:
+ É usado para treinamento de modelo e inferência/pontuação em lote
+ É único - para computação de vários nós
+ Realiza o gerenciamento de cluster e o plano de trabalho para o usuário
+ Dimensionado automaticamente por padrão
+ Suporte para recursos de CPU e GPU
+ Permite o uso de VMs de baixa prioridade para redução de custos

A Computação do Azure Machine Learning pode ser criada no Python, pelo portal do Azure ou pela CLI. Precisa ser criada na região do seu workspace e não pode ser anexada a outro workspace. Esse destino de computação usa um contêiner do Docker para execução e empacota as dependências para replicar o mesmo ambiente em todos os nós.

> [!Warning]
> É recomendável criar um novo workspace para usar a Computação do Azure Machine Learning. Há uma possibilidade remota de que os usuários que tentarem criar a Computação do Azure Machine Learning em um workspace existente recebam um erro. A computação existente no workspace deve continuar a funcionar sem problemas.

### <a name="azure-machine-learning-sdk-for-python-v102"></a>SDK do Azure Machine Learning para Python v1.0.2
+ **Alterações da falha**
  + Nesta versão, removemos o suporte para a criação de uma VM do Azure Machine Learning. Ainda é possível anexar uma VM em nuvem existente ou um servidor local remoto.
  + Também removemos o suporte para o IA do Lote, agora, o suporte para todos ocorrerá por meio da Computação do Azure Machine Learning.

+ **Novo**
  + Para pipelines do aprendizado de máquina:
    + [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [MpiStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **Atualizado**
  + Para pipelines do aprendizado de máquina:
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) aceita runconfig agora
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) agora copia de e para uma fonte de dados SQL
    + Agendar a funcionalidade no SDK para criar e atualizar agendas e executar pipelines publicados

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>SDK de preparação de dados do Azure Machine Learning v0.5.2
+ **Alterações da falha**
  * `SummaryFunction.N` foi renomeado para `SummaryFunction.Count`.

+ **Correções**
  * Use o mais recente AML Run Token ao ler e escrever em datastores em corridas remotas. Anteriormente, se o Token de Execução do AML fosse atualizado no Python, o runtime de preparação de dados não seria atualizado com o novo Token de Execução do AML.
  * Mensagens de erro mais claras adicionais
  * to_spark_dataframe() não falhará mais quando o Spark usar a serialização `Kryo`
  * Agora, o Inspetor de Contagem de Valor pode mostrar mais de mil valores únicos
  * Random Split não falha mais se o Fluxo de Dados original não tiver um nome

+ **Mais informações**
  * [SDK de preparação de dados do Azure Machine Learning](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Documentos e notebooks
+ Pipelines de ML
  + Notebooks novos e atualizados para começar a ver exemplos de pipelines, escopo de lote e transferência de estilo: https://aka.ms/aml-pipeline-notebooks
  + Saiba como [criar seu primeiro pipeline](how-to-create-your-first-pipeline.md)
  + Saiba como [executar previsões em lotes usando pipelines](how-to-use-parallel-run-step.md)
+ Computação de destino do Azure Machine Learning
  + [Notebooks de exemplo](https://aka.ms/aml-notebooks) agora são atualizados para usar essa nova computação gerenciada.
  + [Saiba mais sobre essa computação](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Portal do Azure: novos recursos
+ Crie e gerencie tipos de [Computação do Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) no portal.
+ Monitore o uso de cota e a [cota de solicitações](how-to-manage-quotas.md) da Computação do Azure Machine Learning.
+ Veja o status do cluster da Computação do Machine Learning em tempo real.
+ Foi adicionado suporte à rede virtual para a criação de Computação do Azure Machine Learning e do Serviço de Kubernetes do Azure.
+ Execute novamente os pipelines publicados com parâmetros existentes.
+ Novos [gráficos de aprendizado de máquina automatizado](how-to-understand-automated-ml.md) para modelos de classificação (gráfico de importância de recursos de comparação de precisão e calibragem com explicabilidade de modelo) e modelos de regressão (resíduos e gráfico de importância de recursos com explicabilidade de modelo).
+ Os pipelines podem ser exibidos no portal do Azure




## <a name="2018-11-20"></a>20-11-2018

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>SDK do Azure Machine Learning para Python v0.1.80

+ **Alterações da falha**
  * Namespace *azureml.Train.widgets* foi movido para *azureml.widgets*.
  * *azureml.core.compute.AmlCompute* substitui as seguintes classes - *azureml.core.compute.BatchAICompute* e *azureml.core.compute.DSVMCompute*. A última classe será removida nas versões subsequentes. A classe AmlCompute tem agora uma definição mais fácil e simplesmente precisa de um vm_size e do max_nodes e dimensionará automaticamente a seu cluster de 0 para o max_nodes quando um trabalho for enviado. Nossos [notebooks de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/training) foram atualizados com essas informações e devem fornecer exemplos de uso. Esperamos que você goste dessa simplificação e dos muitos dos recursos mais interessantes para ficar em uma versão posterior!

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>SDK de preparação de dados do AML v0.5.1

Saiba mais sobre o SDK de preparação de dados lendo [docs de referência](https://aka.ms/data-prep-sdk).
+ **Novos recursos**
   * Criada uma nova CLI DataPrep para executar pacotes DataPrep e exibir o perfil de dados para um conjunto de dados ou o fluxo de dados
   * API de SetColumnType reprojetado para melhorar a usabilidade
   * Smart_read_file renomeado para auto_read_file
   * Agora inclui distorção e curtose no Perfil de Dados
   * Pode criar amostra com amostragem estratificada
   * Pode ler de arquivos zip que contenham arquivos CSV
   * Pode dividir conjuntos de dados por linha com a divisão aleatória (por exemplo, nos conjuntos de teste-treinamento)
   * Pode obter os tipos de dados de coluna de um fluxo de dados ou um perfil de dados chamando `.dtypes`
   * Pode obter a contagem de linhas de um fluxo de dados ou um perfil de dados chamando `.row_count`

+ **Correções**
   * Corrigidos para conversão dupla
   * Ativo corrigido após qualquer adição de coluna
   * Corrigido um problema com FuzzyGrouping, onde ele não detectaria grupos em alguns casos
   * Função de classificação corrigida para respeitar a ordem de classificação de várias colunas
   * Fixos e/ou expressões para ser semelhante a como `pandas` lida com isso
   * Leitura corrigida do caminho dbfs
   * Mensagens de erro tornadas mais compreensíveis
   * Agora não falha mais ao ler no alvo de computação remota usando um token AML
   * Agora não falhará na DSVM do Linux
   * Agora não falha quando os valores de cadeia de caracteres não são em predicados de cadeia de caracteres
   * Agora manipula erros de asserção ao fluxo de dados deve falhar corretamente
   * Agora dá suporte a locais de armazenamento dbutils montados no Azure Databricks

## <a name="2018-11-05"></a>05-11-2018

### <a name="azure-portal"></a>Portal do Azure
O portal Azure para Azure Machine Learning tem as seguintes atualizações:
  * Uma nova **Pipelines** guia para pipelines publicados.
  * Adicionado suporte para anexar a um cluster HDInsight existente como um destino de computação.

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>SDK de aprendizado de máquina do AML para Python v0.1.74

+ **Alterações da falha**
  * Workspace.compute_targets, datastores, experimentos, imagens, modelos e *webservices* são propriedades em vez de métodos. Por exemplo, substitua *Workspace.compute_targets()* com *Workspace.compute_targets*.
  * *Run.get_context* pretere *Run.get_submitted_run*. O último método será removido em versões subsequentes.
  * A classe *PipelineData* agora espera um objeto de armazenamento de dados como um parâmetro em vez de datastore_name. Da mesma forma, o *Pipeline* aceita default_datastore em vez de default_datastore_name.

+ **Novos recursos**
  * O caderno de amostras de [amostra do AML](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb) agora usa etapas de MPI.
  * O widget RunDetails para notebooks Jupyter é atualizado para mostrar uma visualização do pipeline.

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>SDK de preparação de dados do AML v0.4.0

+ **Novos recursos**
  * Tipo de contagem adicionada ao perfil de dados
  * Contagem de valores e histograma estão agora disponíveis
  * Mais percentis no perfil de dados
  * Mediana está disponível em Summarize
  * Agora há suporte para Python 3.7
  * Quando você salva um fluxo de dados que contém datastores em um pacote DataPrep, as informações do armazenamento de dados serão mantidas como parte do pacote DataPrep
  * A gravação no armazenamento de dados agora é suportada

+ **Bug corrigido**
  * Extensões de inteiro não assinado de 64 bits agora são tratadas corretamente no Linux
  * Etiqueta de texto incorreta corrigida para arquivos de texto simples em smart_read
  * O tipo de coluna de cadeia de caracteres agora aparece na visualização de métricas
  * A contagem de tipos agora é fixada para mostrar ValueKinds mapeados para um único FieldType em vez de um individual
  * Write_to_csv não falha quando o caminho é fornecido como uma cadeia de caracteres
  * Ao usar Substituir, deixar "encontrar" em branco não falhará mais

## <a name="2018-10-12"></a>12-10-2018

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>SDK de aprendizado de máquina do Azure para Python v0.1.68

+ **Novos recursos**
  * Suporte a vários locatários ao criar novo workspace.

+ **Bugs corrigidos**
  * Você não precisa fixar a versão da biblioteca pynacl ao implantar o serviço web.

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>SDK de preparação de dados de aprendizado de máquina do Azure v0.3.0

+ **Novos recursos**
  * Adicionado método transform_partition_with_file (script_path), que permite aos usuários passar no caminho de um arquivo Python para executar

## <a name="2018-10-01"></a>01-10-2018

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>SDK de aprendizado de máquina do Azure para Python v0.1.65
A [versão 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65) inclui novos recursos, mais documentação, correções de bugs e mais [exemplos de blocos de notas](https://aka.ms/aml-notebooks).

Veja [a lista de problemas conhecidos](resource-known-issues.md) para aprender sobre erros e soluções conhecidas.

+ **Alterações da falha**
  * Workspace.Experiments, Workspace.models, Workspace.compute_targets, Workspace.images, dicionário de retorno Workspace.web_services, retornado anteriormente de lista. Veja a documentação da API [azureml.core.Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py).

  * Automatizados de aprendizado de máquina removido erro normalizado quadrada da média das métricas principais.

+ **HyperDrive**
  * Várias correções de bugs do HyperDrive para Bayesian, melhorias de desempenho para obter chamadas de métricas.
  * Atualização de Tensorflow 1.10 do 1.9
  * Otimização de imagem do Docker para inicialização a frio.
  * O trabalho agora reporta o status correto, mesmo se ele sair com um código de erro diferente de 0.
  * Validação de atributo RunConfig no SDK.
  * O objeto de execução HyperDrive suporta cancelamentos semelhantes a uma execução normal: não é necessário passar nenhum parâmetro.
  * Melhorias de widget para manter o estado dos valores suspensos para execuções distribuídas e execuções do HyperDrive.
  * TensorBoard e outros arquivos de log suportam fixos para o servidor Parameter.
  * Suporte ao Intel (R) MPI no lado do serviço.
  * Bugfix ao ajuste de parâmetro para correção de execução distribuída durante a validação no BatchAI.
  * O Context Manager agora identifica a instância primária.

+ **Experiência do portal do Azure**
  * log_table () e log_row () são suportados em detalhes da execução.
  * Crie automaticamente gráficos para tabelas e linhas com 1, 2 ou 3 colunas numéricas e uma coluna categórica opcional.

+ **Aprendizado automatizado de máquina**
  * Melhor tratamento e documentação de erros
  * Corrigidos problemas de desempenho de recuperação de propriedades de execução.
  * Fixo continuar a execução do problema.
  * Corrigido :::no-loc text="ensembling"::: problemas de iteração.
  * Bug deslocada de treinamento fixo no MAC OS.
  * Curva média de solicitação de pull/ROC no cenário de validação personalizada da macro da resolução.
  * Removida a lógica extra de índice.
  * Removemos o filtro de get_output API.

+ **Pipelines**
  * Adicionado um método Pipeline.publish () para publicar um pipeline diretamente, sem precisar executar uma execução primeiro.
  * Adicionado um método PipelineRun.get_pipeline_runs () para buscar as execuções de pipeline que foram geradas a partir de um pipeline publicado.

+ **Project Brainwave**
  * Suporte atualizado para novos modelos de IA disponíveis em FPGAs.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>SDK de preparação de dados de aprendizado de máquina do Azure v0.2.0
[Versão 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) inclui recursos e correções de bug a seguir:

+ **Novos recursos**
  * Suporte para codificação one-hot
  * Suporte para a transformação de quantil

+ **Bug corrigido:**
  * Funciona com qualquer versão do Tornado, sem necessidade de rebaixar sua versão do Tornado
  * O valor conta para todos os valores, não apenas os três primeiros

## <a name="2018-09-public-preview-refresh"></a>09-2018 (atualização da visualização pública)

Um novo e atualizado lançamento do Azure Machine Learning: Leia mais sobre esta versão:https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>Próximas etapas

Leia a visão geral do [Azure Machine Learning](overview-what-is-azure-ml.md).
