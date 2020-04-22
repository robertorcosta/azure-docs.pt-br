---
title: Benefícios de usar arquivos Do Azure NetApp com banco de dados Oracle | Microsoft Docs
description: Descreve a tecnologia e fornece uma comparação de desempenho entre o Oracle Direct NFS (dNFS) e o cliente NFS tradicional. Mostra as vantagens de usar o dNFS com arquivos Do Azure NetApp.
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
ms.date: 04/20/2020
ms.author: b-juche
ms.openlocfilehash: d28f5469174a2659869ebb01ef01653b7190e30e
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81772074"
---
# <a name="benefits-of-using-azure-netapp-files-with-oracle-database"></a>Benefícios de usar arquivos Do Azure NetApp com o Banco de Dados Oracle

O Oracle Direct NFS (dNFS) permite obter um desempenho superior ao driver NFS do próprio sistema operacional. Este artigo explica a tecnologia e fornece uma comparação de desempenho entre o dNFS e o cliente NFS tradicional (Kernel NFS). Ele também mostra as vantagens e a facilidade de usar o dNFS com arquivos Do Azure NetApp.  

## <a name="how-oracle-direct-nfs-works"></a>Como funciona o Oracle Direct NFS

O Oracle Direct NFS (dNFS) ignora o cache de buffer do sistema operacional. Os dados são armazenados em cache apenas uma vez no espaço do usuário, eliminando a sobrecarga das cópias de memória.  

O cliente NFS tradicional usa um único fluxo de rede, como mostra o exemplo a seguir: 

![Cliente NFS tradicional usando um único fluxo de rede](../media/azure-netapp-files/solutions-traditional-nfs-client-using-single-network-flow.png)

Em contraste, o Oracle dNFS melhora o desempenho equilibrando o tráfego de rede em vários fluxos de rede. Esse recurso permite que o banco de dados Oracle estabeleça dinamicamente um número significativo de 650 conexões de rede distintas, como mostrado no exemplo abaixo:  

![Oracle Direct NFS melhorando o desempenho](../media/azure-netapp-files/solutions-oracle-direct-nfs-performance-load-balancing.png)

O [Oracle FAQ for Direct NFS](http://www.orafaq.com/wiki/Direct_NFS) mostra que o Oracle dNFS é um cliente NFS otimizado. Ele fornece acesso rápido e escalável ao armazenamento NFS que está localizado em dispositivos de armazenamento NAS (acessíveis sobre TCP/IP). O dNFS é incorporado no kernel de banco de dados, assim como o ASM, que é usado principalmente com armazenamento DAS ou SAN. Como tal, *a diretriz é usar o dNFS ao implementar o armazenamento NAS e usar o ASM ao implementar o armazenamento de SAN.*

dNFS é a opção padrão no Oracle 18c e o padrão para RAC.

O dNFS está disponível a partir do Oracle Database 11g. O diagrama abaixo compara o dNFS com o NFS nativo. Quando você usa dNFS, um banco de dados Oracle que é executado em uma máquina virtual do Azure pode dirigir mais I/O do que o cliente NFS nativo.

![Comparação de arquivos Oracle e Azure NetApp do dNFS com NFS nativo](../media/azure-netapp-files/solutions-oracle-azure-netapp-files-comparing-dnfs-native-nfs.png)

Você pode ativar ou desativar o dNFS executando dois comandos e reiniciando o banco de dados.

Para habilitar:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_on`

Para desativar:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_off`

## <a name="azure-netapp-files-combined-with-oracle-direct-nfs"></a>Arquivos Do Azure NetApp combinados com o Oracle Direct NFS

Você pode melhorar o desempenho do Oracle dNFS com o serviço Azure NetApp Files. O serviço lhe dá controle total sobre o desempenho do aplicativo. Ele pode atender a aplicações extremamente exigentes. A combinação de Oracle dNFS com Azure NetApp Files fornece grande vantagem para suas cargas de trabalho.

## <a name="next-steps"></a>Próximas etapas

- [Arquiteturas da solução usando o Azure NetApp Files](azure-netapp-files-solution-architectures.md)
- [Visão geral dos aplicativos e soluções Oracle no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-overview)