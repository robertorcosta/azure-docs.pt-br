---
title: nuvem de primavera az
description: Gerencie a Nuvem de Primavera do Azure usando o Azure CLI
author: bmitchell287
ms.service: spring-cloud
ms.topic: reference
ms.date: 10/03/2019
ms.author: brendm
ms.openlocfilehash: 33d13d2d4fa9003ef041c4c96be83a69ac595a78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298789"
---
# <a name="az-spring-cloud"></a>az primavera-nuvem

## <a name="manage-azure-spring-cloud-using-the-azure-cli"></a>Gerencie a Nuvem de Primavera do Azure usando o Azure CLI

>[!Note]
> No momento, o Azure Spring Cloud está em versão prévia.  Esses comandos podem ser alterados ou removidos em uma versão futura.

| az primavera-nuvem |  |
|------|------:|
| [az primavera-nuvem criar](#az-spring-cloud-create) | Crie uma instância da Nuvem de Primavera do Azure. |
| [az primavera-nuvem excluir](#az-spring-cloud-delete) | Exclua uma instância da Nuvem de Primavera do Azure. |
| [az primavera-nuvem lista](#az-spring-cloud-list) | Liste todas as instâncias do Azure Spring Cloud no grupo de recursos dado, caso contrário, liste os IDs de assinatura. |
| [az primavera-nuvem show](#az-spring-cloud-show) | Mostre os detalhes de uma Nuvem de Primavera do Azure. |

| az spring-cloud app | Comandos para gerenciar aplicativos na Nuvem de Primavera do Azure.  |
| ---- | ----: |
| [az spring-cloud app criar](#az-spring-cloud-app-create) | Crie um novo aplicativo com uma implantação padrão na Nuvem de Primavera do Azure. |
| [az spring-cloud app excluir](#az-spring-cloud-app-delete) | Exclua um aplicativo na Nuvem de Primavera do Azure. |
| [az spring-cloud app implantar](#az-spring-cloud-app-deploy) | Implante a partir de código-fonte ou um binário pré-construído para um aplicativo e atualizar configurações relacionadas. |
| [az spring-cloud app list](#az-spring-cloud-app-list) | Liste todos os aplicativos na Nuvem de Primavera do Azure. |
| [az spring-cloud app reiniciar](#az-spring-cloud-app-restart) | Reinicie as instâncias do aplicativo usando padrões de implantação de produção. |
| [az escala de aplicativo primavera-nuvem](#az-spring-cloud-app-scale) | Dimensione manualmente um aplicativo ou suas implantações. |
| [az spring-cloud app set-deployment](#az-spring-cloud-app-set-deployment) | Defina a implantação de produção de um aplicativo. |
| [az spring-cloud app show](#az-spring-cloud-app-show) | Mostre os detalhes de um aplicativo na Nuvem da Primavera do Azure. |
| [az spring-cloud app show-deploy-log](#az-spring-cloud-app-show-deploy-log) | Mostrar registros de compilação para a última implantação da origem. Padrão para implantação de produção. |
| [az spring-cloud app começar](#az-spring-cloud-app-start) | Iniciar instâncias do aplicativo usando padrões de implantação de produção. |
| [az spring-cloud app stop](#az-spring-cloud-app-stop) | Pare as instâncias do aplicativo usando padrões de implantação de produção. |
| [az primavera-nuvem atualização de aplicativo](#az-spring-cloud-app-update) | Atualize a configuração do aplicativo especificado. |

| az spring-cloud app binding | Comandos para gerenciar vinculações com o Azure Data Services.  O aplicativo deve ser reiniciado antes que essas configurações entrem em vigor. |
| --- | ---: |
| [az spring-cloud lista de vinculação de aplicativos](#az-spring-cloud-app-binding-list) | Liste todas as vinculações de serviço em um aplicativo. |
| [az spring-cloud app binding remove](#az-spring-cloud-app-binding-remove) | Remova uma vinculação de serviço do aplicativo. |
| [az spring-cloud app binding show](#az-spring-cloud-app-binding-show) | Mostre os detalhes de uma ligação de serviço. |
| [az spring-cloud app bindingcosmos adicionar](#az-spring-cloud-app-binding-cosmos-add) | Vincule um Azure CosmosDB com o aplicativo. |
| [az spring-cloud app bindingcosmos atualização](#az-spring-cloud-app-binding-cosmos-update) | Atualize um serviço Azure CosmosDB vinculado. |
| [az spring-cloud app binding mysql add](#az-spring-cloud-app-binding-mysql-add) | Vincule um banco de dados Azure para MySQL com o aplicativo. |
| [az spring-cloud app binding mysql update](#az-spring-cloud-app-binding-mysql-update) | Atualize um banco de dados Azure para vinculação de serviço MySQL. |
| [az spring-cloud app binding redis add](#az-spring-cloud-app-binding-redis-add) | Vincule um cache azure para Redis com o aplicativo. |
| [az spring-cloud app binding redis atualização redis](#az-spring-cloud-app-binding-redis-update) | Atualize um cache do Azure para vinculação do serviço Redis. |

| az spring-cloud implantação de aplicativo | Comandos para gerenciar o ciclo de vida de implantação de um aplicativo no Azure Spring Cloud. |
| --- | ---: |
| [az spring-cloud implantação de aplicativo criar](#az-spring-cloud-app-deployment-create) | Crie uma implantação de preparação para o aplicativo. |
| [az spring-cloud app exclusão](#az-spring-cloud-app-deployment-delete) | Exclua uma implantação do aplicativo. |
| [az spring-cloud lista de implantação de aplicativos](#az-spring-cloud-app-deployment-list) | Liste todas as implantações em um aplicativo. |
| [az spring-cloud app implantação show](#az-spring-cloud-app-deployment-show) | Mostre detalhes da implantação. |

| az mola-nuvem config-servidor | Comandos para gerenciar o Azure Spring Cloud Config Server. |
| --- | ---: |
| [az mola-nuvem config-servidor claro](#az-spring-cloud-config-server-clear) | Apagar todas as configurações do Servidor Config. |
| [az mola-nuvem configuração-servidor conjunto](#az-spring-cloud-config-server-set) | Defina o Config Server a partir de um arquivo YAML. |
| [az spring-cloud config-server show](#az-spring-cloud-config-server-show) | Mostrar configuração do servidor de configuração Config. |
| [az mola-nuvem config servidor git conjunto](#az-spring-cloud-config-server-git-set) | Defina as propriedades git para o Servidor config.  Os valores anteriores serão substituídos. |
| [az mola-nuvem config servidor git repo adicionar](#az-spring-cloud-config-server-git-repo-add) | Adicione uma nova configuração de repositório git ao Servidor config. |
| [az spring-cloud config server git repo list](#az-spring-cloud-config-server-git-repo-list) | Liste todas as configurações do repositório git para o Servidor de configuração. |
| [az mola-nuvem config servidor git repo remover](#az-spring-cloud-config-server-git-repo-remove) | Remova o repositório git especificado do Servidor Config. |

| az primavera-nuvem ponto final de teste | Comandos para gerenciar testes de ponto final no Azure Spring Cloud |
| --- | ---: |
| [az spring-cloud test-endpoint desativar](#az-spring-cloud-test-endpoint-disable) | Desativar o ponto final do teste. |
| [az spring-cloud test-endpoint ativar](#az-spring-cloud-test-endpoint-enable) | Habilite o ponto final do teste. |
| [az primavera-nuvem lista de ponto final de teste](#az-spring-cloud-test-endpoint-list) | Listar as teclas de ponto final do teste. |
| [az spring-cloud test-endpoint renovar-key](#az-spring-cloud-test-endpoint-renew-key) | Regenerar uma chave de ponto final de teste. |

## <a name="az-spring-cloud-create"></a>az primavera-nuvem criar

Crie um novo aplicativo com uma implantação padrão na Nuvem de Primavera do Azure.

```azurecli
az spring-cloud create  --name -n
                        --resource-group -g
                        --location -l
                        --no-wait
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --nome -n | Nome para esta instância azure Spring Cloud. |
| --resource-group -g | Especifica o grupo de recursos para este aplicativo.  Configure o grupo padrão usando`az configure --defaults group=<name>` |

| Parâmetros opcionais | |
| --- | ---: |
| --location -l | Especifica a localização do servidor para este aplicativo.  Encontre locais válidos usando`az account list-locations` |
| --no-wait | Não faça para que as operações de longa duração se concluam.

### <a name="examples"></a>Exemplos

Crie uma nova Nuvem de Primavera do Azure em WestUS

```azurecli
az spring-cloud create -n MyService -g MyResourceGroup -l westus
```

## <a name="az-spring-cloud-delete"></a>az primavera-nuvem excluir

Exclua uma instância da Nuvem de Primavera do Azure.

```azurecli
az spring cloud --name -n
                --resource-group -g
                --no-wait
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --nome -n | Nome da instância azure Spring Cloud a ser excluído. |
| --resource-group -g | Nome do grupo de recursos ao qual pertence a Nuvem de Primavera do Azure. |

| Parâmetros opcionais | |
| --- | ---: |
| -não-esperar | Não espere que as operações de longa duração terminem. |

### <a name="example"></a>Exemplo

Exclua uma instância da Nuvem de Primavera do Azure chamada 'MyService' do 'MyResourceGroup'.

```azurecli
az spring-cloud delete -n MyService -g MyResourceGroup
```

## <a name="az-spring-cloud-list"></a>az primavera-nuvem lista

Liste todas as instâncias do Azure Spring Cloud associadas ao determinado grupo de recursos. Se nenhum grupo de recursos for especificado, liste os IDs de assinatura.

```azurecli
az spring-cloud list --resource-group -g
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --resource-group -g | Nome do grupo de recursos. |

## <a name="az-spring-cloud-show"></a>az primavera-nuvem show

Mostrar os detalhes para a instância especificada do Azure Spring Cloud.

```azurecli
az spring-cloud show --name -n
                     -- resource-group -g
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --nome -n | Nome da instância da Nuvem de Primavera do Azure. |
| --resource-group -g | Nome do Grupo de Recursos ao qual pertence a instância do Azure Spring Cloud.

## <a name="az-spring-cloud-app-create"></a>az spring-cloud app criar

Crie um novo aplicativo em uma Nuvem de Primavera do Azure.

```azurecli
az spring-cloud app create --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --enable-persistent-storage
                            --instance-count
                            --is-public
                            --memory
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --nome -n | Nome do aplicativo. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço -s | Nome da Nuvem da Primavera de Azure.  Você pode configurar o `az configure --defaults spring-cloud=<name>`serviço padrão usando . |

| Parâmetros opcionais | |
| --- | ---: |
| --cpu | Número de núcleos virtuais por instância.  Padrão: 1. |
| --habilitar-ativar-armazenamento persistente | $True.  Se for verdade, monta um disco de 50GB com o caminho padrão. |
| --instance-count | Número de instâncias.  Padrão: 1. |
| --é público | $True.  Se for verdade, atribui um domínio público. |
| --memory | Número de GB de memória por instância.  Padrão: 1. |

### <a name="examples"></a>Exemplos

Crie um aplicativo com a configuração padrão.

```azurecli
az spring-cloud app create -n MyApp -s MyService
```

Crie um aplicativo acessível ao público com 3 instâncias.  Cada instância tem 3 GB de memória e 2 núcleos de CPU.

```azurecli
az spring-cloud app create -n MyApp -s MyService --is-public true --cpu 2 --memory 3
```

## <a name="az-spring-cloud-app-delete"></a>az spring-cloud app excluir

Exclui um aplicativo na Nuvem de Primavera do Azure.

```azurecli
az spring cloud app delete  --name -n
                            --resource-group -g
                            --service -s
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --nome -n | Nome do aplicativo. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço -s | Nome da Nuvem da Primavera de Azure.  Você pode configurar o `az configure --defaults spring-cloud=<name>`serviço padrão usando . |

## <a name="az-spring-cloud-app-deploy"></a>az spring-cloud app implantar

Implante um aplicativo para o Azure Spring Cloud a partir do código-fonte ou de um binário pré-construído e atualize configurações relacionadas.

```azurecli
az spring cloud app deploy  --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --deployment -d
                            --env
                            --instance-count
                            --jar-path
                            --jvm-options
                            --memory
                            --no-wait
                            --runtime-version
                            --target-module
                            --version
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --nome -n | Nome do aplicativo. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço -s | Nome da Nuvem da Primavera de Azure.  Você pode configurar o `az configure --defaults spring-cloud=<name>`serviço padrão usando . |

| Parâmetros opcionais | |
| --- | ---: |
| --cpu | Número de núcleos de CPI virtuais por instância. |
| --implantação -d | Nome de uma implantação de aplicativo existente.  Padrão para a implantação da produção, se não especificado. |
| --env | Variáveis de ambiente separadas pelo espaço no formato 'key[=value]'. |
| --instance-count | Número de ocorrências. |
| --jar-path | Se fornecido, implante o frasco do caminho dado. Caso contrário, implante a pasta atual como um piche. |
| --jvm-opções | Uma seqüência contendo opções JVM.  Use '=' em vez de ' para evitar erros de análise de conchas. Por exemplo, `--jvm-options='-Xms1024m -Xmx2048m`. |
| --memory | Número de GB de memória por instância. |
| --no-wait | Não espere que as operações de longa duração terminem. |
| --versão em tempo de execução | Versão em tempo de execução do idioma usado no aplicativo.  Valores `Java_11`permitidos: , `Java_8`. |
| --módulo-alvo | Módulo infantil a ser implantado.  Necessário quando vários pacotes de jarro são construídos a partir do código fonte. |
| --versão | Versão de implantação.  Inalterado se não for definido. |

### <a name="examples"></a>Exemplos

Implantar código fonte em um aplicativo. Isso embalará o diretório atual, construirá um binário usando o Serviço de Compilação Pivô e, em seguida, implantará no aplicativo.

```azurecli
az spring-cloud app deploy -n MyApp -s MyService
```

Implante um frasco pré-construído em um aplicativo usando opções JVM e variáveis de ambiente.

```azurecli
az spring-cloud app deploy -n MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseG1GC -XX:+UseStringDeduplication" --env foo=bar
```

Implantar código fonte para uma implantação específica de um aplicativo.

```azurecli
az spring-cloud app deploy -n MyApp -s Myspring-cloud -d green-deployment
```

## <a name="az-spring-cloud-app-list"></a>az spring-cloud app list

Liste todos os aplicativos na instância do Azure Spring Cloud.

```azurecli
az spring-cloud app list --resource-group -g
                         --service -s
```

|Parâmetros obrigatórios | |
| --- | ---: |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço -s | Nome da Nuvem da Primavera de Azure.  Você pode configurar o `az configure --defaults spring-cloud=<name>`serviço padrão usando . |

## <a name="az-spring-cloud-app-restart"></a>az spring-cloud app reiniciar

Reiniciar instâncias do aplicativo.  Padrão para a implantação da produção.

```azurecli
az spring-cloud app restart --name -n
                            --resource-group -g
                            --service -s
                            --deployment -d
                            --no-wait
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --nome -n | Nome do aplicativo. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço -s | Nome da Nuvem da Primavera de Azure.  Você pode configurar o `az configure --defaults spring-cloud=<name>`serviço padrão usando . |

| Parâmetros opcionais | |
| --- | ---: |
| --implantação -d | Nome da implantação existente do aplicativo.  Padrão para a implantação da produção, se não especificado. |
| --no-wait | Não espere que as operações de longa duração terminem. |

## <a name="az-spring-cloud-app-scale"></a>az escala de aplicativo primavera-nuvem

Dimensione manualmente um aplicativo ou suas implantações.

```azurecli
az spring-cloud app scale --name -n
                          --resource-group -g
                          --service -s
                          --cpu
                          --deployment -d
                          --instance-count
                          --memory
                          --no-wait
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --nome -n | Nome do aplicativo. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço -s | Nome da Nuvem da Primavera de Azure.  Você pode configurar o `az configure --defaults spring-cloud=<name>`serviço padrão usando . |

| Parâmetros opcionais | |
| --- | ---: |
| --cpu | Número de núcleos de CPU virtuais por instância do aplicativo. |
| --implantação -d | Nome da implantação existente do aplicativo.  Padrão para a implantação da produção, se não especificado. |
| --instance-count | Número de instâncias deste aplicativo. |
| --memory | Número de GB de memória por instância do aplicativo. |
| --no-wait | Não espere que as operações de longa duração terminem. |

### <a name="examples"></a>Exemplos

Dimensione um aplicativo para 4 núcleos de CPU e 8 GB de memória por instância.

```azurecli
az spring-cloud app scale -n MyApp -s MyService --cpu 3 --memory 8
```

Dimensione uma implantação do aplicativo para 5 instâncias.

```azurecli
az spring-cloud app scale -n MyApp -s MyService -d green-deployment --instance-count 5
```

## <a name="az-spring-cloud-app-set-deployment"></a>az spring-cloud app set-deployment

Defina as opções de configuração para a implantação de produção do aplicativo.

```azurecli
az spring-cloud app set-deployment --deployment -d
                                   --name -n
                                   --resource-group -g
                                   --service -s
                                   --no-wait
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --implantação -d | Nome de uma implantação existente do aplicativo. |
| --nome -n | Nome do aplicativo. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço -s | Nome da Nuvem da Primavera de Azure.  Você pode configurar o `az configure --defaults spring-cloud=<name>`serviço padrão usando . |

| Parâmetros opcionais | |
| --- | ---: |
| --no-wait | Não espere que as operações de longa duração terminem. |

### <a name="examples"></a>Exemplos

Troque uma implantação de preparação do aplicativo para a produção.

```azurecli
az spring-cloud app set-deployment -d green-deployment -n MyApp -s MyService
```

## <a name="az-spring-cloud-app-show"></a>az spring-cloud app show

Mostre os detalhes de um aplicativo na Nuvem da Primavera do Azure.

```azurecli
az spring-cloud app show --name -n
                         --resource-group -g
                         --service -s
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --nome -n | Nome do aplicativo. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço -s | Nome da Nuvem da Primavera de Azure.  Você pode configurar o `az configure --defaults spring-cloud=<name>`serviço padrão usando . |

## <a name="az-spring-cloud-app-show-deploy-log"></a>az spring-cloud app show-deploy-log

Mostrar o registro de compilação da última implantação do código-fonte.  Padrão para o ambiente de produção.

```azurecli
az spring-cloud app show-deploy-log --name -n
                                    --resource-group -g
                                    --service -s
                                    --deployment -d
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --nome -n | Nome do aplicativo. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço -s | Nome da Nuvem da Primavera de Azure.  Você pode configurar o `az configure --defaults spring-cloud=<name>`serviço padrão usando . |

| Parâmetros opcionais | |
| --- | ---: |
| --implantação -d | Nome de uma implantação existente do aplicativo.  Padrão para o ambiente de produção. |

## <a name="az-spring-cloud-app-start"></a>az spring-cloud app começar

Inicia instâncias do aplicativo.  Padrão para o ambiente de produção.

```azurecli
az spring-cloud app start --name -n
                          --resource-group -g
                          --service -s
                          --deployment -d
                          --no-wait
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --nome -n | Nome do aplicativo. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço -s | Nome da Nuvem da Primavera de Azure.  Você pode configurar o `az configure --defaults spring-cloud=<name>`serviço padrão usando . |

| Parâmetros opcionais | |
| --- | ---: |
| --implantação -d | Nome de uma implantação existente do aplicativo.  Padrão para o ambiente de produção. |
| --no-wait | Não espere que as operações de longa duração terminem. |

## <a name="az-spring-cloud-app-stop"></a>az spring-cloud app stop

Pare as instâncias do aplicativo.  Padrão para o ambiente de produção.

```azurecli
az spring-cloud app stop --name -n
                         --resource-group -g
                         --service -s
                         --deployment -d
                         --no-wait
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --nome -n | Nome do aplicativo. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço -s | Nome da Nuvem da Primavera de Azure.  Você pode configurar o `az configure --defaults spring-cloud=<name>`serviço padrão usando . |

| Parâmetros opcionais | |
| --- | ---: |
| --implantação -d | Nome de uma implantação existente do aplicativo.  Padrão para o ambiente de produção. |
| --no-wait | Não espere que as operações de longa duração terminem. |

## <a name="az-spring-cloud-app-update"></a>az primavera-nuvem atualização de aplicativo

Atualize a configuração armazenada de um aplicativo.

```azurecli
az spring-cloud app update --name -n
                           --resource-group -g
                           --service -s
                           --deployment -d
                           --enable-persistent-storage
                           --env
                           --is-public
                           --jvm-options
                           --no-wait
                           --runtime-version
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --nome -n | Nome do aplicativo. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço -s | Nome da Nuvem da Primavera de Azure.  Você pode configurar o `az configure --defaults spring-cloud=<name>`serviço padrão usando . |

| Parâmetros opcionais | |
| --- | ---: |
| --implantação -d | Nome de uma implantação existente do aplicativo.  Padrão para o ambiente de produção. |
| --habilitar-ativar-armazenamento persistente | Booliano.  Se for verdade, monte um disco de 50GB com o caminho padrão. |
| --env | Variáveis de ambiente separadas pelo espaço no formato 'key[=value]'. |
| --é público | Booliano.  Se for verdade, atribua um domínio público ao aplicativo. |
| --jvm-opções | Uma seqüência contendo opções JVM.  Use '=' em vez de ' para evitar erros de análise de conchas. Por exemplo, `--jvm-options='-Xms1024m -Xmx2048m`. |
| --no-wait | Não espere que as operações de longa duração terminem. |
| --versão em tempo de execução | Versão em tempo de execução do idioma usado no aplicativo.  Valores `Java_11`permitidos: , `Java_8`. |

### <a name="example"></a>Exemplo

Adicione uma variável de ambiente para o aplicativo.

```azurecli
az spring-cloud app update --env foo=bar
```

## <a name="az-spring-cloud-app-binding-list"></a>az spring-cloud lista de vinculação de aplicativos

Liste todas as vinculações de serviço em um aplicativo.

```azurecli
az spring-cloud app binding list --app
                                 --resource-group -g
                                 --service -s
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --app | Nome do aplicativo. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço -s | Nome da Nuvem da Primavera de Azure.  Você pode configurar o `az configure --defaults spring-cloud=<name>`serviço padrão usando . |

## <a name="az-spring-cloud-app-binding-remove"></a>az spring-cloud app binding remove

Remova uma vinculação de serviço do aplicativo.

```azurecli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --app | Nome do aplicativo. |
| --nome | Nome da vinculação de serviço a ser removido. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço -s | Nome da Nuvem da Primavera de Azure.  Você pode configurar o `az configure --defaults spring-cloud=<name>`serviço padrão usando . |

## <a name="az-spring-cloud-app-binding-show"></a>az spring-cloud app binding show

Mostre os detalhes de uma ligação de serviço.

```azurecli
az spring-cloud app binding show --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --app | Nome do aplicativo. |
| --nome | Nome da vinculação do serviço. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço -s | Nome da Nuvem da Primavera de Azure.  Você pode configurar o `az configure --defaults spring-cloud=<name>`serviço padrão usando . |

## <a name="az-spring-cloud-app-binding-cosmos-add"></a>az spring-cloud app bindingcosmos adicionar

Vincule um Azure Cosmos DB com o aplicativo.

```azurecli
az spring-cloud app binding cosmos add --api-type
                                       --app
                                       --name -n
                                       --resource-group -g
                                       --resource-id
                                       --service -s
                                       --collection-name
                                       --database-name
                                       --key-space
```

```azurecli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --tipo api | Especifique o tipo de API usando um dos seguintes valores: cassandra, gremlin, mongo, sql, tabela. |
| --app | Nome do aplicativo. |
| --nome | Nome da vinculação do serviço. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço -s | Nome da Nuvem da Primavera de Azure.  Você pode configurar o `az configure --defaults spring-cloud=<name>`serviço padrão usando . |

|Parâmetros opcionais | |
| --- | ---: |
| --nome de coleção | Nome da coleção.  Necessário ao usar Gremlin. |
| --nome de banco de dados | Nome do banco de dados.  Necessário ao usar Mongo, SQL e Gremlin. |
| --espaço-chave | Espaço-chave cassandra.  Exigido ao usar Cassandra. |

## <a name="az-spring-cloud-app-binding-cosmos-update"></a>az spring-cloud app bindingcosmos atualização

```azurecli
az spring-cloud app binding cosmos update --app
                                          --name -n
                                          --resource-group -g
                                          --service -s
                                          --collection-name
                                          --database-name
                                          --key-space
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --app | Nome do aplicativo. |
| --nome | Nome da vinculação do serviço. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço -s | Nome da Nuvem da Primavera de Azure.  Você pode configurar o `az configure --defaults spring-cloud=<name>`serviço padrão usando . |

|Parâmetros opcionais | |
| --- | ---: |
| --nome de coleção | Nome da coleção.  Necessário ao usar Gremlin. |
| --nome de banco de dados | Nome do banco de dados.  Necessário ao usar Mongo, SQL e Gremlin. |
| --espaço-chave | Espaço-chave cassandra.  Exigido ao usar Cassandra. |

## <a name="az-spring-cloud-app-binding-mysql-add"></a>az spring-cloud app binding mysql add

```azurecli
az spring-cloud app binding mysql add --app
                                      --database-name
                                      --key
                                      --name -n
                                      --resource-group -g
                                      --service -s
                                      --username
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --app | Nome do aplicativo. |
| --nome de banco de dados | Nome do banco de dados. |
| --chave | API chave do serviço. |
| --nome | Nome da vinculação do serviço. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --id de recursos | ID de recurso do Azure do serviço para vincular. |
| --serviço -s | Nome da Nuvem da Primavera de Azure.  Você pode configurar o `az configure --defaults spring-cloud=<name>`serviço padrão usando . |
| --username | Nome de usuário para acesso ao banco de dados. |

## <a name="az-spring-cloud-app-binding-mysql-update"></a>az spring-cloud app binding mysql update

Atualize a conexão de vinculação do serviço do aplicativo a um banco de dados Azure para MySQL.

```azurecli
az spring-cloud app binding mysql update --add
                                         --name -n
                                         --resource-group -g
                                         --service -s
                                         --database-name
                                         --key
                                         --username
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --app | Nome do aplicativo. |
| --nome | Nome da vinculação do serviço. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço -s | Nome da Nuvem da Primavera de Azure.  Você pode configurar o `az configure --defaults spring-cloud=<name>`serviço padrão usando . |

| Parâmetros opcionais | |
| --- | ---: |
| --nome de banco de dados | Nome do banco de dados. |
| --chave | API chave do serviço. |
| --username | Nome de usuário para acesso ao banco de dados. |

## <a name="az-spring-cloud-app-binding-redis-add"></a>az spring-cloud app binding redis add

Vincule um cache azure para Redis com o aplicativo.

```azurecli
az spring-cloud app binding redis add --app
                                      --name -n
                                      --resource-group -g
                                      --resource-id
                                      --service -s
                                      --disable-ssl
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --app | Nome do aplicativo. |
| --nome | Nome da vinculação do serviço. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --id de recursos | ID de recurso do Azure do serviço com o que você deseja vincular. |
| --serviço -s | Nome da Nuvem da Primavera de Azure.  Você pode configurar o `az configure --defaults spring-cloud=<name>`serviço padrão usando . |

| Parâmetros opcionais | |
| --- | ---: |
| --desativar-ssl | Desativar o TLS. |

## <a name="az-spring-cloud-app-binding-redis-update"></a>az spring-cloud app binding redis atualização redis

Atualize um serviço de vinculação para cache do Azure para Redis.

| Parâmetros obrigatórios | |
| --- | ---: |
| --app | Nome do aplicativo. |
| --nome | Nome da vinculação do serviço. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço -s | Nome da Nuvem da Primavera de Azure.  Você pode configurar o `az configure --defaults spring-cloud=<name>`serviço padrão usando . |

| Parâmetros opcionais | |
| --- | ---: |
| --desativar-ssl | Desativar o TLS. |

## <a name="az-spring-cloud-app-deployment-create"></a>az spring-cloud implantação de aplicativo criar

Crie uma implantação de preparação para o aplicativo.

Para implantar código ou atualizar configurações para `az spring-cloud app deploy --deployment <staging-deployment>` uma implantação existente, use ou 'az spring-cloud app update --deployment <staging deployment>.

| Parâmetros obrigatórios | |
| --- | ---: |
| --app | Nome do aplicativo. |
| --nome | Nome da vinculação do serviço. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço -s | Nome da Nuvem da Primavera de Azure.  Você pode configurar o `az configure --defaults spring-cloud=<name>`serviço padrão usando . |

| Parâmetros opcionais | |
| --- | ---: |
| --cpu | Número de núcleos de CPU virtuais por instância.  Padrão: 1 |
| --env | Variáveis de ambiente separadas pelo espaço no formato 'key[=value]'. |
| --instance-count | Número de ocorrências. Padrão: 1. |
| --jar-path | Se for fornecido, implante o frasco.  Caso contrário, implante a pasta atual como um piche. |
| --jvm-opções | Uma seqüência contendo opções JVM.  Use '=' em vez de ' para evitar erros de análise de conchas. Por exemplo, `--jvm-options='-Xms1024m -Xmx2048m`. |
| --memory | Número de GB de memória por instância. |
| --no-wait | Não espere que as operações de longa duração terminem. |
| --versão em tempo de execução | Versão em tempo de execução do idioma usado no aplicativo.  Valores `Java_11`permitidos: , `Java_8`. |
| --skip-clone-configurações | Crie uma implantação de preparação clonando as configurações atuais de implantação da produção. |
| --módulo-alvo | Módulo infantil a ser implantado.  Necessário quando vários pacotes de jarro são construídos a partir do código fonte. |
| --versão | Versão de implantação.  Inalterado se não for definido. |

### <a name="examples"></a>Exemplos

Implantar código fonte para uma nova implantação do aplicativo.  Isso embalará o diretório atual, construirá um binário usando o Sistema de Compilação Pivô e, em seguida, implantará.

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService
```

Implante um jarro pré-construído em um aplicativo com opções JVM e variáveis de ambiente.

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseStringDeDuplication" --env foo=bar
```

## <a name="az-spring-cloud-app-deployment-delete"></a>az spring-cloud app exclusão

Exclua uma implantação do aplicativo.

```azurecli
az spring-cloud app deployment delete --app
                                      --name -n
                                      --resource-group -g
                                      --service -s
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --app | Nome do aplicativo. |
| --nome | Nome da implantação. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço -s | Nome da Nuvem da Primavera de Azure.  Você pode configurar o `az configure --defaults spring-cloud=<name>`serviço padrão usando . |

## <a name="az-spring-cloud-app-deployment-list"></a>az spring-cloud lista de implantação de aplicativos

Liste todas as implantações em um aplicativo.

```azurecli
az spring-cloud app deployment list --app
                                    --resource-group -g
                                    --service -s
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --app | Nome do aplicativo. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço -s | Nome da Nuvem da Primavera de Azure.  Você pode configurar o `az configure --defaults spring-cloud=<name>`serviço padrão usando . |

## <a name="az-spring-cloud-app-deployment-show"></a>az spring-cloud app implantação show

Mostre detalhes de uma implantação.

```azurecli
az spring-cloud app deployment show --app
                                    --name -n
                                    --resource-group -g
                                    --service -s
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --app | Nome do aplicativo. |
| --nome | Nome da implantação. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --serviço -s | Nome da Nuvem da Primavera de Azure.  Você pode configurar o `az configure --defaults spring-cloud=<name>`serviço padrão usando . |

## <a name="az-spring-cloud-config-server-clear"></a>az mola-nuvem config-servidor claro

Apagar todas as configurações no Servidor Config.

```azurecli
az spring-cloud config-server clear --name
                                        --resource-group -g
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --nome | Nome da Nuvem da Primavera de Azure. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-config-server-set"></a>az mola-nuvem configuração-servidor conjunto

Defina as configurações do Servidor config usando um arquivo YAML.

```azurecli
az spring-cloud config-server set --config-file
                                  --name -n
                                  --resource-group -g
                                  --no-wait
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --config-file | Caminho do arquivo para um manifesto YAML para a configuração do Servidor de configuração. |
| --nome | Nome da Nuvem da Primavera de Azure. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |

| Parâmetros opcionais | |
| --- | ---: |
| --no-wait | Não faça para que as operações de longa duração se concluam.

## <a name="az-spring-cloud-config-server-show"></a>az spring-cloud config-server show

Mostrar as configurações do Servidor de configuração.

```azurecli
az spring-cloud config-server show --name -n
                                   --resource-group -g
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --nome | Nome da Nuvem da Primavera de Azure. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-config-server-git-set"></a>az mola-nuvem config-servidor git conjunto

Defina as propriedades git para o Servidor config.  Isso substituirá todas as propriedades git existentes.

```azurecli
az spring-cloud config-server git set --name -n
                                      --resource-group g
                                      --uri
                                      --defer
                                      --host-key
                                      --host-key-algorithm
                                      --label
                                      --password
                                      --private-key
                                      --search-paths
                                      --strict-host-key-checking
                                      --username
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --nome | Nome da Nuvem da Primavera de Azure. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --uri | URI da configuração adicionada. |

| Parâmetros opcionais | |
| --- | ---: |
| --adiar | Armazene temporariamente o objeto no cache local em vez de enviar para o Azure.  Use `az cache` para ver/limpar. |
| --host-key | Tecla host para a configuração adicionada. |
| --host-key-algorithm | Algoritmo de chave de host para a configuração adicionada. |
| --rótulo | Etiqueta da configuração adicionada. |
| --password | Senha da configuração adicionada. |
| --chave privada | Chave privada da configuração adicionada. |
| --caminhos de busca | Procurar caminhos da configuração adicionada.  Use delimitadores de comma para vários caminhos. |
| --rigorosa verificação de chaves de host | Permite uma verificação rigorosa da chave de host da configuração adicionada. |
| --username | Nome de usuário da configuração adicionada. |

## <a name="az-spring-cloud-config-server-git-repo-add"></a>az mola-nuvem config-server git repo adicionar

```azurecli
az spring-cloud config-server git repo add --name -n
                                           --repo-name
                                           --resource-group -g
                                           --uri
                                           --defer
                                           --host-key
                                           --host-key-algorithm
                                           --label
                                           --password
                                           --pattern
                                           --private-key
                                           --search-paths
                                           --strict-host-key-checking
                                           --username
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --nome | Nome da Nuvem da Primavera de Azure. |
| --repo-nome | URI do repo. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --uri | URI da configuração adicionada. |

| Parâmetros opcionais | |
| --- | ---: |
| --adiar | Armazene temporariamente o objeto no cache local em vez de enviar para o Azure.  Use `az cache` para ver/limpar. |
| --host-key | Tecla host para a configuração adicionada. |
| --host-key-algorithm | Algoritmo de chave de host para a configuração adicionada. |
| --rótulo | Etiqueta da configuração adicionada. |
| --password | Senha da configuração adicionada. |
| --padrão | Padrão para o repo.  Use delimitadores de comma para vários caminhos.|
| --chave privada | Chave privada da configuração adicionada. |
| --caminhos de busca | Procurar caminhos da configuração adicionada.  Use delimitadores de comma para vários caminhos. |
| --rigorosa verificação de chaves de host | Permite uma verificação rigorosa da chave de host da configuração adicionada. |
| --username | Nome de usuário da configuração adicionada. |

## <a name="az-spring-cloud-config-server-git-repo-list"></a>az spring-cloud config-server git repo list

Liste todos os repos os git definidos no Servidor Config

```azurecli
az spring-cloud config-server git repo list --name -n
                                       --resource-group -g
                                       --defer
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --nome | Nome da Nuvem da Primavera de Azure. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |

| Parâmetros opcionais | |
| --- | ---: |
| --adiar | Armazene temporariamente o objeto no cache local em vez de enviar para o Azure.  Use `az cache` para ver/limpar. |

## <a name="az-spring-cloud-config-server-git-repo-remove"></a>az mola-nuvem config-server git repo remover

Remova uma configuração de recompra de git existente do Servidor Config.

```azurecli
az spring-cloud config-server git repo remove --name -n
                                         --repo-name
                                         --resource-group -g
                                         --defer
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --nome | Nome da Nuvem da Primavera de Azure. |
| --repo-nome | URI do repo. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |

| Parâmetros opcionais | |
| --- | ---: |
| --adiar | Armazene temporariamente o objeto no cache local em vez de enviar para o Azure.  Use `az cache` para ver/limpar. |

## <a name="az-spring-cloud-test-endpoint-disable"></a>az spring-cloud test-endpoint desativar

Desativar o ponto final do teste da Nuvem de Primavera do Azure

```azurecli
az spring-cloud test-endpoint disable --name -n
                                      --resource-group -g
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --nome | Nome da Nuvem da Primavera de Azure. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-test-endpoint-enable"></a>az spring-cloud test-endpoint ativar

Habilite o ponto final de teste para a Nuvem de Primavera do Azure. 

```azurecli
az spring-cloud test-endpoint enable --name -n
                                     --resource-group -g
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --nome | Nome da Nuvem da Primavera de Azure. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |

## <a name="az-spring-cloud-test-endpoint-list"></a>az primavera-nuvem lista de ponto final de teste 

Liste as teclas de ponto final de teste disponíveis para a Nuvem de Primavera do Azure.

```azurecli
az spring-cloud test-endpoint list --name -n
                                   --resource-group -g
                                   --app
                                   --deployment -d
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --nome | Nome da Nuvem da Primavera de Azure. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |

| Parâmetros opcionais | |
| --- | ---: |
| --app | Nome do aplicativo. |
| --implantação -d | Nome de uma implantação existente do aplicativo.  Padrão de produção se não especificado. |

## <a name="az-spring-cloud-test-endpoint-renew-key"></a>az spring-cloud test-endpoint renovar-key

Regenerar uma chave de ponto de teste para a Nuvem de Primavera do Azure.

```azurecli
az spring-cloud test-endpoint renew-key --name -n
                                        --resource-group -g
                                        --type
```

| Parâmetros obrigatórios | |
| --- | ---: |
| --nome | Nome da Nuvem da Primavera de Azure. |
| --resource-group -g | Nome do grupo de recursos.  Você pode configurar o grupo padrão usando `az configure --defaults group=<name>`. |
| --tipo | Tipo de tecla de ponto final de teste.  Valores permitidos: Primário, Secundário. |
