---
title: include file
description: include file
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/11/2021
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3da4fd26b3f985e034ca60039c09412e8237e965
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98109451"
---
Führen Sie diese Schritte aus, wenn Sie ein allgemeines Speicherkonto vom Typ „General Purpose v2“ über das Azure-Portal erstellen möchten:

1. Wählen Sie im Menü des Azure-Portals **Alle Dienste** aus. Geben Sie in der Liste mit den Ressourcen **Speicherkonten** ein. Sobald Sie mit der Eingabe beginnen, wird die Liste auf der Grundlage Ihrer Eingabe gefiltert. Wählen Sie **Speicherkonten**.
1. Klicken Sie im angezeigten Fenster **Speicherkonten** auf **Hinzufügen**.
1. Wählen Sie auf der Registerkarte **Grundlagen** das Abonnement aus, in dem das Speicherkonto erstellt werden soll.
1. Wählen Sie unter dem Feld **Ressourcengruppe** Ihre gewünschte Ressourcengruppe aus, oder erstellen Sie eine neue.  Weitere Informationen zu Azure-Ressourcengruppen finden Sie unter [Übersicht über den Azure Resource Manager](../articles/azure-resource-manager/management/overview.md).
1. Geben Sie als Nächstes einen Namen für Ihr Speicherkonto ein. Der gewählte Name muss innerhalb von Azure eindeutig sein. Der Name muss ebenfalls zwischen 3 und 24 Zeichen lang sein und darf nur Zahlen und Kleinbuchstaben enthalten.
1. Wählen Sie einen Standort für Ihr Speicherkonto aus, oder verwenden Sie den Standardstandort.
1. Wählen Sie eine Leistungsstufe aus. Die Standardstufe ist *Standard*.
1. Legen Sie das Feld **Kontoart** auf *V2 (General Purpose v2)* fest.
1. Geben Sie an, wie das Speicherkonto repliziert werden soll. Die Standardreplikationsoption ist *Georedundanter Speicher mit Lesezugriff (RA-GRS)* . Weitere Informationen zu verfügbaren Replikationsoptionen finden Sie unter [Azure Storage-Redundanz](../articles/storage/common/storage-redundancy.md).
1. Weitere Optionen sind auf den Registerkarten **Netzwerk**, **Schutz von Daten**, **Erweitert** und **Tags** verfügbar. Wenn Sie Azure Data Lake Storage verwenden möchten, wählen Sie die Registerkarte **Erweitert** aus, und legen Sie dann **Hierarchischer Namespace** auf **Aktiviert** fest. Weitere Informationen finden Sie unter [Azure Data Lake Storage Gen2: Einführung](../articles/storage/blobs/data-lake-storage-introduction.md).
1. Wählen Sie **Überprüfen + erstellen**, um die Speicherkontoeinstellungen zu überprüfen und das Konto zu erstellen.
1. Klicken Sie auf **Erstellen**.

Die folgende Abbildung zeigt die Einstellungen auf der Registerkarte **Grundlagen** für ein neues Speicherkonto:

:::image type="content" source="media/storage-create-account-portal-include/account-create-portal.png" alt-text="Screenshot: Erstellen eines Speicherkontos im Azure-Portal":::
