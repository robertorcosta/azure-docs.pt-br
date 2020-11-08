---
title: Como usar Análise de Texto para integridade
titleSuffix: Azure Cognitive Services
description: Saiba como extrair e rotular informações médicas de textos clínicos não estruturados com o Análise de Texto para fins de integridade.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: aahi
ms.openlocfilehash: e3e0ae444e2b3b6ac195a83653baf4b71bac6644
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363860"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>Como usar Análise de Texto para integridade (versão prévia)

> [!NOTE]
> O Análise de Texto para o contêiner de integridade foi atualizado recentemente. Consulte [o que há de novo](../whats-new.md) para obter mais informações sobre as alterações recentes. Lembre-se de efetuar pull do contêiner mais recente para usar as atualizações listadas.

> [!IMPORTANT] 
> O Análise de Texto for Health é uma funcionalidade de visualização fornecida "no estado em que se encontra" e "com todas as falhas". Dessa forma, o **análise de texto para integridade (versão prévia) não deve ser implementado ou implantado em nenhum uso de produção.** A Análise de Texto para a integridade não é pretendida ou disponibilizada para uso como um dispositivo médico, suporte clínico, ferramenta de diagnóstico ou outra tecnologia destinada a ser usada no diagnóstico, remediação, redução, tratamento ou prevenção de doenças ou outras condições, e nenhuma licença ou direito é concedido pela Microsoft para usar esse recurso para tais finalidades. Essa funcionalidade não foi projetada ou destinada a ser implementada ou implantada como um substituto para consultoria Médica profissional ou opinião de saúde, diagnóstico, tratamento ou julgamento clínico de um profissional de saúde, e não deve ser usada como tal. O cliente é exclusivamente responsável por qualquer uso de Análise de Texto para fins de integridade. A Microsoft não garante que Análise de Texto de saúde ou qualquer material fornecido em conexão com a capacidade seja suficiente para qualquer finalidade médica ou atenda aos requisitos de saúde ou médicos de qualquer pessoa. 


O Análise de Texto for Health é um serviço em contêineres que extrai e rotula informações médicas relevantes de textos não estruturados, como notas do médico, resumos de descarga, documentos clínicos e registros eletrônicos de saúde.  

## <a name="features"></a>Recursos

O Análise de Texto para o contêiner de integridade executa atualmente o reconhecimento de entidade nomeada (NER), extração de relações, negação de entidade e vinculação de entidade para texto em idioma inglês em seu próprio ambiente de desenvolvimento que atenda aos seus requisitos específicos de segurança e de governança de dados.

#### <a name="named-entity-recognition"></a>[Reconhecimento de entidade nomeada](#tab/ner)

O reconhecimento de entidade nomeada detecta palavras e frases mencionadas em texto não estruturado que pode ser associado a um ou mais tipos semânticos, como diagnóstico, nome do medicamentos, sintoma/sinal ou idade.

> [!div class="mx-imgBorder"]
> ![NER de integridade](../media/ta-for-health/health-named-entity-recognition.png)

#### <a name="relation-extraction"></a>[Extração de relações](#tab/relation-extraction)

A extração de relações identifica conexões significativas entre os conceitos mencionados no texto. Por exemplo, uma relação "hora da condição" é encontrada pela Associação de um nome de condição com um horário. 

> [!div class="mx-imgBorder"]
> ![Renovar integridade](../media/ta-for-health/health-relation-extraction.png)


#### <a name="entity-linking"></a>[Vinculação de Identidade](#tab/entity-linking)

A vinculação de entidade ambiguidade entidades distintas associando entidades nomeadas mencionadas em texto a conceitos encontrados em um banco de dados predefinido de conceitos. Por exemplo, o sistema de idiomas médicos unificados (UMLS).

> [!div class="mx-imgBorder"]
> ![Integridade EL](../media/ta-for-health/health-entity-linking.png)

O Análise de Texto para integridade dá suporte à vinculação com os vocabulários de integridade e biomédica encontrados na fonte de conhecimento do metadicionário de[UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)(Unified Medical Language System).

#### <a name="negation-detection"></a>[Detecção de negação](#tab/negation-detection) 

O significado do conteúdo médico é altamente afetado por modificadores como negação, o que pode ter implicação crítica se for diagnosticado. O Análise de Texto para integridade dá suporte à detecção de negação para as diferentes entidades mencionadas no texto. 

> [!div class="mx-imgBorder"]
> ![NEG de integridade](../media/ta-for-health/health-negation.png)

---

Consulte as [categorias de entidade](../named-entity-types.md?tabs=health) retornadas por análise de texto para integridade para obter uma lista completa de entidades com suporte.

## <a name="supported-languages"></a>Idiomas com suporte

O Análise de Texto para fins de integridade só dá suporte a documentos de idioma inglês.

## <a name="request-access-to-the-container-registry"></a>Solicitar acesso ao Registro de contêiner

Preencha e envie o [formulário de solicitação de contêineres de serviços cognitivas](https://aka.ms/csgate) para solicitar acesso ao contêiner. No momento, você não será cobrado por Análise de Texto para uso de integridade. 

[!INCLUDE [Request access to the container registry](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="install-the-container"></a>Instalar o contêiner

Há várias maneiras que você pode instalar e executar o contêiner. 

- Use o [portal do Azure](text-analytics-how-to-install-containers.md?tabs=healthcare) para criar um recurso de análise de texto e use o Docker para obter seu contêiner.
- Use os scripts do PowerShell e do [CLI do Azure](/cli/azure/?view=azure-cli-latest) a seguir para automatizar a configuração do contêiner de implantação de recursos.

### <a name="install-the-container-using-azure-web-app-for-containers"></a>Instalar o contêiner usando o Azure Aplicativo Web para Contêineres

O Azure [aplicativo Web para contêineres](https://azure.microsoft.com/services/app-service/containers/) é um recurso do Azure dedicado à execução de contêineres na nuvem. Ele traz recursos prontos para uso, como dimensionamento automático, suporte a contêineres do Docker e ao Docker Compose, suporte a HTTPS e muito mais.

> [!NOTE]
> Usando o aplicativo Web do Azure, você obterá automaticamente um domínio na forma de `<appservice_name>.azurewebsites.net`

Execute este script do PowerShell usando o CLI do Azure para criar um Aplicativo Web para Contêineres, usando sua assinatura e a imagem de contêiner sobre HTTPS. Aguarde até que o script seja concluído (aproximadamente 25-30 minutos) antes de enviar a primeira solicitação.

```bash
$subscription_name = ""                    # THe name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           #    and AppSerivce to be attached to.
$resources_location = ""                   # This is the location you wish the AppServicePlan to be deployed to.
                                           #    You can use the "az account list-locations -o table" command to
                                           #    get the list of available locations and location code names.
$appservice_plan_name = ""                 # This is the AppServicePlan name you wish to have.
$appservice_name = ""                      # This is the AppService resource name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az appservice plan create -n $appservice_plan_name -g $resource_group_name --is-linux -l $resources_location --sku P3V2
az webapp create -g $resource_group_name -p $appservice_plan_name -n $appservice_name -i $DOCKER_IMAGE_NAME -s $DOCKER_REGISTRY_SERVER_USERNAME -w $DOCKER_REGISTRY_SERVER_PASSWORD
az webapp config appsettings set -g $resource_group_name -n $appservice_name --settings Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: https://<appservice_name>.azurewebsites.net
```

### <a name="install-the-container-using-azure-container-instance"></a>Instalar o contêiner usando a instância de contêiner do Azure

Você também pode usar uma ACI (instância de contêiner do Azure) para facilitar a implantação. ACI é um recurso que permite executar contêineres do Docker sob demanda em um ambiente gerenciado e sem servidor do Azure. 

Consulte [como usar as instâncias de contêiner do Azure](text-analytics-how-to-use-container-instances.md) para obter etapas sobre como implantar um recurso ACI usando o portal do Azure. Você também pode usar o script do PowerShell abaixo usando CLI do Azure, que criará um ACI em sua assinatura usando a imagem de contêiner.  Aguarde até que o script seja concluído (aproximadamente 25-30 minutos) antes de enviar a primeira solicitação.  Devido ao limite do número máximo de CPUs por recurso ACI, não selecione esta opção se você espera enviar mais de 5 documentos grandes (aproximadamente 5000 caracteres cada) por solicitação.
Consulte o artigo de [suporte regional do ACI](../../../container-instances/container-instances-region-availability.md) para obter informações de disponibilidade. 

> [!NOTE] 
> As instâncias de contêiner do Azure não incluem suporte a HTTPS para os domínios internos. Se você precisar de HTTPS, será necessário configurá-lo manualmente, incluindo a criação de um certificado e o registro de um domínio. Você pode encontrar instruções para fazer isso com o NGINX abaixo.

```bash
$subscription_name = ""                    # The name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           # and AppService to be attached to.
$resources_location = ""                   # This is the location you wish the web app to be deployed to.
                                           # You can use the "az account list-locations -o table" command to
                                           # Get the list of available locations and location code names.
$azure_container_instance_name = ""        # This is the AzureContainerInstance name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DNS_LABEL = ""                            # This is the DNS label name you wish your ACI will have
$DOCKER_REGISTRY_LOGIN_SERVER = "containerpreview.azurecr.io"
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az container create --resource-group $resource_group_name --name $azure_container_instance_name --image $DOCKER_IMAGE_NAME --cpu 4 --memory 12 --registry-login-server $DOCKER_REGISTRY_LOGIN_SERVER --registry-username $DOCKER_REGISTRY_SERVER_USERNAME --registry-password $DOCKER_REGISTRY_SERVER_PASSWORD --port 5000 --dns-name-label $DNS_LABEL --environment-variables Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: http://<unique_dns_label>.<resource_group_region>.azurecontainer.io:5000
```

### <a name="secure-aci-connectivity"></a>Conectividade segura do ACI

Por padrão, não há nenhuma segurança fornecida ao usar ACI com a API do contêiner. Isso ocorre porque normalmente os contêineres serão executados como parte de um pod que é protegido do exterior por uma ponte de rede. No entanto, você pode modificar um contêiner com um componente front-end, mantendo o ponto de extremidade do contêiner privado. Os exemplos a seguir usam [Nginx](https://www.nginx.com) como um gateway de entrada para dar suporte a autenticação HTTPS/SSL e certificado de cliente.

> [!NOTE]
> NGINX é um servidor e proxy HTTP de software livre e de alto desempenho. Um contêiner NGINX pode ser usado para encerrar uma conexão TLS para um único contêiner. Também são possíveis soluções de terminação TLS baseadas em NGINX de entrada mais complexas.

#### <a name="set-up-nginx-as-an-ingress-gateway"></a>Configurar o NGINX como um gateway de entrada

O NGINX usa [arquivos de configuração](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) para habilitar recursos em tempo de execução. Para habilitar o encerramento de TLS para outro serviço, você deve especificar um certificado SSL para encerrar a conexão TLS e  `proxy_pass` especificar um endereço para o serviço. Um exemplo é fornecido abaixo.


> [!NOTE]
> `ssl_certificate` Espera que um caminho seja especificado no sistema de arquivos local do contêiner NGINX. O endereço especificado para `proxy_pass` deve estar disponível na rede do contêiner nginx.

O contêiner NGINX carregará todos os arquivos no `_.conf_` que estão montados em no `/etc/nginx/conf.d/` caminho de configuração de http.

```nginx
server {
  listen              80;
  return 301 https://$host$request_uri;
}
server {
  listen              443 ssl;
  # replace with .crt and .key paths
  ssl_certificate     /cert/Local.crt;
  ssl_certificate_key /cert/Local.key;

  location / {
    proxy_pass http://cognitive-service:5000;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP  $remote_addr;
  }
}
```

#### <a name="example-docker-compose-file"></a>Exemplo de arquivo Docker Compose

O exemplo abaixo mostra como um arquivo [Docker Compose](https://docs.docker.com/compose/reference/overview) pode ser criado para implantar o Nginx e o análise de texto para os contêineres de integridade:

```yaml
version: "3.7"
services:
  cognitive-service:
    image: {IMAGE_ID}
    ports:
      - 5000:5000
    environment:
      - eula=accept
      - billing={ENDPOINT_URI}
      - apikey={API_KEY}
      - Logging:Disk:Format=json
    volumes:
        # replace with path to logs folder
      - <path-to-logs-folder>:/output
  nginx:
    image: nginx
    ports:
      - 443:443
    volumes:
        # replace with paths for certs and conf folders
      - <path-to-certs-folder>:/cert
      - <path-to-conf-folder>:/etc/nginx/conf.d/
```

Para iniciar esse arquivo do Docker Compose, execute o seguinte comando em um console no nível raiz do arquivo:

```bash
docker-compose up
```

Para obter mais informações, consulte a documentação do NGINX na [terminação do SSL do Nginx](https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-http/).


## <a name="example-api-request"></a>Solicitação de API de exemplo
O contêiner fornece APIs de ponto de extremidade de previsão de consulta baseadas em REST.  Também fornecemos uma ferramenta de visualização no contêiner que é acessível acrescentando a **demonstração** ao ponto de extremidade do contêiner, por exemplo:

```bash
http://<serverURL>:5000/demo
```

Use a solicitação de ondulação de exemplo abaixo para enviar uma consulta ao contêiner que você implantou, substituindo a `serverURL` variável pelo valor apropriado.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

O JSON a seguir é um exemplo de um arquivo JSON anexado à Análise de Texto para o corpo do POST da solicitação de API de integridade:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

## <a name="api-response-body"></a>Corpo da resposta da API

O JSON a seguir é um exemplo da Análise de Texto para o corpo da resposta da API de integridade:

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "id": "0",
                    "offset": 17,
                    "length": 11,
                    "text": "itchy sores",
                    "category": "SymptomOrSign",
                    "confidenceScore": 1.0,
                    "isNegated": false
                }
            ]
        },
        {
            "id": "2",
            "entities": [
                {
                    "id": "0",
                    "offset": 11,
                    "length": 4,
                    "text": "50mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "1",
                    "offset": 16,
                    "length": 8,
                    "text": "benadryl",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "isNegated": false,
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C0700899"
                        },
                        {
                            "dataSource": "CHV",
                            "id": "0000044903"
                        },
                        {
                            "dataSource": "MMSL",
                            "id": "899"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "D004155"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C300"
                        },
                        {
                            "dataSource": "NCI_DTP",
                            "id": "NSC0033299"
                        },
                        {
                            "dataSource": "PDQ",
                            "id": "CDR0000039163"
                        },
                        {
                            "dataSource": "PSY",
                            "id": "05760"
                        },
                        {
                            "dataSource": "RXNORM",
                            "id": "203457"
                        }
                    ]
                },
                {
                    "id": "2",
                    "offset": 32,
                    "length": 11,
                    "text": "twice daily",
                    "category": "Frequency",
                    "confidenceScore": 1.0,
                    "isNegated": false
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
        }
    ],
    "errors": [],
    "modelVersion": "2020-07-24"
}
```

### <a name="negation-detection-output"></a>Saída de detecção de negação

Ao usar a detecção de negação, em alguns casos um único termo de negação pode abordar vários termos ao mesmo tempo. A negação de uma entidade reconhecida é representada na saída JSON pelo valor booliano do `isNegated` sinalizador:

```json
{
  "id": "2",
  "offset": 90,
  "length": 10,
  "text": "chest pain",
  "category": "SymptomOrSign",
  "score": 0.9972,
  "isNegated": true,
  "links": [
    {
      "dataSource": "UMLS",
      "id": "C0008031"
    },
    {
      "dataSource": "CHV",
      "id": "0000023593"
    },
    ...
```

### <a name="relation-extraction-output"></a>Saída de extração de relação

A saída de extração de relação contém referências de URI para a *origem* da relação e seu *destino*. As entidades com a função de relação de `ENTITY` são atribuídas ao `target` campo. As entidades com a função de relação de `ATTRIBUTE` são atribuídas ao `source` campo. As relações de abreviação contêm bidirecional `source` e `target` campos e `bidirectional` serão definidas como `true` . 

```json
"relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
  },
...
]
```

## <a name="see-also"></a>Confira também

* [Visão geral da Análise de Texto](../overview.md)
* [Categorias de entidade nomeada](../named-entity-types.md)
* [Novidades](../whats-new.md)