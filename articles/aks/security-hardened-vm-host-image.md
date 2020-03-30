---
title: Endurecimento de segurança em hosts de máquinas virtuais AKS
description: Conheça o endurecimento de segurança no sistema operacional host AKS VM
services: container-service
author: saudas
ms.topic: article
ms.date: 09/11/2019
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: d4105a9fba3c40c563198040afb811625727ead0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594373"
---
# <a name="security-hardening-in-aks-virtual-machine-hosts"></a>Endurecimento de segurança em hosts de máquinas virtuais AKS 

O Azure Kubernetes Service (AKS) é um serviço seguro compatível com os padrões SOC, ISO, PCI DSS e HIPAA. Este artigo abrange o endurecimento de segurança aplicado aos hosts de máquinas virtuais AKS. Para obter mais informações sobre a segurança da AKS, consulte [conceitos de segurança para aplicativos e clusters no Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/concepts-security).

Os clusters AKS são implantados em máquinas virtuais host, que executam um sistema operacional otimizado de segurança. Este sistema operacional host é atualmente baseado em uma imagem Ubuntu 16.04.LTS com um conjunto de etapas adicionais de endurecimento de segurança aplicadas (ver detalhes de endurecimento de segurança).   

O objetivo do sistema operacional host endurecido de segurança é reduzir a área de superfície de ataque e permitir a implantação de contêineres de forma segura. 

> [!Important]
> O sistema operacional endurecido de segurança não é cis comparada. Embora existam sobreposições com os benchmarks da CIS, o objetivo não é ser compatível com cis. O objetivo do endurecimento do sistema operacional host é convergir em um nível de segurança consistente com os padrões internos de segurança do host da Própria Microsoft. 

## <a name="security-hardening-features"></a>Recursos de endurecimento de segurança 

* O AKS fornece um sistema operacional host otimizado de segurança por padrão. Não há opção atual para selecionar um sistema operacional alternativo. 

* O Azure aplica patches diários (incluindo patches de segurança) aos hosts de máquinas virtuais AKS. Alguns desses patches exigirão uma reinicialização, enquanto outros não. Você é responsável por agendar reinicializações do host AKS VM conforme necessário. Para obter orientações sobre como automatizar patches AKS, consulte [os nomes AKS de correção](https://docs.microsoft.com/azure/aks/node-updates-kured).

Abaixo está um resumo do trabalho de endurecimento de imagem que é implementado no AKS-Engine para produzir o sistema operacional host otimizado de segurança. O trabalho foi implementado [neste projeto GitHub.](https://github.com/Azure/aks-engine/projects/7)  

O AKS-Engine não promove ou adere a qualquer padrão de segurança específico no momento, mas os IDs de auditoria cis (Center for Internet Security) são fornecidos para conveniência, quando aplicável. 

## <a name="whats-configured"></a>O que está configurado?

| Cis  | Descrição da auditoria| 
|---|---|
| 1.1.1.1 |Certifique-se de que a montagem de sistemas de arquivos cramfs está desativada|
| 1.1.1.2 |Certifique-se de que a montagem de sistemas de arquivos freevxfs está desativada|
| 1.1.1.3 |Certifique-se de que a montagem dos sistemas de arquivos jffs2 está desativada|
| 1.1.1.4 |Certifique-se de que a montagem dos sistemas de arquivos HFS está desativada|
| 1.1.1.5 |Certifique-se de que a montagem dos sistemas de arquivos HFS Plus está desativada|
|1.4.3 |Garantir a autenticação necessária para um único modo de usuário |
|1.7.1.2 |Certifique-se de que o banner de aviso de login local esteja configurado corretamente |
|1.7.1.3 |Certifique-se de que o banner de aviso de login remoto esteja configurado corretamente |
|1.7.1.5 |Certifique-se de que as permissões em /etc/issue estão configuradas |
|1.7.1.6 |Certifique-se de que as permissões em /etc/issue.net estão configuradas |
|2.1.5 |Certifique-se de que --streaming-conexão-tempo-tempo não está definido como 0 |
|3.1.2 |Certifique-se de que o envio de redirecionamento de pacotes está desativado |
|3.2.1 |Certifique-se de que os pacotes roteados de origem não são aceitos |
|3.2.2 |Certifique-se de que os redirecionamentos do ICMP não sejam aceitos |
|3.2.3 |Certifique-se de que redirecionamentos seguros do ICMP não sejam aceitos |
|3.2.4 |Certifique-se de que pacotes suspeitos sejam registrados |
|3.3.1 |Certifique-se de que os anúncios do roteador IPv6 não sejam aceitos |
|3.5.1 |Certifique-se de que o DCCP está desativado |
|3.5.2 |Certifique-se de que o SCTP está desativado |
|3.5.3 |Certifique-se de que o RDS está desativado |
|3.5.4 |Certifique-se de que o TIPC está desativado |
|4.2.1.2 |Certifique-se de que o registro está configurado |
|5.1.2 |Certifique-se de que as permissões em /etc/crontab estão configuradas |
|5.2.4 |Certifique-se de que o encaminhamento do SSH X11 está desativado |
|5.2.5 |Certifique-se de que o SSH MaxAuthTries está definido como 4 ou menos |
|5.2.8 |Certifique-se de que o login raiz do SSH está desativado |
|5.2.10 |Certifique-se de que o SSH PermitUserEnvironment está desativado |
|5.2.11 |Certifique-se de que apenas algoritmos MAX aprovados sejam usados |
|5.2.12 |Certifique-se de que o intervalo de tempo de ocioso SSH está configurado |
|5.2.13 |Certifique-se de que o SSH LoginGraceTime está definido como um minuto ou menos |
|5.2.15 |Certifique-se de que o banner de aviso SSH está configurado |
|5.3.1 |Certifique-se de que os requisitos de criação de senhas sejam configurados |
|5.4.1.1 |Certifique-se de que o vencimento da senha é de 90 dias ou menos |
|5.4.1.4 |Certifique-se de que o bloqueio de senha inativo é de 30 dias ou menos |
|5.4.4 |Certifique-se de que o umask padrão do usuário é 027 ou mais restritivo |
|5.6 |Certifique-se de que o acesso ao comando su é restrito|

## <a name="additional-notes"></a>Observações adicionais
 
* Para reduzir ainda mais a área da superfície de ataque, alguns drivers de módulo de kernel desnecessários foram desativados no sistema operacional. 

* O sistema operacional endurecido de segurança NÃO é suportado fora da plataforma AKS. 

## <a name="next-steps"></a>Próximas etapas  

Consulte os artigos a seguir para obter mais informações sobre a segurança da AKS: 

[Serviço Azure Kubernetes (AKS)](https://docs.microsoft.com/azure/aks/intro-kubernetes)

[Considerações de segurança aks](https://docs.microsoft.com/azure/aks/concepts-security)

[Melhores práticas da AKS](https://docs.microsoft.com/azure/aks/best-practices)
