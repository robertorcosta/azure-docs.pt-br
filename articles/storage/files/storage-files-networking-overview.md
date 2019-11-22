---
title: Considerações de rede dos Arquivos do Azure | Microsoft Docs
description: Uma visão geral das opções de rede para Arquivos do Azure.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 596479652478bffb6d18a90fc53d5972b3839408
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73126532"
---
# <a name="azure-files-networking-considerations"></a>Considerações de rede dos Arquivos do Azure 
Você pode se conectar a um compartilhamento de arquivo do Azure de duas maneiras:

- Acessando o compartilhamento diretamente por meio dos protocolos SMB ou FileREST. Esse padrão de acesso é empregado principalmente quando é possível eliminar o máximo possível de servidores locais.
- Criando um cache do compartilhamento de arquivo do Azure em um servidor local com a Sincronização de Arquivos do Azure e acessando os dados do compartilhamento de arquivo do servidor local com o protocolo de sua escolha (SMB, NFS, FTPS etc.) para seu caso de uso. Esse padrão de acesso é útil porque ele combina o melhor de desempenho local e escala de nuvem e serviços anexáveis sem servidor, como o Backup do Azure.

Este artigo se concentra em como configurar a rede para quando seu caso de uso chamar o acesso ao compartilhamento de arquivo do Azure diretamente, em vez de usar a Sincronização de Arquivos do Azure. Para obter mais informações sobre considerações de rede para uma implantação da Sincronização de Arquivos do Azure, consulte [Como configurar as definições de firewall e proxy da Sincronização de Arquivos do Azure](storage-sync-files-firewall-and-proxy.md).

## <a name="storage-account-settings"></a>Configurações da conta de armazenamento
Uma conta de armazenamento é um constructo de gerenciamento que representa um pool compartilhado de armazenamento no qual você pode implantar vários compartilhamentos de arquivos bem como outros recursos de armazenamento, como filas ou contêineres de blob. As contas de armazenamento do Azure expõem dois conjuntos básicos de configurações para proteger a rede: criptografia em trânsito e firewalls e VNets (redes virtuais).

### <a name="encryption-in-transit"></a>Criptografia em trânsito
Por padrão, todas as contas de armazenamento do Azure têm criptografia em trânsito habilitada. Isso significa que quando você montar um compartilhamento de arquivo via SMB ou acessá-lo por meio do protocolo FileREST (por exemplo, por meio do portal do Azure, do PowerShell/CLI ou de SDKs do Azure), os Arquivos do Azure só permitirão a conexão se for feita com o SMB 3.0 e com criptografia ou HTTPS. Os clientes que não são compatíveis com SMB 3.0 ou os clientes que são compatíveis com SMB 3.0, mas não com a criptografia SMB, não poderão montar o compartilhamento de arquivo do Azure se a criptografia em trânsito estiver habilitada. Para obter mais informações sobre quais sistemas operacionais são compatíveis com o SMB 3.0 com criptografia, consulte nossa documentação detalhada para [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md) e [Linux](storage-how-to-use-files-linux.md). Todas as versões atuais do PowerShell, da CLI e dos SDKs são compatíveis com HTTPS.  

Você pode desabilitar a criptografia em trânsito para uma conta de armazenamento do Azure. Quando a criptografia estiver desabilitada, os Arquivos do Azure também permitirão o SMB 2.1, o SMB 3.0 sem criptografia e as chamadas à API FileREST não criptografadas sobre HTTP. O principal motivo para desabilitar a criptografia em trânsito é dar suporte a um aplicativo herdado que deve ser executado em um sistema operacional mais antigo, como o Windows Server 2008 R2 ou a distribuição mais antiga do Linux. Os Arquivos do Azure só permitem conexões SMB 2.1 na mesma região do Azure que o compartilhamento de arquivo do Azure. Um cliente SMB 2.1 fora da região do Azure do compartilhamento de arquivo do Azure, como local ou em uma região diferente do Azure, não será capaz de acessar o compartilhamento de arquivo.

Para obter mais informações sobre criptografia em trânsito, consulte [Exigir transferência segura no armazenamento do Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="firewalls-and-virtual-networks"></a>Firewalls e redes virtuais 
Um firewall é uma política de rede cujas solicitações têm permissão de acessar os compartilhamentos de arquivo do Azure e outros recursos de armazenamento em sua conta de armazenamento. Quando uma conta de armazenamento é criada com as configurações de rede padrão, ela não é restrita a uma rede específica e, portanto, é acessível pela Internet. Isso não significa que qualquer pessoa na Internet possa acessar os dados armazenados nos compartilhamentos de arquivo do Azure hospedados em sua conta de armazenamento, mas, em vez disso, a conta de armazenamento aceitará solicitações autorizadas de qualquer rede. As solicitações podem ser autorizadas com uma chave de conta de armazenamento, um token de SAS (Assinatura de Acesso Compartilhado) (somente FileREST) ou com uma entidade de usuário Active Directory. 

A política de firewall para uma conta de armazenamento pode ser usada para restringir o acesso a determinados intervalos ou endereços IP ou a uma rede virtual. Em geral, a maioria das políticas de firewall para uma conta de armazenamento restringirá o acesso de rede a uma rede virtual. 

Uma [VNet](../../virtual-network/virtual-networks-overview.md), ou rede virtual, é semelhante a uma rede tradicional que você operaria em seu próprio datacenter. Ela permite que você crie um canal de comunicação seguro entre seus recursos do Azure, como compartilhamentos de arquivo do Azure, VMs, Bancos de Dados SQL etc. Como uma conta de armazenamento do Azure ou uma VM do Azure, uma VNet é um recurso do Azure que é implantado em um grupo de recursos. Com a configuração de rede adicional, as VNets do Azure também podem estar conectadas às suas redes locais.

Quando recursos como uma VM do Azure são adicionados a uma rede virtual, um NIC (adaptador de rede virtual) conectado à máquina virtual é restrito especificamente a essa VNet. Isso é possível porque as VMs do Azure são computadores virtualizados, que, claramente, têm NICs. As máquinas virtuais são oferecidas como parte da lista de produtos de IaaS, ou infraestrutura como serviço, do Azure. Como os compartilhamentos de arquivo do Azure são compartilhamentos de arquivo sem servidor, eles não têm um NIC para você adicionar a uma VNet. Dito de uma maneira diferente, os Arquivos do Azure são oferecidos como parte da linha de produtos de PaaS, ou plataforma como serviço, do Azure. Para habilitar uma conta de armazenamento a fazer parte de uma VNet, o Azure é compatível com um conceito de serviços PaaS chamados de pontos de extremidade de serviço. Um ponto de extremidade de serviço permite que os serviços de PaaS façam parte de uma rede virtual. Para saber mais sobre os pontos de extremidade do serviço, consulte [Pontos de extremidade de serviço de rede virtual](../../virtual-network/virtual-network-service-endpoints-overview.md).

Uma conta de armazenamento pode ser adicionada a uma ou mais redes virtuais. Para saber mais sobre como adicionar sua conta de armazenamento a uma rede virtual ou definir outras configurações de firewall, consulte [Configurar redes virtuais e firewalls de armazenamento do Azure](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="azure-networking"></a>Rede do Azure
Por padrão, os serviços do Azure, incluindo os Arquivos do Azure, podem ser acessados pela Internet. Como o tráfego padrão para sua conta de armazenamento é criptografado (e montagens SMB 2.1 nunca são permitidas fora de uma região do Azure), não há nada inerentemente inseguro sobre o acesso de seus compartilhamentos de arquivo do Azure pela Internet. Com base na política de sua organização ou em requisitos regulatórios exclusivos, você poderá exigir comunicação mais restritiva com o Azure e, assim, o Azure fornecerá várias maneiras de restringir como o tráfego de fora dele chega aos Arquivos do Azure. Você pode proteger ainda mais sua rede ao acessar o compartilhamento de arquivo do Azure usando as seguintes ofertas de serviço:

- [Gateway de VPN do Azure](../../vpn-gateway/vpn-gateway-about-vpngateways.md): Um gateway de VPN é um tipo específico de gateway de rede virtual que é usado para enviar tráfego criptografado entre uma rede virtual do Azure e uma localização alternativa (como localmente) na Internet. Um Gateway de VPN do Azure é um recurso do Azure que pode ser implantado em um grupo de recursos junto com uma conta de armazenamento ou outros recursos do Azure. Os gateways de VPN expõem dois tipos diferentes de conexões:
    - Conexões de gateway de [VPN P2S (ponto a site)](../../vpn-gateway/point-to-site-about.md), que são conexões VPN entre o Azure e um cliente individual. Essa solução é útil principalmente para dispositivos que não fazem parte da rede local de sua organização, como telecomutadores que desejam poder montar o compartilhamento de arquivo do Azure de casa, de uma cafeteria ou de um hotel em trânsito. Para usar uma conexão VPN P2S com os Arquivos do Azure, será preciso configurar uma conexão VPN P2S para cada cliente que desejar se conectar. 
    - [VPN S2S (site a site)](../../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti), que são conexões VPN entre o Azure e a rede de sua organização. Uma conexão VPN S2S permite que você configure uma conexão VPN uma vez, para um servidor VPN ou dispositivo hospedado na rede de sua organização, em vez de fazer isso para cada dispositivo cliente que precisar acessar o compartilhamento de arquivo do Azure.
- [ExpressRoute](../../expressroute/expressroute-introduction.md), que permite que você crie uma rota definida entre o Azure e sua rede local que não atravesse a Internet. Como o ExpressRoute fornece um caminho dedicado entre o datacenter local e o Azure, ele pode ser útil quando o desempenho da rede é importante. O ExpressRoute também é uma boa opção quando os requisitos regulatórios ou de política de sua organização exigem um caminho determinístico para seus recursos na nuvem.

## <a name="securing-access-from-on-premises"></a>Como proteger o acesso localmente 
Ao migrar compartilhamentos de arquivo de uso geral (para itens como documentos do Office, PDFs, documentos CAD etc.) para os Arquivos do Azure, os usuários normalmente continuam precisando acessar seus arquivos de dispositivos locais, como estações de trabalho, laptops e tablets. A principal consideração para um compartilhamento de arquivo de uso geral é como os usuários locais podem acessar com segurança seus compartilhamentos de arquivo por meio da Internet ou da WAN.

A maneira mais fácil de acessar o compartilhamento de arquivo do Azure localmente é abrir sua rede local para a porta 445, a porta usada pelo SMB e montar o caminho UNC fornecido pelo portal do Azure. Isso não requer rede especial. Muitos clientes relutam em abrir a porta 445 devido a uma orientação de segurança desatualizada sobre o SMB 1.0, que a Microsoft não considera um protocolo seguro para a Internet. Os Arquivos do Azure não implementam o SMB 1.0. 

O SMB 3.0 foi projetado com o requisito explícito de ser protocolos de compartilhamento de arquivo seguros da Internet. Portanto, ao usar o SMB 3.0+, da perspectiva da rede de computadores, a abertura da porta 445 não é diferente de abrir a porta 443, a porta usada para conexões HTTPS. Em vez de bloquear a porta 445 para evitar tráfego SMB 1.0 inseguro, a Microsoft recomenda as seguintes etapas:

> [!Important]  
> Mesmo que você decida deixar a porta 445 fechada ao tráfego de saída, a Microsoft ainda recomenda seguir estas etapas para remover o SMB 1.0 de seu ambiente.

1. Verifique se o SMB 1.0 foi removido ou desabilitado nos dispositivos de sua organização. Todas as versões atualmente compatíveis com o Windows e o Windows Server dão suporte à remoção ou à desabilitação do SMB 1.0 e, desde o lançamento do Windows 10, versão 1709, não há instalação do SMB 1.0 no Windows por padrão. Para saber mais sobre como desabilitar o SMB 1.0, consulte nossas páginas específicas do sistema operacional:
    - [Como proteger o Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [Como proteger o Linux](storage-how-to-use-files-linux.md#securing-linux)
1. Certifique-se de que nenhum produto em sua organização exija o SMB 1.0 e remova aqueles que o fazem. Mantemos um [Centro de roteamento de produtos do SMB1](https://aka.ms/stillneedssmb1), que contém todos os produtos da Microsoft e de terceiros que exigem o SMB 1.0. 
1. (Opcional) Use um firewall de terceiros com a rede local de sua organização para evitar tráfego do SMB 1.0.

Se sua organização exigir que a porta 445 seja bloqueada por política ou regulamento, você poderá usar o Gateway de VPN do Azure ou o ExpressRoute para encapsular o tráfego pela porta 443. Para saber mais sobre as etapas específicas para implantação, consulte nossas páginas de instruções específicas:
- [Configurar uma VPN S2S (site a site) para uso com os Arquivos do Azure](storage-files-configure-s2s-vpn.md)
- [Configurar uma VPN P2S (ponto a site) no Windows para uso com os Arquivos do Azure](storage-files-configure-p2s-vpn-windows.md)
- [Configurar uma VPN P2S (Ponto a Site) no Linux para usar com os Arquivos do Azure](storage-files-configure-p2s-vpn-linux.md)

Sua organização pode ter o requisito adicional de que o tráfego de saída do site local deve seguir um caminho determinístico para seus recursos na nuvem. Nesse caso, o ExpressRoute é capaz de atender a esse requisito.

## <a name="securing-access-from-cloud-resources"></a>Como proteger o acesso de recursos da nuvem
Geralmente, quando um aplicativo local é enviado por lift-and-shift para a nuvem, o aplicativo e os dados dele são movidos ao mesmo tempo. Isso significa que a principal consideração para uma migração lift-and-shift é bloquear o acesso ao compartilhamento de arquivo do Azure para máquinas virtuais específicas ou serviços do Azure que exigem acesso ao compartilhamento de arquivo para operar. 

Talvez você queira usar as VNets para limitar quais VMs ou outros recursos do Azure têm permissão para fazer conexões de rede (montagens SMB ou chamadas à API REST para o compartilhamento de arquivo do Azure). É sempre recomendável colocar o compartilhamento de arquivo do Azure em uma VNet se você permitir tráfego não criptografado em sua conta de armazenamento. Caso contrário, usar ou não as VNets é uma decisão que deve ser orientada pelos seus requisitos de negócios e pela política organizacional.

O principal motivo para permitir o tráfego não criptografado para o compartilhamento de arquivo do Azure é a compatibilidade com o Windows Server 2008 R2, o Windows 7 ou outro sistema operacional mais antigo, acessando o compartilhamento de arquivo do Azure com SMB 2.1 (ou SMB 3.0 sem criptografia para algumas distribuições do Linux). Não recomendamos o uso de SMB 2.1 ou SMB 3.0 sem criptografia em sistemas operacionais compatíveis com o SMB 3.0+ com criptografia.

## <a name="see-also"></a>Consulte também
- [Visão geral do Arquivos do Azure](storage-files-introduction.md)
- [Planejando uma implantação de Arquivos do Azure](storage-files-planning.md)