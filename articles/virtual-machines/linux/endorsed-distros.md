---
title: Distribuições do Linux endossadas no Azure
description: Saiba mais sobre o Linux nas distribuições endossadas do Azure, incluindo diretrizes para Ubuntu, CentOS, Oracle e SUSE.
services: virtual-machines-linux
documentationcenter: ''
author: gbowerman
manager: gwallace
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: 2777a526-c260-4cb9-a31a-bdfe1a55fffc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/04/2019
ms.author: guybo
ms.openlocfilehash: cc2fedcd4816b55aaed3573ce2593919770a4152
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062651"
---
# <a name="endorsed-linux-distributions-on-azure"></a>Distribuições Linux endossadas no Azure
Parceiros fornecem imagens do Linux no Azure Marketplace. Trabalhamos com várias comunidades do Linux para adicionar ainda mais opções à lista de Distribuição endossadas. Enquanto isso, para as distribuições que não estão disponíveis no Marketplace, você pode sempre colocar seu próprio Linux seguindo as orientações em [Criar e carregar um disco rígido virtual que contém o sistema operacional Linux](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic).

## <a name="supported-distributions-and-versions"></a>Distribuições e versões com suporte
A tabela a seguir lista as distribuições e versões do Linux com suporte no Azure. Consulte [o Suporte para imagens Linux no Microsoft Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) para obter informações mais detalhadas sobre suporte para Linux e tecnologia de código aberto no Azure.

Os drivers LIS (Serviços de Integração do Linux) para Hyper-V e Azure são módulos de kernel que a Microsoft contribui diretamente para o kernel upstream do Linux.  Alguns drivers LIS são incorporados no kernel de distribuição por padrão. Distribuições mais antigas que se baseiam no Red Hat Enterprise (RHEL)/CentOS estão disponíveis como um download separado em [Serviços de integração do Linux versão 4.2 para o Hyper-V e Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=55106). Consulte [Requisitos de kernel do Linux](create-upload-generic.md#linux-kernel-requirements) para obter mais informações sobre os drivers LIS.

O Agente Linux do Azure já vem pré-instalado nas imagens do Azure Marketplace e normalmente está disponível no repositório de pacotes de distribuição. O código-fonte pode ser encontrado no [GitHub](https://github.com/azure/walinuxagent).


| Distribuição | Versão | Drivers | Agente |
| --- | --- | --- | --- |
| CentOS |CentOS 6.3+, 7.0+, 8.0+ |CentOS 6.3: [Download de LIS](https://www.microsoft.com/download/details.aspx?id=55106)<p>CentOS 6.4 +: no kernel |Pacote: Em [repo](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) em "WALinuxAgent" <br/>Código-fonte: [GitHub](https://github.com/Azure/WALinuxAgent) |
| [CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) |494.4.0+ |No kernel |Código-fonte: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent) |
| Debian |Debian 7.9+, 8.2+, 9, 10 |No kernel |Pacote: no repositório, em "waagent"  <br/>Código-fonte: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Oracle Linux |6.4+, 7.0+ |No kernel |Pacote: no repositório, em "WALinuxAgent"  <br/>Código-fonte: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| Red Hat Enterprise Linux |RHEL 6.7+, 7.1+, 8.0+ |No kernel |Pacote: no repositório, em "WALinuxAgent"  <br/>Código-fonte: [GitHub](https://github.com/Azure/WALinuxAgent) |
| SUSE Linux Enterprise |SLES/SLES para SAP<br>11 SP4<br>12 SP1+<br>15|No kernel |Pacote:<p> para 11, no repositório [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools)<br>para 12, incluído no módulo "Public Cloud" em "python-azure-agent"<br/>Código-fonte: [GitHub](https://go.microsoft.com/fwlink/p/?LinkID=250998) |
| openSUSE |openSUSE Leap 42.2+ |No kernel |Pacote: no repositório [Cloud:Tools](https://build.opensuse.org/project/show/Cloud:Tools) em "python-azure-agent" <br/>Código-fonte: [GitHub](https://github.com/Azure/WALinuxAgent) |
| Ubuntu |Ubuntu 12.04+ **<sup>1</sup>** |No kernel |Pacote: no repositório, em "WALinuxAgent"  <br/>Código-fonte: [GitHub](https://github.com/Azure/WALinuxAgent) |

  - **<sup>1</sup>** Informações sobre suporte estendido para Ubuntu 12.04 e 14.04 podem ser encontradas aqui: [Ubuntu Extended Security Maintenance](https://www.ubuntu.com/esm).


## <a name="image-update-cadence"></a>Cadência de atualização de imagem
O Azure exige que os editores das distribuições Linux endossadas atualizem regularmente suas imagens no Azure Marketplace com os patches e correções de segurança mais recentes, em uma cadência trimestral ou mais rápida. As imagens atualizadas no Azure Marketplace estão disponíveis automaticamente para os clientes como novas versões de um SKU de imagem. Mais informações sobre como encontrar imagens linux: [encontre imagens de VM Linux no Azure Marketplace](https://docs.microsoft.com/azure/virtual-machines/linux/cli-ps-findimage).

### <a name="additional-links"></a>Links adicionais
 - [Ciclo de vida da imagem da nuvem pública SUSE](https://www.suse.com/c/suse-public-cloud-image-life-cycle/)

## <a name="azure-tuned-kernels"></a>Núcleos azure-afinados

O Azure trabalha em estreita colaboração com várias distribuições Linux endossadas para otimizar as imagens que eles publicaram no Azure Marketplace. Um aspecto dessa colaboração é o desenvolvimento de kernels Linux "sintonizados" que são otimizados para a plataforma Azure e entregues como componentes totalmente suportados da distribuição Linux. Os núcleos Azure-Tuned incorporam novos recursos e melhorias de desempenho e em uma cadência mais rápida (tipicamente trimestral) em comparação com os núcleos padrão ou genéricos disponíveis na distribuição.

Na maioria dos casos, você encontrará esses kernels pré-instalados nas imagens padrão no Azure Marketplace e, portanto, os clientes do Azure terão imediatamente o benefício desses kernels otimizados. Mais informações sobre esses kernels Azure-Tuned podem ser encontradas nos seguintes links:

 - CentOS Azure-Tuned Kernel - Disponível através do CentOS Virtualization SIG - [Mais informações](https://wiki.centos.org/SpecialInterestGroup/Virtualization)
 - Debian Cloud Kernel - Disponível com a imagem Debian 10 e Debian 9 "backports" no Azure - [Mais informações](https://wiki.debian.org/Cloud/MicrosoftAzure)
 - Kernel azure-tuned SLES - [Mais informações](https://www.suse.com/c/a-different-builtin-kernel-for-azure-on-demand-images/)
 - Kernel azure-tuned do Ubuntu - [Mais informações](https://blog.ubuntu.com/2017/09/21/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)


## <a name="partners"></a>Parceiros

### <a name="coreos"></a>CoreOS
[https://coreos.com/docs/running-coreos/cloud-providers/azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

No site CoreOS:

*O CoreOS foi projetado para segurança, consistência e confiabilidade. Em vez de instalar os pacotes por meio de yum ou apt, o CoreOS usa contêineres do Linux para gerenciar seus serviços em um nível mais alto de abstração. Um único código de serviço e todas as dependências são empacotados em um contêiner que pode ser executado em uma ou várias máquinas CoreOS.*

### <a name="credativ"></a>Credativ
[https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure](https://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

A Credativ é uma empresa de serviços e consultoria independente, especializada no desenvolvimento e na implementação de soluções profissionais com o uso de software gratuito. A Credative, por ser especialista e líder em software gratuito, é reconhecida internacionalmente com vários departamentos de TI que usam o suporte que a empresa fornece. Em conjunto com a Microsoft, a Credativ está preparando imagens correspondentes do Debian para Debian 8 (Jessie) e Debian antes do 7 (Wheezy). As duas imagens são especialmente projetadas para execução no Azure e podem ser facilmente gerenciadas por meio da plataforma. A empresa também dará suporte a atualização e manutenção de longo prazo das imagens Debian para Azure por meio de seus Centros de Suporte de Software Livre.

### <a name="oracle"></a>Oracle
[https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

A estratégia da Oracle é oferecer um amplo portfólio de soluções para nuvens públicas e privadas. A estratégia oferece aos clientes a opção e a flexibilidade em como implantar o software da Oracle nas nuvens da Oracle e em outras nuvens. A parceria da Oracle com a Microsoft permite aos clientes implantar um software Oracle em nuvens públicas e privadas da Microsoft com a certeza da certificação e do suporte da Oracle.  O compromisso e o investimento da Oracle em soluções de nuvem pública e privada Oracle permanecem inalterados.

### <a name="red-hat"></a>Red Hat
[https://www.redhat.com/en/partners/strategic-alliance/microsoft](https://www.redhat.com/en/partners/strategic-alliance/microsoft)

A Red Hat, a principal provedora de soluções de software livre do mundo, ajuda mais de 90% das empresas da Fortune 500 a resolver desafios comerciais, a alinhar suas estratégias comerciais e de TI e a preparar-se para o futuro da tecnologia. A empresa faz isso fornecendo soluções seguras por meio de um modelo de negócios aberto e de um modelo de assinatura acessível e previsível.

### <a name="suse"></a>SUSE
[https://www.suse.com/suse-linux-enterprise-server-on-azure](https://www.suse.com/suse-linux-enterprise-server-on-azure)

O SUSE Linux Enterprise Server no Azure é uma plataforma testada que fornece confiabilidade e segurança superiores para a computação em nuvem. A plataforma Linux versátil do SUSE se integra perfeitamente aos serviços de nuvem do Azure para oferecer um ambiente de nuvem facilmente gerenciável. Com mais de 9.200 aplicativos certificados de mais de 1.800 fornecedores de software independentes para SUSE Linux Enterprise Server, o SUSE garante que as cargas de trabalho em execução compatíveis no data center podem ser implantadas com segurança no Azure.

### <a name="canonical"></a>Canônico
[https://www.ubuntu.com/cloud/azure](https://www.ubuntu.com/cloud/azure)

O controle aberto da comunidade e a engenharia da Canonical impulsionam o sucesso do Ubuntu no cliente, no servidor e na computação em nuvem, que inclui serviços de nuvem pessoais para os consumidores. A visão do Canonical de uma plataforma unificada e gratuita no Ubuntu, do telefone à nuvem, fornece uma família de interfaces coerentes para o telefone, tablet, TV e área de trabalho. Essa visão torna Ubuntu a primeira opção para instituições diversificadas de provedores de nuvem pública para fabricantes de eletrônicos para os consumidores e um favorito entre especialistas em tecnologias individuais.

Com desenvolvedores e centros de engenharia no mundo inteiro, a Canonical está posicionada exclusivamente para fazer parceria com fabricantes de hardware, provedores de conteúdo e desenvolvedores de software para oferecer soluções de Ubuntu no mercado para PCs, servidores e dispositivos portáteis.
