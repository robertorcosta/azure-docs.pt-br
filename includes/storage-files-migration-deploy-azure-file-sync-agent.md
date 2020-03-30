---
title: Implantando o agente Azure File Sync
description: Implantando o agente Azure File Sync. Um bloco de texto comum, compartilhado entre os docs de migração.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 694becc49667204ef2071a140bb6330285089039
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209409"
---
Nesta seção, você instala o agente Azure File Sync no seu Servidor Windows.
O [guia de implantação](../articles/storage/files/storage-sync-files-deployment-guide.md) ilustra que você precisa desativar a segurança reforçada do **IE**. A segurança reforçada do IE é uma medida de segurança que não é aplicável ao Azure File Sync e desligá-lo permite que você se autentique no Azure sem problemas.

Abra o PowerShell e instale os módulos PowerShell necessários com os seguintes comandos. Certifique-se de instalar o módulo completo e o provedor NuGet quando solicitado:

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

Se você tem algum problema em chegar à internet a partir de seu servidor, agora é a hora de resolvê-los. O Azure File Sync usa qualquer conexão de rede disponível à internet.
Também é suportado o suporte de um servidor proxy para acessar a internet. Você pode configurar um proxy em toda a máquina agora ou especificar um proxy que apenas a sincronização de arquivos usará durante a instalação do agente.

Se isso significa que você precisa abrir seus firewalls para este servidor, então essa pode ser uma abordagem aceitável para você. No final da instalação do servidor, após o registro do servidor concluído, haverá um relatório de conectividade de rede mostrando os URLs de ponto final exatos no Azure, que a sincronização de arquivos precisa para se comunicar com a região que você selecionou. O relatório também informa a razão pela qual a comunicação é necessária. Você pode usar o relatório para bloquear os firewalls ao redor deste servidor, para URLs específicos.

Você também pode seguir uma abordagem mais conservadora, na qual você não abre os firewalls de largura, mas limita o servidor para se comunicar com espaços de nomes DNS de nível superior - há mais documentação e detalhes disponíveis no artigo de configurações de proxy e firewall do [Azure File Sync.](../articles/storage/files/storage-sync-files-firewall-and-proxy.md) Siga suas próprias práticas recomendadas em rede.

No final do assistente de *instalação* do servidor, um assistente de *registro* do servidor aparecerá.
Registre o servidor no recurso Storage Sync Service Azure de antes.

Essas etapas são descritas com mais detalhes no guia de implantação, incluindo os módulos PowerShell acima que você deve instalar primeiro: [instalação do agente Azure File Sync](../articles/storage/files/storage-sync-files-deployment-guide.md).

O agente mais recente deve ser usado e pode ser baixado no Microsoft Download Center: [Azure File Sync - agent](https://aka.ms/AFS/agent "Download do agente Azure File Sync").

Após uma instalação bem-sucedida e registro do servidor, você pode verificar se você completou com sucesso esta etapa: Navegue até o recurso do Serviço de Sincronização de Armazenamento no portal Azure e siga o menu à esquerda para "Servidores registrados". Você verá seu servidor listado lá imediatamente.
