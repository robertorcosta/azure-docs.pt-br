---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 2d2a82552a846cedfa5da3bb6ec6df8a40b67732
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671534"
---
Embora o recurso seja fácil de configurar, isso não significa que sua experiência estará livre de problemas. Se você encontrar problemas ao acessar o ponto de extremidade desejado, há utilitários que você pode usar para testar a conectividade do console do aplicativo. Há dois consoles que você pode usar. Uma é o console do Kudu e a outra é o console no portal do Azure. Para acessar o console do Kudu do aplicativo, vá para Ferramentas -> Kudu. Você também pode acessar o console do Kudo em [SiteName]. SCM. azurewebsites. net. Depois que o site for carregado, vá para a guia Console de depuração. Para acessar o console do portal do Azure hospedado, em seguida, em seu aplicativo, acesse ferramentas-> console. 

#### <a name="tools"></a>Ferramentas
As ferramentas **ping**, **nslookup**e **tracert** não funcionarão no console do devido a restrições de segurança. Para preencher o void, duas ferramentas separadas são adicionadas. Para testar a funcionalidade do DNS, adicionamos uma ferramenta chamada nameresolver.exe. A sintaxe do é:

    nameresolver.exe hostname [optional: DNS Server]

Você pode usar **nameresolver** para verificar os nomes de host de que seu aplicativo depende. Dessa forma, você pode testar se não há nada mal configurado no DNS ou se não tem acesso ao seu servidor DNS. Você pode ver o servidor DNS que seu aplicativo usará no console examinando as variáveis de ambiente WEBSITE_DNS_SERVER e WEBSITE_DNS_ALT_SERVER.

A próxima ferramenta permite testar a conectividade do TCP de uma combinação de host e porta. Essa ferramenta é chamada **tcpping** e a sintaxe é:

    tcpping.exe hostname [optional: port]

O utilitário **tcpping** informa se você pode acessar um host específico e uma porta. Ele só pode mostrar êxito se: houver um aplicativo escutando na combinação de porta e host, e houver acesso de rede de seu aplicativo para o host e porta especificados.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Depurar o acesso a recursos hospedados por VNet
Há várias coisas que podem impedir que seu aplicativo alcance um host e uma porta específicos. Na maioria das vezes, é uma dentre três coisas:

* **Há um firewall no caminho.** Se houver um firewall no caminho, você atingirá o tempo limite de TCP. O tempo limite de TCP é 21 segundos neste caso. Use a ferramenta **tcpping** para testar a conectividade. Tempos limite de TCP podem ocorrer por muitos motivos, além de firewalls, mas comece com isso. 
* **O DNS não está acessível.** O tempo limite do DNS é de três segundos por servidor DNS. Se você tiver dois servidores DNS, o tempo limite será de seis segundos. Use nameresolver para ver se o DNS está funcionando. Lembre-se de que você não pode usar nslookup, pois ele não usa o DNS com o qual sua VNet está configurada. Se estiver inacessível, você pode ter um firewall ou NSG bloqueando o acesso ao DNS ou pode estar inoperante.

Se esses itens não responderem a seus problemas, veja primeiro algo como: 

**Integração de VNet regional**
* seu destino é um endereço não RFC1918 e você não tem WEBSITE_VNET_ROUTE_ALL definido como 1?
* Há um NSG bloqueando a saída de sua sub-rede de integração?
* Se estiver indo pelo ExpressRoute ou por uma VPN, o gateway local será configurado para rotear o tráfego de volta para o Azure? Se você puder acessar pontos de extremidade em sua VNet, mas não no local, verifique suas rotas.
* Você tem permissões suficientes para definir a delegação na sub-rede de integração? Durante a configuração de integração VNet regional, sua sub-rede de integração será delegada para Microsoft. Web. A interface do usuário de integração VNet delegará a sub-rede ao Microsoft. Web automaticamente. Se sua conta não tiver permissões de rede suficientes para definir a delegação, você precisará de alguém que possa definir atributos em sua sub-rede de integração para delegar a sub-rede. Para delegar manualmente a sub-rede de integração, vá para a interface do usuário da sub-rede da rede virtual do Azure e defina delegação para Microsoft. Web. 

**Integração VNet necessária do gateway**
* é o intervalo de endereços de ponto a site nos intervalos RFC 1918 (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255)?
* O Gateway é mostrado como estando em execução no Portal? Se o gateway estiver inativo, ative-o.
* Os certificados são mostrados como estando em sincronia ou você suspeita que a configuração de rede foi alterada?  Se os certificados estiverem fora de sincronia ou se você suspeitar de que houve uma alteração na configuração da VNet que não foi sincronizada com suas ASPs, pressione "sincronizar rede".
* Se estiver passando por uma VPN, o gateway local configurado para rotear o tráfego de volta para o Azure? Se você puder acessar pontos de extremidade em sua VNet, mas não no local, verifique suas rotas.
* Você está tentando usar um gateway de coexistência que dá suporte a ponto a site e ExpressRoute? Não há suporte para gateways de coexistência com integração VNet.

A depuração de problemas de rede é um desafio porque não é possível ver o que está bloqueando o acesso a uma combinação de hosts específicos: porta. Algumas das causas incluem:

* Você tem um firewall em seu host que impede o acesso à porta do aplicativo do intervalo de IP ponto a site. O cruzamento de sub-redes geralmente exige acesso Público.
* Seu host de destino está inoperante.
* Seu aplicativo está inoperante.
* Você tinha o IP ou o nome de host incorreto.
* Seu aplicativo está ouvindo em uma porta diferente da que você esperava. Você pode corresponder a sua ID de processo com a porta de escuta usando "netstat -aon" no host do ponto de extremidade. 
* Os grupos de segurança de rede são configurados de forma que impeçam o acesso ao host do seu aplicativo e à porta do intervalo de IP ponto a site.

Lembre-se de que você não sabe qual endereço seu aplicativo realmente usará. Pode ser qualquer endereço na sub-rede de integração ou intervalo de endereços de ponto a site, portanto, você precisa permitir o acesso de todo o intervalo de endereços. 

As etapas de depuração adicionais incluem:

* Conecte-se a uma VM em sua VNet e tente acessar a porta do host de recursos: a partir daí. Para testar o acesso TCP, use o comando do PowerShell **test-netconnection**. A sintaxe do é:

      test-netconnection hostname [optional: -Port]

* abrir um aplicativo em uma VM e testar o acesso ao host e à porta do console a partir do seu aplicativo usando o **tcpping**

#### <a name="on-premises-resources"></a>Recursos locais ####

Se o aplicativo não puder acessar um recurso local, verifique se é possível acessar o recurso da sua VNet. Use o comando do PowerShell **test-netconnection** para verificar se há acesso TCP. Se sua VM não conseguir acessar o recurso local, sua conexão VPN ou ExpressRoute poderá não estar configurada corretamente.

Se a VM hospedada na VNet puder acessar seu sistema local, mas seu aplicativo não, isso provavelmente ocorrerá devido a um dos seguintes motivos:

* Suas rotas não estão configuradas com os intervalos de endereços de sua sub-rede ou ponto a site em seu gateway local.
* Os grupos de segurança de rede estão bloqueando o acesso ao intervalo de IP ponto a site.
* Seus firewalls locais estão bloqueando o tráfego do intervalo de IP ponto a site.
* Você está tentando acessar um endereço não RFC 1918 usando o recurso de integração VNet regional.