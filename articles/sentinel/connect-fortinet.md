---
title: Conectar a Fortinet data ao Azure Sentinel | Microsoft Docs
description: Conecte seu dispositivo Fortinet ao Azure Sentinel para exibir painéis, criar alertas personalizados e melhorar a investigação. 
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 8aa8599cbaab6af00d7b4122b94c9e24870881f3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86511323"
---
# <a name="connect-fortinet-to-azure-sentinel"></a>Conectar o Fortinet ao Azure Sentinel



Este artigo explica como conectar seu dispositivo Fortinet ao Azure Sentinel. O Fortinet data Connector permite que você conecte facilmente seus logs da Fortinet com o Azure Sentinel, exiba painéis, crie alertas personalizados e melhore a investigação. O uso do Fortinet no Azure Sentinel fornecerá mais informações sobre o uso da Internet da sua organização e aprimorará seus recursos de operação de segurança. 


 
## <a name="forward-fortinet-logs-to-the-syslog-agent"></a>Encaminhe os logs da Fortinet para o agente de syslog

Configure a Fortinet para encaminhar mensagens de syslog no formato CEF para seu espaço de trabalho do Azure por meio do agente de syslog.

1. Abra a CLI no seu dispositivo Fortinet e execute os seguintes comandos:

    ```console
    config log syslogd setting
    set format cef
    set port 514
    set server <ip_address_of_Receiver>
    set status enable
    end
    ```

    - Substitua o **endereço IP** do servidor pelo endereço IP do agente.
    - Defina a **porta do syslog** como **514** ou a porta definida no agente.
    - Para habilitar o formato CEF nas versões iniciais do FortiOS, talvez seja necessário executar o comando Set **CSV Disable**.
 
   > [!NOTE] 
   > Para obter mais informações, vá para a [biblioteca de documentos do Fortinet](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary). Selecione sua versão e use o **manual** e a **referência de mensagem de log**.

1. Para usar o esquema relevante no Azure Monitor Log Analytics para os eventos da Fortinet, procure `CommonSecurityLog` .

1. Prossiga para a [ETAPA 3: Validar a conectividade](connect-cef-verify.md).


## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a conectar o Fortinet appliances ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.


