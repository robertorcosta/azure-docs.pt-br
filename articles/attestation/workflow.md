---
title: Fluxo de trabalho do Atestado do Azure
description: O fluxo de trabalho do Atestado do Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 240b27f897d8e7a34026701cf7fdc844eb9d4086
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236806"
---
# <a name="workflow"></a>Fluxo de trabalho

O Atestado do Microsoft Azure recebe as evidências de enclaves e a avalia em relação à linha de base de segurança do Azure e às políticas configuráveis. Após a confirmação bem-sucedida, o Atestado do Azure gera um token de atestado para confirmar a confiabilidade do enclave.

Os seguintes atores estão envolvidos em um fluxo de trabalho do Atestado do Azure:

- **Terceira parte confiável**: o componente que se baseia no Atestado do Azure para verificar a validade do enclave. 
- **Cliente**: o componente que coleta informações de um enclave e envia solicitações para o Atestado do Azure. 
- **Atestado do Azure**: o componente que aceita as evidências do enclave por parte do cliente, valida-a e retorna o token de atestado para o cliente


## <a name="enclave-validation-work-flow"></a>Fluxo de trabalho de validação de enclave

Estas são as etapas gerais em um fluxo de trabalho típico de atestado de enclave do SGX (usando o Atestado do Azure):

1. O cliente coleta as evidências de um enclave. As evidências são informações sobre o ambiente do enclave e a biblioteca de clientes em execução no enclave.
1. O cliente tem um URI que se refere a uma instância do Atestado do Azure. O cliente é autenticado no Azure AD e obtém um token de acesso.
1. O cliente envias evidências para o Atestado do Azure junto com o token de acesso. As informações exatas enviadas ao provedor dependem do tipo de enclave.
1. O Atestado do Azure valida as informações enviadas e avalia-as em relação a uma política configurada. Se a verificação for realizada com sucesso, o Atestado do Azure emitirá um token de atestado e o retornará ao cliente. Se essa etapa falhar, o Atestado do Azure relatará um erro ao cliente. 
1. O cliente envia o token de atestado para a terceira parte confiável. A terceira parte confiável chama o ponto de extremidade de metadados de chave pública do Atestado do Azure para recuperar os certificados de autenticação. Em seguida, a terceira parte confiável verifica a assinatura do token de atestado e garante a confiabilidade do enclave. 

![Fluxo de validação do enclave](./media/validation-flow.png)


## <a name="next-steps"></a>Próximas etapas
- [Como criar e assinar uma política de atestado](author-sign-policy.md)
- [Configurar o Atestado do Azure usando o PowerShell](quickstart-powershell.md)
