---
title: Solucionando problemas de status degradado do Gerenciador de Tráfego
description: Como solucionar problemas de perfis do Gerenciador de Tráfego quando ele aparece com status de degradado.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: rohink
ms.openlocfilehash: c398763405472c9018a5c30d34fbd3963ecb93b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938377"
---
# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Solucionando problemas de status degradado do Gerenciador de Tráfego do Azure

Este artigo descreve como solucionar problemas de um perfil do Gerenciador de Tráfego do Azure que mostra um estado degradado. Como um primeiro passo para solucionar problemas, um estado degradado do Azure Traffic Manager é habilitar o registro de diagnóstico.  Consulte [Ativar registros de diagnóstico](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-diagnostic-logs) para obter mais informações. Para esse cenário, considere a possibilidade de que você configurou um perfil do Gerenciador de Tráfego apontando para alguns dos serviços hospedados do cloudapp.net. Se a integridade do seu Gerenciador de Tráfego exibe um status **Degradado**, o status de um ou mais pontos de extremidade pode ser **Degradado**:

![status do ponto de extremidade degradado](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degradedifonedegraded.png)

Se a integridade do seu Gerenciador de Tráfego exibe um status **Inativo**, ambos os pontos de extremidade podem ser **Desabilitado**:

![Status do Gerenciador de Tráfego Inativo](./media/traffic-manager-troubleshooting-degraded/traffic-manager-inactive.png)

## <a name="understanding-traffic-manager-probes"></a>Noções básicas sobre as investigações do Gerenciador de Tráfego

* O Gerenciador de Tráfego considera um ponto de extremidade como estando ONLINE somente quando a investigação recebe uma resposta HTTP 200 do caminho de investigação. Se você remeto qualquer outro código de resposta HTTP, você deve adicionar esse código de resposta às faixas de código de [status esperadas](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring#configure-endpoint-monitoring) do perfil do Gerenciador de tráfego.
* Uma resposta de redirecionamento de 30x é tratada como falha, a menos que você tenha especificado isso como um código de resposta válido nas [faixas](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring#configure-endpoint-monitoring) de código de status esperado do perfil do Gerenciador de tráfego. O Gerenciador de Tráfego não sonda o alvo de redirecionamento.
* Para investigações de HTTPs, os erros de certificado são ignorados.
* O conteúdo real do caminho de investigação não importa, contanto que uma resposta 200 seja retornada. A investigação de uma URL para algum conteúdo estático como “/favicon.ico” é uma técnica comum. O conteúdo dinâmico, como as páginas ASP, nem sempre poderá retornar a resposta 200, mesmo quando o aplicativo estiver íntegro.
* Uma prática recomendada é definir o caminho da sonda para algo que tenha lógica suficiente para determinar que o site está para cima ou para baixo. No exemplo anterior, ao configurar o caminho como “/favicon.ico”, você está apenas testando se w3wp.exe está respondendo. Essa investigação pode não indicar que o aplicativo Web está íntegro. Uma opção melhor seria definir um caminho para algo como “/Probe.aspx”, que tem lógica para determinar a integridade do site. Por exemplo, você poderá usar contadores de desempenho para a utilização da CPU ou medir o número de solicitações com falha. Se preferir, você poderá tentar acessar os recursos de banco de dados ou o estado de sessão para verificar se o aplicativo Web está funcionando.
* Se todos os pontos de extremidade em um perfil estiverem degradados, o Gerenciador de Tráfego tratará todos os pontos de extremidade como íntegros e encaminhará o tráfego para todos eles. Esse comportamento garante que os problemas com o mecanismo de investigação não resultam em uma interrupção completa do serviço.

## <a name="troubleshooting"></a>Solução de problemas

Para solucionar uma falha de investigação, você precisa de uma ferramenta que mostra o retorno de código de status HTTP da URL de investigação. Há várias ferramentas disponíveis que mostram a resposta HTTP bruta.

* [Fiddler](https://www.telerik.com/fiddler)
* [Onda](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

Além disso, você pode usar a guia Rede das Ferramentas de Depuração F12 no Internet Explorer para exibir as respostas HTTP.

Para este exemplo, queremos ver a resposta\/da nossa URL do teste: http: /watestsdp2008r2.cloudapp.net:80/Probe. O exemplo do PowerShell a seguir ilustra o problema.

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

[O que é gerente de tráfego](traffic-manager-overview.md)

[Serviços de Nuvem](https://go.microsoft.com/fwlink/?LinkId=314074)

[Serviço de aplicativo do Azure](https://azure.microsoft.com/documentation/services/app-service/web/)

[Operações no Gerenciador de Tráfego (referência de API REST)](https://go.microsoft.com/fwlink/?LinkId=313584)

[Cmdlets do Gerenciador de Tráfego do Azure][1]

[1]: https://docs.microsoft.com/powershell/module/az.trafficmanager
