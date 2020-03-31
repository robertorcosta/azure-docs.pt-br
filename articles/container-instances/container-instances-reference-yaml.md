---
title: Referência YAML para grupo de contêineres
description: Referência para o arquivo YAML suportado pelo Azure Container Instances para configurar um grupo de contêineres
ms.topic: article
ms.date: 08/12/2019
ms.openlocfilehash: 8497330a327201c4c64e9f7ae57e6fc4225b52de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896562"
---
# <a name="yaml-reference-azure-container-instances"></a>Referência YAML: Instâncias do contêiner Azure

Este artigo abrange a sintaxe e as propriedades do arquivo YAML suportado pelo Azure Container Instances para configurar um [grupo de contêineres](container-instances-container-groups.md). Use um arquivo YAML para inserir a configuração do grupo no comando [az container create][az-container-create] no Azure CLI. 

Um arquivo YAML é uma maneira conveniente de configurar um grupo de contêineres para implantações reprodutíveis. É uma alternativa concisa para usar um [modelo de Gerenciador de recursos](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups) ou os SDKs a6Ks de instâncias de contêiner do Azure para criar ou atualizar um grupo de contêineres.

> [!NOTE]
> Esta referência se aplica a arquivos YAML para a `2018-10-01`versão aPI Rest API do Azure Container Instances .

## <a name="schema"></a>Esquema 

O esquema para o arquivo YAML segue, incluindo comentários para destacar propriedades-chave. Para obter uma descrição das propriedades neste esquema, consulte a seção Valores da [propriedade.](#property-values)

```yml
name: string  # Name of the container group
apiVersion: '2018-10-01'
location: string
tags: {}
identity: 
  type: string
  userAssignedIdentities: {}
properties: # Properties of container group
  containers: # Array of container instances in the group
  - name: string # Name of an instance
    properties: # Properties of an instance
      image: string # Container image used to create the instance
      command:
      - string
      ports: # External-facing ports exposed on the instance, must also be set in group ipAddress property 
      - protocol: string
        port: integer
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      resources: # Resource requirements of the instance
        requests:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
        limits:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
      volumeMounts: # Array of volume mounts for the instance
      - name: string
        mountPath: string
        readOnly: boolean
      livenessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
      readinessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
  imageRegistryCredentials: # Credentials to pull a private image
  - server: string
    username: string
    password: string
  restartPolicy: string
  ipAddress: # IP address configuration of container group
    ports:
    - protocol: string
      port: integer
    type: string
    ip: string
    dnsNameLabel: string
  osType: string
  volumes: # Array of volumes available to the instances
  - name: string
    azureFile:
      shareName: string
      readOnly: boolean
      storageAccountName: string
      storageAccountKey: string
    emptyDir: {}
    secret: {}
    gitRepo:
      directory: string
      repository: string
      revision: string
  diagnostics:
    logAnalytics:
      workspaceId: string
      workspaceKey: string
      logType: string
      metadata: {}
  networkProfile: # Virtual network profile for container group
    id: string
  dnsConfig: # DNS configuration for container group
    nameServers:
    - string
    searchDomains: string
    options: string
```

## <a name="property-values"></a>Valores de propriedade

As tabelas a seguir descrevem os valores necessários para definir no esquema.

<a id="Microsoft.ContainerInstance/containerGroups" />

### <a name="microsoftcontainerinstancecontainergroups-object"></a>Objeto Microsoft.ContainerInstance/containerGroups

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  name | string | Sim | O nome do grupo de contêineres. |
|  apiVersion | enum | Sim | 01-10-2018 |
|  local | string | Não | O local do recurso. |
|  marcas | objeto | Não | As etiquetas de recursos. |
|  identidade | objeto | Não | A identidade do grupo de contêineres, se configurada. - [Objeto ContainerGroupIdentity](#ContainerGroupIdentity) |
|  properties | objeto | Sim | [Objeto ContainerGroupProperties](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>Objeto ContainerGroupIdentity

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  type | enum | Não | O tipo de identidade usada para o grupo de contêineres. O tipo 'SystemAssigned, UserAssigned' inclui uma identidade criada implicitamente e um conjunto de identidades atribuídas ao usuário. O tipo 'Nenhum' removerá quaisquer identidades do grupo de contêineres. - SystemAssigned, UserAssigned, SystemAssigned, UserAssigned, None |
|  userAssignedIdentities | objeto | Não | A lista de identidades de usuários associadas ao grupo de contêineres. As principais referências do dicionário de identidade do usuário serão IDs de recursos do Azure Resource Manager no formulário: '/assinaturas/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'. |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>Objeto ContainerGroupProperties

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  containers | matriz | Sim | Os contêineres dentro do grupo de contêineres. - [Objeto de contêiner](#Container) |
|  imageRegistryCredenciais | matriz | Não | As credenciais de registro de imagem pelas quais o grupo de contêineres é criado. - [Objeto ImageRegistryCredential](#ImageRegistryCredential) |
|  reinicializaçãoPolítica | enum | Não | Reinicie a política para todos os contêineres dentro do grupo de contêineres. - `Always`Sempre reinicie- `OnFailure` Reinicie na falha - `Never` Nunca reinicie. - Sempre, OnFailure, Never |
|  ipAddress | objeto | Não | O tipo de endereço IP do grupo de contêineres. - [Objeto IpAddress](#IpAddress) |
|  osType | enum | Sim | O tipo de sistema operacional exigido pelos contêineres do grupo de contêineres. - Windows ou Linux |
|  volumes | matriz | Não | A lista de volumes que podem ser montados por contêineres neste grupo de contêineres. - [Objeto de volume](#Volume) |
|  diagnóstico | objeto | Não | As informações de diagnóstico para um grupo de contêineres. - [Objeto ContainerGroupDiagnostics](#ContainerGroupDiagnostics) |
|  perfil de rede | objeto | Não | As informações do perfil da rede para um grupo de contêineres. - [Objeto ContainerGroupNetworkProfile](#ContainerGroupNetworkProfile) |
|  dnsConfig | objeto | Não | As informações de configuração de DNS para um grupo de contêineres. - [DnsObjetodeconfiguração](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>Objeto de contêiner

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  name | string | Sim | O nome fornecido pelo usuário da instância do contêiner. |
|  properties | objeto | Sim | As propriedades da ocorrência do contêiner. - [Objeto ContainerProperties](#ContainerProperties) |


<a id="ImageRegistryCredential" />

### <a name="imageregistrycredential-object"></a>Objeto ImageRegistryCredential

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  Servidor | string | Sim | O servidor de registro de imagem docker sem um protocolo como "http" e "https". |
|  Nome de Usuário | string | Sim | O nome de usuário do registro privado. |
|  password | string | Não | A senha do registro privado. |


<a id="IpAddress" />

### <a name="ipaddress-object"></a>Objeto IpAddress

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  ports | matriz | Sim | A lista de portas expostas no grupo de contêineres. - [Objeto de porta](#Port) |
|  type | enum | Sim | Especifica se o IP está exposto à internet pública ou ao VNET privado. - Público ou Privado |
|  Ip | string | Não | O IP exposto à internet pública. |
|  dnsNameLabel | string | Não | O rótulo de nome Dns para o IP. |


<a id="Volume" />

### <a name="volume-object"></a>Objeto de volume

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  name | string | Sim | O nome do volume. |
|  azureFile | objeto | Não | O volume do Arquivo Azure. - [Objeto AzureFileVolume](#AzureFileVolume) |
|  emptyDir | objeto | Não | O volume vazio do diretório. |
|  segredo | objeto | Não | O volume secreto. |
|  gitRepo | objeto | Não | O volume de relo git. - [Objeto GitRepoVolume](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>Objeto ContainerGroupDiagnostics

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | objeto | Não | Informações de análise de log de grupo de contêineres. - [Objeto LogAnalytics](#LogAnalytics) |


<a id="ContainerGroupNetworkProfile" />

### <a name="containergroupnetworkprofile-object"></a>Objeto ContainerGroupNetworkProfile

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  id | string | Sim | O identificador para um perfil de rede. |


<a id="DnsConfiguration" />

### <a name="dnsconfiguration-object"></a>DnsObjetodeconfiguração

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  Nameservers | matriz | Sim | Os servidores DNS para o grupo de contêineres. - corda |
|  searchDomínios | string | Não | Os domínios de pesquisa DNS para pesquisa de nome de host no grupo de contêineres. |
|  opções | string | Não | As opções de DNS para o grupo de contêineres. |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>Objeto ContainerProperties

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  image | string | Sim | O nome da imagem usada para criar a instância do contêiner. |
|  command | matriz | Não | Os comandos para executar dentro da instância do contêiner na forma exec. - corda |
|  ports | matriz | Não | As portas expostas na instância do contêiner. - [Objeto ContainerPort](#ContainerPort) |
|  Environmentvariables | matriz | Não | As variáveis de ambiente a serem definidas na instância do contêiner. - [Objeto EnvironmentVariable](#EnvironmentVariable) |
|  recursos | objeto | Sim | Os requisitos de recursos da instância do contêiner. - [Objeto Requisitos de recursos](#ResourceRequirements) |
|  volumeSMounts | matriz | Não | O volume é montado à ocorrência do contêiner. - [Objeto VolumeMount](#VolumeMount) |
|  livenessProbe | objeto | Não | A sonda de vida. - [Objeto ContainerProbe](#ContainerProbe) |
|  prontidãoSonda | objeto | Não | A sonda de prontidão. - [Objeto ContainerProbe](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>Objeto de porta

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  protocolo | enum | Não | O protocolo associado à porta. - TCP ou UDP |
|  porta | inteiro | Sim | O número da porta. |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>Objeto AzureFileVolume

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  Nome_do_compartilhamento | string | Sim | O nome do compartilhamento Arquivo Azure a ser montado como um volume. |
|  readOnly | booleano | Não | A bandeira indicando se o Arquivo Azure compartilhado montado como um volume é somente leitura. |
|  storageAccountName | string | Sim | O nome da conta de armazenamento que contém o compartilhamento de arquivos do Azure. |
|  storageAccountKey | string | Não | A chave de acesso à conta de armazenamento usada para acessar o compartilhamento de arquivos do Azure. |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>Objeto GitRepoVolume

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  directory | string | Não | Nome do diretório alvo. Não deve conter ou começar com '..'.  Se '.' for fornecido, o diretório de volume será o repositório git.  Caso contrário, se especificado, o volume conterá o repositório git no subdiretório com o nome dado. |
|  repository | string | Sim | URL de repositório |
|  revision | string | Não | Comprometa hash para a revisão especificada. |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>Objeto LogAnalytics

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  workspaceId | string | Sim | O id do espaço de trabalho para análise de log |
|  espaço de trabalhoChave | string | Sim | A chave do espaço de trabalho para análise de log |
|  logType | enum | Não | O tipo de log a ser usado. - ContainerInsights ou ContainerInstanceLogs |
|  metadata | objeto | Não | Metadados para análise de log. |


<a id="ContainerPort" />

### <a name="containerport-object"></a>Objeto ContainerPort

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  protocolo | enum | Não | O protocolo associado à porta. - TCP ou UDP |
|  porta | inteiro | Sim | O número da porta exposto dentro do grupo de contêineres. |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>Objeto EnvironmentVariable

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  name | string | Sim | O nome da variável de ambiente. |
|  value | string | Não | O valor da variável de ambiente. |
|  secureValue | string | Não | O valor da variável ambiente seguro. |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>Objeto Requisitos de recursos

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  solicitações | objeto | Sim | As solicitações de recursos desta instância de contêiner. - [Objeto Solicitações de recursos](#ResourceRequests) |
|  limites | objeto | Não | Os limites de recursos desta instância de contêiner. - [Objeto ResourceLimits](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>Objeto VolumeMount

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  name | string | Sim | O nome do suporte de volume. |
|  mountPath | string | Sim | O caminho dentro do recipiente onde o volume deve ser montado. Não deve conter cólon (:). |
|  readOnly | booleano | Não | A bandeira indicando se o suporte de volume é somente leitura. |


<a id="ContainerProbe" />

### <a name="containerprobe-object"></a>Objeto ContainerProbe

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  exec | objeto | Não | O comando de execução para sondar - [Objeto ContainerExec](#ContainerExec) |
|  httpGet | objeto | Não | As configurações http get para sondar - [ContainerHttpGet object](#ContainerHttpGet) |
|  inicialDelaySeconds | inteiro | Não | Os segundos de atraso iniciais. |
|  períodoSegundos | inteiro | Não | O período segundos. |
|  limite de falha | inteiro | Não | O limiar de falha. |
|  successThreshold | inteiro | Não | O limiar de sucesso. |
|  tempooutSeconds | inteiro | Não | Os segundos de tempo. |


<a id="ResourceRequests" />

### <a name="resourcerequests-object"></a>Objeto Solicitações de recursos

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  memóriaInGB | número | Sim | A solicitação de memória em GB desta instância de contêiner. |
|  cpu | número | Sim | A solicitação de CPU desta instância de contêiner. |
|  Gpu | objeto | Não | A solicitação de GPU desta instância de contêiner. - [Objeto GPUResource](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>Objeto ResourceLimits

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  memóriaInGB | número | Não | O limite de memória em GB desta instância de contêiner. |
|  cpu | número | Não | O limite da CPU desta instância de contêiner. |
|  Gpu | objeto | Não | O limite de GPU desta instância de contêiner. - [Objeto GPUResource](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>Objeto ContainerExec

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  command | matriz | Não | Os comandos para executar dentro do contêiner. - corda |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>ContêinerhttpGet objeto

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  caminho | string | Não | O caminho para a sonda. |
|  porta | inteiro | Sim | O número da porta para sondar. |
|  scheme | enum | Não | O esquema. - http ou https |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>Objeto GPUResource

|  Nome | Type | Obrigatório | Valor |
|  ---- | ---- | ---- | ---- |
|  count | inteiro | Sim | A contagem do recurso de GPU. |
|  sku | enum | Sim | O SKU do recurso GPU. - K80, P100, V100 |


## <a name="next-steps"></a>Próximas etapas

Veja o tutorial [Implantar um grupo de vários contêineres usando um arquivo YAML](container-instances-multi-container-yaml.md).

Veja exemplos de uso de um arquivo YAML para implantar grupos de contêineres em uma [rede virtual](container-instances-vnet.md) ou que [monte um volume externo](container-instances-volume-azure-files.md).

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

