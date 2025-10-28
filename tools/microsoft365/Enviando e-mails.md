# Enviando e-mails no Microsoft 365 usando OAuth 2.0 (e SMTP Relay)

## 📌 Resumo da Mudança

Desde **25 de setembro de 2025**, a Microsoft **desativou permanentemente a Autenticação Básica** (usuário/senha) no **Exchange Online (Microsoft 365)**.
Agora, apenas a **OAuth 2.0 (Autenticação Moderna)** — baseada em **tokens temporários de acesso** — é aceita para todos os aplicativos, serviços e dispositivos.

> **Erro comum após a mudança:**
>
> ```
> 550 5.7.30 Basic authentication is not supported for Client Submission
> ```

---

## 🔄 O que mudou

* ❌ **Autenticação básica (`user/password`)** não é mais suportada
* ❌ **Senhas de aplicativo (App Passwords)** foram descontinuadas
* ✅ **OAuth 2.0 (Autenticação Moderna)** é agora obrigatória

  * Tokens válidos por cerca de **1 hora**
  * Emitidos pelo **Microsoft Entra ID (Azure AD)**
  * Suporte a **MFA** e **Acesso Condicional**

---

## ⚙️ Configuração — Microsoft Graph (OAuth 2.0 + Token)

### 1️⃣ Pré-requisitos

* Acesso ao **Entra ID**: [https://entra.microsoft.com/](https://entra.microsoft.com/)
* **Caixa de correio válida** (ex.: `remetente@seudominio.com`)
* **Domínio verificado** em seu tenant
* Ferramentas: **cURL/Postman** ou **Go**

---

### 2️⃣ Registrar o Aplicativo

1. Vá em **App registrations** → **New registration**

   * **Name:** `Email Relay OAuth`
   * **Supported account types:** *Single tenant*
   * **Redirect URI:** *não necessário*
   * Clique em **Register**

> ```
> Copie:
>
> **Directory (tenant) ID** → `TENANT_ID`
> **Application (client) ID** → `CLIENT_ID`
> ```

2. Acesse **Certificates & secrets** → **New client secret**

   * Defina uma validade e **copie o valor de *Value*** (não será exibido novamente)

3. Vá em **API permissions** → **Add a permission** → **Microsoft Graph** → **Application permissions**

   * Selecione `Mail.Send` → Clique em **Grant admin consent**

---

### 3️⃣ Gerar Token OAuth 2.0

**Fluxo de Credenciais de Cliente (Client Credentials Flow):**

```bash
curl -X POST https://login.microsoftonline.com/<TENANT_ID>/oauth2/v2.0/token \
 -H "Content-Type: application/x-www-form-urlencoded" \
 -d "client_id=<CLIENT_ID>" \
 -d "client_secret=<CLIENT_SECRET>" \
 -d "scope=https://graph.microsoft.com/.default" \
 -d "grant_type=client_credentials"
```

**Resposta esperada:**

```json
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGci..."
}
```

Use o valor de `access_token` no cabeçalho HTTP: `Authorization: Bearer <token>`.

---

### #️⃣ Exemplo de Envio de E-mail (Go)

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io"
    "net/http"
    "net/url"
)

type tokenResp struct{ AccessToken string `json:"access_token"` }

func getToken(tenantID, clientID, clientSecret string) (string, error) {
    endpoint := fmt.Sprintf("https://login.microsoftonline.com/%s/oauth2/v2.0/token", tenantID)
    form := url.Values{
        "client_id":     {clientID},
        "client_secret": {clientSecret},
        "scope":         {"https://graph.microsoft.com/.default"},
        "grant_type":    {"client_credentials"},
    }
    req, _ := http.NewRequest("POST", endpoint, bytes.NewBufferString(form.Encode()))
    req.Header.Set("Content-Type", "application/x-www-form-urlencoded")
    resp, err := http.DefaultClient.Do(req)
    if err != nil { return "", err }
    defer resp.Body.Close()
    b, _ := io.ReadAll(resp.Body)
    if resp.StatusCode != http.StatusOK { return "", fmt.Errorf("falha ao obter token: %s - %s", resp.Status, string(b)) }
    var tr tokenResp
    json.Unmarshal(b, &tr)
    return tr.AccessToken, nil
}

func sendMail(sender, token string) error {
    body := map[string]any{
        "message": map[string]any{
            "subject": "Teste – Microsoft Graph API",
            "body": map[string]string{"contentType": "Text", "content": "E-mail automático enviado via OAuth 2.0 (Go)."},
            "toRecipients": []map[string]any{
                {"emailAddress": map[string]string{"address": "destinatario@exemplo.com"}},
            },
        },
        "saveToSentItems": false,
    }
    b, _ := json.Marshal(body)
    req, _ := http.NewRequest("POST", fmt.Sprintf("https://graph.microsoft.com/v1.0/users/%s/sendMail", url.PathEscape(sender)), bytes.NewReader(b))
    req.Header.Set("Authorization", "Bearer "+token)
    req.Header.Set("Content-Type", "application/json")
    resp, err := http.DefaultClient.Do(req)
    if err != nil { return err }
    defer resp.Body.Close()
    if resp.StatusCode != http.StatusAccepted { rb, _ := io.ReadAll(resp.Body); return fmt.Errorf("falha ao enviar e-mail: %s - %s", resp.Status, string(rb)) }
    return nil
}
```

> ✅ Resposta de sucesso: **`HTTP 202 Accepted`**

---

# 📨 SMTP Relay (Exchange Online)

### 1️⃣ Pré-requisitos

* **IP público fixo** (ex.: `191.209.58.228`)
* **Domínio verificado** no Microsoft 365
* **Porta TCP 25** liberada (entrada/saída)
* **PTR/rDNS** configurado apontando para o hostname do domínio

---

### 2️⃣ Criar o Conector

Acesse: [https://admin.cloud.microsoft/exchange?#/connectors](https://admin.cloud.microsoft/exchange?#/connectors)

* **From:** *Your organization’s email server*
* **To:** *Microsoft 365*
* Nome: `SMTP Relay – On-prem`
* Selecione identificação por **IP** e adicione o IP público autorizado

> ⚠️ O e-mail precisa sair exatamente do IP autorizado; caso contrário, o relay falhará.

---

### 3️⃣ Configurar o Registro SPF

Adicione ou edite o registro **TXT** (apenas **um** por domínio):

```txt
v=spf1 ip4:191.209.58.228 include:spf.protection.outlook.com -all
```

Se já existir um SPF, mescle as entradas em **um único registro**.

---

### 4️⃣ Descobrir o Smart Host (MX)

```bash
nslookup -type=mx seudominio.com
```

Use o resultado como **smart host** (porta **25**), por exemplo:

```
seudominio-com.mail.protection.outlook.com
```

---

### #️⃣ Exemplo de Envio de E-mail (Go)

```go
package main

import (
	"fmt"
	"log"
	"net/smtp"
	"os"
)

// Configurações do relay
const (
	smtpHost = "seudominio-com.mail.protection.outlook.com" // Smart host do seu domínio (MX)
	smtpPort = "25"                                         // Porta padrão do relay
)

func main() {
	// Endereço do remetente (precisa pertencer ao domínio verificado)
	from := "remetente@seudominio.com"
	to := []string{"destinatario@exemplo.com"}

	// Corpo da mensagem
	msg := []byte("To: destinatario@exemplo.com\r\n" +
		"Subject: Teste de envio via SMTP Relay (Go)\r\n" +
		"Content-Type: text/plain; charset=UTF-8\r\n\r\n" +
		"Olá!\nEste é um teste de envio via SMTP Relay (autenticado por IP) no Microsoft 365.\n" +
		"Enviado usando Go e net/smtp.\r\n")

	// Endereço do servidor
	addr := fmt.Sprintf("%s:%s", smtpHost, smtpPort)

	log.Printf("🔄 Conectando a %s para envio de e-mail...", addr)

	// Envia a mensagem sem autenticação (relay autorizado por IP)
	err := smtp.SendMail(addr, nil, from, to, msg)
	if err != nil {
		log.Fatalf("❌ Falha ao enviar e-mail: %v", err)
	}

	fmt.Println("✅ E-mail enviado com sucesso via SMTP Relay.")
}
```
