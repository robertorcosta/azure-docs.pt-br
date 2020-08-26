---
title: Impedir takeovers de subdomínio com registros de alias de DNS do Azure e verificação de domínio personalizado do serviço de Azure App
description: Saiba como evitar a ameaça comum de alta gravidade do subdomínio tomada
services: security
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2020
ms.author: memildin
ms.openlocfilehash: e378ffe00be9215c692a832e232fac7e866ab3c9
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88890817"
---
# <a name="prevent-dangling-dns-entries-and-avoid-subdomain-takeover"></a>Impedir entradas DNS pendente e evitar subdomínio tomada

Este artigo descreve a ameaça de segurança comum do subdomínio tomada e as etapas que você pode executar para atenuá-lo.


## <a name="what-is-subdomain-takeover"></a>O que é subdomínio tomada?

Subdomínio takeovers são uma ameaça comum e de alta gravidade para as organizações que criam e excluem muitos recursos regularmente. Um subdomínio tomada pode ocorrer quando você tem um registro DNS que aponta para um recurso do Azure desprovisionado. Esses registros DNS também são conhecidos como entradas "pendente DNS". Os registros CNAME são especialmente vulneráveis a essa ameaça.

Um cenário comum para um subdomínio tomada:

1. Um site é criado. 

    Neste exemplo, `app-contogreat-dev-001.azurewebsites.net`.

1. Uma entrada CNAME é adicionada ao DNS apontando para o site. 

    Neste exemplo, o seguinte nome amigável foi criado: `greatapp.contoso.com` .

1. Depois de alguns meses, o site não é mais necessário, portanto, ele é excluído **sem** excluir a entrada DNS correspondente. 

    A entrada DNS CNAME agora é "pendente".

1. Quase imediatamente depois que o site é excluído, um ator de ameaça descobre o site ausente e cria seu próprio site em `app-contogreat-dev-001.azurewebsites.net` .

    Agora, o tráfego destinado a `greatapp.contoso.com` vai para o site do Azure do ator de ameaça e o ator de ameaça no controle do conteúdo exibido. 

    O DNS pendente foi explorado e o subdomínio "GreatApp" da Contoso foi vítima de subdomínio tomada. 

![Subdomínio tomada de um site desprovisionado](./media/subdomain-takeover/subdomain-takeover.png)



## <a name="the-risks-of-subdomain-takeover"></a>Os riscos de subdomínio tomada

Quando um registro DNS aponta para um recurso que não está disponível, o próprio registro deve ter sido removido da zona DNS. Se ele não tiver sido excluído, será um registro de "DNS pendente" e criará a possibilidade de subdomínio tomada.

As entradas DNS pendente possibilitam que os atores de ameaça assumam o controle do nome DNS associado para hospedar um site ou serviço mal-intencionado. Páginas e serviços mal-intencionados no subdomínio de uma organização podem resultar em:

- **Perda de controle sobre o conteúdo do subdomínio** -negativo Pressione sobre a incapacidade da sua organização de proteger seu conteúdo, bem como os danos à marca e a perda de confiança.

- **Coleta de cookies de visitantes desconhecidos** – é comum que os aplicativos Web exponham cookies de sessão para subdomínios (*. contoso.com), consequentemente, qualquer subdomínio pode acessá-los. Os atores de ameaça podem usar tomada de subdomínio para criar uma página de aparência autêntica, enganar os usuários que não estão fazendo a visita e colher seus cookies (até mesmo cookies seguros). Um equívoco comum é que o uso de certificados SSL protege seu site e os cookies de seus usuários, de um tomada. No entanto, um ator de ameaça pode usar o subdomínio seqüestrado para se aplicar e receber um certificado SSL válido. Os certificados SSL válidos concedem a eles acesso a cookies seguros e podem aumentar ainda mais a legitimidade percebida do site mal-intencionado.

- **Campanhas de phishing** -os subdomínios de autenticidade podem ser usados em campanhas de phishing. Isso é verdadeiro para sites mal-intencionados e também para registros MX que permitiriam que o ator de ameaça recebesse emails endereçados a um subdomínio legítimo de uma marca conhecida.

- **Riscos adicionais** – sites mal-intencionados podem ser usados para escalonar para outros ataques clássicos, como XSS, CSRF, CORS bypass e muito mais.



## <a name="preventing-dangling-dns-entries"></a>Impedindo entradas DNS pendente

Garantir que sua organização tenha implementado processos para evitar entradas DNS pendente e o subdomínio resultante takeovers seja uma parte crucial do seu programa de segurança.

As medidas preventivas disponíveis para você hoje estão listadas abaixo.


### <a name="use-azure-dns-alias-records"></a>Usar registros de alias do DNS do Azure

Os registros de [alias](https://docs.microsoft.com/azure/dns/dns-alias#scenarios) do DNS do Azure podem impedir referências pendentes ao acoplar o ciclo de vida de um registro DNS com um recurso do Azure. Por exemplo, considere um registro DNS qualificado como um registro de alias para apontar para um endereço IP público ou um perfil do Gerenciador de Tráfego. Se você excluir esses recursos subjacentes, o registro de alias de DNS se tornará um conjunto de registros vazio. Ele não faz mais referência ao recurso excluído. É importante observar que há limites para o que você pode proteger com registros de alias. Hoje, a lista está limitada a:

- Porta da frente do Azure
- Perfis do Gerenciador de Tráfego
- Pontos de extremidade da CDN (rede de distribuição de conteúdo) do Azure
- IPs Públicos

Apesar das ofertas de serviço limitadas atuais, recomendamos o uso de registros de alias para se defender do tomada de subdomínio sempre que possível.

[Saiba mais](https://docs.microsoft.com/azure/dns/dns-alias#capabilities) sobre os recursos dos registros de alias do DNS do Azure.



### <a name="use-azure-app-services-custom-domain-verification"></a>Usar a verificação de domínio personalizado do serviço de Azure App

Ao criar entradas DNS para Azure App serviço, crie um asuid. subdomínio Registro TXT com a ID de verificação de domínio. Quando existe um registro TXT, nenhuma outra assinatura do Azure pode validar o domínio personalizado, o que o leva. 

Esses registros não impedem que alguém crie o serviço de Azure App com o mesmo nome que está em sua entrada CNAME. Sem a capacidade de provar a propriedade do nome de domínio, os atores de ameaças não podem receber tráfego nem controlar o conteúdo.

[Saiba mais](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain) sobre como mapear um nome DNS personalizado existente para Azure app serviço.



### <a name="build-and-automate-processes-to-mitigate-the-threat"></a>Crie e automatize processos para atenuar a ameaça

Muitas vezes, os desenvolvedores e as equipes de operações executam processos de limpeza para evitar ameaças de DNS pendente. As práticas a seguir ajudarão a garantir que sua organização Evite perder essa ameaça. 

- **Criar procedimentos para prevenção:**

    - Instrua os desenvolvedores de aplicativos a redirecionar endereços sempre que eles excluirem recursos.

    - Coloque "remover a entrada DNS" na lista de verificações necessárias ao encerrar um serviço.

    - Coloque os [bloqueios de exclusão](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) em todos os recursos que tenham uma entrada DNS personalizada. Um bloqueio de exclusão serve como um indicador de que o mapeamento deve ser removido antes que o recurso seja desprovisionado. Medidas como essa podem funcionar apenas quando combinadas com programas de educação interna.

- **Criar procedimentos para descoberta:**

    - Examine os registros DNS regularmente para garantir que seus subdomínios estejam todos mapeados para os recursos do Azure que:

        - Exist-consulte as zonas DNS para ver os recursos que apontam para os subdomínios do Azure, como *. azurewebsites.net ou *. cloudapp.azure.com (consulte [esta lista de referência](azure-domains.md)).
        - Você tem certeza de que possui todos os recursos que seus subdomínios DNS estão direcionando.

    - Mantenha um catálogo de serviços de seus pontos de extremidade de FQDN (nome de domínio totalmente qualificado) do Azure e os proprietários do aplicativo. Para criar o catálogo de serviços, execute o seguinte script de consulta do grafo de recursos do Azure. Esse script projeta as informações do ponto de extremidade FQDN dos recursos aos quais você tem acesso e as gera em um arquivo CSV. Se você tiver acesso a todas as assinaturas para seu locatário, o script considerará todas essas assinaturas, conforme mostrado no script de exemplo a seguir. Para limitar os resultados a um conjunto específico de assinaturas, edite o script conforme mostrado.

        >[!IMPORTANT]
        > **Permissões** – execute a consulta como um usuário que tem acesso a todas as suas assinaturas do Azure. 
        >
        > **Limitações** – o grafo de recursos do Azure tem limites de limitação e de paginação que você deve considerar se tiver um ambiente grande do Azure. [Saiba mais](https://docs.microsoft.com/azure/governance/resource-graph/concepts/work-with-data) sobre como trabalhar com grandes conjuntos de dados de recursos do Azure. O script de exemplo a seguir usa o envio em lote de assinatura para evitar essas limitações.

        ```powershell
        
            # Fetch the full array of subscription IDs.
            $subscriptions = Get-AzSubscription

            $subscriptionIds = $subscriptions.Id
                    # Output file path and names
                    $date = get-date
                    $fdate = $date.ToString("MM-dd-yyy hh_mm_ss tt")
                    $fdate #log to console
                    $rpath = [Environment]::GetFolderPath("MyDocuments") + '\' # Feel free to update your path.
                    $rname = 'Tenant_FQDN_Report_' + $fdate + '.csv' # Feel free to update the document name.
                    $fpath = $rpath + $rname
                    $fpath #This is the output file of FQDN report.

            # queries
            $allTypesFqdnsQuery = "where type in ('microsoft.network/frontdoors',
                                    'microsoft.storage/storageaccounts',
                                    'microsoft.cdn/profiles/endpoints',
                                    'microsoft.network/publicipaddresses',
                                    'microsoft.network/trafficmanagerprofiles',
                                    'microsoft.containerinstance/containergroups',
                                    'microsoft.web/sites',
                                    'microsoft.web/sites/slots')
                        | extend FQDN = case(
                            type =~ 'microsoft.network/frontdoors', properties['cName'],
                            type =~ 'microsoft.storage/storageaccounts', parse_url(tostring(properties['primaryEndpoints']['blob'])).Host,
                            type =~ 'microsoft.cdn/profiles/endpoints', properties['hostName'],
                            type =~ 'microsoft.network/publicipaddresses', properties['dnsSettings']['fqdn'],
                            type =~ 'microsoft.network/trafficmanagerprofiles', properties['dnsConfig']['fqdn'],
                            type =~ 'microsoft.containerinstance/containergroups', properties['ipAddress']['fqdn'],
                            type =~ 'microsoft.web/sites', properties['defaultHostName'],
                            type =~ 'microsoft.web/sites/slots', properties['defaultHostName'],
                            '')
                        | project id, type, name, FQDN
                        | where isnotempty(FQDN)";

            $apiManagementFqdnsQuery = "where type =~ 'microsoft.apimanagement/service'
                        | project id, type, name,
                            gatewayUrl=parse_url(tostring(properties['gatewayUrl'])).Host,
                            portalUrl =parse_url(tostring(properties['portalUrl'])).Host,
                            developerPortalUrl = parse_url(tostring(properties['developerPortalUrl'])).Host,
                            managementApiUrl = parse_url(tostring(properties['managementApiUrl'])).Host,
                            gatewayRegionalUrl = parse_url(tostring(properties['gatewayRegionalUrl'])).Host,
                            scmUrl = parse_url(tostring(properties['scmUrl'])).Host,
                            additionaLocs = properties['additionalLocations']
                        | mvexpand additionaLocs
                        | extend additionalPropRegionalUrl = tostring(parse_url(tostring(additionaLocs['gatewayRegionalUrl'])).Host)
                        | project id, type, name, FQDN = pack_array(gatewayUrl, portalUrl, developerPortalUrl, managementApiUrl, gatewayRegionalUrl, scmUrl,             
                            additionalPropRegionalUrl)
                        | mvexpand FQDN
                        | where isnotempty(FQDN)";

            $queries = @($allTypesFqdnsQuery, $apiManagementFqdnsQuery);

            # Paging helper cursor
            $Skip = 0;
            $First = 1000;

            # If you have large number of subscriptions, process them in batches of 2,000.
            $counter = [PSCustomObject] @{ Value = 0 }
            $batchSize = 2000
            $response = @()

            # Group the subscriptions into batches.
            $subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

            foreach($query in $queries)
            {
                # Run the query for each subscription batch with paging.
                foreach ($batch in $subscriptionsBatch)
                { 
                    $Skip = 0; #Reset after each batch.

                    $response += do { Start-Sleep -Milliseconds 500;   if ($Skip -eq 0) {$y = Search-AzGraph -Query $query -First $First -Subscription $batch.Group ; } `
                    else {$y = Search-AzGraph -Query $query -Skip $Skip -First $First -Subscription $batch.Group } `
                    $cont = $y.Count -eq $First; $Skip = $Skip + $First; $y; } while ($cont)
                }
            }

            # View the completed results of the query on all subscriptions
            $response | Export-Csv -Path $fpath -Append  

        ```

        Lista de tipos e seus `FQDNProperty` valores, conforme especificado na consulta do grafo de recursos anterior:

        |Nome do recurso  | `<ResourceType>`  | `<FQDNproperty>`  |
        |---------|---------|---------|
        |Porta da frente do Azure|microsoft.network/frontdoors|Properties. cName|
        |Armazenamento do Blobs do Azure|microsoft.storage/storageaccounts|Propriedades. primaryEndpoints. blob|
        |CDN do Azure|microsoft.cdn/profiles/endpoints|Propriedades. hostName|
        |Endereços IP públicos|microsoft.network/publicipaddresses|Properties. dnsSettings. FQDN|
        |Gerenciador de Tráfego do Azure|microsoft.network/trafficmanagerprofiles|Properties. dnsConfig. FQDN|
        |Azure Container Instance|microsoft.containerinstance/containergroups|Propriedades. ipAddress. FQDN|
        |Gerenciamento de API do Azure|microsoft.apimanagement/service|Propriedades. hostnameConfigurations. hostName|
        |Serviço de aplicativo do Azure|microsoft.web/sites|Propriedades. DefaultHostName|
        |Azure App slots de serviço|microsoft.web/sites/slots|Propriedades. DefaultHostName|


- **Criar procedimentos para correção:**
    - Quando as entradas DNS do pendente forem encontradas, sua equipe precisará investigar se algum comprometimento ocorreu.
    - Investigue por que o endereço não foi redirecionado quando o recurso foi encerrado.
    - Exclua o registro DNS se ele não estiver mais em uso ou aponte-o para o recurso correto do Azure (FQDN) de propriedade de sua organização.
 

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os serviços relacionados e os recursos do Azure que você pode usar para se defender do subdomínio tomada, consulte as páginas a seguir.

- [O DNS do Azure dá suporte ao uso de registros de alias para domínios personalizados](https://docs.microsoft.com/azure/dns/dns-alias#prevent-dangling-dns-records)

- [Usar a ID de verificação de domínio ao adicionar domínios personalizados no serviço Azure App](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain#get-domain-verification-id) 

- [Início Rápido: Execute a primeira consulta ao Resource Graph usando o Azure PowerShell](https://docs.microsoft.com/azure/governance/resource-graph/first-query-powershell)
