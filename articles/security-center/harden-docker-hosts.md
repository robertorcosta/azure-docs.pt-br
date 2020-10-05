---
title: Use a central de segurança do Azure para proteger os hosts do Docker e proteger os contêineres
description: Como proteger os hosts do Docker e verificar se eles estão em conformidade com o parâmetro de comparação do Docker do CIS
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 500fa45db7e0e6bffb587d9d352ee1ab49f14703
ms.sourcegitcommit: 5b69ba21787c07547edfbfd5254eaf34315cfadd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91712265"
---
# <a name="harden-your-docker-hosts"></a>Proteger os hosts do Docker

A central de segurança do Azure identifica contêineres não gerenciados hospedados em VMs do Linux IaaS ou em outros computadores Linux que executam contêineres do Docker. A central de segurança avalia continuamente as configurações desses contêineres. Em seguida, ele os compara com o [benchmark do Docker do CIS (Center for Internet Security)](https://www.cisecurity.org/benchmark/docker/).

A central de segurança inclui o conjunto de regras inteiro do parâmetro de comparação do Docker do CIS e o alerta se os contêineres não atenderem a nenhum dos controles. Quando ele encontra configurações incorretas, a central de segurança gera recomendações de segurança. Use a **página recomendações** da central de segurança para exibir recomendações e corrigir problemas.

Quando as vulnerabilidades são encontradas, elas são agrupadas dentro de uma única recomendação.

>[!NOTE]
> Essas verificações de parâmetro de comparação de CIS não serão executadas em instâncias gerenciadas por AKS ou em VMs gerenciadas por databricks.

## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|GA (em disponibilidade geral)|
|Preço:|Requer o [Azure defender para servidores](defender-for-servers-introduction.md)|
|Funções e permissões necessárias:|**Leitor** no espaço de trabalho ao qual o host se conecta|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) National/soberanas (US Gov, China gov, outros gov)|
|||

## <a name="identify-and-remediate-security-vulnerabilities-in-your-docker-configuration"></a>Identificar e corrigir vulnerabilidades de segurança na configuração do Docker

1. No menu da central de segurança, abra a página **recomendações** .

1. O filtro para as **vulnerabilidades de recomendação em configurações de segurança de contêiner deve ser corrigido** e selecionar a recomendação.

    A página recomendação mostra os recursos afetados (hosts do Docker). 

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-found.png" alt-text="Recomendação para corrigir vulnerabilidades em configurações de segurança de contêiner ":::

1. Para exibir e corrigir os controles de CIS que um host específico falhou, selecione o host que você deseja investigar. 

    > [!TIP]
    > Se você iniciou na página de inventário de ativos e chegou essa recomendação a partir daí, selec o botão **executar ação** na página recomendação.
    >
    > :::image type="content" source="./media/monitor-container-security/host-security-take-action-button.png" alt-text="Recomendação para corrigir vulnerabilidades em configurações de segurança de contêiner ":::

    Log Analytics é aberto com uma operação personalizada pronta para ser executada. A consulta personalizada padrão inclui uma lista de todas as regras com falha que foram avaliadas, juntamente com as diretrizes para ajudá-lo a resolver os problemas.

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-in-query.png" alt-text="Recomendação para corrigir vulnerabilidades em configurações de segurança de contêiner ":::

1. Ajuste os parâmetros de consulta, se necessário.

1. Quando tiver certeza de que o comando é apropriado e pronto para o host, selecione **executar**.


## <a name="next-steps"></a>Próximas etapas

A proteção do Docker é apenas um aspecto dos recursos de segurança do contêiner da central de segurança. 

Saiba mais [segurança de contêiner na central de segurança](container-security.md).