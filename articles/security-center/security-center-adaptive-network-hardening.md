---
title: Proteção de rede adaptável na central de segurança do Azure | Microsoft Docs
description: Saiba como usar os padrões de tráfego reais para proteger suas regras de NSG (grupos de segurança de rede) e melhorar ainda mais sua postura de segurança.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 09d62d23-ab32-41f0-a5cf-8d80578181dd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/11/2020
ms.author: memildin
ms.openlocfilehash: bc610fa1d7a5fa1a10db3298164404b92d5d9f85
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2020
ms.locfileid: "79139582"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Proteção de rede adaptável na central de segurança do Azure
Saiba como configurar a proteção de rede adaptável na central de segurança do Azure.

## <a name="what-is-adaptive-network-hardening"></a>O que é a proteção de rede adaptável?
A aplicação de [NSG (grupos de segurança de rede)](https://docs.microsoft.com/azure/virtual-network/security-overview) para filtrar o tráfego de e para os recursos, melhora a sua postura de segurança de rede. No entanto, ainda pode haver alguns casos em que o tráfego real que flui pelo NSG é um subconjunto das regras de NSG definidas. Nesses casos, é possível melhorar ainda mais a postura de segurança ao proteger as regras de NSG, com base nos padrões reais de tráfego.

A proteção de rede adaptável fornece recomendações para proteger ainda mais as regras de NSG. Ele usa um algoritmo de aprendizado de máquina que fatores em tráfego real, configuração confiável conhecida, inteligência contra ameaças e outros indicadores de comprometimento e, em seguida, fornece recomendações para permitir o tráfego somente de tuplas de IP/porta específicas.

Por exemplo, digamos que a regra NSG existente seja permitir o tráfego de 140.20.30.10/24 na porta 22. A recomendação do fortalecimento da rede adaptável, com base na análise, seria restringir o intervalo e permitir o tráfego de 140.23.30.10/29 – que é um intervalo de IP mais estreito e negar todos os outros tráfegos para essa porta.

>[!TIP]
> As recomendações de proteção de rede adaptável só têm suporte em portas específicas. Para obter a lista completa, consulte [quais portas têm suporte?](#which-ports-are-supported) abaixo. 


![Exibição de proteção de rede](./media/security-center-adaptive-network-hardening/traffic-hardening.png)


## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>Exibir alertas e regras de proteção de rede adaptável

1. Na central de segurança, selecione **rede** -> **proteção de rede adaptável**. As VMs de rede são listadas em três guias separadas:
   * **Recursos não íntegros**: VMs que atualmente têm recomendações e alertas que foram disparados executando o algoritmo de proteção de rede adaptável. 
   * **Recursos íntegros**: VMs sem alertas e recomendações.
   * **Recursos não verificados**: VMs para as quais o algoritmo de proteção de rede adaptável não pode ser executado devido a um dos seguintes motivos:
      * As VMs **são VMs clássicas**: há suporte apenas para VMs Azure Resource Manager.
      * **Não há dados suficientes disponíveis**: para gerar recomendações precisas de proteção de tráfego, a central de segurança requer pelo menos 30 dias de dados de tráfego.
      * A **VM não está protegida pelo ASC Standard**: somente as VMs definidas como o tipo de preço Standard da central de segurança são elegíveis para esse recurso.

     ![recursos não íntegros](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. Na guia **recursos não íntegros** , selecione uma VM para exibir seus alertas e as regras de proteção recomendadas a serem aplicadas.

    ![protegendo alertas](./media/security-center-adaptive-network-hardening/anh-recommendation-rules.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>Examinar e aplicar regras recomendadas de proteção de rede adaptável

1. Na guia **recursos não íntegros** , selecione uma VM. Os alertas e as regras de proteção recomendadas são listados.

     ![regras de proteção](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > A guia **regras** lista as regras que o fortalecimento de rede adaptável recomenda que você adicione. A guia **alertas** lista os alertas que foram gerados devido ao tráfego, fluindo para o recurso, que não está dentro do intervalo de IP permitido nas regras recomendadas.

2. Se você quiser alterar alguns dos parâmetros de uma regra, poderá modificá-los, conforme explicado em [modificar uma regra](#modify-rule).
   > [!NOTE]
   > Você também pode [excluir](#delete-rule) ou [Adicionar](#add-rule) uma regra.

3. Selecione as regras que você deseja aplicar no NSG e clique em **impor**.

      > [!NOTE]
      > As regras impostas são adicionadas às NSG que protegem a VM. (Uma VM pode ser protegida por um NSG associado à sua NIC, ou a sub-rede na qual a VM reside, ou ambos)

    ![impor regras](./media/security-center-adaptive-network-hardening/enforce-hard-rule2.png)


### Modificar uma regra <a name ="modify-rule"> </a>

Talvez você queira modificar os parâmetros de uma regra que tenha sido recomendada. Por exemplo, talvez você queira alterar os intervalos de IP recomendados.

Algumas diretrizes importantes para modificar uma regra de proteção de rede adaptável:

* Você pode modificar os parâmetros de "permitir" somente regras. 
* Você não pode alterar as regras "permitir" para se tornarem regras "Deny". 

  > [!NOTE]
  > Criar e modificar regras "Deny" é feito diretamente no NSG. Para obter mais informações, consulte [criar, alterar ou excluir um grupo de segurança de rede](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

* Uma regra **negar todo o tráfego** é o único tipo de regra "negar" que seria listado aqui e não pode ser modificado. Você pode, no entanto, excluí-lo (consulte [excluir uma regra](#delete-rule)).
  > [!NOTE]
  > Uma regra **negar todo o tráfego** é recomendada quando, como resultado da execução do algoritmo, a central de segurança não identifica o tráfego que deve ser permitido, com base na configuração existente do NSG. Portanto, a regra recomendada é negar todo o tráfego para a porta especificada. O nome desse tipo de regra é exibido como "*gerado pelo sistema*". Depois de impor essa regra, seu nome real no NSG será uma cadeia de caracteres composta pelo protocolo, direção do tráfego, "negar" e um número aleatório.

*Para modificar uma regra de proteção de rede adaptável:*

1. Para modificar alguns dos parâmetros de uma regra, na guia **regras** , clique nos três pontos (...) no final da linha da regra e clique em **Editar**.

   ![editar regra](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. Na janela **Editar regra** , atualize os detalhes que você deseja alterar e clique em **salvar**.

   > [!NOTE]
   > Depois de clicar em **salvar**, você alterou a regra com êxito. *No entanto, você não o aplicou ao NSG.* Para aplicá-lo, você deve selecionar a regra na lista e clicar em **impor** (conforme explicado na próxima etapa).

   ![editar regra](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. Para aplicar a regra atualizada, na lista, selecione a regra atualizada e clique em **impor**.

    ![impor regra](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### Adicionar uma nova regra <a name ="add-rule"> </a>

Você pode adicionar uma regra "permitir" que não foi recomendada pela central de segurança.

> [!NOTE]
> Somente as regras "permitir" podem ser adicionadas aqui. Se você quiser adicionar regras "Deny", poderá fazer isso diretamente no NSG. Para obter mais informações, consulte [criar, alterar ou excluir um grupo de segurança de rede](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

*Para adicionar uma regra de proteção de rede adaptável:*

1. Clique em **Adicionar regra** (localizado no canto superior esquerdo).

   ![Adicionar regra](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. Na janela **nova regra** , insira os detalhes e clique em **Adicionar**.

   > [!NOTE]
   > Depois de clicar em **Adicionar**, você adicionou a regra com êxito e ela está listada com as outras regras recomendadas. No entanto, você não o aplicou no NSG. Para ativá-lo, você deve selecionar a regra na lista e clicar em **impor** (conforme explicado na próxima etapa).

3. Para aplicar a nova regra, na lista, selecione a nova regra e clique em **impor**.

    ![impor regra](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### Excluir uma regra <a name ="delete-rule"> </a>

Quando necessário, você pode excluir uma regra recomendada para a sessão atual. Por exemplo, você pode determinar que a aplicação de uma regra sugerida poderia bloquear o tráfego legítimo.

*Para excluir uma regra de proteção de rede adaptável para sua sessão atual:*

1. Na guia **regras** , clique nos três pontos (...) no final da linha da regra e clique em **excluir**.  

    ![regras de proteção](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)

 

## <a name="which-ports-are-supported"></a>Quais portas têm suporte?

As recomendações de proteção de rede adaptável só têm suporte em portas específicas. Esta tabela fornece a lista completa:

|Porta|Protocolo|Serviço associado|
|:---:|:----:|:----|
|13|UDP|Serviço de dia|
|17|UDP|Protocolo QOTD|
|19|UDP|Protocolo de cobrança|
|22|TCP|SSH|
|23|TCP|Telnet|
|53|UDP|DNS|
|69|UDP|TFTP|
|81|TCP|Potencialmente mal-intencionado (nó de saída do TOR)|
|111|TCP/UDP|RPC|
|119|TCP|NNTP|
|123|UDP|NTP|
|135|TCP/UDP|Mapeador de ponto de extremidade; RPC DCE|
|137|TCP/UDP|Serviço de nomes NetBIOS|
|138|TCP/UDP|Serviço de datagrama NetBIOS|
|139|TCP|Serviço de sessão NetBIOS|
|161|TCP/UDP|Gets|
|162|TCP/UDP|Gets|
|389|TCP|LDAP|
|445|TCP|SMB|
|512|TCP|Rexec|
|514|TCP|Shell remoto|
|593|TCP/UDP|RPC HTTP|
|636|TCP|LDAP|
|873|TCP|Rsync|
|1433|TCP|MS SQL|
|1434|UDP|MS SQL|
|1900|UDP|SSDP|
|1900|UDP|SSDP|
|2049|TCP/UDP|NFS|
|2301|TCP|Serviço de gerenciamento da Compaq|
|2323|TCP|3D-nfsd|
|2381|TCP|Serviço de gerenciamento da Compaq|
|3268|TCP|LDAP|
|3306|TCP|MySQL|
|3389|TCP|RDP|
|4333|TCP|mSQL|
|5353|UDP|mDNS|
|5432|TCP|PostgreSQL|
|5555|TCP|Agente pessoal; HP OmniBack|
|5800|TCP|VNC|
|5900|TCP|Framebuffer remoto; VNC|
|5900|TCP|VNC|
|5985|TCP|Windows PowerShell|
|5986|TCP|Windows PowerShell|
|6379|TCP|Redis|
|6379|TCP|Redis|
|7000|TCP|Cassandra|
|7001|TCP|Cassandra|
|7199|TCP|Cassandra|
|8081|TCP|CosmosDB Administrador de proxy Sun|
|8089|TCP|Splunk|
|8545|TCP|Potencialmente mal-intencionado (Cryptominer)|
|9042|TCP|Cassandra|
|9160|TCP|Cassandra|
|9300|TCP|Elasticsearch|
|11211|UDP|Memcached|
|16379|TCP|Redis|
|26379|TCP|Redis|
|27017|TCP|MongoDB|
|37215|TCP|Potencialmente mal-intencionado|
||||