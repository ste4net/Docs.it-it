---
title: Hosting di ASP.NET Core in Linux con Apache
description: Informazioni su come configurare Apache come server proxy inverso in CentOS per reindirizzare il traffico HTTP a un'app Web ASP.NET Core in esecuzione su Kestrel.
author: spboyer
manager: wpickett
ms.author: spboyer
ms.custom: mvc
ms.date: 03/13/2018
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: host-and-deploy/linux-apache
ms.openlocfilehash: 473585f1be180645395c14a154c9c017ca50edab
ms.sourcegitcommit: 74be78285ea88772e7dad112f80146b6ed00e53e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
ms.locfileid: "33962817"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a>Hosting di ASP.NET Core in Linux con Apache

Di [Shayne Boyer](https://github.com/spboyer)

Questa guida fornisce informazioni su come configurare [Apache](https://httpd.apache.org/) come server proxy inverso in [CentOS 7](https://www.centos.org/) per reindirizzare il traffico HTTP a un'app Web ASP.NET Core in esecuzione su [Kestrel](xref:fundamentals/servers/kestrel). L'[estensione mod_proxy](http://httpd.apache.org/docs/2.4/mod/mod_proxy.html) e i moduli correlati creano il proxy inverso del server.

## <a name="prerequisites"></a>Prerequisiti

1. Server che esegue CentOS 7 con un account utente standard con privilegio sudo
2. App ASP.NET Core

## <a name="publish-the-app"></a>Pubblicare l'app

Pubblicare l'app come una [distribuzione autonoma](/dotnet/core/deploying/#self-contained-deployments-scd) nella configurazione di rilascio per il runtime CentOS 7 (`centos.7-x64`). Copiare il contenuto della cartella *bin/Release/netcoreapp2.0/centos.7-x64/publish* nel server tramite SCP, FTP o un altro metodo di trasferimento di file.

> [!NOTE]
> In uno scenario di distribuzione di produzione un flusso di lavoro di integrazione continua esegue le operazioni di pubblicazione dell'app e di copia degli asset nel server. 

## <a name="configure-a-proxy-server"></a>Configurazione di un server proxy

Un proxy inverso è una configurazione comune per la gestione delle app Web dinamiche. Il proxy inverso termina la richiesta HTTP e la inoltra all'app ASP.NET.

Un server proxy inoltra le richieste del client a un altro server anziché evaderle da solo. Un proxy inverso inoltra a una destinazione fissa, in genere per conto di client non autorizzati. In questa guida Apache viene configurato come proxy inverso in esecuzione nello stesso server usato da Kestrel per gestire l'app ASP.NET Core.

Poiché le richieste vengono inoltrate dal proxy inverso, usare il middleware delle intestazioni inoltrate dal pacchetto [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/). Il middleware aggiorna `Request.Scheme` usando l'intestazione `X-Forwarded-Proto`, in modo che gli URI di reindirizzamento e altri criteri di sicurezza funzionino correttamente.

Quando si usa qualsiasi tipo di middleware di autenticazione, il middleware delle intestazioni inoltrate deve essere eseguito per primo. Questo ordine garantisce che il middleware di autenticazione sia in grado di usare i valori delle intestazioni e di generare gli URI di reindirizzamento corretti.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Richiamare il metodo [UseForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) in `Startup.Configure` prima di chiamare [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication) o un middleware con schema di autenticazione simile. Configurare il middleware per l'inoltro delle intestazioni `X-Forwarded-For` e `X-Forwarded-Proto`:

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Richiamare il metodo [UseForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) in `Startup.Configure` prima di chiamare [UseIdentity](/dotnet/api/microsoft.aspnetcore.builder.builderextensions.useidentity) e [UseFacebookAuthentication](/dotnet/api/microsoft.aspnetcore.builder.facebookappbuilderextensions.usefacebookauthentication) o un middleware con schema di autenticazione simile. Configurare il middleware per l'inoltro delle intestazioni `X-Forwarded-For` e `X-Forwarded-Proto`:

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseIdentity();
app.UseFacebookAuthentication(new FacebookOptions()
{
    AppId = Configuration["Authentication:Facebook:AppId"],
    AppSecret = Configuration["Authentication:Facebook:AppSecret"]
});
```

---

Se non sono specificate opzioni [ForwardedHeadersOptions](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) per il middleware, le intestazioni predefinite per l'inoltro sono `None`.

Potrebbero essere necessari interventi di configurazione aggiuntivi per le app ospitate dietro a server proxy e a servizi di bilanciamento del carico. Per altre informazioni, vedere [Configurare ASP.NET Core per l'utilizzo di server proxy e servizi di bilanciamento del carico](xref:host-and-deploy/proxy-load-balancer).

### <a name="install-apache"></a>Installare Apache

Aggiornare i pacchetti CentOS alle versioni stabili più recenti:

```bash
sudo yum update -y
```

Installare il server Web Apache in CentOS con un singolo comando `yum`:

```bash
sudo yum -y install httpd mod_ssl
```

Esempio di output dopo l'esecuzione del comando:

```bash
Downloading packages:
httpd-2.4.6-40.el7.centos.4.x86_64.rpm               | 2.7 MB  00:00:01
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Installing : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 
Verifying  : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 

Installed:
httpd.x86_64 0:2.4.6-40.el7.centos.4

Complete!
```

> [!NOTE]
> In questo esempio, l'output rispecchia httpd.86_64 poiché la versione CentOS 7 è a 64 bit. Per verificare dove è installato Apache, eseguire `whereis httpd` da un prompt dei comandi.

### <a name="configure-apache-for-reverse-proxy"></a>Configurare Apache per il proxy inverso

I file di configurazione per Apache si trovano all'interno della directory `/etc/httpd/conf.d/`. Qualsiasi file con l'estensione *.conf* viene elaborato in ordine alfabetico, in aggiunta ai file di configurazione dei moduli in `/etc/httpd/conf.modules.d/`, che contiene tutti i file di configurazione necessari per caricare i moduli.

Creare un file di configurazione denominato *hellomvc.conf* per l'app:

```
<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ServerName www.example.com
    ServerAlias *.example.com
    ErrorLog ${APACHE_LOG_DIR}hellomvc-error.log
    CustomLog ${APACHE_LOG_DIR}hellomvc-access.log common
</VirtualHost>
```

Il blocco `VirtualHost` può comparire più volte, in uno o più file su un server. Nel file di configurazione precedente Apache accetta il traffico pubblico sulla porta 80. Il dominio `www.example.com` viene gestito e l'alias `*.example.com` viene risolto nello stesso sito. Per altre informazioni, vedere [Name-based virtual host support](https://httpd.apache.org/docs/current/vhosts/name-based.html) (Supporto degli host virtuali basati sul nome). Le richieste vengono trasmesse tramite proxy alla radice sulla porta 5000 del server all'indirizzo 127.0.0.1. Per la comunicazione bidirezionale, sono necessari `ProxyPass` e `ProxyPassReverse`.

> [!WARNING]
> Se non si specifica una [direttiva ServerName](https://httpd.apache.org/docs/current/mod/core.html#servername) corretta nel blocco **VirtualHost**, l'app può risultare esposta a vulnerabilità di sicurezza. L'associazione con caratteri jolly del sottodominio (ad esempio, `*.example.com`) non costituisce un rischio per la sicurezza se viene controllato l'intero dominio padre (a differenza di `*.com`, che è vulnerabile). Vedere la [sezione 5.4 di RFC7230](https://tools.ietf.org/html/rfc7230#section-5.4) per altre informazioni.

La registrazione può essere configurata per ogni `VirtualHost` usando le direttive `ErrorLog` e `CustomLog`. `ErrorLog` è la posizione in cui il server registra gli errori e `CustomLog` imposta il nome del file e il formato del file di log. In questo caso, si tratta della posizione in cui vengono registrate le informazioni sulla richiesta. È presente una riga per ogni richiesta.

Salvare il file e testare la configurazione. Se tutto ciò viene superato, la risposta deve essere `Syntax [OK]`.

```bash
sudo service httpd configtest
```

Riavviare Apache:

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitoring-the-app"></a>Monitoraggio dell'app

Apache è ora configurato in modo da inoltrare le richieste eseguite a `http://localhost:80` all'app ASP.NET Core eseguita in Kestrel all'indirizzo `http://127.0.0.1:5000`.  Tuttavia, Apache non è configurato per la gestione del processo Kestrel. Usare *systemd* e creare un file del servizio per avviare e monitorare l'app Web sottostante. *systemd* è un sistema di inizializzazione che offre molte funzionalità potenti per l'avvio, l'arresto e la gestione dei processi. 


### <a name="create-the-service-file"></a>Creare il file del servizio

Creare il file di definizione del servizio:

```bash
sudo nano /etc/systemd/system/kestrel-hellomvc.service
```

Un esempio di file del servizio per l'app:

```
[Unit]
Description=Example .NET Web API App running on CentOS 7

[Service]
WorkingDirectory=/var/aspnetcore/hellomvc
ExecStart=/usr/local/bin/dotnet /var/aspnetcore/hellomvc/hellomvc.dll
Restart=always
# Restart service after 10 seconds if dotnet service crashes
RestartSec=10
SyslogIdentifier=dotnet-example
User=apache
Environment=ASPNETCORE_ENVIRONMENT=Production 

[Install]
WantedBy=multi-user.target
```

> [!NOTE]
> **Utente**: se l'utente *apache* non viene usato dalla configurazione, è necessario creare prima l'utente e assegnargli correttamente la proprietà dei file.

> [!NOTE]
> Per alcuni valori (ad esempio, le stringhe di connessione SQL) è necessario usare caratteri di escape, in modo da consentire ai provider di configurazione di leggere le variabili di ambiente. Usare il comando seguente per generare un valore con caratteri di escape corretti per l'uso nel file di configurazione:
>
> ```console
> systemd-escape "<value-to-escape>"
> ```

Salvare il file e abilitare il servizio:

```bash
systemctl enable kestrel-hellomvc.service
```

Avviare il servizio e verificare che sia in esecuzione:

```bash
systemctl start kestrel-hellomvc.service
systemctl status kestrel-hellomvc.service

● kestrel-hellomvc.service - Example .NET Web API App running on CentOS 7
    Loaded: loaded (/etc/systemd/system/kestrel-hellomvc.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-hellomvc.service
            └─9021 /usr/local/bin/dotnet /var/aspnetcore/hellomvc/hellomvc.dll
```

Con il proxy inverso configurato e Kestrel gestito tramite *systemd*, l'app Web è completamente configurata ed è possibile accedervi da un browser nel computer locale all'indirizzo `http://localhost`. Se si esaminano le intestazioni di risposta, l'intestazione **Server** indica che l'app ASP.NET Core è gestita da Kestrel:

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="viewing-logs"></a>Vista dei log

Poiché l'app Web che usa Kestrel viene gestita tramite *systemd*, gli eventi e i processi vengono registrati in un giornale di registrazione centralizzato. Tuttavia, questo giornale include le voci per tutti i servizi e i processi gestiti da *systemd*. Per visualizzare le voci specifiche di `kestrel-hellomvc.service`, usare il comando seguente:

```bash
sudo journalctl -fu kestrel-hellomvc.service
```

Per il filtro di data e ora, specificare le opzioni di tempo con il comando. Ad esempio, usare `--since today` per filtrare in base al giorno corrente o `--until 1 hour ago` per visualizzare le voci dell'ora precedente. Per altre informazioni, vedere la [pagina di manuale per journalctl](https://www.unix.com/man-page/centos/1/journalctl/).

```bash
sudo journalctl -fu kestrel-hellomvc.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="securing-the-app"></a>Protezione dell'app

### <a name="configure-firewall"></a>Configurare firewall

*Firewalld* è un daemon dinamico per gestire il firewall con il supporto per le aree di rete. È comunque possibile usare iptables per gestire le porte e il filtro dei pacchetti. *Firewalld* dovrebbe essere installato per impostazione predefinita. È possibile usare `yum` per installare il pacchetto o verificare che sia installato.

```bash
sudo yum install firewalld -y
```

Usare `firewalld` per aprire solo le porte necessarie per l'app. In questo caso vengono usate le porte 80 e 443. I comandi seguenti impostano in modo permanente le porte 80 e 443 per l'apertura:

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

Ricaricare le impostazioni del firewall. Verificare i servizi e le porte disponibili nell'area predefinita. Le opzioni sono disponibili controllando `firewall-cmd -h`.

```bash 
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

```bash
public (default, active)
interfaces: eth0
sources: 
services: dhcpv6-client
ports: 443/tcp 80/tcp
masquerade: no
forward-ports: 
icmp-blocks: 
rich rules: 
```

### <a name="ssl-configuration"></a>Configurazione SSL

Per configurare Apache per SSL, viene usato il modulo *mod_ssl*. Durante l'installazione del modulo *httpd*, è stato installato anche il modulo *mod_ssl*. Se non è stato installato, usare `yum` per aggiungerlo alla configurazione.

```bash
sudo yum install mod_ssl
```
Per imporre SSL, installare il modulo `mod_rewrite` per abilitare la riscrittura degli URL:

```bash
sudo yum install mod_rewrite
```

Modificare il file *hellomvc.conf* per abilitare la riscrittura degli URL e proteggere le comunicazioni sulla porta 443:

```
<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/ [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ErrorLog /var/log/httpd/hellomvc-error.log
    CustomLog /var/log/httpd/hellomvc-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

> [!NOTE]
> Questo esempio usa un certificato generato localmente. **SSLCertificateFile** deve essere il file del certificato primario per il nome di dominio. **SSLCertificateKeyFile** deve essere il file di chiave generato quando viene creato il CSR. **SSLCertificateChainFile** deve essere il file di certificato intermedio (se presente) che è stato fornito dall'autorità di certificazione.

Salvare il file e testare la configurazione:

```bash
sudo service httpd configtest
```

Riavviare Apache:

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a>Altri suggerimenti di Apache

### <a name="additional-headers"></a>Intestazioni aggiuntive

Al fine di garantire la protezione dagli attacchi dannosi, vi sono alcune intestazioni che devono essere modificate o aggiunte. Verificare che il modulo `mod_headers` sia installato:

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a>Proteggere Apache dagli attacchi di clickjacking

Il [clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), anche noto come *attacco UI Redress*, è un attacco dannoso in cui un visitatore di un sito Web viene indotto a fare clic su un collegamento o un pulsante in una pagina diversa da quella che sta attualmente visualizzando. Usare `X-FRAME-OPTIONS` per proteggere il sito.

Modificare il file *httpd.conf*:

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

Aggiungere la riga `Header append X-FRAME-OPTIONS "SAMEORIGIN"`. Salvare il file. Riavviare Apache.

#### <a name="mime-type-sniffing"></a>Analisi del tipo MIME

L'intestazione `X-Content-Type-Options` impedisce a Internet Explorer di eseguire l'*analisi del tipo MIME*, ovvero di determinare il `Content-Type` di un file dal contenuto del file. Se il server imposta l'intestazione `Content-Type` su `text/html` con l'opzione `nosniff` impostata, Internet Explorer esegue il rendering del contenuto come `text/html`, indipendentemente dal contenuto del file.

Modificare il file *httpd.conf*:

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

Aggiungere la riga `Header set X-Content-Type-Options "nosniff"`. Salvare il file. Riavviare Apache.

### <a name="load-balancing"></a>Bilanciamento del carico 

Questo esempio illustra come impostare e configurare Apache CentOS 7 e Kestrel nello stesso computer dell'istanza. Per evitare di avere un singolo punto di errore, l'uso di *mod_proxy_balancer* e la modifica di **VirtualHost** consentono la gestione di più istanze delle app Web dietro il server proxy di Apache.

```bash
sudo yum install mod_proxy_balancer
```

Nel file di configurazione illustrato di seguito, un'istanza aggiuntiva dell'app `hellomvc` viene configurata per l'esecuzione sulla porta 5001. La sezione *Proxy* è impostata con una configurazione del servizio di bilanciamento del carico, con due membri per il bilanciamento del carico di *byrequests*.

```
<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/ [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPass / balancer://mycluster/ 

    ProxyPassReverse / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5001/

    <Proxy balancer://mycluster>
        BalancerMember http://127.0.0.1:5000
        BalancerMember http://127.0.0.1:5001 
        ProxySet lbmethod=byrequests
    </Proxy>

    <Location />
        SetHandler balancer
    </Location>
    ErrorLog /var/log/httpd/hellomvc-error.log
    CustomLog /var/log/httpd/hellomvc-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

### <a name="rate-limits"></a>Limiti di velocità
Usando *mod_ratelimit*, incluso nel modulo *httpd*, è possibile limitare la larghezza di banda del client:

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```
Il file di esempio limita la larghezza di banda a 600 KB al secondo nel percorso radice:

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```
