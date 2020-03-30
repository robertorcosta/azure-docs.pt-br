---
title: Perguntas frequentes sobre o desempenho do SMB para arquivos do Azure NetApp| Microsoft Docs
description: Respostas frequentemente feitas perguntas sobre o desempenho do SMB para arquivos do Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: b-juche
ms.openlocfilehash: 24b3710861f0ee158619ae9103584dcdb181f3d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460442"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>Perguntas frequentes sobre o desempenho do SMB para arquivos do Azure NetApp

Este artigo responde a perguntas frequentes (FAQs) sobre as práticas recomendadas de desempenho do SMB para arquivos Do Azure NetApp.

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>O SMB Multichannel está habilitado em ações de SMB? 

Sim, o SMB Multichannel é habilitado por padrão, uma alteração colocada em prática no início de janeiro de 2020. Todos os compartilhamentos de SMB pré-datando os volumes existentes de SMB tiveram o recurso ativado, e todos os volumes recém-criados também terão o recurso ativado no momento da criação. 

Qualquer conexão SMB estabelecida antes da ativação do recurso precisará ser redefinida para aproveitar a funcionalidade Multicanal SMB. Para redefinir, você pode desconectar e reconectar o compartilhamento de SMB.

## <a name="is-rss-supported"></a>O RSS é suportado?

Sim, o Azure NetApp Files suporta o rss (receive-side-scaling).

Com o SMB Multichannel ativado, um cliente SMB3 estabelece várias conexões TCP para o servidor SMB do Azure NetApp Files son a uma placa de interface de rede (NIC) que é capaz de RSS único. 

## <a name="which-windows-versions-support-smb-multichannel"></a>Quais versões do Windows suportam o SMB Multichannel?

O Windows tem suportado o SMB Multichannel desde o Windows 2012 para permitir o melhor desempenho.  Consulte [Implantar o Multicanal SMB](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11)) e [os fundamentos do SMB Multichannel](https://blogs.technet.microsoft.com/josebda/2012/06/28/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0/) para obter detalhes. 


## <a name="does-my-azure-virtual-machine-support-rss"></a>Minha máquina virtual Azure suporta RSS?

Para ver se os NICs da máquina virtual do `Get-SmbClientNetworkInterface` Azure suportam `RSS Capable`rSS, execute o comando da seguinte forma e verifique o campo : 

![Suporte rss para máquina virtual Azure](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>O Azure NetApp Files suporta o SMB Direct?

Não, o Azure NetApp Files não suporta SMB Direct. 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>Qual é o benefício do SMB Multichannel? 

O recurso SMB Multichannel permite que um cliente SMB3 estabeleça um pool de conexões em uma única placa de interface de rede (NIC) ou várias NICs e as use para enviar solicitações para uma única sessão de SMB. Em contraste, por design, SMB1 e SMB2 exigem que o cliente estabeleça uma conexão e envie todo o tráfego de SMB para uma determinada sessão sobre essa conexão. Essa conexão única limita o desempenho geral do protocolo que pode ser alcançado a partir de um único cliente.

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>Devo configurar várias NICs no meu cliente para SMB?

Não. O cliente SMB corresponderá à contagem de NIC retornada pelo servidor SMB.  Cada volume de armazenamento é acessível a partir de um e apenas um ponto final de armazenamento.  Isso significa que apenas uma NIC será usada para qualquer relação SMB.  

Como mostra `Get-SmbClientNetworkInterace` a saída abaixo, a máquina virtual tem duas interfaces de rede : 15 e 12.  Como mostrado abaixo `Get-SmbMultichannelConnection`sob o comando, embora existam dois NICS capazes de RSS, apenas a interface 12 é usada em conexão com o compartilhamento de SMB; interface 15 não está em uso.

![NICS com capacidade para RSS](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>A equipe NIC é suportada no Azure?

Nic Teaming não é suportado no Azure. Embora várias interfaces de rede sejam suportadas em máquinas virtuais DoZure, elas representam uma construção lógica e não física. Como tal, eles não fornecem tolerância a falhas.  Além disso, a largura de banda disponível para uma máquina virtual Do Zure é calculada para a própria máquina e não para qualquer interface de rede individual.

## <a name="whats-the-performance-like-for-smb-multichannel"></a>Como é o desempenho do SMB Multichannel?

Os testes e gráficos a seguir demonstram o poder do SMB Multichannel em cargas de trabalho de instância única.

### <a name="random-io"></a>I/O aleatório  

Com o SMB Multichannel desativado no cliente, foram realizados testes de leitura e gravação puros de 8-KiB utilizando fio e um conjunto de trabalho de 40 GiB.  O compartilhamento de SMB foi destacado entre cada teste, com incrementos da contagem `1``4`de`8``16`conexão do cliente SMB por configurações de interface de rede RSS de , , , , . `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>` Os testes mostram que `4` a configuração padrão de é suficiente para cargas de trabalho intensivas de I/O; incrementando `8` `16` e não teve efeito. 

O `netstat -na | findstr 445` comando provou que conexões adicionais `1` foram `4` `8` estabelecidas `16`com incrementos de para e para .  Quatro núcleos de CPU foram totalmente utilizados para SMB `Per Processor Network Activity Cycles` durante cada teste, conforme confirmado pela estatística perfmon (não incluída neste artigo.)

![Testes aleatórios de I/O](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

A máquina virtual Do Zure não afeta os limites de I/O de armazenamento SMB (nem NFS).  Como mostrado abaixo, o tipo de ocorrência D16 tem uma taxa limitada de 32.000 para IOPS de armazenamento em cache e 25.600 para IOPS de armazenamento não armazenado.  No entanto, o gráfico acima mostra significativamente mais I/O sobre SMB.

![Comparação aleatória de I/O](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>IO seqüencial 

Testes semelhantes aos testes aleatórios de I/O descritos acima foram realizados com I/O seqüencial de 64-KiB. Embora os aumentos na contagem de conexão do cliente por interface de rede RSS além de 4' não tenham tido efeito perceptível na I/O aleatória, o mesmo não se aplica à I/O seqüencial. Como mostra o gráfico a seguir, cada aumento está associado a um aumento correspondente no throughput de leitura. O throughput de gravação permaneceu plano devido às restrições de largura de banda de rede colocadas pelo Azure para cada tipo/tamanho de instância. 

![Testes sequenciais de I/O](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

O Azure coloca limites de taxa de rede em cada tipo/tamanho de máquina virtual. O limite de tarifa é imposto apenas no tráfego de saída. O número de NICs presentes em uma máquina virtual não tem relação com a quantidade total de largura de banda disponível para a máquina.  Por exemplo, o tipo de instância D16 tem um limite de rede imposto de 8000 Mbps (1.000 MiB/s).  Como mostra o gráfico seqüencial acima, o limite afeta o tráfego de saída (grava), mas não leituras multicanal.

![Comparação seqüencial de I/O](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="is-accelerated-networking-recommended"></a>O Accelerated Networking é recomendado?

Para o máximo desempenho, recomenda-se configurar [a Rede Acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) sempre que possível. Tenha em mente as seguintes considerações:  

* O portal Azure permite o Accelerated Networking por padrão para máquinas virtuais que suportam esse recurso.  No entanto, outros métodos de implantação, como Ansible e ferramentas de configuração semelhantes, podem não.  A falha na habilitação da Rede Acelerada pode atrapalhar o desempenho de uma máquina.  
* Se a Rede Acelerada não estiver habilitada na interface de rede de uma máquina virtual devido à sua falta de suporte para um tipo ou tamanho de instância, ela permanecerá desativada com tipos de instâncias maiores. Você precisará de intervenção manual nesses casos.

## <a name="are-jumbo-frames-supported"></a>Os quadros jumbo são suportados?

Os quadros Jumbo não são suportados com máquinas virtuais Azure.

## <a name="is-smb-signing-supported"></a>A assinatura de SMB é suportada? 

O protocolo SMB fornece a base para compartilhamento de arquivos e impressão e outras operações de rede, como administração remota do Windows. Para impedir ataques man-in-the-middle que modificam pacotes SMB em trânsito, o protocolo SMB oferece suporte para assinatura digital de pacotes SMB. 

SMB Signing é suportado para todas as versões de protocolo SMB que são suportadas pelo Azure NetApp Files. 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>Qual é o impacto de desempenho da Assinatura de SMB?  

A assinatura de SMB tem um efeito deletério sobre o desempenho do SMB. Entre outras possíveis causas da degradação do desempenho, a assinatura digital de cada pacote consome CPU adicional do lado do cliente, como mostra a saída de perfmon abaixo. Neste caso, o Core 0 aparece responsável pelo SMB, incluindo a Assinatura de SMB.  Uma comparação com os números de leitura seqüencial não-multicanais na seção anterior mostra que a assinatura de SMB reduz o throughput geral de 875MiB/s para aproximadamente 250MiB/s. 

![Impacto do desempenho de assinatura de SMB](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)


## <a name="next-steps"></a>Próximas etapas  

- [Perguntas frequentes sobre arquivos do Azure NetApp](azure-netapp-files-faqs.md)
- Consulte os [Arquivos Do Azure NetApp: Compartilhamentos gerenciados de arquivos corporativos para cargas de trabalho smb](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1) sobre o uso de compartilhamentos de arquivos SMB com arquivos Do Azure NetApp.
