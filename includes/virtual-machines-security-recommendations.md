---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: barclayn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/13/2019
ms.author: barclayn
ms.custom: include file
ms.openlocfilehash: d0ede337f2ae17f28cd02df7ed35919bd9955e7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048555"
---
Este artigo contém recomendações de segurança para máquinas virtuais do Azure. Siga essas recomendações para ajudar a cumprir as obrigações de segurança descritas em nosso modelo de responsabilidade compartilhada. As recomendações também ajudarão você a melhorar a segurança geral para suas soluções de aplicativos web. Para obter mais informações sobre o que a Microsoft faz para cumprir as responsabilidades do provedor de serviços, consulte [responsabilidades compartilhadas para computação em nuvem](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).

Algumas das recomendações deste artigo podem ser automaticamente abordadas pelo Azure Security Center. O Azure Security Center é a primeira linha de defesa para seus recursos no Azure. Ele analisa periodicamente o estado de segurança de seus recursos do Azure para identificar possíveis vulnerabilidades de segurança. Em seguida, recomenda como lidar com as vulnerabilidades. Para obter mais informações, consulte [recomendações de segurança no Azure Security Center](../articles/security-center/security-center-recommendations.md).

Para obter informações gerais sobre o Azure Security Center, consulte [o que é o Azure Security Center?](../articles/security-center/security-center-intro.md).

## <a name="general"></a>Geral

| Recomendação | Comentários | Central de Segurança |
|-|----|--|
| Quando você criar imagens VM personalizadas, aplique as atualizações mais recentes. | Antes de criar imagens, instale as últimas atualizações para o sistema operacional e para todos os aplicativos que farão parte da sua imagem.  | - |
| manter as VMs atualizadas. | Você pode usar a solução [Update Management](../articles/automation/automation-update-management.md) no Azure Automation para gerenciar atualizações do sistema operacional para seus computadores Windows e Linux no Azure. | [Sim](../articles/security-center/security-center-apply-system-updates.md) |
| Afaste suas VMs. | [O Azure Backup](../articles/backup/backup-overview.md) ajuda a proteger os dados do aplicativo e tem custos operacionais mínimos. Erros de aplicativo podem corromper seus dados e erros humanos podem introduzir bugs em seus aplicativos. O Azure Backup protege suas VMs que executam Windows e Linux. | - |
| Use várias VMs para maior resiliência e disponibilidade. | Se a VM for executada em aplicativos que devem estar altamente disponíveis, use várias VMs ou [conjuntos de disponibilidade](../articles/virtual-machines/windows/manage-availability.md). | - |
| Adotar uma estratégia de continuidade de negócios e recuperação de desastres (BCDR). | O Azure Site Recovery permite que você escolha entre diferentes opções projetadas para suportar a continuidade de negócios. Ele suporta diferentes cenários de replicação e failover. Para obter mais informações, consulte [Sobre a Recuperação do Site](../articles/site-recovery/site-recovery-overview.md). | - |

## <a name="data-security"></a>Segurança de dados

| Recomendação | Comentários | Central de Segurança |
|-|----|--|
| Criptografe discos do sistema operacional. | [O Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) ajuda você a criptografar seus discos VM Windows e Linux IaaS. Sem as chaves necessárias, o conteúdo dos discos criptografados é ilegível. A criptografia de disco protege os dados armazenados de acesso não autorizado que de outra forma seria possível se o disco fosse copiado.| [Sim](../articles/security-center/security-center-apply-disk-encryption.md) |
| Criptografe discos de dados. | [O Azure Disk Encryption](../articles/security/azure-security-disk-encryption-overview.md) ajuda você a criptografar seus discos VM Windows e Linux IaaS. Sem as chaves necessárias, o conteúdo dos discos criptografados é ilegível. A criptografia de disco protege os dados armazenados de acesso não autorizado que de outra forma seria possível se o disco fosse copiado.| -  |
| Limitar o software instalado. | Limite o software instalado ao que for necessário para aplicar com sucesso sua solução. Esta diretriz ajuda a reduzir a superfície de ataque da sua solução. | - |
| Use antivírus ou antimalware. | No Azure, você pode usar software antimalware de fornecedores de segurança como Microsoft, Symantec, Trend Micro e Kaspersky. Este software ajuda a proteger suas VMs contra arquivos maliciosos, adware e outras ameaças. Você pode implantar o Microsoft Antimalware com base nas cargas de trabalho do aplicativo. Use configuração personalizada básica segura por padrão ou avançada. Para obter mais informações, consulte [o Microsoft Antimalware for Azure Cloud Services and Virtual Machines](../articles/security/azure-security-antimalware.md). | - |
| Guarde com segurança chaves e segredos. | Simplifique o gerenciamento de seus segredos e chaves, fornecendo aos proprietários de aplicativos uma opção segura e gerenciada centralmente. Esta gestão reduz o risco de um compromisso acidental ou vazamento. O Azure Key Vault pode armazenar com segurança suas chaves em módulos de segurança de hardware (HSMs) certificados para fips 140-2 Nível 2. Se você precisar usar FIPs 140.2 Nível 3 para armazenar suas chaves e segredos, você pode usar [o HSM Dedicado do Azure](../articles/dedicated-hsm/overview.md). | - |

## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso 

| Recomendação | Comentários | Central de Segurança |
|-|----|--|
| Centralize a autenticação de VM. | Você pode centralizar a autenticação de suas VMs Windows e Linux usando [a autenticação do Azure Active Directory](../articles/active-directory/develop/authentication-scenarios.md). | - |

## <a name="monitoring"></a>Monitoramento

| Recomendação | Comentários | Central de Segurança |
|-|----|--|
| Monitore suas VMs. | Você pode usar [o Monitor Azure para VMs](../articles/azure-monitor/insights/vminsights-overview.md) para monitorar o estado de suas VMs azure e conjuntos de escala de máquinas virtuais. Problemas de desempenho com uma máquina virtual podem levar a interrupção do serviço, o que viola o princípio de segurança de disponibilidade. | - |

## <a name="networking"></a>Rede

| Recomendação | Comentários | Central de Segurança |
|-|----|--|
| Restringir o acesso aos portos de gestão. | Os invasores verificam os intervalos de IP da nuvem pública para portas de gerenciamento abertas e tentam ataques "fáceis", como senhas comuns e vulnerabilidades não corrigidas conhecidas. Você pode usar [o acesso VM just-in-time (JIT)](../articles/security-center/security-center-just-in-time.md) para bloquear o tráfego de entrada de suas VMs azure, reduzindo a exposição a ataques e fornecendo conexões fáceis para VMs quando necessário. | - |
| Limitar o acesso à rede. | Os grupos de segurança da rede permitem restringir o acesso à rede e controlar o número de pontos finais expostos. Para obter mais informações, consulte [Criar, alterar ou excluir um grupo de segurança de rede](../articles/virtual-network/manage-network-security-group.md). | - |

## <a name="next-steps"></a>Próximas etapas

Verifique com seu provedor de aplicativos para saber sobre os requisitos adicionais de segurança. Para obter mais informações sobre o desenvolvimento de aplicativos seguros, consulte [documentação de desenvolvimento seguro](../articles/security/fundamentals/abstract-develop-secure-apps.md).
