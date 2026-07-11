# 🌐 DNS & HTTP Traffic Analysis — Brute Force + DNS Spoofing

> Análise de tráfego DNS/HTTP com identificação de ataque de força bruta, comprometimento de site legítimo e distribuição de malware via redirecionamento DNS.

---

## 🎯 Objetivo

Investigar um incidente de segurança reportado por usuários do site `yummyrecipesforme.com`, analisando logs de tráfego capturados com **tcpdump** para identificar o vetor de ataque, protocolo envolvido e propor medidas de mitigação.

---

## 🚨 Resultado da Análise

| Campo | Detalhe |
|---|---|
| **Protocolo envolvido** | HTTP (porta 80) + DNS |
| **Tipo de ataque** | Brute Force + DNS Spoofing + Malware |
| **Alvo inicial** | Conta admin de `yummyrecipesforme.com` |
| **Vetor** | Arquivo malicioso disfarçado de atualização de navegador |
| **Site falso** | `greatrecipesforme.com` → IP `192.0.2.17` |
| **Impacto** | Computadores dos usuários comprometidos |
| **Causa raiz** | Senha fraca + ausência de 2FA na conta admin |

---

## 🧰 Ferramentas Utilizadas

![tcpdump](https://img.shields.io/badge/tcpdump-Network_Analyzer-informational?style=for-the-badge&logo=linux&logoColor=white)
![DNS](https://img.shields.io/badge/DNS-Protocol-blue?style=for-the-badge)
![HTTP](https://img.shields.io/badge/HTTP-Protocol-orange?style=for-the-badge)

---

## 📂 Estrutura do Repositório

```
dns-spoofing-analysis/
├── dns-spoofing-incident-report.md        ← Relatório completo do incidente
├── tcpdump_traffic_log.pdf                ← Log de tráfego capturado
├── How_to_read_the_tcpdump_traffic_log.pdf
└── README.md
```

---

## 📖 Linha do Tempo do Ataque

```
[ANTES]
Atacante realiza Brute Force na conta admin
        ↓
Senha comprometida → atacante altera credenciais
        ↓
Código malicioso injetado no site legítimo

[DURANTE — 14:18]
Usuário acessa yummyrecipesforme.com via HTTP
        ↓
DNS resolve → IP 203.0.113.22 (legítimo)
        ↓
Site solicita download de "atualização de navegador"
        ↓
Usuário executa o arquivo malicioso

[PÓS-INFECÇÃO — 14:20]
DNS consulta greatrecipesforme.com
        ↓
Resposta: IP 192.0.2.17 (site falso do atacante)
        ↓
Tráfego redirecionado → dados exfiltrados
        ↓
Computador do usuário comprometido 💀
```

---

## 🛡️ Recomendações

**Contra Brute Force:**
- ✅ Implementar **2FA** na conta de administrador
- ✅ Bloquear IP após tentativas de login falhas
- ✅ Exigir senhas com 15+ caracteres
- ✅ Proibir reutilização de senhas anteriores

**Contra DNS Spoofing:**
- ✅ Habilitar **DNSSEC**
- ✅ Forçar **HTTPS** em todo o site
- ✅ Implementar **Content Security Policy (CSP)**
- ✅ Monitorar integridade do código do site

---

## 📄 Relatório Completo

👉 [dns-spoofing-incident-report.md](./dns-spoofing-incident-report.md)

---

*Portfólio de Cibersegurança — Davi Santos*  
[![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/davi-santos-8b9569286)
