---
title: Gateway de dados no local para serviços de análise do Azure | Microsoft Docs
description: Um gateway local será necessário se o servidor do Analysis Services no Azure for se conectar a fontes de dados locais.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 648646b6f973762245c344cd2629a874a219b170
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310145"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>Conectando-se a fontes de dados locais com gateway de dados on-premises

O gateway de dados local fornece transferência de dados segura entre fontes de dados locais e seus servidores do Azure Analysis Services na nuvem. Além de trabalhar com vários servidores do Azure Analysis Services na mesma região, a versão mais recente do gateway também funciona com a azure Logic Apps, Power BI, Power Apps e Power Automate. Embora o gateway instalado seja o mesmo em todos esses serviços, os Serviços de Análise do Azure e os Aplicativos Lógicos têm algumas etapas adicionais.

As informações fornecidas aqui são específicas de como o Azure Analysis Services funciona com o Gateway de Dados On-premises. Para saber mais sobre o gateway em geral e como ele funciona com outros [serviços, veja O que é um gateway de dados no local?](/data-integration/gateway/service-gateway-onprem)

Para os Serviços de Análise do Azure, a configuração com o gateway pela primeira vez é um processo de quatro partes:

- **Baixar e executar a instalação** – esta etapa instala um serviço de gateway em um computador em sua organização. Você também pode entrar no Azure usando uma conta no Azure AD do seu [locatário](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant). Não há suporte para contas B2B (convidadas) do Azure.

- **Registrar seu gateway** – nesta etapa, você especifica um nome e uma chave de recuperação para o gateway e seleciona uma região, registrando o gateway no Serviço de Nuvem do Gateway. Seu recurso gateway pode ser registrado em qualquer região, mas é recomendável que seja na mesma região que seus servidores de Serviços de Análise. 

- **Crie um recurso de gateway no Azure** - Nesta etapa, você cria um recurso de gateway no Azure.

- **Conecte seus servidores ao recurso gateway** - Uma vez que você tenha um recurso de gateway, você pode começar a conectar servidores a ele. Você pode conectar vários servidores e outros recursos desde que estejam na mesma região.



## <a name="how-it-works"></a><a name="how-it-works"> </a>Como funciona
O gateway que você instala em um computador de sua organização é executado como um serviço Windows, o **Gateway de dados local**. Esse serviço local é registrado no Serviço de Nuvem do Gateway por meio do Barramento de Serviço do Azure. Em seguida, você cria um recurso de gateway de dados on-premises para sua assinatura do Azure. Os servidores do Azure Analysis Services estão conectados ao recurso gateway do Azure. Quando modelos em seu servidor precisarem se conectar às fontes de dados locais para consultas ou processamento, um fluxo de dados e consultas atravessará o recurso de gateway, o Barramento de Serviço do Azure, o serviço de gateway de dados local e suas fontes de dados. 

![Como ele funciona](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Fluxo de dados e consultas:

1. Uma consulta é criada pelo serviço de nuvem com as credenciais criptografadas para a fonte de dados local. Ela é enviada para uma fila de processamento do gateway.
2. O serviço de nuvem do gateway analisa a consulta e envia a solicitação para o [Barramento de Serviço do Azure](https://azure.microsoft.com/documentation/services/service-bus/).
3. O gateway de dados local sonda o Barramento de Serviço do Azure para verificar solicitações pendentes.
4. O gateway obtém a consulta, descriptografa as credenciais e conecta-se às fontes de dados com essas credenciais.
5. O gateway envia a consulta à fonte de dados para execução.
6. Os resultados são enviados da fonte de dados de volta para o gateway e, em seguida, para o serviço de nuvem e seu servidor.

## <a name="installing"></a>Instalando

Ao instalar um ambiente de Serviços de Análise do Azure, é importante que você siga as [etapas descritas no Instalar e configurar o gateway de dados local para os Serviços de Análise do Azure](analysis-services-gateway-install.md). Este artigo é específico para o Azure Analysis Services. Ele inclui etapas adicionais necessárias para configurar um recurso de gateway de dados on-premises no Azure e conectar o servidor Azure Analysis Services ao recurso.

## <a name="ports-and-communication-settings"></a>Portas e configurações de comunicação

O gateway cria uma conexão de saída para o Barramento de Serviço do Azure. Ele se comunica nas portas de saída: TCP 443 (padrão), 5671, 5672, 9350 a 9354.  O gateway não exige portas de entrada.

Você pode precisar incluir endereços IP para sua região de dados em seu firewall. Baixe a [lista de IPs de Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=56519). A lista é atualizada semanalmente. Os Endereços IP listados na lista de IP de Datacenter do Azure estão em notação CIDR. Para saber mais, consulte [Roteamento entre Domínios sem Classificação](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

A seguir estão nomes de domínio totalmente qualificados usados pelo gateway.

| Nomes de domínio | Portas de saída | Descrição |
| --- | --- | --- |
| *.powerbi.com |80 |HTTP usado para baixar o instalador. |
| *.powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *.login.windows.net, login.live.com, aadcdn.msauth.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Advanced Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |Ouvintes de Retransmissão do Barramento de Serviço por meio de TCP (requer 443 para aquisição de token de Controle de Acesso) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Usado para testar a conectividade com a Internet se o gateway não poder ser acessado pelo serviço do Power BI. |
| *.microsoftonline p.com |443 |Usado para autenticação, dependendo da configuração. |
| dc.services.visualstudio.com  |443 |Usado pelo AppInsights para coletar telemetria. |

### <a name="forcing-https-communication-with-azure-service-bus"></a><a name="force-https"></a>Forçar a comunicação HTTPS com o Barramento de Serviço do Azure

Você pode forçar o gateway para se comunicar com o Barramento de Serviço do Azure usando HTTPS em vez de TCP direto; no entanto, fazer isso pode reduzir consideravelmente o desempenho. Você pode modificar o arquivo *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* alterando o valor de `AutoDetect` para `Https`. Normalmente, esse arquivo fica localizado em *C:\Arquivos de Programas\Gateway de dados local*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="next-steps"></a>Próximas etapas 

Os seguintes artigos estão incluídos no gateway de dados on-premises conteúdo geral que se aplica a todos os serviços que o gateway suporta:

* [Faq de gateway de dados no local](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)   
* [Usar o aplicativo de gateway de dados local](https://docs.microsoft.com/data-integration/gateway/service-gateway-app)   
* [Administração de nível de locatário](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin)
* [Configurar configurações de proxy](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)   
* [Ajustar as configurações de comunicação](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)   
* [Configurar arquivos de log](https://docs.microsoft.com/data-integration/gateway/service-gateway-log-files)   
* [Solução de problemas](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [Monitorar e otimizar o desempenho do gateway](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)
