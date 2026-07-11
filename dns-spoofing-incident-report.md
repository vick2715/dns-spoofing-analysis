# 🛡️ Security Incident Report
**Analista:** Davi Santos  
**Data:** 2024  
**Classificação:** 🔴 Crítico  
**Protocolo Envolvido:** HTTP / DNS  
**Tipo de Ataque:** Brute Force + DNS Spoofing + Distribuição de Malware

---

## 📋 Seção 1 — Identificação do Protocolo de Rede

O protocolo de rede envolvido no incidente é o **HTTP (Hypertext Transfer Protocol)**, operando na **camada de aplicação** do modelo TCP/IP.

A identificação foi baseada em três evidências:

| Evidência | Detalhe |
|---|---|
| **Log tcpdump** | Requisições DNS para `yummyrecipesforme.com` resolvidas via `dns.google.domain` |
| **Tráfego HTTP** | Conexão estabelecida na porta `80` (`.http`) entre a máquina e o site |
| **Redirecionamento** | Nova consulta DNS para `greatrecipesforme.com` com IP diferente (`192.0.2.17`) |

> O arquivo malicioso foi transportado até os computadores dos usuários utilizando o protocolo **HTTP na camada de aplicação**, sem nenhuma criptografia, o que facilitou sua distribuição.

---

## 📋 Seção 2 — Documentação do Incidente

### 🚨 Relato Inicial

Vários clientes contataram o suporte do site `yummyrecipesforme.com` relatando que, ao acessar a página, foram solicitados a baixar e executar um arquivo que prometia acesso a novas receitas. Após a execução, os computadores passaram a operar com lentidão. O proprietário do site também relatou que perdeu acesso à sua conta de administrador.

### 🔬 Investigação

O analista de cibersegurança utilizou um **ambiente sandbox isolado** para acessar o site sem comprometer a rede corporativa. Em seguida, executou o **tcpdump** para capturar o tráfego de rede gerado pela interação com o site. Ao ser solicitado, o analista fez o download e executou o arquivo suspeito. O navegador foi imediatamente redirecionado para um site falso (`greatrecipesforme.com`).

### 🔍 Análise do Log tcpdump

**Fase 1 — Acesso legítimo (14:18):**
```
14:18:32  your.machine → dns.google    [DNS] Consulta: yummyrecipesforme.com
14:18:32  dns.google   → your.machine  [DNS] Resposta: 203.0.113.22
14:18:36  your.machine → yummyrecipesforme.com [SYN]      Handshake TCP iniciado
14:18:36  yummyrecipesforme.com → your.machine [SYN-ACK]  Servidor responde
14:18:36  your.machine → yummyrecipesforme.com [ACK]      Conexão estabelecida
14:18:36  your.machine → yummyrecipesforme.com [HTTP GET]  Download do arquivo malicioso
```

**Fase 2 — Redirecionamento malicioso (14:20):**
```
14:20:32  your.machine → dns.google         [DNS] Consulta: greatrecipesforme.com
14:20:32  dns.google   → your.machine       [DNS] Resposta: 192.0.2.17 ⚠️ IP diferente!
14:25:29  your.machine → greatrecipesforme.com [SYN]     Conexão com site falso
14:25:29  greatrecipesforme.com → your.machine [SYN-ACK] Site falso responde
          ↳ Tráfego massivo na porta 80 — dados exfiltrados
```

### 🧩 Conclusão da Análise

A investigação do código-fonte revelou que um atacante comprometeu o servidor web através de um **ataque de força bruta** na conta de administrador, alterando a senha e bloqueando o proprietário. Em seguida, inseriu código malicioso no site que simulava uma atualização de navegador. Ao executar o arquivo, o malware redirecionou os usuários para `greatrecipesforme.com` — um site falso controlado pelo atacante — e comprometeu os computadores das vítimas.

---

## 📋 Seção 3 — Recomendações de Mitigação

### 🔐 Contra Ataques de Força Bruta

| Medida | Como protege |
|---|---|
| **Autenticação de Dois Fatores (2FA)** | Mesmo que a senha seja descoberta, o atacante não consegue acessar sem o código OTP enviado ao e-mail ou celular |
| **Bloquear senhas anteriores** | Impede reutilização de senhas padrão ou antigas que o atacante já conhece |
| **Exigir senhas longas (15+ caracteres)** | Senhas maiores aumentam exponencialmente o tempo necessário para força bruta |
| **Rate limiting de login** | Bloquear IP após X tentativas falhas consecutivas |
| **CAPTCHA no login** | Dificulta automação de tentativas por bots |

### 🌐 Contra DNS Spoofing e Distribuição de Malware

| Medida | Como protege |
|---|---|
| **DNSSEC** | Assina criptograficamente as respostas DNS, impedindo spoofing |
| **HTTPS obrigatório (TLS)** | Criptografa o tráfego, dificultando injeção de conteúdo malicioso |
| **Content Security Policy (CSP)** | Impede execução de scripts não autorizados no site |
| **Monitoramento de integridade** | Alertas automáticos para alterações não autorizadas no código do site |

---

## 📎 Evidências

| Arquivo | Descrição |
|---|---|
| [tcpdump_traffic_log.pdf](./tcpdump_traffic_log.pdf) | Log de tráfego DNS e HTTP capturado com tcpdump |
| [How_to_read_the_tcpdump_traffic_log.pdf](./How_to_read_the_tcpdump_traffic_log.pdf) | Guia de interpretação do log tcpdump |
| [Security_incident_report_exemplar.pdf](./Security_incident_report_exemplar.pdf) | Relatório exemplar de referência |

---

## ✅ Resumo Executivo

```
ATAQUE:    Brute Force → comprometimento da conta admin
VETOR:     Injeção de código malicioso no site legítimo
PROTOCOLO: HTTP (porta 80) — sem criptografia
IMPACTO:   Usuários infectados + redirecionados para site falso
CAUSA RAIZ: Senha fraca do administrador + ausência de 2FA
```

---

*Relatório produzido como parte do portfólio de Cibersegurança — Davi Santos*  
*Ferramenta utilizada: tcpdump | Protocolos analisados: DNS / HTTP / TCP*
