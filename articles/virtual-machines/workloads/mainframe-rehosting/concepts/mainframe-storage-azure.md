---
title: Mova o armazenamento do mainframe para o armazenamento do Azure
description: Os recursos de armazenamento Azure maciçamente escaláveis podem ajudar as organizações baseadas em mainframe a migrar e modernizar os aplicativos IBM z14.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: storage
ms.openlocfilehash: 86419811cdf2c11204caae0ca5bf6f65fba063d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76288907"
---
# <a name="move-mainframe-storage-to-azure"></a>Mova o armazenamento do mainframe para o Azure

Para executar cargas de trabalho de mainframe no Microsoft Azure, você precisa saber como os recursos do seu mainframe se comparam ao Azure. Os recursos de armazenamento maciçamente escaláveis podem ajudar as organizações a começar a se modernizar sem abandonar os aplicativos em que confiam.

O Azure fornece recursos e capacidade de armazenamento semelhantes a mainframes que é comparável aos sistemas baseados em Z14 da IBM (o modelo mais atual a partir desta escrita). Este artigo diz como obter resultados comparáveis no Azure.

## <a name="mainframe-storage-at-a-glance"></a>Armazenamento de mainframe em um relance

O mainframe da IBM caracteriza o armazenamento de duas maneiras. O primeiro é um dispositivo de armazenamento de acesso direto (DASD). O segundo é o armazenamento seqüencial. Para gerenciar o armazenamento, o mainframe fornece o DFSMS (Data Facility Storage Management Subsystem). Ele gerencia o acesso de dados aos vários dispositivos de armazenamento.

[DASD](https://en.wikipedia.org/wiki/Direct-access_storage_device) refere-se a um dispositivo separado para armazenamento secundário (não na memória) que permite que um endereço único seja usado para acesso direto aos dados. Originalmente, o termo DASD se aplicava a discos giratórios, tambores magnéticos ou células de dados. No entanto, agora o termo também pode se aplicar a dispositivos de armazenamento de estado sólido (SSDs), redes de área de armazenamento (SANs), nas unidades de armazenamento conectados à rede (NAS) e unidades ópticas. Para efeitos deste documento, o DASD refere-se a discos giratórios, SANs e SSDs.

Em contraste com o armazenamento DASD, o armazenamento seqüencial em um mainframe refere-se a dispositivos como unidades de fita onde os dados são acessados a partir de um ponto de partida e, em seguida, lidos ou gravados em uma linha.

Os dispositivos de armazenamento são normalmente conectados usando uma conexão de fibra (FICON) ou são acessados diretamente no barramento io do mainframe usando [HiperSockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm), uma tecnologia IBM para comunicações de alta velocidade entre partições em um servidor com um hipervisor.

A maioria dos sistemas de mainframe separa o armazenamento em dois tipos:

- *O armazenamento on-line* (também conhecido como armazenamento quente) é necessário para operações diárias. O armazenamento DASD é geralmente usado para este fim. No entanto, o armazenamento seqüencial, como backups diários em fita (lógico ou físico), também pode ser usado para este fim.

- *O armazenamento de* arquivos (também conhecido como armazenamento a frio) não é garantido para ser montado em um determinado momento. Em vez disso, é montado e acessado conforme necessário. O armazenamento de arquivos é frequentemente implementado usando backups de fita seqüencial (lógicoou físico) para armazenamento.

## <a name="mainframe-versus-io-latency-and-iops"></a>Mainframe versus latência IO e IOPS

Os mainframes são frequentemente usados para aplicativos que requerem IO de alto desempenho e baixa latência IO. Eles podem fazer isso usando as conexões FICON para dispositivos IO e HiperSockets. Quando os HiperSockets são usados para conectar aplicativos e dispositivos diretamente ao canal IO de um mainframe, a latência nos microssegundos pode ser alcançada.

## <a name="azure-storage-at-a-glance"></a>Armazenamento azure em um relance

As opções de infra-estrutura como serviço[(IaaS)](https://azure.microsoft.com/overview/what-is-iaas/)do Azure fornecem capacidade de mainframe comparável.

A Microsoft oferece petabytes de armazenamento para aplicativos hospedados no Azure, e você tem várias opções de armazenamento. Estes vão desde o armazenamento SSD para alto desempenho até o armazenamento blob de baixo custo para armazenamento em massa e arquivos. Além disso, o Azure fornece uma opção de redundância de dados para armazenamento — algo que requer mais esforço para configurar em um ambiente de mainframe.

O armazenamento Azure está disponível como [Discos Azure,](/azure/virtual-machines/windows/managed-disks-overview) [Arquivos Azure](/azure/storage/files/storage-files-introduction)e [Blobs Do Azure,](/azure/storage/blobs/storage-blobs-overview) conforme a tabela a seguir resume. Saiba mais sobre [quando usar cada um](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Type</th><th>Descrição</th><th>Use quando quiser:</th></tr>
</thead>
<tbody>
<tr><td>Arquivos do Azure
</td>
<td>
Fornece uma interface SMB, bibliotecas de clientes e uma interface <a href="https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api">REST</a> que permite acesso de qualquer lugar a arquivos armazenados.
</td>
<td><ul>
<li>Levante e mude um aplicativo para a nuvem quando o aplicativo usa as APIs do sistema de arquivos nativas para compartilhar dados entre ele e outros aplicativos em execução no Azure.</li>
<li>Ferramentas de desenvolvimento e depuração de lojas que precisam ser acessadas de muitas VMs.</li>
</ul>
</td>
</tr>
<tr><td>Blobs do Azure
</td>
<td>Fornece bibliotecas de clientes e uma interface <a href="https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api">REST</a> que permite que dados não estruturados sejam armazenados e acessados em grande escala em blobs de blocos. Também dá suporte ao <a href="/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2</a> para soluções de análise de big data do enterprise.
</td>
<td><ul>
<li>Suporte a streaming e cenários de acesso aleatório em um aplicativo.</li>
<li>Tenha acesso aos dados do aplicativo de qualquer lugar.</li>
<li>Construa um lago de dados corporativos no Azure e realize análises de big data.</li>
</ul></td>
</tr>
<tr><td>Discos do Azure
</td>
<td>Fornece bibliotecas de clientes e uma interface <a href="https://docs.microsoft.com/rest/api/compute/disks">REST</a> que permite que os dados sejam armazenados e acessados de forma persistente e acessadas a partir de um disco rígido virtual conectado.
</td>
<td><ul>
<li>Aplicativos de elevação e mudança que usam APIs nativas do sistema de arquivos para ler e gravar dados em discos persistentes.</li>
<li>Armazene dados que não são necessários para serem acessados de fora da VM à qual o disco está conectado.</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Armazenamento quente (on-line) e frio (arquivo)

O tipo de armazenamento para um determinado sistema depende dos requisitos do sistema, incluindo tamanho de armazenamento, throughput e IOPS. Para armazenamento do tipo DASD em um mainframe, os aplicativos no Azure normalmente usam o armazenamento de unidade Azure Disks. Para armazenamento de arquivos de mainframe, o armazenamento blob é usado no Azure.

Os SSDs fornecem o maior desempenho de armazenamento no Azure. As seguintes opções estão disponíveis (a partir da redação deste documento):

| Type         | Tamanho           | IOPS                  |
|--------------|----------------|-----------------------|
| SSD Ultra    | 4 GB a 64 TB  | 1.200 a 160.000 IOPS |
| SSD Premium  | 32 GB a 32 TB | 12 a 15.000 IOPS     |
| SSD Standard | 32 GB a 32 TB | 12 a 2.000 IOPS      |

O armazenamento Blob fornece o maior volume de armazenamento no Azure. Além do tamanho do armazenamento, o Azure oferece armazenamento gerenciado e não gerenciado. Com o armazenamento gerenciado, o Azure cuida do gerenciamento das contas de armazenamento subjacentes. Com o armazenamento não gerenciado, o usuário assume a responsabilidade de configurar contas de armazenamento do tamanho apropriado para atender aos requisitos de armazenamento.

## <a name="next-steps"></a>Próximas etapas

- [Migração de mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Rehospedagem do mainframe em máquinas virtuais do Azure](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [Mover o computador de mainframe para o Azure](mainframe-compute-Azure.md)
- [Decidindo quando usar Blobs do Azure, Arquivos do Azure ou Discos do Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)
- [Discos gerenciados padrão de SSD para cargas de trabalho do Azure VM](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)

### <a name="ibm-resources"></a>Recursos da IBM

- [Sysplex paralelo no IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS e a Instalação de Acoplamento: Além do Básico](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Criando usuários necessários para uma instalação de recurso db2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - Criar comando de instância](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 solução de banco de dados clustered pureScale](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Nuvem do governo do Microsoft Azure para aplicativos de mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft e FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Mais recursos de migração

- [Guia de deslocamento e comparação de precisão de Data Center Virtual do Azure](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
