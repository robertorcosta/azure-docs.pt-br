---
title: Solucionando problemas de status degradado do Gerenciador de Tráfego
description: Como solucionar problemas de perfis do Gerenciador de Tráfego quando ele aparece com status de degradado.
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: allensu
ms.openlocfilehash: 8b8fbdf55e408874f6a6e83d6333583238865b5c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227712"
---
# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Solucionando problemas de status degradado do Gerenciador de Tráfego do Azure

Este artigo descreve como solucionar problemas de um perfil do Gerenciador de Tráfego do Azure que mostra um estado degradado. As a first step in troubleshooting a Azure Traffic Manager degraded state is to enable diagnostic logging.  Refer to [Enable diagnostic logs](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-diagnostic-logs) for more information. Para esse cenário, considere a possibilidade de que você configurou um perfil do Gerenciador de Tráfego apontando para alguns dos serviços hospedados do cloudapp.net. Se a integridade do seu Gerenciador de Tráfego exibe um status **Degradado**, o status de um ou mais pontos de extremidade pode ser **Degradado**:

![status do ponto de extremidade degradado](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degradedifonedegraded.png)

Se a integridade do seu Gerenciador de Tráfego exibe um status **Inativo**, ambos os pontos de extremidade podem ser **Desabilitado**:

![Status do Gerenciador de Tráfego Inativo](./media/traffic-manager-troubleshooting-degraded/traffic-manager-inactive.png)

## <a name="understanding-traffic-manager-probes"></a>Noções básicas sobre as investigações do Gerenciador de Tráfego

* O Gerenciador de Tráfego considera um ponto de extremidade como estando ONLINE somente quando a investigação recebe uma resposta HTTP 200 do caminho de investigação. If you application returns any other HTTP response code you should add that response code to [Expected status code ranges](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring#configure-endpoint-monitoring) of your Traffic Manager profile.
* A 30x redirect response is treated as failure unless you have specified this as a valid response code in [Expected status code ranges](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring#configure-endpoint-monitoring) of your Traffic Manager profile. Traffic Manager does not probe the redirection target.
* Para investigações de HTTPs, os erros de certificado são ignorados.
* O conteúdo real do caminho de investigação não importa, contanto que uma resposta 200 seja retornada. A investigação de uma URL para algum conteúdo estático como “/favicon.ico” é uma técnica comum. O conteúdo dinâmico, como as páginas ASP, nem sempre poderá retornar a resposta 200, mesmo quando o aplicativo estiver íntegro.
* A best practice is to set the probe path to something that has enough logic to determine that the site is up or down. No exemplo anterior, ao configurar o caminho como “/favicon.ico”, você está apenas testando se w3wp.exe está respondendo. Essa investigação pode não indicar que o aplicativo Web está íntegro. Uma opção melhor seria definir um caminho para algo como “/Probe.aspx”, que tem lógica para determinar a integridade do site. Por exemplo, você poderá usar contadores de desempenho para a utilização da CPU ou medir o número de solicitações com falha. Se preferir, você poderá tentar acessar os recursos de banco de dados ou o estado de sessão para verificar se o aplicativo Web está funcionando.
* Se todos os pontos de extremidade em um perfil estiverem degradados, o Gerenciador de Tráfego tratará todos os pontos de extremidade como íntegros e encaminhará o tráfego para todos eles. Esse comportamento garante que os problemas com o mecanismo de investigação não resultam em uma interrupção completa do serviço.

## <a name="troubleshooting"></a>Solução de Problemas

Para solucionar uma falha de investigação, você precisa de uma ferramenta que mostra o retorno de código de status HTTP da URL de investigação. Há várias ferramentas disponíveis que mostram a resposta HTTP bruta.

* [Fiddler](https://www.telerik.com/fiddler)
* [curl](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

Além disso, você pode usar a guia Rede das Ferramentas de Depuração F12 no Internet Explorer para exibir as respostas HTTP.

For this example we want to see the response from our probe URL: http:\//watestsdp2008r2.cloudapp.net:80/Probe. O exemplo do PowerShell a seguir ilustra o problema.

```powershell
Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Saída de exemplo:

    StatusCode StatusDescription
    ---------- -----------------
           301 Moved Permanently

Observe que recebemos uma resposta de redirecionamento. Como mencionamos anteriormente, qualquer StatusCode diferente de 200 é considerado uma falha. O Gerenciador de Tráfego altera o status do ponto de extremidade para Offline. Para resolver o problema, verifique a configuração do site para garantir que o StatusCode apropriado pode ser retornado do caminho de investigação. Reconfigure a investigação do Gerenciador de Tráfego para que ela aponte para um caminho que retorna uma resposta 200.

Se a investigação estiver usando o protocolo HTTPS, talvez seja necessário desabilitar a verificação de certificado, para evitar erros de SSL/TLS durante o teste. As seguintes instruções do PowerShell desabilitam a validação de certificado na sessão atual do PowerShell:

```powershell
add-type @"
using System.Net;
using System.Security.Cryptography.X509Certificates;
public class TrustAllCertsPolicy : ICertificatePolicy {
    public bool CheckValidationResult(
    ServicePoint srvPoint, X509Certificate certificate,
    WebRequest request, int certificateProblem) {
    return true;
    }
}
"@
[System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy
```

## <a name="next-steps"></a>Próximas etapas

[Sobre os métodos de roteamento de tráfego do Gerenciador de Tráfego](traffic-manager-routing-methods.md)

[O que é o Gerenciador de Tráfego](traffic-manager-overview.md)

[Serviços de Nuvem](https://go.microsoft.com/fwlink/?LinkId=314074)

[Serviço de aplicativo do Azure](https://azure.microsoft.com/documentation/services/app-service/web/)

[Operações no Gerenciador de Tráfego (referência de API REST)](https://go.microsoft.com/fwlink/?LinkId=313584)

[Cmdlets do Gerenciador de Tráfego do Azure][1]

[1]: https://docs.microsoft.com/powershell/module/az.trafficmanager
