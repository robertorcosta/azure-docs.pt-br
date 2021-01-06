---
title: O que há de novo no Azure defender para IoT
description: Este artigo permite que você saiba o que há de novo na versão mais recente do defender para IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2021
ms.author: shhazam
ms.openlocfilehash: 0281ebcdf1da27513e9b9256b508d911ec7697b5
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937553"
---
# <a name="whats-new"></a>O que há de novo

O defender para IoT 10,0 fornece aprimoramentos de recursos que melhoram a segurança, o gerenciamento e a usabilidade.

## <a name="security"></a>Segurança

Foram feitos aprimoramentos de recuperação de senha e certificado para esta versão.

### <a name="certificates"></a>Certificados
  
Esta versão permite que você:

- Carregue certificados SSL diretamente para os sensores e consoles de gerenciamento locais.
- Execute a validação entre o console de gerenciamento local e os sensores conectados e entre um console de gerenciamento e um console de gerenciamento de alta disponibilidade. A validação é baseada em datas de expiração, autenticidade da CA raiz e listas de certificados revogados.  Se a validação falhar, a sessão não continuará.

Para atualizações:

- Não há nenhuma alteração no certificado SSL ou na funcionalidade de validação durante a atualização.
- Após a atualização, os usuários administrativos do console de gerenciamento local e do sensor podem substituir os certificados SSL ou ativar a validação do certificado SSL na janela Configurações do sistema, certificado SSL.  

Para instalações novas:

- Durante o primeiro logon, os usuários precisam usar um certificado SSL (recomendado) ou um certificado autoassinado gerado localmente (não recomendado)
- A validação de certificado é ativada por padrão para instalações novas.

### <a name="password-recovery"></a>Recuperação de senha
  
Os usuários administrativos do sensor e do console de gerenciamento local agora podem recuperar senhas do Azure defender para o portal de IoT. Antes, a recuperação de senha exigia a intervenção da equipe de suporte.

## <a name="onboarding"></a>Integração

### <a name="on-premises-management-console---committed-devices"></a>Console de gerenciamento local-dispositivos confirmados

Seguindo a entrada inicial do console de gerenciamento local, agora os usuários são solicitados a carregar um arquivo de ativação. O arquivo contém o número agregado de dispositivos a serem monitorados na rede organizacional. Esse número é referido como o número de dispositivos confirmados.
Os dispositivos confirmados são definidos durante o processo de integração no Azure defender para o portal de IoT, no qual o arquivo de ativação é gerado.
Os usuários de primeira vez e os usuários que estão atualizando são necessários para carregar o arquivo de ativação.
Após a ativação inicial, o número de dispositivos detectados na rede pode exceder o número de dispositivos confirmados. Esse evento pode acontecer, por exemplo, se você conectar mais sensores ao console de gerenciamento. Se houver uma discrepância entre o número de dispositivos detectados e o número de dispositivos confirmados, um aviso aparecerá no console de gerenciamento. Se esse evento ocorrer, você deverá carregar um novo arquivo de ativação.

### <a name="pricing-page-options"></a>Opções de página de preços

A página de preços permite que você integre novas assinaturas ao Azure defender para IoT e defina os dispositivos confirmados em sua rede.  
Além disso, a página de preços agora permite que você gerencie as assinaturas existentes associadas a um sensor e atualize o compromisso do dispositivo.

### <a name="view-and-manage-onboarded-sensors"></a>Exibir e gerenciar sensores integrados

Uma nova página do portal de sensores e sites permite que você:

- Adicione informações descritivas sobre o sensor. Por exemplo, uma zona associada ao sensor ou marcas de texto livre.
- Exiba e filtre informações do sensor. Por exemplo, exiba detalhes sobre os sensores que estão conectados à nuvem ou localmente gerenciados ou exibam informações sobre sensores em uma zona específica.  

## <a name="usability"></a>Usabilidade

### <a name="azure-sentinel-new-connector-page"></a>Página novo conector do Azure Sentinel

A página do conector de dados do Azure defender para IoT no Azure Sentinel foi reformulada. O conector de dados agora é baseado em assinaturas em vez de hubs IoT; permitir que os clientes gerenciem melhor sua conexão de configuração para o Azure Sentinel.

### <a name="azure-portal-permission-updates"></a>portal do Azure atualizações de permissão  

O leitor de segurança e o suporte ao administrador de segurança foram adicionados.

## <a name="other-updates"></a>Outras atualizações

### <a name="access-group---zone-permissions"></a>Permissões de zona de grupo de acesso
  
As regras de grupo de acesso do console de gerenciamento local não incluirão a opção de conceder acesso a uma zona específica. Não há nenhuma alteração na definição de regras que usam sites, regiões e unidades de negócios.   Após a atualização, os grupos de acesso que continham regras que permitem o acesso a zonas específicas serão modificados para permitir o acesso ao site pai, incluindo todas as suas zonas.

### <a name="terminology-changes"></a>Alterações de terminologia

O termo ativo foi renomeado como dispositivo no sensor e no console de gerenciamento local, relatórios e outras interfaces de solução.
Nos alertas do console de gerenciamento local e do sensor, o termo gerenciar esse evento tem sido chamado de etapas de correção.

## <a name="next-steps"></a>Próximas etapas

[Introdução ao defender para IoT](getting-started.md)
