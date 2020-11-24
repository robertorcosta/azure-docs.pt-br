---
title: Configurar uma rede virtual-cache Premium do Azure para Redis
description: Saiba como criar e gerenciar suporte de Rede Virtual para o Cache do Azure na camada Premium para instâncias do Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: 5fd82105c94bb9be2d07c8843834465821acd8bc
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95803786"
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-cache-for-redis"></a>Como configurar suporte de Rede Virtual para um Cache do Azure Premium para Redis
O Cache do Azure para Redis apresenta diferentes ofertas de cache que fornecem flexibilidade na escolha do tamanho e dos recursos de cache, incluindo recursos da camada Premium como clustering, persistência e suporte de rede virtual. Uma VNet é uma rede privada na nuvem. Quando uma instância do Cache do Azure para Redis é configurada com uma rede virtual, ela não é endereçável publicamente e somente pode ser acessada de máquinas virtuais e aplicativos dentro da rede virtual. Este artigo descreve como configurar suporte de rede virtual para uma instância do Cache do Azure Premium para Redis.

> [!NOTE]
> O Cache do Azure para Redis dá suporte tanto para VNets do Resource Manager como Clássicas.
> 
> 

## <a name="why-vnet"></a>Por que a VNet?
A implantação de[ VNet (Rede Virtual do Microsoft Azure)](https://azure.microsoft.com/services/virtual-network/) fornece segurança e isolamento aprimorados para o Cache do Azure para Redis, bem como para sub-redes, políticas de controle de acesso e outros recursos para restringir ainda mais o acesso.

## <a name="virtual-network-support"></a>Suporte de rede virtual
O suporte da VNet (Rede Virtual) é configurado na folha **Novo Cache do Azure para Redis** durante a criação do cache. 

1. Para criar um cache Premium, entre no [portal do Azure](https://portal.azure.com) e selecione **criar um recurso**. Observe, além de criar caches no portal do Azure, você também pode criá-los usando modelos do Resource Manager, o PowerShell ou o CLI do Azure. Para obter mais informações sobre a criação de um Cache do Azure para Redis, consulte [Criar um cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Criar recurso.":::
   
2. Na página **Novo**, selecione **Bancos de dados** e, em seguida, **Cache do Azure para Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Selecionar o Cache do Azure para Redis.":::

3. Na página **novo cache Redis** , defina as configurações para seu novo cache Premium.
   
   | Configuração      | Valor sugerido  | DESCRIÇÃO |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nome DNS** | Insira um nome global exclusivo. | O nome de cache precisa ser uma cadeia de caracteres com 1 a 63 caracteres que contém somente números, letras ou hifens. O nome precisa começar e terminar com um número ou uma letra e não pode conter hifens consecutivos. O *nome do host* de sua instância de cache será *\<DNS name>.redis.cache.windows.net*. | 
   | **Assinatura** | Na lista suspensa e selecione sua assinatura. | A assinatura na qual essa nova instância do Cache do Azure para Redis será criada. | 
   | **Grupo de recursos** | Menu suspenso e selecione um grupo de recursos ou selecione **criar novo** e insira um novo nome de grupo de recursos. | Nome do grupo de recursos no qual o cache e outros recursos serão criados. Ao colocar todos os seus recursos de aplicativos em um só grupo de recursos, você pode gerenciá-los ou excluí-los juntos com facilidade. | 
   | **Localidade** | Menu suspenso e selecione um local. | Selecione uma [região](https://azure.microsoft.com/regions/) perto de outros serviços que usarão o cache. |
   | **Tipo de cache** | Menu suspenso e selecione um cache Premium para configurar os recursos premium. Para obter detalhes, veja [preços do cache do Azure para Redis](https://azure.microsoft.com/pricing/details/cache/). |  O tipo de preço determina o tamanho, o desempenho e os recursos disponíveis para o cache. Para obter mais informações, confira [Visão geral do Cache do Azure para Redis](cache-overview.md). |

4. Selecione a guia **Rede** ou clique no botão **Rede** na parte inferior da página.

5. Na guia **rede** , selecione **redes virtuais** como seu método de conectividade. Para usar uma nova rede virtual, crie-a primeiro seguindo as etapas em [criar uma rede virtual usando o portal do Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network) ou [criar uma rede virtual (clássica) usando o portal do Azure](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal) e, em seguida, retorne à folha **novo cache do Azure para Redis** para criar e configurar seu cache Premium.

   > [!IMPORTANT]
   > Ao implantar um Cache do Azure para Redis em uma VNet do Resource Manager, o cache deve estar em uma sub-rede dedicada que não contém nenhum outro recurso, exceto o Cache do Azure para Redis. Se uma tentativa de implantar um Cache do Azure para Redis for realizada em uma VNet do Resource Manager para uma sub-rede contendo outros recursos, a implantação falhará.
   > 
   > 

   | Configuração      | Valor sugerido  | Descrição |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Rede Virtual** | Na lista suspensa e selecione sua rede virtual. | Selecione uma rede virtual que esteja na mesma assinatura e local que o cache. | 
   | **Sub-rede** | Na lista suspensa e selecione sua sub-rede. | O intervalo de endereços da sub-rede deve estar na notação CIDR (por exemplo, 192.168.1.0/24). Ele deve estar contido no espaço de endereço da rede virtual. | 
   | **Endereço IP estático** | Adicional Insira um endereço IP estático. | Se você não especificar um IP estático, um endereço IP será escolhido automaticamente. | 

   > [!IMPORTANT]
   > O Azure reserva alguns endereços IP em cada sub-rede, os quais não podem ser usados. O primeiro e o último endereço IP das sub-redes são reservados para fins de conformidade de protocolo, juntamente com três outros endereços usados para os serviços do Azure. Para obter mais informações, consulte [Existem restrições quanto ao uso de endereços IP dentro dessas sub-redes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
   > 
   > Além dos endereços IP usados pela infraestrutura do Azure VNET, cada instância de Redis na sub-rede usa dois endereços IP por fragmento e um endereço IP adicional para o balanceador de carga. Considera-se que um cache não clusterizado tem um fragmento.
   > 
   > 

6. Selecione **Próximo: Avançado** ou clique no botão **Próximo: Avançado** na parte inferior da página.

7. Na guia **avançado** de uma instância de cache Premium, defina as configurações para porta não TLS, clustering e persistência de dados. 

8. Selecione **Próximo: Marcas** ou clique no botão **Próximo: Botão** Categorias na parte inferior da página.

9. Opcionalmente, na guia **Marcas**, insira o nome e o valor caso deseje categorizar o recurso. 

10. Selecione **Examinar + criar**. Você será levado para a guia Examinar + criar, na qual o Azure validará a configuração.

11. Depois que a mensagem em verde Validação aprovada for exibida, selecione **Criar**.

A criação do cache demora um pouco. Monitore o progresso na página **Visão Geral** do Cache do Azure para Redis. Quando o **Status** for mostrado como **Em execução**, o cache estará pronto para uso. Após o cache ser criado, você pode exibir a configuração da VNet clicando em **Rede Virtual** no menu **Recursos**.

![Rede virtual][redis-cache-vnet-info]

Para conectar a instância de Cache do Azure para Redis usando uma VNet, especifique o nome do host do cache na cadeia de conexão, conforme mostrado no exemplo a seguir:

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

## <a name="azure-cache-for-redis-vnet-faq"></a>Perguntas frequentes sobre o Cache do Azure para Redis
A lista a seguir contém respostas para perguntas frequentes sobre o dimensionamento do Cache do Azure para Redis.

* Quais são alguns problemas comuns de configuração incorreta com o Cache do Azure para Redis e VNets?
* [Como posso verificar se meu cache está funcionando em uma VNET?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* Ao tentar conectar o Cache do Azure para Redis em uma VNET, por que estou recebendo um erro informando que o certificado remoto é inválido?
* [Posso usar VNets com um cache básico ou Standard?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* Por que a criação de um Cache do Azure para Redis falha em algumas sub-redes, mas não em outras?
* [Quais são os requisitos de espaço de endereço de sub-rede?](#what-are-the-subnet-address-space-requirements)
* [Todos os recursos de cache funcionam ao hospedar um cache em uma rede virtual?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-vnets"></a>Quais são alguns problemas comuns de configuração incorreta com o Cache do Azure para Redis e VNets?
Quando o Cache do Azure para Redis for hospedado em uma VNet, serão usadas as portas nas tabelas a seguir. 

>[!IMPORTANT]
>Se as portas nas tabelas a seguir estiverem bloqueadas, é possível que o cache não funcione corretamente. Ter uma ou mais dessas portas bloqueadas é o problema mais comum de configuração incorreta ao usar o Cache do Azure para Redis em uma rede virtual.
> 
> 

- [Requisitos de porta de saída](#outbound-port-requirements)
- [Requisitos de porta de entrada](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Requisitos de porta de saída

Há nove requisitos de porta de saída. As solicitações de saída nesses intervalos são de saída para outros serviços necessários para que o cache funcione ou seja interno à sub-rede Redis para comunicação entre nós. Para a replicação geográfica, existem outros requisitos de saída para a comunicação entre as sub-redes do cache primário e de réplica.

| Porta(s) | Direção | Protocolo de Transporte | Finalidade | IP local | IP remoto |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Saída |TCP |Dependências de Redis no Armazenamento do Azure/PKI (Internet) | (Sub-rede Redis) |* <sup>quatro</sup> |
| 443 | Saída | TCP | Dependência Redis em Azure Key Vault e Azure Monitor | (Sub-rede Redis) | AzureKeyVault, AzureMonitor <sup>1</sup> |
| 53 |Saída |TCP/UDP |Dependências Redis no DNS (Internet/Rede Virtual) | (Sub-rede Redis) | 168.63.129.16 e 169.254.169.254 <sup>2</sup> e qualquer servidor DNS personalizado para a sub-rede <sup>3</sup> |
| 8443 |Saída |TCP |Comunicações internas para Redis | (Sub-rede Redis) | (Sub-rede Redis) |
| 10221-10231 |Saída |TCP |Comunicações internas para Redis | (Sub-rede Redis) | (Sub-rede Redis) |
| 20226 |Saída |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis) |
| 13000-13999 |Saída |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis) |
| 15000-15999 |Saída |TCP |Comunicações internas para Redis e Geo-Replication | (Sub-rede Redis) |(Sub-rede Redis) (Sub-rede par de réplica geográfica) |
| 6379-6380 |Saída |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis) |

<sup>1</sup> você pode usar as marcas de serviço ' AzureKeyVault ' e ' AzureMonitor ' com grupos de segurança de rede do Resource Manager.

<sup>2</sup> esses endereços IP pertencentes à Microsoft são usados para resolver a VM do host que serve o DNS do Azure.

<sup>3</sup> não é necessário para sub-redes sem servidor DNS personalizado ou caches Redis mais recentes que ignoram o DNS personalizado.

<sup>4</sup> para obter mais informações, consulte [requisitos adicionais de conectividade de rede de VNET](#additional-vnet-network-connectivity-requirements).

#### <a name="geo-replication-peer-port-requirements"></a>Requisitos de porta de pares de replicação geográfica

Se você estiver usando a replicação geográfica entre caches em redes virtuais do Azure, observe que a configuração recomendada é desbloquear as portas 15000-15999 para toda a sub-rede nas direções de entrada e saída para ambos os caches, para que todos os componentes da réplica na sub-rede possam se comunicar diretamente entre si mesmo no caso de um failover geográfico futuro.

#### <a name="inbound-port-requirements"></a>Requisitos de porta de entrada

Há oito requisitos de intervalo de portas de entrada. As solicitações de entrada nesses intervalos são recebidas de outros serviços hospedados na mesma VNET ou internas para as comunicações de sub-rede Redis.

| Porta(s) | Direção | Protocolo de Transporte | Finalidade | IP local | IP remoto |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Entrada |TCP |Comunicação do cliente com o Redis, Balanceamento de Carga do Azure | (Sub-rede Redis) | (Sub-rede Redis), (sub-rede do cliente), AzureLoadBalancer <sup>1</sup> |
| 8443 |Entrada |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis) |
| 8500 |Entrada |TCP/UDP |Balanceamento de Carga do Azure | (Sub-rede Redis) | AzureLoadBalancer |
| 10221-10231 |Entrada |TCP |Comunicação de cliente com clusters Redis, comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis), AzureLoadBalancer, (sub-rede do cliente) |
| 13000-13999 |Entrada |TCP |Comunicação do cliente com Clusters Redis, Balanceamento de Carga do Azure | (Sub-rede Redis) | (Sub-rede Redis), (sub-rede do cliente), AzureLoadBalancer |
| 15000-15999 |Entrada |TCP |Comunicação de cliente com clusters Redis, balanceamento de carga do Azure e Geo-Replication | (Sub-rede Redis) | (Sub-rede Redis), (sub-rede do cliente), AzureLoadBalancer, (sub-rede par de réplica geográfica) |
| 16001 |Entrada |TCP/UDP |Balanceamento de Carga do Azure | (Sub-rede Redis) | AzureLoadBalancer |
| 20226 |Entrada |TCP |Comunicações internas para Redis | (Sub-rede Redis) |(Sub-rede Redis) |

<sup>1</sup> você pode usar a marca de serviço ' AzureLoadBalancer ' (Gerenciador de recursos) (ou ' AZURE_LOADBALANCER ' para clássico) para criar as regras de NSG.

#### <a name="additional-vnet-network-connectivity-requirements"></a>Requisitos de conectividade de rede VNET adicionais

Há requisitos de conectividade de rede para o Cache do Azure para Redis que podem não ser atendidos inicialmente em uma rede virtual. O Cache do Azure para Redis requer que todos os itens a seguir funcionem corretamente quando usados em uma rede virtual.

* Conectividade de rede de saída para pontos de extremidade do Armazenamento do Azure em todo o mundo. Isso inclui pontos de extremidade localizados na mesma região que a instância do Cache do Azure para Redis, bem como pontos de extremidade de armazenamento localizados em **outras** regiões do Azure. Os pontos de extremidade do armazenamento do Azure são resolvidos nos seguintes domínios DNS: *Table.Core.Windows.net*, *BLOB.Core.Windows.net*, *Queue.Core.Windows.net* e *File.Core.Windows.net*. 
* Conectividade de rede de saída para *OCSP.DigiCert.com*, *crl4.DigiCert.com*, *OCSP.msocsp.com*, *mscrl.Microsoft.com*, *crl3.DigiCert.com*, *cacerts.DigiCert.com*, *oneocsp.Microsoft.com* e *CRL.Microsoft.com*. Essa conectividade é necessária para dar suporte à funcionalidade TLS/SSL.
* A configuração DNS para a rede virtual deve ser capaz de resolver todos os pontos de extremidade e domínios mencionados nos pontos anteriores. Esses requisitos de DNS podem ser atendidos, garantindo que uma infraestrutura de DNS válida seja configurada e mantida para a rede virtual.
* Conectividade de rede de saída para os seguintes Azure Monitor pontos de extremidade, que são resolvidos nos seguintes domínios DNS: *shoebox2-Black.shoebox2.Metrics.nsatc.net*, *North-prod2.prod2.Metrics.nsatc.net*, *azglobal-Black.azglobal.Metrics.nsatc.net*, *shoebox2-Red.shoebox2.Metrics.nsatc.net*, *East-prod2.prod2.Metrics.nsatc.net*, *azglobal-Red.azglobal.Metrics.nsatc.net*.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>Como verificar se o cache está funcionando em uma VNET?

>[!IMPORTANT]
>Ao se conectar a um cache do Azure para a instância Redis hospedada em uma VNET, os clientes de cache devem estar na mesma VNET ou em uma VNET com emparelhamento VNET habilitado na mesma região do Azure. O Emparelhamento VNET global não tem suporte no momento. Isso inclui todos os aplicativos de teste ou ferramentas de diagnóstico do ping. Independentemente de onde o aplicativo cliente está hospedado, os grupos de segurança de rede ou outras camadas de rede devem ser configurados de modo que o tráfego de rede do cliente tenha permissão para alcançar a instância Redis.
>
>

Uma vez configurados os requisitos de porta conforme descrito na seção anterior, você pode verificar se o cache está funcionando executando as etapas a seguir.

- [Reinicialize](cache-administration.md#reboot) todos os nós de cache. Se todas as dependências de cache necessárias não podem ser acessadas (conforme documentado em [Requisitos de porta de entrada](cache-how-to-premium-vnet.md#inbound-port-requirements) e [Requisitos de porta de saída](cache-how-to-premium-vnet.md#outbound-port-requirements)), não será possível reiniciar o cache com êxito.
- Uma vez que os nós de cache tenham reinicializado (conforme relatado pelo status de cache no portal do Azure), você poderá executar os testes a seguir:
  - Execute o ping no ponto de extremidade do cache (usando a porta 6380) de um computador que esteja na mesma VNET que o cache, usando [tcping](https://www.elifulkerson.com/projects/tcping.php). Por exemplo:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Se a ferramenta `tcping` relata que a porta está aberta, o cache está disponível para a conexão de clientes na VNET.

  - Outra maneira de testar é criar um cliente de cache de teste (que pode ser um simples aplicativo de console usando StackExchange.Redis) que se conecta ao cache e adiciona e recupera alguns itens do cache. Instale o aplicativo de cliente de exemplo em uma VM que está na mesma VNET que o cache e execute-o para verificar a conectividade com o cache.


### <a name="when-trying-to-connect-to-my-azure-cache-for-redis-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid"></a>Ao tentar conectar o Cache do Azure para Redis em uma VNET, por que estou recebendo um erro informando que o certificado remoto é inválido?

Ao tentar conectar um Cache do Azure para Redis em uma VNET, você verá um erro de validação de certificado como este:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

A causa pode ser que você está se conectando ao host pelo endereço IP. É recomendável usar o nome do host. Em outras palavras, use o seguinte:     

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Evite usar o endereço IP semelhante à seguinte cadeia de caracteres de conexão:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Se não for possível resolver o nome DNS, algumas bibliotecas de cliente incluem opções como `sslHost` que é fornecido pelo cliente StackExchange.Redis. Isso permite que você substitua o nome do host usado para validação de certificado. Por exemplo:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>Posso usar VNets com um cache básico ou Standard?
As VNets podem ser usadas apenas com os caches Premium.

### <a name="why-does-creating-an-azure-cache-for-redis-fail-in-some-subnets-but-not-others"></a>Por que a criação de um Cache do Azure para Redis falha em algumas sub-redes, mas não em outras?
Se você estiver implantando um cache do Azure para Redis em uma VNet, o cache deverá estar em uma sub-rede dedicada que não contém nenhum outro tipo de recurso. Se for feita uma tentativa de implantar um cache do Azure para Redis em uma sub-rede VNet do Resource Manager que contenha outros recursos (como gateways de aplicativo, NAT de saída e assim por diante), a implantação normalmente falhará. Você deve excluir os recursos existentes de outros tipos antes de poder criar um novo cache do Azure para Redis.

Você também deve ter endereços IP suficientes disponíveis na sub-rede.

### <a name="what-are-the-subnet-address-space-requirements"></a>Quais são os requisitos de espaço de endereço de sub-rede?
O Azure reserva alguns endereços IP em cada sub-rede, os quais não podem ser usados. O primeiro e o último endereço IP das sub-redes são reservados para fins de conformidade de protocolo, juntamente com três outros endereços usados para os serviços do Azure. Para obter mais informações, consulte [Existem restrições quanto ao uso de endereços IP dentro dessas sub-redes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Além dos endereços IP usados pela infraestrutura de VNET do Azure, cada instância de Redis na sub-rede usa dois endereços IP por fragmento de cluster (além de endereços IP adicionais para réplicas adicionais, se houver) e um endereço IP adicional para o balanceador de carga. Considera-se que um cache não clusterizado tem um fragmento.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Todos os recursos de cache funcionam ao hospedar um cache em uma rede virtual?
Quando o seu cache faz parte de uma VNET, somente os clientes na VNET podem acessar o cache. Como resultado, os recursos de gerenciamento de cache a seguir não funcionarão neste momento.

* Console do Redis – como o console do Redis é executado no navegador local, que geralmente está em um computador de desenvolvedor que não está conectado à VNET, ele não pode se conectar ao seu cache.


## <a name="use-expressroute-with-azure-cache-for-redis"></a>Usar ExpressRoute com Cache do Azure para Redis

Os clientes podem conectar um circuito da [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) à infraestrutura de rede virtual, estendendo, assim, a rede local até o Azure. 

Por padrão, um circuito ExpressRoute recém-criado não executa túnel forçado (anúncio de uma rota padrão, 0.0.0.0/0) em uma VNET. Como resultado, a conectividade da Internet de saída é permitida diretamente da VNET e os aplicativos clientes podem conectar outros pontos de extremidade do Azure, incluindo o Cache do Azure para Redis.

No entanto, uma configuração de cliente comum é usar o túnel forçado (anunciar uma rota padrão), que força o tráfego de Internet de saída para o fluxo local. Esse fluxo de tráfego interromperá a conectividade com o Cache do Azure para Redis se o tráfego de saída for bloqueado no local, de modo que a instância do Cache do Azure para Redis não consiga comunicar-se com suas dependências.

A solução é definir uma (ou mais) UDRs (rotas definidas pelo usuário) na sub-rede contendo o Cache do Azure para Redis. Uma UDR define rotas específicas de sub-rede que serão consideradas no lugar da rota padrão.

Se possível, é recomendável usar a seguinte configuração:

* A configuração de ExpressRoute anuncia 0.0.0.0/0 e, por padrão, encapsula à força todo o tráfego de saída no local.
* A UDR aplicada à sub-rede contendo o Cache do Azure para Redis define 0.0.0.0/0 com uma rota de trabalho para tráfego TCP/IP para a Internet pública, por exemplo, configurando o próximo tipo salto para 'Internet'.

O efeito combinado dessas etapas é que a UDR do nível de sub-rede tem precedência sobre o túnel forçado do ExpressRoute, garantindo o acesso à Internet de saída do Cache do Azure para Redis.

Conectar uma instância do Cache do Azure para Redis de um aplicativo local usando o ExpressRoute não é um cenário de uso típico devido a motivos de desempenho (para melhor desempenho, os clientes do Cache do Azure para Redis devem estar na mesma região do Cache do Azure para Redis).

>[!IMPORTANT] 
>As rotas definidas em um UDR **devem** ser específicas o suficiente para ter precedência sobre todas as rotas anunciadas pela configuração do ExpressRoute. O exemplo a seguir usa o intervalo de endereços 0.0.0.0/0 amplo, e potencialmente pode ser substituído por acidente pelos anúncios de rota usando intervalos de endereços mais específicos.

>[!WARNING]  
>O Cache do Azure para redis não tem suporte com configurações do ExpressRoute que **incorretamente cruzam anúncios de rotas do caminho de emparelhamento público para o caminho de emparelhamento privado**. As configurações de ExpressRoute com emparelhamento público definido receberão anúncios de rota da Microsoft para um grande conjunto de intervalos de endereços IP do Microsoft Azure. Se esses intervalos de endereço forem anunciados incorretamente de modo cruzado no caminho de emparelhamento privado, o resultado será que todos os pacotes de rede de saída da sub-rede da instância do Cache do Azure para Redis serão incorretamente encapsulados para a infraestrutura de rede local do cliente. Esse fluxo de rede interrompe o Cache do Azure para Redis. A solução para esse problema é parar as rotas de anúncios cruzados do caminho de emparelhamento público para o caminho de emparelhamento particular.


Informações básicas sobre as rotas definidas pelo usuário estão disponíveis nesta [visão geral](../virtual-network/virtual-networks-udr-overview.md).

Para obter mais informações sobre o ExpressRoute, consulte [Visão geral técnica do ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o cache do Azure para recursos do Redis.

* [Cache do Azure para camadas de serviço do Redis Premium](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
