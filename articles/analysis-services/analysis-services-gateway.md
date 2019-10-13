---
title: Gateway de dados local para Azure Analysis Services | Microsoft Docs
description: Um gateway local será necessário se o servidor do Analysis Services no Azure for se conectar a fontes de dados locais.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8d9df32070ff252dff791650788888d1d9a6ce84
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72294945"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>Conectando-se a fontes de dados locais com o gateway de dados local

O gateway de dados local fornece transferência de dados segura entre fontes de dados locais e seus servidores do Azure Analysis Services na nuvem. Além de trabalhar com diversos servidores do Azure Analysis Services na mesma região, a versão mais recente do gateway também funciona com os Aplicativos Lógicos do Azure, o Power BI, o Power Apps e o Microsoft Flow. É possível associar vários serviços na mesma assinatura e mesma região com um único gateway. Embora o gateway que você instalar seja o mesmo em todos esses serviços, Azure Analysis Services e aplicativos lógicos têm algumas etapas adicionais.

Por Azure Analysis Services, obter a configuração com o gateway na primeira vez é um processo de quatro partes:

- **Baixar e executar a instalação** – esta etapa instala um serviço de gateway em um computador em sua organização. Você também pode entrar no Azure usando uma conta no Azure AD do seu [locatário](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant). Não há suporte para contas B2B (convidadas) do Azure.

- **Registrar seu gateway** – nesta etapa, você especifica um nome e uma chave de recuperação para o gateway e seleciona uma região, registrando o gateway no Serviço de Nuvem do Gateway. O recurso de gateway pode ser registrado em qualquer região, mas recomendamos que seja na mesma região que os seus servidores do Analysis Services. 

- **Criar um recurso de gateway no Azure** – nesta etapa, você cria um recurso de gateway em sua assinatura do Azure.

- **Conectar os servidores ao recurso de gateway** – assim que tiver um recurso de gateway em sua assinatura, você pode começar a conectar seus servidores a ele. Você pode conectar vários servidores e outros recursos, desde que estejam na mesma assinatura e na mesma região.

## <a name="how-it-works"> </a>Como funciona
O gateway que você instala em um computador de sua organização é executado como um serviço Windows, o **Gateway de dados local**. Esse serviço local é registrado no Serviço de Nuvem do Gateway por meio do Barramento de Serviço do Azure. Em seguida, você cria um recurso de gateway de dados local para sua assinatura do Azure. Seus servidores de Azure Analysis Services são então conectados ao recurso de gateway do Azure. Quando modelos em seu servidor precisarem se conectar às fontes de dados locais para consultas ou processamento, um fluxo de dados e consultas atravessará o recurso de gateway, o Barramento de Serviço do Azure, o serviço de gateway de dados local e suas fontes de dados. 

![Como ele funciona](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Fluxo de dados e consultas:

1. Uma consulta é criada pelo serviço de nuvem com as credenciais criptografadas para a fonte de dados local. Ela é enviada para uma fila de processamento do gateway.
2. O serviço de nuvem do gateway analisa a consulta e envia a solicitação para o [Barramento de Serviço do Azure](https://azure.microsoft.com/documentation/services/service-bus/).
3. O gateway de dados local sonda o Barramento de Serviço do Azure para verificar solicitações pendentes.
4. O gateway obtém a consulta, descriptografa as credenciais e conecta-se às fontes de dados com essas credenciais.
5. O gateway envia a consulta à fonte de dados para execução.
6. Os resultados são enviados da fonte de dados de volta para o gateway e, em seguida, para o serviço de nuvem e seu servidor.

## <a name="installing"></a>Instalando

Ao instalar o para um ambiente de Azure Analysis Services, é importante seguir as etapas descritas em [instalar e configurar o gateway de dados local para Azure Analysis Services](analysis-services-gateway-install.md). Este artigo é específico para Azure Analysis Services. Ele inclui etapas adicionais necessárias para configurar um recurso de gateway de dados local no Azure e conectar seu servidor de Azure Analysis Services ao recurso.

## <a name="ports-and-communication-settings"></a>Portas e configurações de comunicação

O gateway cria uma conexão de saída para o Barramento de Serviço do Azure. Ele se comunica nas portas de saída: TCP 443 (padrão), 5671, 5672, 9350 a 9354.  O gateway não exige portas de entrada.

Talvez seja necessário colocar os endereços IP da lista de permissões em sua região de dados em seu firewall. Baixe a [lista de IPs de Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). A lista é atualizada semanalmente. Os Endereços IP listados na lista de IP de Datacenter do Azure estão em notação CIDR. Para saber mais, consulte [Roteamento entre Domínios sem Classificação](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Estes são os nomes de domínio totalmente qualificados usados pelo Gateway.

| Nomes de domínio | Portas de saída | DESCRIÇÃO |
| --- | --- | --- |
| *.powerbi.com |80 |HTTP usado para baixar o instalador. |
| *.powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *. login.windows.net, login.live.com, aadcdn.msauth.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Advanced Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |Ouvintes de Retransmissão do Barramento de Serviço por meio de TCP (requer 443 para aquisição de token de Controle de Acesso) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Usado para testar a conectividade com a Internet se o gateway não poder ser acessado pelo serviço do Power BI. |
| *.microsoftonline p.com |443 |Usado para autenticação, dependendo da configuração. |
| dc.services.visualstudio.com  |443 |Usado pelo AppInsights para coletar telemetria. |

### <a name="force-https"></a>Forçar comunicação HTTPS com o Barramento de Serviço do Azure

Você pode forçar o gateway para se comunicar com o Barramento de Serviço do Azure usando HTTPS em vez de TCP direto; no entanto, fazer isso pode reduzir consideravelmente o desempenho. Você pode modificar o arquivo *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* alterando o valor de `AutoDetect` para `Https`. Normalmente, esse arquivo fica localizado em *C:\Arquivos de Programas\Gateway de dados local*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="next-steps"></a>Próximas etapas 

Os artigos a seguir estão incluídos no conteúdo geral do gateway de dados local que se aplica a todos os serviços aos quais o gateway dá suporte:

* [Perguntas frequentes de gateway de dados no local](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)   
* [Usar o aplicativo de gateway de dados local](https://docs.microsoft.com/data-integration/gateway/service-gateway-app)   
* [Administração no nível do locatário](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin)
* [Definir configurações de proxy](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)   
* [Ajustar as configurações de comunicação](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)   
* [Configurar arquivos de log](https://docs.microsoft.com/data-integration/gateway/service-gateway-log-files)   
* [Solucionar problemas](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [Monitorar e otimizar o desempenho do gateway](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)
