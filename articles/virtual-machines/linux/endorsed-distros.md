---
title: Distribuições do Linux endossadas no Azure
description: Saiba mais sobre o Linux nas distribuições endossadas do Azure, incluindo diretrizes para Ubuntu, CentOS, Oracle e SUSE.
services: virtual-machines-linux
documentationcenter: ''
author: gbowerman
manager: gwallace
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/04/2019
ms.author: guybo
ms.openlocfilehash: a2edc8d0116778e292f0e44761100d3c1e9212ff
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86527380"
---
# <a name="endorsed-linux-distributions-on-azure"></a>Distribuições do Linux endossadas no Azure

Parceiros fornecem imagens do Linux no Azure Marketplace. A Microsoft trabalha com várias comunidades do Linux para adicionar ainda mais tipos à lista de distribuição endossada. Para distribuições que não estão disponíveis no Marketplace, você sempre pode trazer seu próprio Linux seguindo as diretrizes em [criar e carregar um disco rígido virtual que contém o sistema operacional Linux](./create-upload-generic.md).

## <a name="supported-distributions-and-versions"></a>Distribuições e versões com suporte

A tabela a seguir lista as distribuições e versões do Linux com suporte no Azure. Consulte [suporte para imagens do Linux no Microsoft Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) para obter informações mais detalhadas sobre o suporte para Linux e tecnologia de software livre no Azure.

Os drivers LIS (Serviços de Integração do Linux) para Hyper-V e Azure são módulos de kernel que a Microsoft contribui diretamente para o kernel upstream do Linux. Alguns drivers LIS são incorporados no kernel de distribuição por padrão. Distribuições mais antigas que se baseiam no Red Hat Enterprise (RHEL)/CentOS estão disponíveis como um download separado em [Serviços de integração do Linux versão 4.2 para o Hyper-V e Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=55106). Consulte [Requisitos de kernel do Linux](create-upload-generic.md#linux-kernel-requirements) para obter mais informações sobre os drivers LIS.

O Agente Linux do Azure já vem pré-instalado nas imagens do Azure Marketplace e normalmente está disponível no repositório de pacotes de distribuição. O código-fonte pode ser encontrado no [GitHub](https://github.com/azure/walinuxagent).

| Distribuição | Versão | Drivers | Agente |
| --- | --- | --- | --- |
| CentOS por software de Wave não autorizado |CentOS 6. x, 7. x, 8. x |CentOS 6,3: [download do LIS](https://www.microsoft.com/download/details.aspx?id=55106)<p>CentOS 6.4 +: no kernel |Pacote: no [repositório](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) em "WALinuxAgent" <br/>Código-fonte: [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/)<p> CoreOS agora é o [fim da vida](https://coreos.com/os/eol/) de 26 de maio de 2020. |Não está mais disponível | | |
| Debian da Credativ |8.x, 9.x |No kernel |Pacote: no repositório, em "waagent"  <br/>Código-fonte: [GitHub](https://github.com/Azure/WALinuxAgent) |
|Flatcar contêiner Linux por Kinvolk| Estável, Edge| | |
| Oracle Linux pela Oracle |6.x, 7.x, 8.x |No kernel |Pacote: no repositório, em "WALinuxAgent"  <br/>Código-fonte: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux por Red Hat |6.x, 7.x, 8.x |No kernel |Pacote: no repositório, em "WALinuxAgent"  <br/>Código-fonte: [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise pelo SUSE |SLES/SLES para SAP 11. x, 12. x, 15. x <br/> [Ciclo de vida da imagem de nuvem pública SUSE](https://www.suse.com/c/suse-public-cloud-image-life-cycle/) |No kernel |Pacote:<p> para 11, no repositório [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools)<br>para 12, incluído no módulo "Public Cloud" em "python-azure-agent"<br/>Código-fonte: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE por SUSE |openSUSE Leap 15.x |No kernel |Pacote: no repositório [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) em "python-azure-agent" <br/>Código-fonte: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu por Canonical |Servidor Ubuntu e pro. 16. x, 18. x, 20. x<p>Informações sobre o suporte estendido para Ubuntu 12, 4 e 14, 4 podem ser encontradas aqui: [manutenção de segurança estendida do Ubuntu](https://www.ubuntu.com/esm). |No kernel |Pacote: no repositório, em "WALinuxAgent"  <br/>Código-fonte: [GitHub](https://github.com/Azure/WALinuxAgent) |

## <a name="image-update-cadence"></a>Cadência da atualização da imagem

O Azure exige que os editores das distribuições do Linux endossadas atualizem regularmente suas imagens no Azure Marketplace com os patches e correções de segurança mais recentes, a uma cadência trimestral ou mais rápida. As imagens atualizadas no Azure Marketplace estão disponíveis automaticamente para clientes como novas versões de uma SKU de imagem. Mais informações sobre como encontrar imagens do Linux: [Localizar imagens de VM do Linux no Azure Marketplace](./cli-ps-findimage.md).

## <a name="azure-tuned-kernels"></a>Kernels ajustados para o Azure

O Azure trabalha em conjunto com várias distribuições do Linux endossadas para otimizar as imagens publicadas no Azure Marketplace. Um aspecto dessa colaboração é o desenvolvimento de kernels do Linux "ajustados" que são otimizados para a plataforma Azure e fornecidos como componentes totalmente suportados da distribuição do Linux. Os kernels ajustados para o Azure incorporam novos recursos e melhorias de desempenho e em uma cadência mais rápida (normalmente trimestral) em comparação com os kernels padrão ou genéricos que estão disponíveis na distribuição.

Na maioria dos casos, você encontrará esses kernels pré-instalados nas imagens padrão no Azure Marketplace para que os clientes obtenham imediatamente os benefícios desses kernels otimizados. Mais informações sobre esses kernels ajustados para o Azure podem ser encontradas nos links a seguir:

- [Kernel do CentOS Azure ajustado – disponível por meio do CentOS Virtualization SIG](https://wiki.centos.org/SpecialInterestGroup/Virtualization)
- [Kernel de nuvem Debian-disponível com a imagem "backports" do Debian 10 e Debian 9 no Azure](https://wiki.debian.org/Cloud/MicrosoftAzure)
- [Kernel do SLES ajustado para o Azure](https://www.suse.com/c/a-different-builtin-kernel-for-azure-on-demand-images/)
- [Kernel do Ubuntu ajustado para Azure](https://blog.ubuntu.com/2017/09/21/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)

## <a name="partners"></a>Parceiros

### <a name="coreos"></a>CoreOS

O CoreOS está agendado para ser [encerrado](https://coreos.com/os/eol/) em 26 de maio de 2020.
A Microsoft tem dois (2) canais de migração para usuários do CoreOS.

- Flatcar by Kinvolk (consulte a entrada "flatcar contêiner Linux por Kinvolk".)
- [Sistema operacional Fedora Core](https://docs.fedoraproject.org/en-US/fedora-coreos/provisioning-azure/) (os clientes devem carregar suas próprias imagens. Aqui está a [documentação de migração](https://docs.fedoraproject.org/en-US/fedora-coreos/migrate-cl/)).

### <a name="credativ"></a>Credativ

[https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

A Credativ é uma empresa de serviços e consultoria independente, especializada no desenvolvimento e na implementação de soluções profissionais com o uso de software gratuito. A Credative, por ser especialista e líder em software gratuito, é reconhecida internacionalmente com vários departamentos de TI que usam o suporte que a empresa fornece. Em conjunto com a Microsoft, a Credativ está preparando imagens correspondentes do Debian para Debian 8 (Jessie) e Debian antes do 7 (Wheezy). As duas imagens são especialmente projetadas para execução no Azure e podem ser facilmente gerenciadas por meio da plataforma. A empresa também dará suporte a atualização e manutenção de longo prazo das imagens Debian para Azure por meio de seus Centros de Suporte de Software Livre.

### <a name="oracle"></a>Oracle

[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

A estratégia da Oracle é oferecer um amplo portfólio de soluções para nuvens públicas e privadas. A estratégia oferece aos clientes a opção e a flexibilidade em como implantar o software da Oracle nas nuvens da Oracle e em outras nuvens. A parceria da Oracle com a Microsoft permite que os clientes implantem software Oracle em nuvens públicas e privadas da Microsoft com a confiança da certificação e do suporte da Oracle.  O compromisso e o investimento da Oracle em soluções de nuvem pública e privada da Oracle estão inalterados.

### <a name="red-hat"></a>Red Hat

[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

O principal provedor de soluções de software livre do mundo, a Red Hat ajuda mais de 90% das empresas da Fortune 500 a resolverem desafios comerciais, a alinhar suas estratégias de ti e de negócios e a se preparar para o futuro da tecnologia. A empresa faz isso fornecendo soluções seguras por meio de um modelo de negócios aberto e de um modelo de assinatura acessível e previsível.

### <a name="suse"></a>SUSE

[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

O SUSE Linux Enterprise Server no Azure é uma plataforma testada que fornece confiabilidade e segurança superiores para a computação em nuvem. A plataforma Linux versátil do SUSE se integra perfeitamente aos serviços de nuvem do Azure para oferecer um ambiente de nuvem facilmente gerenciável. Com mais de 9.200 aplicativos certificados de mais de 1.800 fornecedores de software independentes para SUSE Linux Enterprise Server, o SUSE garante que as cargas de trabalho em execução compatíveis no data center podem ser implantadas com segurança no Azure.

### <a name="canonical"></a>Canônico

[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

O controle aberto da comunidade e a engenharia da Canonical impulsionam o sucesso do Ubuntu no cliente, no servidor e na computação em nuvem, que inclui serviços de nuvem pessoais para os consumidores. A visão do Canonical de uma plataforma unificada e gratuita no Ubuntu, do telefone à nuvem, fornece uma família de interfaces coerentes para o telefone, tablet, TV e área de trabalho. Essa visão torna Ubuntu a primeira opção para instituições diversificadas de provedores de nuvem pública para fabricantes de eletrônicos para os consumidores e um favorito entre especialistas em tecnologias individuais.

Com desenvolvedores e centros de engenharia no mundo inteiro, a Canonical está posicionada exclusivamente para fazer parceria com fabricantes de hardware, provedores de conteúdo e desenvolvedores de software para oferecer soluções de Ubuntu no mercado para PCs, servidores e dispositivos portáteis.
