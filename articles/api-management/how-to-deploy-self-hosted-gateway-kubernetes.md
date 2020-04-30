---
title: Implantar o gateway auto-hospedado em kubernetes | Microsoft Docs
description: Saiba como implantar o componente de gateway auto-hospedado do gerenciamento de API do Azure para kubernetes
services: api-management
author: vladvino
manager: gwallace
ms.service: api-management
ms.workload: mobile
ms.topic: article
ms.author: apimpm
ms.date: 04/23/2020
ms.openlocfilehash: 7802614540bd5e553fbf1d7a0884ffa2f7433c37
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205099"
---
# <a name="deploy-self-hosted-gateway-to-kubernetes"></a>Implantar o gateway auto-hospedado no kubernetes

Este artigo descreve as etapas para implantar o componente de gateway auto-hospedado do gerenciamento de API do Azure para um cluster kubernetes.

## <a name="prerequisites"></a>Pré-requisitos

- Conclua o início rápido a seguir: [criar uma instância de gerenciamento de API do Azure](get-started-create-service-instance.md)
- Crie um cluster kubernetes.
> [!TIP]
> [Clusters de nó único](https://kubernetes.io/docs/setup/#learning-environment) funcionam bem para fins de desenvolvimento e avaliação. Use clusters de vários nós [certificados pelo kubernetes](https://kubernetes.io/partners/#conformance) no local ou na nuvem para cargas de trabalho de produção.
- [Provisione um recurso de gateway auto-hospedado em sua instância de gerenciamento de API](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-to-kubernetes"></a>Implantar para o Kubernetes

1. Selecione **gateways** em **implantação e infraestrutura**.
2. Selecione o recurso de gateway auto-hospedado que você pretende implantar.
3. Selecione **implantação**.
4. Observe que um token de acesso na caixa de texto de **token** foi gerado automaticamente para você usando os valores padrão de **expiração** e **chave secreta** . Se necessário, escolha os valores desejados em um ou ambos os controles para gerar um novo token.
5. Selecione a guia **kubernetes** em **scripts de implantação**.
6. Clique no link **<gateway-name> arquivo. yml** e baixe o arquivo YAML.
7. Selecione o ícone de **cópia** localizado no canto inferior direito da caixa de texto **implantar** para salvar `kubectl` os comandos na área de transferência.
8. Colar comandos na janela de terminal (ou comando). O primeiro comando cria um segredo kubernetes que contém o token de acesso gerado na etapa 4. O segundo comando aplica o arquivo de configuração baixado na etapa 6 ao cluster kubernetes e espera que o arquivo esteja presente no diretório atual.
9. Execute os comandos para criar os objetos kubernetes necessários no [namespace padrão](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) e iniciar Pod (s) de gateway auto-hospedados da [imagem de contêiner](https://aka.ms/apim/sputnik/dhub) baixada do registro de contêiner da Microsoft.
10. Execute o comando mostrado abaixo para verificar se a implantação foi bem-sucedida. Observe que pode levar algum tempo para que todos os objetos sejam criados e que os Pod (s) sejam inicializados.
```console
kubectl get deployments
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
<gateway-name>   1/1     1            1           18s
```
11. Execute o comando mostrado abaixo para verificar se o serviço foi criado com êxito. Observe que o IPs de serviço e as portas serão diferentes.
```console
kubectl get services
NAME             TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
<gateway-name>   LoadBalancer   10.99.236.168   <pending>     80:31620/TCP,443:30456/TCP   9m1s
```
12. Volte para a portal do Azure e selecione **visão geral**.
13. **Status** mostrando um ícone de marca de seleção verde seguido pela contagem de nós que correspondem ao número de réplicas especificado no arquivo YAML confirma que os pods implantados de gateway auto-hospedado estão se comunicando com êxito com o serviço de gerenciamento de API e têm uma "pulsação" regular.

![status do gateway](media/how-to-deploy-self-hosted-gateway-kubernetes/status.png)

> [!TIP]
> Execute <code>kubectl logs deployment/<gateway-name></code> o comando para exibir os logs de um pod selecionado aleatoriamente se houver mais de um.
> Execute <code>kubectl logs -h</code> para um conjunto completo de opções de comando, por exemplo, como exibir logs para um pod ou contêiner específico.

## <a name="production-deployment-considerations"></a>Considerações sobre implantação de produção

### <a name="access-token"></a>Token de acesso
Sem um gateway de token de acesso válido, não é possível acessar e baixar a configuração do ponto de extremidade de dados de configuração do serviço de gerenciamento de API associado. O token de acesso pode ser válido por um máximo de 30 dias. Ele deve ser regenerado e o cluster configurado com um token atualizado manualmente ou por meio da automação antes de expirar. Ao automatizar a atualização de token, use essa [operação](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/gateway/generatetoken) de API de gerenciamento para gerar um novo token. Siga este [link](https://kubernetes.io/docs/concepts/configuration/secret) para obter informações sobre como gerenciar segredos do kubernetes.

### <a name="namespace"></a>Namespace
Os [namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) kubernetes ajudam a dividir um único cluster entre várias equipes, projetos ou aplicativos. Os namespaces fornecem um escopo para recursos e nomes e podem ser associados a uma cota de recursos e a políticas de controle de acesso.
Os comandos fornecidos no portal do Azure criar recursos de gateway auto-hospedados no namespace **padrão** que é criado automaticamente, existem em todos os clusters e não podem ser excluídos.
Considere [criar e implantar](https://kubernetesbyexample.com/ns/) o gateway auto-hospedado em um namespace separado na produção.

### <a name="number-of-replicas"></a>Número de réplicas
O número mínimo de réplicas adequadas na produção é dois.

Por padrão, o gateway auto-hospedado é implantado com uma [estratégia](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy)de implantação **RollingUpdate** . Examine os valores padrão e considere definir explicitamente os campos [**maxUnavailable**](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-unavailable) e [**maxSurge**](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-surge) , especialmente ao usar uma contagem de réplicas alta.

### <a name="container-resources"></a>Recursos de contêiner
Por padrão, o arquivo YAML fornecido no portal do Azure não especifica solicitações de recursos de contêiner.

É impossível prever e recomendar de forma confiável a quantidade de recursos de CPU e memória por contêiner e o número de réplicas necessárias para dar suporte a uma carga de trabalho específica devido a muitos fatores em jogo, por exemplo:

- Hardware específico no qual o cluster está sendo executado
- Presença e tipo de virtualização
- Número e taxa de conexões de cliente simultâneas
- Taxa de solicitação
- Tipo e número de políticas configuradas
- Tamanho da carga e se as cargas são armazenadas em buffer ou transmitidas
- Latência do serviço de back-end

É recomendável definir a solicitação de recursos para 2 núcleos e 2 GiB como um ponto de partida, executar um teste de carga e escalar verticalmente/horizontalmente ou reduzir/base os resultados.

### <a name="container-image-tag"></a>Marca de imagem de contêiner
O arquivo YAML fornecido no portal do Azure usa a marca **mais recente** que sempre faz referência à versão mais recente da imagem de contêiner de gateway auto-hospedado.

Considere usar uma marca de versão específica em produção para evitar a atualização não intencional para uma versão mais recente.

Siga este [link](https://mcr.microsoft.com/v2/azure-api-management/gateway/tags/list) para ver a lista completa de marcas disponíveis.

### <a name="dns-policy"></a>Política de DNS
A resolução de nomes DNS desempenha um papel crítico na capacidade do gateway de hospedagem interna para se conectar a dependências no Azure e enviar chamadas de API para serviços de back-end.

O arquivo YAML fornecido no portal do Azure aplica a política de [**ClusterFirst**](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) padrão que faz com que as solicitações de resolução de nomes não resolvidas pelo DNS do cluster sejam encaminhadas para o servidor DNS upstream herdado do nó.

Siga este [link](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service) para saber mais sobre a resolução de nomes no kubernetes e considere a personalização da [política DNS](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) ou da[configuração do NS](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-config) D conforme apropriado para sua configuração.

### <a name="configuration-backup"></a>Backup de configuração
Siga este [link](self-hosted-gateway-overview.md#connectivity-to-azure) para saber mais sobre o comportamento do gateway auto-hospedado na presença de uma interrupção de conectividade temporária do Azure.
Configure o volume de armazenamento local para o contêiner de gateway auto-hospedado, para que ele possa persistir uma cópia de backup da configuração baixada mais recente e, se a conectividade estiver inativa, use-a após a reinicialização. O caminho de montagem do volume <code>/apim/config</code>deve ser. Veja um exemplo [aqui](https://github.com/Azure/api-management-self-hosted-gateway/blob/master/examples/self-hosted-gateway-with-configuration-backup.yaml).
Para saber mais sobre o armazenamento no kubernetes, siga este [link](https://kubernetes.io/docs/concepts/storage/volumes/).

### <a name="local-logs-and-metrics"></a>Métricas e logs locais
O gateway auto-hospedado envia telemetria para [Azure monitor](api-management-howto-use-azure-monitor.md) e [aplicativo Azure insights](api-management-howto-app-insights.md) por definições de configuração no serviço de gerenciamento de API associado.
Quando a [conectividade com o Azure](self-hosted-gateway-overview.md#connectivity-to-azure) é temporariamente perdida, o fluxo de telemetria para o Azure é interrompido e os dados são perdidos durante a interrupção.
Considere [Configurar o monitoramento local](how-to-configure-local-metrics-logs.md) para garantir a capacidade de observar o tráfego da API e evitar a perda de telemetria durante interrupções de conectividade do Azure.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o gateway auto-hospedado, confira [visão geral do gateway de gerenciamento de API do Azure-hospedado](self-hosted-gateway-overview.md)