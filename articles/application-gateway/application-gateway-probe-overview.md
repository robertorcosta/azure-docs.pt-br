---
title: Visão geral do monitoramento de integridade do Gateway de Aplicativo do Azure
description: Aplicativo Azure gateway monitora a integridade de todos os recursos em seu pool de back-end e remove automaticamente qualquer recurso considerado não íntegro do pool.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2020
ms.author: victorh
ms.openlocfilehash: c5a53167c6a4ca6c886b858a1608eaa173185bd8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335854"
---
# <a name="application-gateway-health-monitoring-overview"></a>Visão geral do monitoramento de integridade do Gateway de Aplicativo

Por padrão, o Gateway de Aplicativo do Azure monitora a integridade de todos os recursos em seu pool de back-end e remove automaticamente qualquer recurso do pool que não for considerado íntegro. O Gateway de Aplicativo continua monitorando as instâncias não íntegras e as adiciona de volta ao pool de back-end íntegro depois que elas se tornarem disponíveis e responderem a investigações de integridade. O Gateway de Aplicativo envia as investigações de integridade na mesma porta que é definida nas configurações de HTTP do back-end. A configuração assegura que a investigação teste a mesma porta que os clientes usariam para se conectar ao back-end. 

O gateway de aplicativo de endereço IP de origem usado para investigações de integridade depende do pool de back-end:
 
- Se o pool de back-end for um ponto de extremidade público, o endereço de origem será o endereço IP público de front-end do gateway de aplicativo.
- Se o pool de back-end for um ponto de extremidade privado, o endereço IP de origem será do espaço de endereço IP privado da sub-rede do gateway de aplicativo.


![exemplo de investigação de gateway de aplicativo][1]

Além de usar o monitoramento da investigação de integridade padrão, você também pode personalizar a investigação de integridade para atender às necessidades do seu aplicativo. Neste artigo, serão abordadas as investigações de integridade padrão e personalizadas.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-health-probe"></a>Investigação de integridade padrão

Um Application Gateway configura automaticamente uma investigação de integridade padrão quando você não define nenhuma configuração de investigação personalizada. O comportamento de monitoramento funciona fazendo uma solicitação HTTP para os endereços IP configurados para o pool de back-end. Para investigações padrão, se as configurações de HTTP de back-end estão configuradas para HTTPS, a investigação também usa HTTPS para testar a integridade dos back-ends.

Por exemplo: configure seu Application Gateway para usar os servidores de back-end, A, B e C para receber o tráfego de rede HTTP na porta 80. O monitoramento de integridade padrão testa os três servidores a cada 30 segundos para receber uma de HTTP íntegra. Uma resposta de HTTP íntegra tem um [código de status](https://msdn.microsoft.com/library/aa287675.aspx) entre 200 e 399.

Se a verificação de investigação padrão falhar para o servidor A, o Application Gateway o remove do seu pool de back-end e o tráfego de rede para de fluir para este servidor. A investigação padrão ainda continua a verificar o servidor A a cada 30 segundos. Quando o servidor A responde com êxito a uma solicitação de uma investigação de integridade padrão, ele é adicionado de volta como íntegro ao pool de back-end e o tráfego começa a fluir para o servidor novamente.

### <a name="probe-matching"></a>Correspondência de investigação

Por padrão, uma resposta HTTP (S) com código de status entre 200 e 399 é considerada íntegra. As investigações de integridade personalizadas também fornecem suporte a dois critérios correspondentes. Os critérios de correspondência podem ser usados para modificar opcionalmente a interpretação padrão do que faz uma resposta íntegra.

A seguir estão os critérios de correspondência: 

- **Correspondência de código de status de resposta HTTP** - Critério de correspondência de teste para aceitar o código de resposta HTTP ou intervalos de códigos de resposta especificados pelo usuário. Códigos de status de resposta separados por vírgulas individuais ou um intervalo de código de status têm suporte.
- **Correspondência do corpo da resposta HTTP** - Critério de correspondência da análise que examina o corpo da resposta HTTP e corresponde a uma cadeia de caracteres especificada pelo usuário. A correspondência só procura a presença da cadeia de caracteres especificada pelo usuário no corpo da resposta e não é uma correspondência de expressão regular completa.

Os critérios de correspondência podem ser especificados usando o cmdlet `New-AzApplicationGatewayProbeHealthResponseMatch`.

Por exemplo:

```azurepowershell
$match = New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
Depois que os critérios de correspondência forem especificados, ele poderá ser anexado à configuração da investigação usando um parâmetro `-Match` no PowerShell.

### <a name="default-health-probe-settings"></a>Configurações da investigação de integridade padrão

| Propriedades da investigação | Valor | Descrição |
| --- | --- | --- |
| URL de investigação |http://127.0.0.1:\<port\>/ |Caminho da URL |
| Intervalo |30 |A quantidade de tempo em segundos a esperar antes da próxima investigação de integridade é enviada.|
| Tempo limite |30 |A quantidade de tempo em segundos o gateway de aplicativo aguarda uma resposta de investigação antes da marcação da investigação como não íntegro. Se uma investigação é retornada como íntegra, o back-end correspondente será imediatamente marcado como íntegro.|
| Limite não íntegro |3 |Governa a quantidade de investigações a serem enviadas caso haja uma falha na investigação de integridade regular. Essas investigações de integridade adicionais são enviadas em sucessão rápida para determinar a integridade do back-end rapidamente e não aguardam o intervalo de investigação. Este behaivor é apenas v1 SKU. No caso do SKU v2, as investigações de integridade esperam o intervalo. O servidor de back-end é marcado após a contagem de falhas de investigação consecutivas atingir o limite de não íntegro. |

> [!NOTE]
> A porta é a mesma das configurações de HTTP de back-end.

A investigação padrão procura apenas em http:\//127.0.0.1:\<porta\> para determinar o status de integridade. Se precisar configurar a investigação de integridade para ir para uma URL personalizada ou modificar outras configurações, você deverá usar investigações personalizadas.

### <a name="probe-intervals"></a>Intervalos de investigação

Todas as instâncias do Gateway de Aplicativo investiga o back-end independente um do outro. A mesma configuração de investigação se aplica a cada instância de Gateway de Aplicativo. Por exemplo, se a configuração de investigação é enviar as investigações de integridade a cada 30 segundos e o gateway de aplicativo tem duas instâncias, em seguida, ambas as instâncias de enviam a investigação de integridade a cada 30 segundos.

Também se houver vários ouvintes, cada ouvinte investiga o back-end independente uns dos outros. Por exemplo, se houver dois ouvintes que aponta para o mesmo pool de back-end em duas portas diferentes (configurado por duas configurações de http de back-end), em seguida, cada ouvinte investiga o mesmo back-end independentemente. Nesse caso, há dois testes de cada instância de gateway de aplicativo para os dois ouvintes. Se houver duas instâncias do gateway de aplicativo nesse cenário, a máquina virtual de back-end veria quatro investigações por intervalo de sondagem configurado.

## <a name="custom-health-probe"></a>Investigação de integridade personalizada

Investigações personalizadas permitem que você tenha um controle mais granular sobre o monitoramento de integridade. Ao usar investigações personalizadas, você pode configurar o intervalo de investigação, a URL e o caminho a testar e quantas respostas com falha devem ser aceitas antes de marcar a instância do pool de back-end como não íntegra.

### <a name="custom-health-probe-settings"></a>Configurações da investigação de integridade personalizada

A tabela a seguir fornece definições para as propriedades de uma investigação de integridade personalizada.

| Propriedades da investigação | Descrição |
| --- | --- |
| Nome |O nome da investigação. Este é o nome usado para se referir à investigação nas configurações de HTTP de back-end. |
| Protocolo |O protocolo usado para enviar a investigação. A investigação usa o protocolo definido nas configurações de HTTP do back-end |
| Host |O nome do host para enviar a investigação. Aplicável somente quando vários sites são configurados no Gateway de Aplicativo; do contrário, use '127.0.0.1'. Este valor é diferente do nome do host de VM. |
| Caminho |O caminho relativo da investigação. Um caminho válido começa com '/'. |
| Intervalo |Intervalo de investigação em segundos. Este valor é o intervalo de tempo entre duas investigações consecutivas. |
| Tempo limite |Tempo limite da investigação em segundos. Se uma resposta válida não for recebida dentro desse período de tempo limite, a investigação será marcada como com falha.  |
| Limite não íntegro |Contagem de repetições da investigação. O servidor de back-end é marcado após a contagem de falhas de investigação consecutivas atingir o limite de não íntegro. |

> [!IMPORTANT]
> Se o Gateway de Aplicativo estiver configurado para um único site, por padrão, o nome do Host deverá ser especificado como '127.0.0.1', a menos que seja configurado de outra forma na investigação personalizada.
> Para referência, uma investigação personalizada é enviada para \<protocol\>://\<host\>:\<port\>\<path\>. A porta usada será a mesma definida nas configurações de HTTP do back-end.

## <a name="nsg-considerations"></a>Considerações NSG

Você deve permitir o tráfego de entrada na Internet nas portas TCP 65503-65534 para o SKU do gateway de aplicativo v1 e as portas TCP 65200-65535 para a SKU V2 com a sub-rede de destino como qualquer e origem como **uma** marca de serviço do **gatewaymanager** . Esse intervalo de porta é necessário para a comunicação da infraestrutura do Azure.

Além disso, a conectividade de Internet de saída não pode ser bloqueada e o tráfego de entrada proveniente da marca **AzureLoadBalancer** deve ser permitido.

Para obter mais informações, confira [Visão geral da configuração do Gateway de Aplicativo](configuration-overview.md#network-security-groups-on-the-application-gateway-subnet).

## <a name="next-steps"></a>Próximas etapas
Depois de aprender sobre o monitoramento de integridade do Gateway de Aplicativo, você poderá configurar uma [investigação de integridade personalizada](application-gateway-create-probe-portal.md) no portal do Azure ou uma [investigação de integridade personalizada](application-gateway-create-probe-ps.md) usando o PowerShell e o modelo de implantação do Azure Resource Manager.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
