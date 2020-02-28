---
title: Métricas personalizadas no Azure Monitor
description: Saiba mais sobre as métricas personalizadas no Azure Monitor e como elas são modeladas.
author: ancav
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 3e3f45c1802d501e2320930c35073ec89ff38124
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77662341"
---
# <a name="custom-metrics-in-azure-monitor"></a>Métricas personalizadas no Azure Monitor

Ao implantar recursos e aplicativos no Azure, é melhor começar a coletar dados telemétricos para obter percepções sobre sua integridade e desempenho. O Azure disponibiliza algumas métricas para você imediatamente. Essas métricas são chamadas de padrão ou plataforma. No entanto, eles são limitados por natureza. Você pode querer coletar alguns indicadores de desempenho personalizados ou métricas específicas de negócios para fornecer insights mais profundos.
Essas **métricas personalizadas** podem ser coletadas por meio da telemetria do aplicativo, um agente que é executado nos recursos do Azure ou até mesmo um sistema de monitoramento externo e enviado diretamente ao Monitor do Azure. Depois de publicados no Azure Monitor, você pode procurar, consultar e alertar sobre métricas personalizadas para os recursos e aplicativos do Azure, lado a lado com as métricas padrão emitidas pelo Azure.

## <a name="send-custom-metrics"></a>Enviar métricas personalizadas
Métricas personalizadas podem ser enviadas ao Monitor do Azure por vários métodos:
- Instrua seu aplicativo usando o SDK do Azure Application Insights e envie a telemetria personalizada ao Monitor do Azure. 
- Instale a extensão WAD (Windows Azure Diagnostics) em [VM](collect-custom-metrics-guestos-resource-manager-vm.md), [máquina virtual do Azure configurada](collect-custom-metrics-guestos-resource-manager-vmss.md), [VM clássica](collect-custom-metrics-guestos-vm-classic.md) ou [Serviços em nuvem clássicos](collect-custom-metrics-guestos-vm-cloud-service-classic.md) e enviar contadores de desempenho para o Monitor do Azure. 
- Instale o [agente InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md) em sua VM do Azure Linux e envie as métricas usando o plug-in de saída do Monitor do Azure.
- Envie métricas personalizadas [diretamente para a API REST do Azure Monitor](../../azure-monitor/platform/metrics-store-custom-rest-api.md), `https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics`.

Quando você envia as métricas personalizadas para o Azure Monitor, cada ponto de dados ou valor, relatado deve incluir as informações a seguir.

### <a name="authentication"></a>Autenticação
Para enviar métricas personalizadas para o Monitor do Azure, a entidade que envia a métrica precisa de um token válido do Azure AD (Azure Active Directory) no cabeçalho **Portador** da solicitação. Há algumas maneiras para adquirir um token de portador válido:
1. [Identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Fornece uma identidade a um recurso do Azure, como uma VM. A MSI (Managed Service Identity, identidade de serviço gerenciado) foi projetada para fornecer permissões de recursos para executar determinadas operações. Um exemplo é permitir que um recurso emita métricas sobre si mesmo. Um recurso, ou seu MSI, pode receber permissões de **Monitoring Metrics Publisher** em outro recurso. Com essa permissão, o MSI também pode emitir métricas para outros recursos.
2. [Entidade de serviço do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals). Nesse cenário, um aplicativo ou serviço do Azure AD pode receber permissões para emitir métricas sobre um recurso do Azure.
Para autenticar a solicitação, o Monitor do Azure valida o token do aplicativo usando as chaves públicas do Azure AD. A função **existente do Monitoring Metrics Publisher** já tem essa permissão. Ele está disponível no portal do Azure. A entidade de serviço, dependendo dos recursos para os quais ela emite métricas personalizadas, pode receber a função **Monitoring Metrics Publisher** no escopo necessário. Exemplos são uma assinatura, grupo de recursos ou recurso específico.

> [!NOTE]  
> Quando você solicita que um token do Azure AD emita métricas personalizadas, verifique se o público ou recurso para o qual o token é solicitado é https://monitoring.azure.com/. Certifique-se de incluir à direita '/'.

### <a name="subject"></a>Assunto
Essa propriedade indica a ID de recurso do Azure para a qual a métrica personalizada é relatada. Essas informações serão codificadas na URL da chamada à API que está sendo feita. Cada API só pode enviar valores de métrica para um único recurso do Azure.

> [!NOTE]  
> Você não pode emitir métricas personalizadas contra o ID do recurso de um grupo de recursos ou assinatura.
>
>

### <a name="region"></a>Região
Essa propriedade captura em que região do Azure o recurso para o qual você está emitindo métricas está implantado. As métricas devem ser emitidas para o ponto de extremidade regional do Azure Monitor que está na mesma região em que o recurso está implantado. Por exemplo, as métricas personalizadas de uma VM implantada no oeste dos EUA devem ser enviadas ao ponto de extremidade regional do Azure Monitor WestUS. As informações de região também estão codificadas na URL da chamada à API.

> [!NOTE]  
> Durante a pré-visualização pública, as métricas personalizadas estão disponíveis apenas em um subconjunto de regiões do Azure. Uma lista de regiões com suporte é fornecida em uma seção posterior deste artigo.
>
>

### <a name="timestamp"></a>Timestamp
Cada ponto de dados enviado ao Azure Monitor deve estar marcado com um carimbo de data/hora. Esse registro de data e hora captura o DateTime no qual o valor da métrica é medido ou coletado. O Monitor do Azure aceita dados de métricas com registros de data e hora em até 20 minutos no passado e 5 minutos no futuro. O carimbo de data/hora deve estar no formato ISO 8601.

### <a name="namespace"></a>{1&gt;Namespace&lt;1}
Namespaces são uma maneira de categorizar ou agrupar métricas semelhantes. Ao usar namespaces, você pode obter isolamento entre grupos de métricas que podem coletar diferentes insights ou indicadores de desempenho. Por exemplo, você pode ter um namespace chamado **contosomemorymetrics** que controla as métricas de uso de memória cujo perfil é seu aplicativo. Outro namespace chamado **contosoapptransaction** pode rastrear todas as métricas sobre transações de usuário em seu aplicativo.

### <a name="name"></a>{1&gt;Nome&lt;1}
**Nome** é o nome da métrica que está sendo relatada. Normalmente, o nome é descritivo para ajudar a identificar o que está sendo medido. Um exemplo é uma métrica que mede o número de bytes de memória usados em uma determinada VM. Pode ter um nome de métrica como **Memory Bytes In Use**.

### <a name="dimension-keys"></a>Chaves de dimensão
Uma dimensão é um par de chave ou valor que ajuda a descrever características adicionais sobre a métrica que está sendo coletada. Usando as características adicionais, você pode coletar mais informações sobre a métrica, o que permite insights mais profundos. Por exemplo, a métrica **Memory Bytes In Use** pode ter uma chave de dimensão chamada **Process** que captura quantos bytes de memória cada processo em uma VM consome. Usando essa chave, você pode filtrar a métrica para ver quantos processos específicos de memória usam ou para identificar os cinco principais processos pelo uso da memória.
As dimensões são opcionais, nem todas as métricas podem ter dimensões. Uma métrica personalizada pode ter até 10 dimensões.

### <a name="dimension-values"></a>Valores de dimensão
Ao relatar um ponto de dados de métrica, para cada chave de dimensão na métrica que está sendo relatada, há um valor de dimensão correspondente. Por exemplo, você pode querer relatar a memória usada pelo ContosoApp em sua VM:

* O nome da métrica seria **Bytes de Memória em Uso**.
* A chave de dimensão seria **processo**.
* O valor da dimensão seria **ContosoApp.exe**.

Ao publicar um valor de métrica, você pode especificar apenas um valor de dimensão por chave de dimensão. Se você coletar a mesma utilização de memória para vários processos na VM, poderá relatar vários valores de métrica para esse registro de data e hora. Cada valor de métrica especificaria um valor de dimensão diferente para a chave de dimensão **Process**.
As dimensões são opcionais, nem todas as métricas podem ter dimensões. Se uma postagem de métrica definir chaves de dimensão, os valores de dimensão correspondentes serão obrigatórios.

### <a name="metric-values"></a>Valores métricos
O Azure Monitor armazena todas as métricas em intervalos com granularidade de um minuto. Entendemos que, durante um determinado minuto, uma métrica pode precisar ser amostrada várias vezes. Um exemplo é a utilização da CPU. Ou talvez precise ser medido para muitos eventos distintos. Um exemplo é latências de transações de entrada. Para limitar o número de valores brutos que você precisa emitir e pagar no Azure Monitor, é possível pré-agregar os valores localmente e emiti-los:

* **Mín.** : O valor mínimo observado de todas as amostras e medições durante o minuto.
* **Máx**: O valor máximo observado de todas as amostras e medições durante o minuto.
* **Sum**: A soma de todos os valores observados de todas as amostras e medições durante o minuto.
* **Contar**: o número de amostras e medições feitas durante o minuto.

Por exemplo, se houver 4 transações de login em seu aplicativo durante um determinado minuto, as latências medidas resultantes para cada um podem ser as seguintes:

|Transação 1|Transação 2|Transação 3|Transação 4|
|---|---|---|---|
|7 ms|4 ms|13 ms|16 ms|
|

Em seguida, a publicação de métrica resultante para o Azure Monitor seria a seguinte:
* Mín.: 4
* Máx.: 16
* Soma: 40
* Contagem: 4

Se seu aplicativo não é capaz de pré-agregar localmente e precisa emitir cada exemplo ou evento separado imediatamente após a coleta, você pode emitir os valores de medida brutos. Por exemplo, toda vez que uma transação de login ocorre no seu aplicativo, você publica uma métrica no Monitor do Azure com apenas uma única medida. Portanto, para uma transação de login que levou 12 ms, a publicação de métrica seria a seguinte:
* Mín.: 12
* Máx.: 12
* Soma: 12
* Contagem: 1

Com esse processo, você pode emitir vários valores para a mesma combinação de métrica e dimensão durante um determinado minuto. O Monitor do Azure, em seguida, pega todos os valores brutos emitidos por um determinado minuto e os agrega juntos.

### <a name="sample-custom-metric-publication"></a>Publicação de métrica personalizada de exemplo
No exemplo a seguir, você cria uma métrica personalizada chamada **Bytes de Memória em Uso** no namespace **Perfil de Memória** da métrica para uma máquina virtual. A métrica tem uma única dimensão chamada **Process**. Para que o carimbo de hora determinado, podemos emitir valores de métrica para dois processos diferentes:

```json
{
    "time": "2018-08-20T11:25:20-7:00",
    "data": {

      "baseData": {

        "metric": "Memory Bytes in Use",
        "namespace": "Memory Profile",
        "dimNames": [
          "Process"        ],
        "series": [
          {
            "dimValues": [
              "ContosoApp.exe"
            ],
            "min": 10,
            "max": 89,
            "sum": 190,
            "count": 4
          },
          {
            "dimValues": [
              "SalesApp.exe"
            ],
            "min": 10,
            "max": 23,
            "sum": 86,
            "count": 4
          }
        ]
      }
    }
  }
```
> [!NOTE]  
> O Application Insights, a extensão de diagnósticos e o agente InfluxData Telegraf já estão configurados para emitir valores métricos em relação ao ponto final regional correto e transportar todas as propriedades precedentes em cada emissão.
>
>

## <a name="custom-metric-definitions"></a>Definições de métricas personalizadas
Não há necessidade de predefinir uma métrica personalizada no Monitor do Azure antes de ser emitida. Cada ponto de dados de métrica publicado contém informações de espaço de nomes, nome e dimensão. Portanto, na primeira vez em que uma métrica personalizada é emitida para o Azure Monitor, uma definição de métrica é criada automaticamente. Essa definição de métrica é, então, detectável em qualquer recurso em que a métrica é emitida por meio das definições de métrica.

> [!NOTE]  
> O Azure Monitor ainda não suporta a definição de **Unidades** para uma métrica personalizada.

## <a name="using-custom-metrics"></a>Usando métricas personalizadas
Depois que as métricas personalizadas são enviadas ao Monitor do Azure, você pode pesquisá-las por meio do portal do Azure e consultá-las por meio das APIs REST do Monitor do Azure. Você também pode criar alertas para notificá-lo quando certas condições forem atendidas.
### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Procurar suas métricas personalizadas no portal do Azure
1.  Vá para o [Portal do Azure](https://portal.azure.com).
2.  Selecione o painel **Monitor**.
3.  Selecione **Métricas**.
4.  Selecione um recurso em que você emitiu métricas personalizadas.
5.  Selecione o namespace de métricas para sua métrica personalizada.
6.  Selecione a métrica personalizada.

## <a name="supported-regions"></a>Regiões com suporte
Durante a pré-visualização pública, a capacidade de publicar métricas personalizadas está disponível apenas em um subconjunto de regiões do Azure. Essa restrição significa que as métricas podem ser publicadas apenas para recursos em uma das regiões suportadas. A tabela a seguir lista o conjunto de regiões do Azure com suporte para métricas personalizadas. Ele também lista os terminais correspondentes que as métricas de recursos nessas regiões devem ser publicadas para:

|Região do Azure |Prefixo de ponto de extremidade regional|
|---|---|
| **EUA e Canadá** | |
|Centro-Oeste dos EUA | https:\//westcentralus.monitoring.azure.com/ |
|Oeste dos EUA 2       | https:\//westus2.monitoring.azure.com/ |
|Centro Norte dos EUA | https:\//northcentralus.monitoring.azure.com
|Centro-Sul dos Estados Unidos| https:\//southcentralus.monitoring.azure.com/ |
|Centro dos EUA      | https:\//centralus.monitoring.azure.com |
|Canadá Central | https:\//canadacentral.Monitoring.Azure.comc
|Leste dos EUA| https:\//eastus.monitoring.azure.com/ |
| **Europa** | |
|Norte da Europa    | https:\//northeurope.monitoring.azure.com/ |
|Europa Ocidental     | https:\//westeurope.monitoring.azure.com/ |
|Sul do Reino Unido | https:\//uksouth.monitoring.azure.com
|França Central | https:\//francecentral.monitoring.azure.com |
| **África** | |
|Norte da África do Sul | https:\//southafricanorth.monitoring.azure.com
| **Ásia** | |
|Índia Central | https:\//centralindia.monitoring.azure.com
|Leste da Austrália | https:\//australiaeast.monitoring.azure.com
|Leste do Japão | https:\//japaneast.monitoring.azure.com
|Sudeste Asiático  | https:\//southeastasia.monitoring.azure.com |
|Leste da Ásia | https:\//eastasia.monitoring.azure.com
|Coreia Central   | https:\//koreacentral.monitoring.azure.com


## <a name="quotas-and-limits"></a>Cotas e limites
O Monitor do Azure impõe os seguintes limites de uso em métricas personalizadas:

|Categoria|Limite|
|---|---|
|Série de tempo de atividade/assinaturas/região|50.000|
|Chaves de dimensão por métrica|10|
|Tamanho da cadeia de caracteres para namespaces de métrica, nomes de métrica, chaves de dimensão e valores de dimensão|256 caracteres|

Uma série temporal ativa é definida como qualquer combinação exclusiva de métrica, chave de dimensão ou valor de dimensão que teve valores de métrica publicados nas últimas 12 horas.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}
Use métricas personalizadas de diferentes serviços: 
 - [Máquinas virtuais](collect-custom-metrics-guestos-resource-manager-vm.md)
 - [Conjunto de dimensionamento de máquinas virtuais](collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Máquinas virtuais do Azure (clássico)](collect-custom-metrics-guestos-vm-classic.md)
 - [Linux Virtual Machine usando o agente Telegraf](collect-custom-metrics-linux-telegraf.md)
 - [REST API](../../azure-monitor/platform/metrics-store-custom-rest-api.md)
 - [Serviços de nuvem clássico](collect-custom-metrics-guestos-vm-cloud-service-classic.md)
 
