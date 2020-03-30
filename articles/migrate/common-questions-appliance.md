---
title: Azure Migrate appliance FAQ
description: Obtenha respostas para perguntas comuns sobre o aparelho Azure Migrate.
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 2ef7de3b31cb11a71ec9379232fc5ff1022cf666
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336985"
---
# <a name="azure-migrate-appliance-common-questions"></a>Aparelho Azure Migrate: Perguntas comuns

Este artigo responde a perguntas comuns sobre o aparelho Azure Migrate. Se você tiver outras perguntas, verifique esses recursos:

- [Perguntas gerais](resources-faq.md) sobre o Azure Migrate
- Perguntas sobre [descoberta, avaliação e visualização de dependência](common-questions-discovery-assessment.md)
- Perguntas sobre [migração de servidores](common-questions-server-migration.md)
- Receba perguntas respondidas no [fórum Azure Migrate](https://aka.ms/AzureMigrateForum)

## <a name="what-is-the-azure-migrate-appliance"></a>O que é o aparelho Azure Migrate?

O aparelho Azure Migrate é um aparelho leve que a ferramenta Azure Migrate: Server Assessment usa para descobrir e avaliar servidores no local. A ferramenta Azure Migrate: Server Migration também usa o aparelho para migração sem agente de VMS VMware no local.

Aqui estão mais informações sobre o aparelho Azure Migrate:

- O aparelho é implantado no local como uma VM ou máquina física.
- O aparelho descobre máquinas no local e envia continuamente metadados da máquina e dados de desempenho para o Azure Migrate.
- A descoberta do dispositivo é sem agente. Nada está instalado em máquinas descobertas.

[Saiba mais](migrate-appliance.md) sobre o aparelho.

## <a name="how-does-the-appliance-connect-to-azure"></a>Como o aparelho se conecta ao Azure?

O aparelho pode se conectar pela internet ou usando o Azure ExpressRoute com peering público/Microsoft.

## <a name="does-appliance-analysis-affect-performance"></a>A análise do aparelho afeta o desempenho?

O aparelho Azure Migrate perfila máquinas no local continuamente para medir dados de desempenho. Esse perfil não tem quase nenhum impacto de desempenho em máquinas com perfil.

## <a name="can-i-harden-the-appliance-vm"></a>Posso endurecer o aparelho VM?

Quando você usa o modelo baixado para criar a VM do aparelho, você pode adicionar componentes (antivírus, por exemplo) ao modelo se você deixar no lugar as regras de comunicação e firewall que são necessárias para o aparelho Azure Migrate.

## <a name="what-network-connectivity-is-required"></a>Que conectividade de rede é necessária?

Consulte os seguintes artigos para obter informações sobre os requisitos de conectividade de rede para o aparelho Azure Migrate:

- **Avaliação vMware**: [acesso à URL](migrate-appliance.md#url-access) e acesso à [porta](migrate-support-matrix-vmware.md#port-access)
- **Migração sem agente VMware**: [acesso à URL](migrate-appliance.md#url-access) e acesso à [porta](migrate-support-matrix-vmware-migration.md#agentless-ports)
- **Avaliação hiper-V**: [acesso à URL](migrate-appliance.md#url-access) e acesso à [porta](migrate-support-matrix-hyper-v.md#port-access)

## <a name="what-data-does-the-appliance-collect"></a>Quais dados o aparelho coleta?

Veja os artigos a seguir para obter informações sobre os dados que o aparelho Azure Migrate coleta em VMs:

- **VMware VM**: [Revisão de](migrate-appliance.md#collected-data---vmware) dados coletados. [
- **Hyper-V VM**: [Revisão de](migrate-appliance.md#collected-data---hyper-v) dados coletados.

## <a name="how-is-data-stored"></a>Como os dados são armazenados?

Os dados coletados pelo aparelho Azure Migrate são armazenados no local do Azure, onde você criou o projeto Azure Migrate.

Aqui estão mais informações sobre como os dados são armazenados:

- Os dados coletados são armazenados com segurança no CosmosDB em uma assinatura da Microsoft. Os dados são excluídos quando você exclui o projeto Azure Migrate. O armazenamento é realizado pelo Azure Migrate. Você não pode escolher especificamente uma conta de armazenamento para os dados coletados.
- Se você usar [visualização de dependência,](concepts-dependency-visualization.md)os dados coletados serão armazenados nos Estados Unidos em um espaço de trabalho do Azure Log Analytics criado em sua assinatura do Azure. Os dados são excluídos quando você exclui o espaço de trabalho do Log Analytics em sua assinatura.

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>Quantos dados são carregados durante o perfil contínuo?

O volume de dados enviados ao Azure Migrate depende de vários parâmetros. Como exemplo, um projeto do Azure Migrate que possui 10 máquinas (cada uma com um disco e uma NIC) envia aproximadamente 50 MB de dados por dia. Esse valor é aproximado; o valor real varia dependendo do número de pontos de dados para os discos e NICs. Se o número de máquinas, discos ou NICs aumentar, o aumento dos dados enviados não é linear.

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>Os dados são criptografados em repouso e em trânsito?

Sim, para ambos:

- Os metadados são enviados com segurança para o serviço Azure Migrate pela internet via HTTPS.
- Metadados são armazenados em um banco de dados [do Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) e no [armazenamento Azure Blob](../storage/common/storage-service-encryption.md) em uma assinatura da Microsoft. Os metadados são criptografados em repouso para armazenamento.
- Os dados para análise de dependência também são criptografados em trânsito (por HTTPS seguro). Ele é armazenado em um espaço de trabalho do Log Analytics em sua assinatura. Os dados são criptografados em repouso para análise de dependência.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>Como o aparelho se conecta ao vCenter Server?

Essas etapas descrevem como o aparelho se conecta ao VMware vCenter Server:

1. O aparelho se conecta ao vCenter Server (porta 443) usando as credenciais fornecidas ao configurar o aparelho.
2. O aparelho usa o VMware PowerCLI para consultar o vCenter Server para coletar metadados sobre as VMs gerenciadas pelo vCenter Server.
3. O aparelho coleta dados de configuração sobre VMs (núcleos, memória, discos, NICs) e o histórico de desempenho de cada VM no último mês.
4. Os metadados coletados são enviados para a ferramenta Azure Migrate: Server Assessment (via INTERNET via HTTPS) para avaliação.

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>O aparelho Azure Migrate pode se conectar a vários servidores vCenter?

Não. Há um mapeamento de um para um entre um [aparelho Azure Migrate](migrate-appliance.md) e o vCenter Server. Para descobrir VMs em várias instâncias do vCenter Server, você deve implantar vários aparelhos. 

## <a name="can-an-azure-migrate-project-have-multiple-appliances"></a>Um projeto do Azure Migrate pode ter vários aparelhos?
Um projeto pode ter vários aparelhos ligados a ele. No entanto, um aparelho só pode ser associado a um projeto. 


## <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>Quantas VMs ou servidores posso descobrir com um aparelho?

Você pode descobrir até 10.000 VMs VMware, até 5.000 VMs Hyper-V e até 250 servidores físicos com um único aparelho. Se você tiver mais máquinas em seu ambiente local, leia sobre [escalar uma avaliação Hyper-V,](scale-hyper-v-assessment.md) [dimensionar uma avaliação de VMware](scale-vmware-assessment.md)e [dimensionar uma avaliação física do servidor](scale-physical-assessment.md).

## <a name="can-i-delete-an-appliance"></a>Posso apagar um aparelho?

Atualmente, a exclusão de um aparelho do projeto não é suportada.

A única maneira de excluir o aparelho é excluir o grupo de recursos que contém o projeto Azure Migrate que está associado ao aparelho.

No entanto, a exclusão do grupo de recursos também exclui outros aparelhos registrados, o inventário descoberto, avaliações e todos os outros componentes do Azure no grupo de recursos que estão associados ao projeto.

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>Posso usar o aparelho com uma assinatura ou projeto diferente?

Depois de usar o aparelho para iniciar a descoberta, você não pode reconfigurar o aparelho para usar com uma assinatura diferente do Azure, e você não pode usá-lo em um projeto diferente do Azure Migrate. Você também não pode descobrir VMs em uma instância diferente do vCenter Server. Configure um novo aparelho para essas tarefas.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Posso configurar o aparelho em uma VM Azure?

Não. Atualmente, essa opção não é suportada. 

## <a name="can-i-discover-on-an-esxi-host"></a>Posso descobrir em um host ESXi?

Não. Para descobrir VMware VMs, você deve ter o vCenter Server.

## <a name="how-do-i-update-the-appliance"></a>Como atualizo o aparelho?

Por padrão, o aparelho e seus agentes instalados são atualizados automaticamente. O aparelho verifica se há atualizações a cada 24 horas. Atualizações que falham são repetidas novamente. 

Apenas o aparelho e os agentes do aparelho são atualizados por essas atualizações automáticas. O sistema operacional não é atualizado pelas atualizações automáticas do Azure Migrate. Use o Windows Updates para manter o sistema operacional atualizado.

## <a name="can-i-check-agent-health"></a>Posso verificar a saúde do agente?

Sim. No portal, acesse a página **de saúde do Agente** para o Azure Migrate: Server Assessment ou Azure Migrate: Ferramenta de migração de servidor. Lá, você pode verificar o status de conexão entre o Azure e os agentes de descoberta e avaliação no aparelho.

## <a name="next-steps"></a>Próximas etapas

Leia a visão geral do [Azure Migrate](migrate-services-overview.md).
