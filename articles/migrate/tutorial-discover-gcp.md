---
title: Ermitteln von GCP-VM-Instanzen mit der Azure Migrate-Serverbewertung
description: Hier erfahren Sie, wie Sie GCP-VM-Instanzen mit der Azure Migrate-Serverbewertung ermitteln.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: 181f645540a267d65b15a0345a61752a8a5f78fa
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704729"
---
# <a name="tutorial-discover-google-cloud-platform-gcp-instances-with-server-assessment"></a>Tutorial: Ermitteln von GCP-Instanzen (Google Cloud Platform) mit Serverbewertung

Im Rahmen der Migration zu Azure ermitteln Sie Ihre Server für die Bewertung und Migration.

In diesem Tutorial wird gezeigt, wie Sie GCP-Instanzen (Google Cloud Platform) mit dem über Azure Migrate bereitgestellten Serverbewertungstool und einer einfachen Azure Migrate-Appliance ermitteln. Sie stellen die Appliance auf einer GCP-VM-Instanz bereit, um kontinuierlich Computer- und Leistungsmetadaten zu ermitteln.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Einrichten eines Azure-Kontos
> * Vorbereiten der GCP-VM-Instanzen für die Ermittlung
> * Erstellen eines Azure Migrate-Projekts
> * Einrichten der Azure Migrate-Appliance
> * Starten der kontinuierlichen Ermittlung

> [!NOTE]
> Tutorials zeigen den schnellsten Weg zum Ausprobieren eines Szenarios, dabei kommen die Standardoptionen zum Einsatz.  

Wenn Sie kein Azure-Abonnement besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/), bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen, überprüfen Sie, ob die folgenden Voraussetzungen erfüllt sind:

**Anforderung** | **Details**
--- | ---
**Appliance** | Sie benötigen eine GCP-VM-Instanz für die Ausführung der Azure Migrate-Appliance. Der Computer sollte über Folgendes verfügen:<br/><br/> - Installation von Windows Server 2016. Das Ausführen der Appliance auf einem Computer mit Windows Server 2019 wird nicht unterstützt.<br/><br/> - 16 GB RAM, 8 vCPUs, etwa 80 GB Speicher auf dem Datenträger und einen externen virtuellen Switch<br/><br/> - eine statische oder dynamische IP-Adresse sowie Internetzugriff (entweder direkt oder über einen Proxy)
**Windows-VM-Instanzen** | Lassen Sie eingehende Verbindungen am WinRM-Port 5985 (HTTP) zu, sodass die Appliance Konfigurations- und Leistungsmetadaten pullen kann.
**Linux-VM-Instanzen** | Lassen Sie eingehende Verbindungen über Port 22 (TCP) zu.

## <a name="prepare-an-azure-user-account"></a>Vorbereiten eines Azure-Benutzerkontos

Zum Erstellen eines Azure Migrate-Projekts und Registrieren der Azure Migrate-Appliance benötigen Sie ein Konto mit den folgenden Berechtigungen:
- Berechtigungen vom Typ „Mitwirkender“ oder „Besitzer“ für ein Azure-Abonnement
- Berechtigungen zum Registrieren von Azure Active Directory-Apps

Wenn Sie gerade erst ein kostenloses Azure-Konto erstellt haben, sind Sie der Besitzer Ihres Abonnements. Wenn Sie nicht der Besitzer des Abonnements sind, müssen Sie mit dem Besitzer zusammenarbeiten, um die Berechtigungen wie folgt zuzuweisen:

1. Suchen Sie im Azure-Portal nach „Abonnements“, und wählen Sie unter **Dienste** die Option **Abonnements** aus.

    ![Suchfeld, um nach dem Azure-Abonnement zu suchen](./media/tutorial-discover-gcp/search-subscription.png)

2. Wählen Sie auf der Seite **Abonnements** das Abonnement aus, in dem Sie ein Azure Migrate-Projekt erstellen möchten. 
3. Wählen Sie im Abonnement die Option **Zugriffssteuerung (IAM)**  > **Zugriff überprüfen** aus.
4. Suchen Sie unter **Zugriff überprüfen** nach dem entsprechenden Benutzerkonto.
5. Klicken Sie unter **Rollenzuweisung hinzufügen** auf **Hinzufügen**.

    ![Suchen nach einem Benutzerkonto, um den Zugriff zu überprüfen und eine Rolle zuzuweisen](./media/tutorial-discover-gcp/azure-account-access.png)

6. Wählen Sie unter **Rollenzuweisung hinzufügen** die Rolle „Mitwirkender“ oder „Besitzer“ und das Konto aus (in unserem Beispiel „azmigrateuser“). Klicken Sie anschließend auf **Speichern**.

    ![Die Seite „Rollenzuweisung“ wird geöffnet, auf der Sie dem Konto eine Rolle zuweisen können.](./media/tutorial-discover-gcp/assign-role.png)

7. Suchen Sie im Portal nach Benutzern, und wählen Sie unter **Dienste** die Option **Benutzer** aus.
8. Vergewissern Sie sich unter **Benutzereinstellungen**, dass Azure AD-Benutzer Anwendungen registrieren können (standardmäßig auf **Ja** festgelegt).

    ![Überprüfen unter „Benutzereinstellungen“, ob Benutzer Active Directory-Apps registrieren können](./media/tutorial-discover-gcp/register-apps.png)


## <a name="prepare-gcp-instances"></a>Vorbereiten von GCP-Instanzen

Richten Sie ein Konto ein, das von der Appliance für den Zugriff auf GCP-VM-Instanzen verwendet werden kann.

- Für Windows-Server:
    - Richten Sie ein lokales Benutzerkonto auf Computern ein, die keiner Domäne beigetreten sind. Richten Sie außerdem ein Domänenkonto auf Computern ein, die keiner Domäne beigetreten sind und die Sie in die Ermittlung einschließen möchten. Fügen Sie das Benutzerkonto den folgenden Gruppen hinzu: 
        - Remoteverwaltungsbenutzer
        - Systemmonitorbenutzer
        - Leistungsprotokollbenutzer
- Für Linux-Server:
    - Sie benötigen ein root-Konto auf den Linux-Servern, die Sie ermitteln möchten. Wenn Sie kein Root-Konto bereitstellen können, lesen Sie die Anweisungen in der [Unterstützungsmatrix](migrate-support-matrix-physical.md#physical-server-requirements) für eine Alternative.
    - Azure Migrate verwendet bei der Ermittlung von AWS-Instanzen die Kennwortauthentifizierung. Die Kennwortauthentifizierung wird von AWS-Instanzen nicht standardmäßig unterstützt. Damit Sie eine Instanz ermitteln können, müssen Sie die Kennwortauthentifizierung aktivieren.
        1. Melden Sie sich bei jedem Linux-Computer an.
        2. Öffnen Sie die Datei „sshd_config“: vi /etc/ssh/sshd_config.
        3. Suchen Sie in der Datei die Zeile **PasswordAuthentication**, und ändern Sie den Wert in **yes**.
        4. Speichern Sie die Datei, und schließen Sie sie. Starten Sie den SSH-Dienst neu.
    - Wenn Sie einen Stammbenutzer zum Ermitteln Ihrer virtuellen Linux-Computer verwenden, stellen Sie sicher, dass die Stammanmeldung auf den virtuellen Computern zulässig ist.
        1. Melden Sie sich bei jedem Linux-Computer an.
        2. Öffnen Sie die Datei „sshd_config“: vi /etc/ssh/sshd_config.
        3. Suchen Sie in der Datei die Zeile **PermitRootLogin**, und ändern Sie den Wert in **yes**.
        4. Speichern Sie die Datei, und schließen Sie sie. Starten Sie den SSH-Dienst neu.

## <a name="set-up-a-project"></a>Einrichten eines Projekts

Richten Sie ein neues Azure Migrate-Projekt ein.

1. Wählen Sie im Azure-Portal **Alle Dienste** aus, und suchen Sie nach **Azure Migrate**.
2. Wählen Sie unter **Dienste** die Option **Azure Migrate** aus.
3. Wählen Sie unter **Übersicht** die Option **Create project** (Projekt erstellen) aus.
5. Wählen Sie unter **Create project** (Projekt erstellen) Ihr Azure-Abonnement und die Ressourcengruppe aus. Falls noch keine vorhanden ist, erstellen Sie eine Ressourcengruppe.
6. Geben Sie unter **Projektdetails** den Projektnamen und die geografische Region an, in der Sie das Projekt erstellen möchten. Beachten Sie die unterstützten geografischen Regionen für [öffentliche Clouds](migrate-support-matrix.md#supported-geographies-public-cloud) und [Azure Government-Clouds](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Felder für Projektname und Region](./media/tutorial-discover-gcp/new-project.png)

7. Klicken Sie auf **Erstellen**.
8. Warten Sie einige Minuten, bis das Azure Migrate-Projekt bereitgestellt wurde.

Das Tool **Azure Migrate- Serverbewertung** wird dem neuen Projekt standardmäßig hinzugefügt.

![Seite mit dem standardmäßig hinzugefügten Serverbewertungstool](./media/tutorial-discover-gcp/added-tool.png)

## <a name="set-up-the-appliance"></a>Einrichten der Appliance

Die Azure Migrate-Appliance ist eine einfache Appliance, die von der Azure Migrate-Serverbewertung für folgende Aufgaben verwendet wird:

- Ermitteln lokaler Server
- Senden von Meta- und Leistungsdaten für ermittelte Server an die Azure Migrate-Serverbewertung

[Erfahren Sie mehr](migrate-appliance.md) über die Azure Migrate-Appliance.


## <a name="appliance-deployment-steps"></a>Schritte für die Appliancebereitstellung

Die Einrichtung der Appliance umfasst Folgendes:
- Geben Sie einen Appliancenamen ein, und generieren Sie einen Azure Migrate-Projektschlüssel im Portal.
- Herunterladen einer gezippten Datei mit dem Azure Migrate-Installationsskript aus dem Azure-Portal.
- Extrahieren der Inhalte aus der gezippten Datei. Starten der PowerShell-Konsole mit Administratorrechten.
- Ausführen des PowerShell-Skripts zum Starten der Appliancewebanwendung.
- Führen Sie eine Erstkonfiguration für die Appliance aus, und registrieren Sie die Appliance beim Azure Migrate-Projekt unter Verwendung des Azure Migrate-Projektschlüssels.

### <a name="generate-the-azure-migrate-project-key"></a>Generieren des Azure Migrate-Projektschlüssels

1. Klicken Sie unter **Migrationsziele** > **Server** > **Azure Migrate: Server Assessment** (Azure Migrate-Serverbewertung) auf **Ermitteln**.
2. Wählen Sie unter **Computer ermitteln** > **Sind Ihre Computer virtualisiert?** die Option **Physisch/Andere (AWS, GCP, Xen usw.)** aus.
3. Geben Sie unter **1: Generate Azure Migrate project key** (1: Generieren eines Azure Migrate-Projektschlüssels) einen Namen für die Azure Migrate-Appliance ein, die Sie für die Ermittlung Ihrer virtuellen GCP-Server einrichten. Für den Namen können bis zu 14 alphanumerische Zeichen angegeben werden.
4. Klicken Sie auf **Schlüssel generieren**, um mit der Erstellung der erforderlichen Azure-Ressourcen zu beginnen. Schließen Sie die Seite „Computer ermitteln“ nicht, während die Ressourcen erstellt werden.
5. Nach der erfolgreichen Erstellung der Azure-Ressourcen wird ein **Azure Migrate-Projektschlüssel** generiert.
6. Kopieren Sie den Schlüssel, da Sie ihn benötigen, um die Registrierung der Appliance während der Konfiguration abzuschließen.

### <a name="download-the-installer-script"></a>Herunterladen des Installationsskripts

Klicken Sie in **2: Azure Migrate-Appliance herunterladen** auf **Herunterladen**.


### <a name="verify-security"></a>Überprüfen der Sicherheit

Vergewissern Sie sich vor der Bereitstellung, dass die gezippte Datei sicher ist.

1. Öffnen Sie auf dem Computer, auf den Sie die Datei heruntergeladen haben, ein Administratorbefehlsfenster.
2. Führen Sie den folgenden Befehl aus, um den Hash für die gezippte Datei zu generieren:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Beispielverwendung für die öffentliche Cloud: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256 ```
    - Beispielverwendung für die Government-Cloud: ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256 ```
3.  Überprüfen Sie die aktuellen Applianceversionen und Hashwerte:
    - Öffentliche Cloud:

        **Szenario** | **Download** | **Hashwert**
        --- | --- | ---
        Physisch (85 MB) | [Aktuelle Version](https://go.microsoft.com/fwlink/?linkid=2140334) | ce5e6f0507936def8020eb7b3109173dad60fc51dd39c3bd23099bc9baaabe29

    - Azure Government:

        **Szenario** | **Download** | **Hashwert**
        --- | --- | ---
        Physisch (85 MB) | [Aktuelle Version](https://go.microsoft.com/fwlink/?linkid=2140338) | ae132ebc574caf231bf41886891040ffa7abbe150c8b50436818b69e58622276
 

### <a name="run-the-azure-migrate-installer-script"></a>Ausführen des Azure Migrate-Installationsskripts
Das Installationsskript führt folgende Schritte aus:

- Installation der Agents und einer Webanwendung für die Ermittlung und Bewertung von GCP-Servern
- Installation von Windows-Rollen, darunter beispielsweise Windows-Aktivierungsdienst, IIS und PowerShell ISE.
- Download und Installation eines wiederbeschreibbaren IIS-Moduls. [Weitere Informationen](https://www.microsoft.com/download/details.aspx?id=7435)
- Aktualisierung eines Registrierungsschlüssels (HKLM) mit dauerhaften Einstellungsdetails für Azure Migrate.
- Erstellung der folgenden Dateien in diesem Pfad:
    - **Konfigurationsdateien**: %Programdata%\Microsoft Azure\Config
    - **Protokolldateien**: %Programdata%\Microsoft Azure\Logs

Führen Sie das Skript wie folgt aus:

1. Extrahieren Sie die gezippte Datei in einem Ordner auf dem Server, der die Appliance hostet.  Führen Sie das Skript nicht auf einem Computer auf einer vorhandenen Azure Migrate-Appliance aus.
2. Starten Sie PowerShell auf dem oben genannten Server mit Administratorberechtigungen (erhöhten Rechten).
3. Ändern Sie das PowerShell-Verzeichnis in den Ordner, in den die Inhalte der gezippten Datei extrahiert wurden, die Sie heruntergeladen haben.
4. Führen Sie das Skript mit dem Namen **AzureMigrateInstaller.ps1** aus, indem Sie den folgenden Befehl ausführen:

    - Öffentliche Cloud: 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```
    - Azure Government: 
    
        ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```

    Das Skript startet die Appliancewebanwendung, nachdem es erfolgreich ausgeführt wurde.

Bei Problemen können Sie zum Troubleshooting unter „C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Zeitstempel</em>.log“ auf die Skriptprotokolle zugreifen.



### <a name="verify-appliance-access-to-azure"></a>Überprüfen des Appliancezugriffs auf Azure

Stellen Sie sicher, dass die Appliance-VM eine Verbindung mit Azure-URLs für [öffentliche](migrate-appliance.md#public-cloud-urls) und [Government](migrate-appliance.md#government-cloud-urls)-Clouds herstellen kann.

### <a name="configure-the-appliance"></a>Konfigurieren der Appliance

Führen Sie die Ersteinrichtung der Appliance durch.

1. Öffnen Sie in einem Browser auf einem beliebigen Computer, der eine Verbindung mit der Appliance herstellen kann, die URL der Appliance-Web-App: **https://*Name oder IP-Adresse der Appliance*: 44368**.

   Alternativ können Sie auch auf dem Desktop auf die App-Verknüpfung klicken, um die App zu öffnen.
2. Akzeptieren Sie die **Lizenzbedingungen**, und lesen Sie die Drittanbieterinformationen.
1. Gehen Sie in der Web-App unter **Erforderliche Komponenten einrichten** wie folgt vor:
    - **Konnektivität**: Die App überprüft, ob der Server über Internetzugriff verfügt. Wenn der Server einen Proxy verwendet:
        - Klicken Sie auf **Proxy einrichten**, um die Proxyadresse im Format http://ProxyIPAddress oder http://ProxyFQDN) und den überwachenden Port anzugeben.
        - Geben Sie die Anmeldeinformationen an, wenn der Proxy eine Authentifizierung erfordert.
        - Es werden nur HTTP-Proxys unterstützt.
        - Wenn Sie Proxydetails hinzugefügt oder den Proxy und/oder die Authentifizierung deaktiviert haben, klicken Sie auf **Speichern**, um die Konnektivitätsprüfung erneut auszulösen.
    - **Uhrzeitsynchronisierung**: Die Uhrzeit wird überprüft. Die Uhrzeit der Appliance muss mit der Internetzeit synchronisiert werden, damit die Ermittlung ordnungsgemäß funktioniert.
    - **Updates installieren**: Azure Migrate-Serverbewertung prüft, ob die neuesten Updates für die Appliance installiert sind. Nachdem die Prüfung abgeschlossen ist, können Sie auf **Appliancedienste anzeigen** klicken, um den Status und die Versionen der auf der Appliance ausgeführten Komponenten anzuzeigen.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrieren der Appliance bei Azure Migrate

1. Fügen Sie den aus dem Portal kopierten **Azure Migrate-Projektschlüssel** ein. Wenn Sie den Schlüssel nicht haben, wechseln Sie zu **Serverbewertung > Ermitteln > Vorhandene Appliances verwalten**, wählen Sie den Appliancenamen aus, den Sie bei der Generierung des Schlüssels angegeben haben, und kopieren Sie den entsprechenden Schlüssel.
1. Für die Authentifizierung bei Azure benötigen Sie einen Gerätecode. Wenn Sie auf **Anmelden** klicken, wird ein modales Dialogfeld mit dem Gerätecode angezeigt. Dies ist in der folgenden Abbildung dargestellt.

    ![Modales Dialogfeld mit Gerätecode](./media/tutorial-discover-vmware/device-code.png)

1. Klicken Sie auf **Copy code & Login** (Code kopieren und anmelden), um den Gerätecode zu kopieren und eine Azure-Anmeldeaufforderung in einem neuen Browsertab zu öffnen. Sollte keine Anmeldung angezeigt werden, vergewissern Sie sich, dass Sie den Popupblocker im Browser deaktiviert haben.
1. Fügen Sie im neuen Tab den Gerätecode ein, und melden Sie sich mit Ihrem Azure-Benutzernamen und dem zugehörigen Kennwort an.
   
   Die Anmeldung mit einer PIN wird nicht unterstützt.
3. Falls Sie den Tab für die Anmeldung versehentlich schließen, ohne die Anmeldung durchzuführen, müssen Sie den Browsertab des Appliance-Konfigurations-Managers aktualisieren, um die Schaltfläche „Anmelden“ wieder zu aktivieren.
1. Wechseln Sie nach der erfolgreichen Anmeldung wieder zum vorherigen Tab mit dem Appliance-Konfigurations-Manager.
4. Wenn das für die Protokollierung verwendete Azure-Benutzerkonto über die richtigen [Berechtigungen](#prepare-an-azure-user-account) für die während der Schlüsselgenerierung erstellten Azure-Ressourcen verfügt, wird die Registrierung der Appliance initiiert.
5. Nachdem die Appliance erfolgreich registriert wurde, können Sie die Registrierungsdetails anzeigen, indem Sie auf **Details anzeigen** klicken.


## <a name="start-continuous-discovery"></a>Starten der kontinuierlichen Ermittlung

Stellen Sie nun eine Verbindung zwischen der Appliance und den zu ermittelnden GCP-Servern her, und starten Sie die Ermittlung.

1. Wählen Sie in **Schritt 1: Angeben von Anmeldeinformationen für die Ermittlung von physischen oder virtuellen Windows- und Linux-Servern** die Option **Anmeldeinformationen hinzufügen** aus.
1. Wählen Sie für Windows-Server den Quelltyp **Windows-Server** aus, geben Sie einen Anzeigenamen für die Anmeldeinformationen ein, und fügen Sie den Benutzernamen und das Kennwort hinzu. Klicken Sie auf **Speichern**.
1. Wenn Sie kennwortbasierte Authentifizierung für Linux-Server verwenden, wählen Sie den Quelltyp **Linux-Server (kennwortbasiert)** aus, geben Sie einen Anzeigenamen für die Anmeldeinformationen ein, und fügen Sie den Benutzernamen und das Kennwort hinzu. Klicken Sie auf **Speichern**.
1. Wenn Sie auf SSH-Schlüsseln basierende Authentifizierung für Linux-Server verwenden, wählen Sie den Quelltyp **Linux-Server (auf SSH-Schlüsseln basierend)** aus, geben Sie einen Anzeigenamen für die Anmeldeinformationen ein, fügen Sie den Benutzernamen hinzu, suchen Sie nach der Datei mit dem privaten SSH-Schlüssel, und wählen Sie diese aus. Klicken Sie auf **Speichern**.

    - Azure Migrate unterstützt den privaten SSH-Schlüssel, der vom Befehl ssh-keygen mithilfe von RSA-, DSA-, ECDSA- und ed25519-Algorithmen generiert wird.
    - Zurzeit unterstützt Azure Migrate keinen auf einer Passphrase basierenden SSH-Schlüssel. Verwenden Sie einen SSH-Schlüssel ohne eine Passphrase.
    - Derzeit wird die von PuTTY generierte Datei mit dem privaten SSH-Schlüssel von Azure Migrate nicht unterstützt.
    - Azure Migrate unterstützt das OpenSSH-Format der Datei mit dem privaten SSH-Schlüssel. Dies ist hier dargestellt:
    
    ![Unterstütztes Format für privaten SSH-Schlüssel](./media/tutorial-discover-physical/key-format.png)


2. Wenn Sie mehrere Anmeldeinformationen gleichzeitig hinzufügen möchten, klicken Sie auf **Weitere hinzufügen**, um die Angeben zu speichern und weitere Anmeldeinformationen hinzuzufügen. 
3. Klicken Sie in **Schritt 2: Bereitstellen von Details zu physischen oder virtuellen Servern** auf **Ermittlungsquelle hinzufügen**, um den **IP-Adresse/FQDN**-Wert des Servers und den Anzeigenamen für Anmeldeinformationen zum Herstellen einer Verbindung mit dem Server anzugeben.
4. Sie können entweder jeweils **ein einzelnes Element** oder **mehrere Elemente** in einem Schritt hinzufügen. Es besteht auch die Möglichkeit, Serverdetails über **CSV importieren** bereitzustellen.

    - Wenn Sie auf **Add single item** (Einzelnes Element hinzufügen) klicken, können Sie den Betriebssystemtyp auswählen, den Anzeigenamen für die Anmeldeinformationen angeben, den **IP-Adresse/FQDN**-Wert des Servers hinzufügen und dann auf **Speichern** klicken.
    - Wenn Sie **mehrere Elemente hinzufügen**, können Sie mehrere Datensätze auf einmal hinzufügen, indem Sie den **IP-Adresse/FQDN**-Wert des Servers mit dem Anzeigenamen für die Anmeldeinformationen im Textfeld angeben.  **Überprüfen** Sie die hinzugefügten Datensätze, und klicken Sie auf **Speichern**.
    - Wenn Sie **CSV importieren** _(standardmäßig ausgewählt)_ auswählen, können Sie eine CSV-Vorlagendatei herunterladen, die Datei mit dem **IP-Adresse/FQDN**-Wert des Servers und einem Anzeigenamen für die Anmeldeinformationen auffüllen. Importieren Sie die Datei dann in die Appliance, **überprüfen** Sie die Datensätze in der Datei, und klicken Sie auf **Speichern**.

5. Wenn Sie auf „Speichern“ klicken, versucht die Appliance, die Verbindung zu den hinzugefügten Servern zu überprüfen, und zeigt den **Überprüfungszustand** in der Tabelle für jeden Server an.
    - Sollte bei der Überprüfung eines Servers ein Fehler auftreten, sehen Sie sich den Fehler an, indem Sie auf **Fehler bei der Überprüfung** in der Spalte „Status“ klicken. Beheben Sie das Problem, und wiederholen Sie die Überprüfung.
    - Um einen Server zu entfernen, klicken Sie auf **Löschen**.
6. Sie können die Verbindung zu Servern jederzeit vor Beginn der Ermittlung **erneut überprüfen**.
7. Klicken Sie auf **Ermittlung starten**, um die Ermittlung der erfolgreich überprüften Server zu starten. Nachdem die Ermittlung erfolgreich gestartet wurde, können Sie den Ermittlungsstatus für jeden Server in der Tabelle überprüfen.


Daraufhin wird die Ermittlung gestartet. Es dauert ca. 2 Minuten pro Server, bis Metadaten des ermittelten Servers im Azure-Portal angezeigt werden.

## <a name="verify-servers-in-the-portal"></a>Überprüfen von Servern im Portal

Nach Abschluss der Ermittlung können Sie überprüfen, ob die Server im Portal angezeigt werden.

1. Öffnen Sie das Azure Migrate-Dashboard.
2. Klicken Sie unter **Azure Migrate – Server** > **Azure Migrate: Server Assessment** (Azure Migrate-Serverbewertung) auf das Symbol mit der Anzahl für **Ermittelte Server**.

## <a name="next-steps"></a>Nächste Schritte

- [Bewerten von GCP-Servern](tutorial-assess-gcp.md) für die Migration zu Azure
- [Informieren Sie sich über die Daten](migrate-appliance.md#collected-data---physical), die von der Appliance im Zuge der Ermittlung gesammelt werden.
