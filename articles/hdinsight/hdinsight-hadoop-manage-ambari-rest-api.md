---
title: Monitorar e gerenciar o Hadoop com a API REST do Ambari – Azure HDInsight
description: Aprenda a usar o Ambari para monitorar e gerenciar clusters Hadoop no Azure HDInsight. Neste documento, você aprenderá como usar a API Ambari REST incluída nos clusters HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: 317d12f6d5dee92d998266d4e9b6d52e6ef9c7a5
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381393"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-rest-api"></a>Gerenciar clusters HDInsight usando a API REST do Apache Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Aprenda a usar a API REST do Apache Ambari para gerenciar e monitorar clusters Apache Hadoop no Azure HDInsight.

## <a name="what-is-apache-ambari"></a>O que é Apache Ambari

[O Apache Ambari](https://ambari.apache.org) simplifica o gerenciamento e o monitoramento de clusters Hadoop, fornecendo uma interface web fácil de usar apoiada por suas [APIs REST](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  Ambari é fornecido por padrão com os clusters HDInsight baseados em Linux.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster Hadoop no HDInsight. Veja [Get Started com hdinsight no Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* Bata no Ubuntu no Windows 10.  Os exemplos deste artigo usam o shell do Bash no Windows 10. Confira o [Guia de instalação do subsistema do Windows para Linux para o Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) para conhecer as etapas de instalação.  Outros [shells do Unix](https://www.gnu.org/software/bash/) também funcionarão.  Os exemplos, com algumas pequenas modificações, podem funcionar em um prompt do Windows Command.  Ou você pode usar o Windows PowerShell.

* jq, um processador JSON de linha de comando.  Veja [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

* Windows PowerShell.  Ou você pode usar [Bash.](https://www.gnu.org/software/bash/)

## <a name="base-uniform-resource-identifier-for-ambari-rest-api"></a>Identificador de recursos uniforme base para Ambari Rest API

 O uri (Uniform Resource Identifier, identificação de recursos uniformes) base para a API Ambari REST no HDInsight é `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`, onde `CLUSTERNAME` está o nome do seu cluster.  Os nomes de cluster em URIs são **sensíveis a maiúsculas e minúsculas**.  Embora o nome do cluster na parte de nome de domínio`CLUSTERNAME.azurehdinsight.net`totalmente qualificado (FQDN) do URI ( ) seja insensível a casos, outras ocorrências no URI são sensíveis a maiúsculas e minúsculas.

## <a name="authentication"></a>Autenticação

Conectar-se ao Ambari no HDInsight requer HTTPS. Use o nome da conta do administrador (o padrão é **admin**) e a senha fornecidos durante a criação do cluster.

Para clusters do Pacote `admin`de Segurança Corporativa, em `username@domain.onmicrosoft.com`vez de, use um nome de usuário totalmente qualificado como .

## <a name="examples"></a>Exemplos

### <a name="setup-preserve-credentials"></a>Configuração (Preservar credenciais)

Preserve suas credenciais para evitar reinseri-las para cada exemplo.  O nome do cluster será preservado em uma etapa separada.

**A. Bash**  
Edite o script `PASSWORD` abaixo substituindo por sua senha real.  Então digite o comando.

```bash
export password='PASSWORD'
```  

**B. PowerShell**  

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Identifique o nome do cluster corretamente cased

O invólucro real do nome do cluster pode ser diferente do que você espera.  Os passos aqui mostrarão o invólucro real e, em seguida, armazená-lo em uma variável para todos os exemplos posteriores.

Edite os scripts `CLUSTERNAME` abaixo para substituir com o nome do cluster. Então digite o comando. (O nome do cluster para fqdn não é sensível a maiúsculas e minúsculas.)

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

### <a name="parsing-json-data"></a>Analisar dados JSON

O exemplo a seguir usa [jq](https://stedolan.github.io/jq/) ou [ConvertFrom-Json](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/convertfrom-json) para analisar `health_report` o documento de resposta JSON e exibir apenas as informações dos resultados.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" \
| jq '.Clusters.health_report'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```

### <a name="get-the-fqdn-of-cluster-nodes"></a>Obter o FQDN de nós do cluster

Você pode precisar saber o nome de domínio totalmente qualificado (FQDN) de um nó de cluster. Você pode recuperar facilmente o FQDN para vários nós no cluster usando os seguintes exemplos:

**Todos os nós**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq -r '.items[].Hosts.host_name'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```

**Nódulos de cabeça**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" \
| jq -r '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Nó de trabalho**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" \
| jq -r '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Nós do Zookeeper**

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
| jq -r ".host_components[].HostRoles.host_name"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

### <a name="get-the-internal-ip-address-of-cluster-nodes"></a>Obter o endereço IP interno de nós de cluster

Os endereços IP retornados pelos exemplos nesta seção não estão diretamente acessíveis pela internet. Eles só estão acessíveis dentro da Rede Virtual Azure que contém o cluster HDInsight.

Para obter mais informações sobre como trabalhar com HDInsight e redes virtuais, consulte [Planejar uma rede virtual para HDInsight](hdinsight-plan-virtual-network-deployment.md).

Para localizar o endereço IP, você deve saber o FQDN (nome de domínio totalmente qualificado) interno dos nós de cluster. Uma vez que o FQDN, em seguida, você pode obter o endereço IP do host. Os exemplos a seguir consultam ambari para o FQDN de todos os nós host. Em seguida, consulta ambari para o endereço IP de cada host.

```bash
for HOSTNAME in $(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```  

```powershell
$uri = "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" 
$resp = Invoke-WebRequest -Uri $uri -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
foreach($item in $respObj.items) {
    $hostName = [string]$item.Hosts.host_name
    $hostInfoResp = Invoke-WebRequest -Uri "$uri/$hostName" `
        -Credential $creds -UseBasicParsing
    $hostInfoObj = ConvertFrom-Json $hostInfoResp
    $hostIp = $hostInfoObj.Hosts.ip
    "$hostName <--> $hostIp"
}
```

### <a name="get-the-default-storage"></a>Obter o armazenamento padrão

Os clusters HDInsight devem usar uma conta de armazenamento Azure ou armazenamento de data lake como o armazenamento padrão. Você pode usar o Ambari para recuperar essas informações após a criação do cluster. Por exemplo, se você quiser ler/gravar dados no contêiner fora do HDInsight.

Os exemplos a seguir recuperar a configuração de armazenamento padrão do cluster:

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq -r '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]  
> Estes exemplos retornam a primeira configuração aplicada ao servidor (`service_config_version=1`) que contém essas informações. Se você recuperar um valor que foi modificado após a criação do cluster, talvez seja necessário listar as versões de configuração e recuperar a mais recente.

O valor de retorno é semelhante a um dos exemplos a seguir:

* `wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net`-Este valor indica que o cluster está usando uma conta de armazenamento do Azure para armazenamento padrão. O valor `ACCOUNTNAME` é o nome da conta de armazenamento. O `CONTAINER` parte é o nome do contêiner de blob na conta de armazenamento. O contêiner é a raiz de armazenamento compatível com HDFS para o cluster.

* `abfs://CONTAINER@ACCOUNTNAME.dfs.core.windows.net` – Este valor indica que o cluster está usando um Azure Data Lake Storage Gen2 para armazenamento padrão. O `ACCOUNTNAME` e `CONTAINER` valores têm os mesmos significados que para o Armazenamento do Microsoft Azure mencionados anteriormente.

* `adl://home`: este valor indica que o cluster está usando um Azure Data Lake Storage Gen1 para armazenamento padrão.

    Para localizar o nome da conta do Data Lake Storage, use os exemplos a seguir:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq -r '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    O valor de retorno é semelhante ao `ACCOUNTNAME.azuredatalakestore.net`, onde `ACCOUNTNAME` é o nome da conta do Data Lake Storage.

    Para localizar o diretório no Data Lake Storage que contém o armazenamento do cluster, use os exemplos a seguir:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq -r '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```  

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    O valor de retorno é semelhante ao `/clusters/CLUSTERNAME/`. Esse valor é um caminho dentro da conta do Data Lake Storage. Esse caminho é a raiz do sistema de arquivos compatível com HDFS para o cluster.  

> [!NOTE]  
> O [cmdlet Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) fornecido pelo [Azure PowerShell](/powershell/azure/overview) também retorna as informações de armazenamento para o cluster.

### <a name="get-all-configurations"></a>Obtenha todas as configurações

Obtenha as configurações que estão disponíveis para o seu cluster.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_configs"
```

```powershell
$respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
    -Credential $creds -UseBasicParsing
$respObj.Content
```

Este exemplo retorna um documento JSON contendo a configuração atual para componentes instalados. Veja o valor *da etiqueta.* O exemplo a seguir é um trecho dos dados retornados de um tipo de cluster Spark.

```json
"jupyter-site" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-client-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
```

### <a name="get-configuration-for-specific-component"></a>Obter configuração para componente específico

Obtenha a configuração para o componente que você está interessado. No exemplo a seguir, substitua `INITIAL` pelo valor retornado da solicitação anterior.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
    -Credential $creds -UseBasicParsing
$resp.Content
```

Este exemplo retorna um documento JSON que contém a configuração atual do componente `livy2-conf`.

### <a name="update-configuration"></a>Atualizar configuração

1. Crie `newconfig.json`.  
   Modificar e, em seguida, digitar os comandos abaixo:

   * Substitua pelo `livy2-conf` novo componente.
   * Substitua pelo `INITIAL` valor `tag` real recuperado para [obter todas as configurações](#get-all-configurations).

     **A. Bash**

     ```bash
     curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" \
     | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     **B. PowerShell**  
     O script PowerShell usa [jq](https://stedolan.github.io/jq/).  Edite `C:\HD\jq\jq-win64` abaixo para refletir seu caminho real e versão do [jq](https://stedolan.github.io/jq/).

     ```powershell
     $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
     $now = Get-Date
     $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
     $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
       -Credential $creds -UseBasicParsing
     $resp.Content | C:\HD\jq\jq-win64 --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     O jq é usado para transformar os dados recuperados do HDInsight em um novo modelo de configuração. Especificamente, esses exemplos fazem as seguintes ações:

   * Cria um valor exclusivo que contém a cadeia de caracteres "version" e a data, que é armazenada em `newtag`.

   * Cria um documento raiz para a nova configuração.

   * Obtém o conteúdo da matriz `.items[]` e o adiciona sob o elemento **desired_config**.

   * Exclui os elementos `href`, `version` e `Config`, pois eles não são necessários para enviar uma nova configuração.

   * Adiciona um elemento `tag` com um valor de `version#################`. A parte numérica tem base na data atual. Cada configuração deve ter uma marca exclusiva.

     Por fim, os dados são salvos no documento `newconfig.json`. A estrutura do documento deve ser semelhante a este exemplo:

     ```json
     {
       "Clusters": {
         "desired_config": {
           "tag": "version1552064778014",
           "type": "livy2-conf",
           "properties": {
             "livy.environment": "production",
             "livy.impersonation.enabled": "true",
             "livy.repl.enableHiveContext": "true",
             "livy.server.csrf_protection.enabled": "true",
               ....
           },
         },
       }
     }
     ```

2. Edite `newconfig.json`.  
   Abra o documento `newconfig.json` e modifique/adicione valores no objeto `properties`. O exemplo a seguir altera o valor de `"livy.server.csrf_protection.enabled"` desde `"true"` até `"false"`.

        "livy.server.csrf_protection.enabled": "false",

    Salve o arquivo assim que terminar de fazer modificações.

3. Enviar `newconfig.json`.  
   Use os seguintes comandos para enviar a configuração atualizada ao Ambari.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" -X PUT -d @newconfig.json "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName"
    ```

    ```powershell
    $newConfig = Get-Content .\newconfig.json
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body $newConfig
    $resp.Content
    ```  

    Esses comandos enviam o conteúdo do arquivo **newconfig.json** para o cluster como a nova configuração. A solicitação retorna um documento JSON. O elemento **versionTag** nesse documento deverá corresponder à versão enviada e o objeto **configs** conterá as alterações de configuração solicitadas.

### <a name="restart-a-service-component"></a>Reiniciar um componente de serviço

Neste ponto, a ui da web Ambari indica que o serviço Spark precisa ser reiniciado antes que a nova configuração possa entrar em vigor. Use as etapas a seguir para reiniciar o serviço.

1. Use o seguinte para habilitar o modo de manutenção do serviço Spark2:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}'
    ```

2. Verificar modo de manutenção  

    Estes comandos enviam um documento JSON para o servidor que ativa o modo de manutenção. Você pode verificar se o serviço está em modo de manutenção usando a seguinte solicitação:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" \
    | jq .ServiceInfo.maintenance_state
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.ServiceInfo.maintenance_state
    ```

    O valor de retorno é `ON`.

3. Em seguida, use o seguinte para desativar o serviço Spark2:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
    $resp.Content
    ```

    A resposta é semelhante ao seguinte exemplo:

    ```json
    {
        "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
        "Requests" : {
            "id" : 29,
            "status" : "Accepted"
        }
    }
    ```

    > [!IMPORTANT]  
    > O valor `href` retornado por esse URI usa o endereço IP interno do nó de cluster. Para usá-lo de fora `10.0.0.18:8080` do cluster, substitua a porção pelo FQDN do cluster.  

4. Verificar solicitação.  
    Edite o comando `29` abaixo substituindo-o pelo valor real para `id` devolvido da etapa anterior.  Os comandos a seguir recuperam o status da solicitação:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" \
    | jq .Requests.request_status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.Requests.request_status
    ```

    Uma resposta de `COMPLETED` indica que a solicitação foi concluída.

5. Uma vez que a solicitação anterior seja concluída, use o seguinte para iniciar o serviço Spark2.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    $resp.Content
    ```

    O serviço agora está usando a nova configuração.

6. Por fim, use o seguinte para desativar o modo de manutenção:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}'
    ```

## <a name="next-steps"></a>Próximas etapas

Para obter uma referência completa da API REST, consulte [Referência de API do Apache Ambari, V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  Veja [também, Autorize usuários para visualizações apache ambari](./hdinsight-authorize-users-to-ambari.md)
